# CI/CD

Pipelines no GitHub Actions. Cada workflow nasce no PR que traz o código que ele testa ou publica; esta página descreve o estado alvo.

## Gatilhos

### PR para `develop` ou `main`: CI

Com filtro por pasta, para rodar só o que mudou.

- **backend:** lint, typecheck, testes unitários, testes de integração com PostgreSQL, Redis e LocalStack como serviços do job, build, build da imagem sem publicar e scan de vulnerabilidades da imagem.
- **frontend:** lint, testes e build.
- **terraform:** `fmt`, `validate`, lint, scan de segurança e `plan` comentado no PR quando a infraestrutura muda.

### Merge em `develop`

Deploy do frontend num ambiente de prévia na Cloudflare.

### Tag `vX.Y.Z` em `main`: release

1. Build das três imagens (gateway, round-engine, settlement-worker), uma vez, multi-arquitetura (amd64 e arm64).
2. Scan da imagem; vulnerabilidade crítica bloqueia a publicação.
3. Push para o **ECR Public** (vitrine) e para o **ECR privado** na região da stack (origem do deploy), com o mesmo digest. Tags `X.Y.Z`, `X.Y` e o SHA do commit.
4. Assinatura com cosign (keyless) e atestados de SBOM e proveniência.
5. GitHub Release com changelog gerado a partir dos Conventional Commits.
6. Deploy do frontend em produção na Cloudflare.
7. Deploy do backend na AWS, com aprovação manual: nova task definition apontando para o digest, atualização dos serviços do ECS e espera até ficarem estáveis. O circuit breaker do ECS faz rollback automático. Se a stack estiver destruída, o job registra que não há ambiente e encerra sem erro.

### Infraestrutura

`terraform apply` só por `workflow_dispatch`, com aprovação manual. Nunca automático no merge.

## Registries

- **ECR privado:** o ECS puxa daqui. Por ter VPC endpoints, permite que as tasks em subnets privadas baixem imagens sem passar por NAT, caso essa seja a escolha de rede (decisão em aberto). Tags imutáveis e política de ciclo de vida guardando as últimas versões.
- **ECR Public:** vitrine pública das imagens, linkada no README. A API do ECR Public opera em `us-east-1`; o job autentica lá para o push público e na região da stack para o privado.
- Docker Hub foi descartado (ADR 0010): limite de pull anônimo arriscado no scale-out e exigiria token de longa duração.

## Credenciais

| Destino | Credencial | Proteção |
|---|---|---|
| AWS | Nenhuma fixa: OIDC do GitHub | Role de `plan` somente leitura; role de release válida só para tags `v*`, limitada a enviar imagens aos dois repositórios ECR e atualizar os serviços do ECS; role de `apply` só por `workflow_dispatch` |
| Cloudflare | API token | Sem OIDC disponível. Token restrito a Workers da conta e à zona do domínio, com expiração, guardado como secret de ambiente |

Regras gerais:

- **Ambientes do GitHub:** `preview` aceita `develop`; `production` aceita só tags `v*` e exige aprovação manual. Secrets de produção ficam no ambiente, inacessíveis a jobs de PR.
- Actions fixadas por SHA, com Dependabot atualizando actions, npm, imagens base e providers do Terraform.
- `permissions` mínimas declaradas em cada workflow; sem `pull_request_target`; nenhum secret para PRs vindos de fork.
- Secret scanning e push protection ligados; CodeQL para TypeScript.
- Imagens com usuário não root, base fixada por digest, `.dockerignore` estrito e nenhum segredo embutido.

## Em que marco cada parte entra

| Marco | Pipeline |
|---|---|
| m0 | Dependabot, template de PR, proteção de branches, esta documentação |
| m1 | CI do backend: lint, typecheck, testes unitários |
| m2 | Testes de integração com serviços no CI |
| m3 | Dockerfile, build e scan no CI, primeira release com push para o ECR |
| frontend | Deploy de prévia e de produção na Cloudflare quando a pasta ganhar código |
| m6 | Terraform no CI, `plan` em PR, `apply` manual, deploy no ECS |
| m7 | Workflow manual do teste de carga com k6 |
