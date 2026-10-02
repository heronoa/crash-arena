# CLAUDE.md

Instruções globais de trabalho. Regras específicas de projeto ficam no `CLAUDE.md` do repositório e têm precedência sobre este arquivo.

## Idioma e tom

- Responder em português (PT-BR). Código, nomes de símbolos, commits e mensagens de log em inglês.
- Direto e analítico. Sem preâmbulo, sem desculpas, sem resumo do que acabou de ser feito se o diff já mostra.

## Princípio central: planejar antes de aplicar

1. **Entender** — ler o código e o contexto relevantes antes de propor qualquer coisa.
2. **Planejar** — produzir um plano explícito (arquivo `.plan.md` quando a tarefa tiver mais de um passo).
3. **Aplicar** — só depois do plano aprovado, em diffs pequenos e verificáveis.

Nunca sair aplicando mudanças "para ver se resolve". Se o caminho não está claro, parar e investigar.

## Pipeline TDD

Comandos disponíveis (em `.claude/commands/`):

| Comando | Função |
|---|---|
| `/create-plan` | Gera o plano da feature dividido em milestones |
| `/tdd-red` | Escreve os testes que falham para o milestone atual |
| `/tdd-impl` | Implementação mínima para os testes passarem |
| `/tdd-green-refactor` | Refatora mantendo a suíte verde |
| `/apply-plan` | Executa um plano já aprovado |

Regras do pipeline:

- Não pular etapas. Testes primeiro, implementação depois. Não alterar teste para fazer a implementação passar sem justificar.
- Rodar a suíte ao fim de cada etapa e reportar o resultado real (não o esperado).
- Um milestone por vez. Não adiantar código do próximo milestone.

### Arquivos de plano

- Local: `ia_context/plans/`
- Nome: `<feature>-m<N>-<camada>.plan.md` — ex.: `enrollment-m1-repository.plan.md`, `enrollment-m2-service.plan.md`, `enrollment-m3-controller.plan.md`
- Front-matter obrigatório:

```yaml
---
feature: <nome>
milestone: m1-repository
executor: claude | deepseek
status: draft | approved | done
---
```

- `executor: claude` → tarefas abertas, de julgamento: arquitetura, design de API, debugging, decisões com trade-off.
- `executor: deepseek` → tarefas fechadas e bem especificadas: implementação mecânica a partir de um plano detalhado.
- Ao criar um plano, sugerir o `executor` de cada milestone com base nesse critério.

### Handoff entre sessões

Toda etapa deve deixar artefato em arquivo (plano atualizado, descrição em Markdown do que mudou e por quê) para que outra sessão — ou outro modelo — retome sem depender do histórico da conversa.

## Edição de código

- Diffs cirúrgicos. Nunca reescrever um arquivo inteiro para mudar poucas linhas.
- Não renomear, reformatar ou "melhorar" código fora do escopo da tarefa.
- Se notar algo fora do escopo que merece atenção, **apontar** no final da resposta — não corrigir por conta própria.
- Não expandir escopo sem perguntar. Escopo pedido = escopo entregue.

## Testes

- Base: testes unitários e e2e (Vitest no stack Node/TypeScript).
- Não criar testes de integração a menos que seja pedido explicitamente.
- e2e é o nível preferido para validar fluxos completos.

## Diagnóstico e debugging

- Separar sempre, de forma explícita:
  - **O que a documentação afirma**
  - **O que foi confirmado neste caso** (log, saída de comando, reprodução)
  - **Hipótese** (ainda não verificada)
- Não rotular nada como "bug" (da lib, do framework, do provider) sem evidência direta. Sem evidência, é hipótese.
- Preferir validação empírica: propor o teste/comando que confirma ou descarta cada hipótese antes de propor a correção.

## Infra (Docker, Terraform, GCP, Proxmox, CI)

- Depurar e planejar primeiro. Entregar o diagnóstico e o plano de ação completo, não uma sequência de comandos tentativa-e-erro.
- Comandos destrutivos ou que alteram estado (`terraform apply`, `docker stack rm`, migrations, `DROP`, `rm -rf`, deletes no GCP) **nunca** são executados sem confirmação explícita. Mostrar antes o `plan`/dry-run.
- Indicar sempre o ambiente-alvo (dev, staging, prod) antes de qualquer ação.

## Segurança e supply chain

- Nunca commitar, imprimir ou logar segredos. Usar variáveis de ambiente / secret manager; se um segredo aparecer no código, apontar.
- CI usa `npm ci`; lockfile sempre commitado.
- `npm audit --audit-level=high` bloqueia build — não contornar.
- Não adicionar dependência nova sem justificar (o que resolve, alternativa sem dependência, manutenção do pacote).

## Documentação

- Todo documento, relatório ou descrição sem formato especificado sai em Markdown (`.md`).
- Decisões arquiteturais relevantes viram ADR (`docs/adr/ADR-XXX-<titulo>.md`): contexto, decisão, alternativas consideradas, consequências.
- Runbooks: sugerir criar um quando um procedimento foi resolvido mais de 2 vezes, ou quando foi um one-off crítico e cheio de armadilhas.

## Stack padrão (quando o projeto não disser o contrário)

NestJS · TypeORM · PostgreSQL · Redis · Docker / Docker Swarm · Terraform · GCP (Cloud SQL, Cloud Build, Artifact Registry, Firebase Hosting) · Woodpecker CI · Vitest