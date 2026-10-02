# ADR 0010: Imagens no ECR público e privado, sem credenciais fixas

- **Status:** Aceita
- **Data:** 2026-10-02

## Contexto

As imagens precisam ser públicas como vitrine e confiáveis como origem do deploy. Docker Hub foi considerado.

## Decisão

Cada release publica a mesma imagem (mesmo digest) no ECR Public, como vitrine, e no ECR privado da região da stack, de onde o ECS puxa. O pipeline autentica na AWS via OIDC do GitHub, sem token de longa duração. Tags imutáveis no ECR privado, assinatura com cosign e atestados de SBOM e proveniência.

## Alternativas consideradas

- Docker Hub: limite de pull anônimo arriscado durante scale-out e token de longa duração no pipeline.
- Só ECR Public: sem VPC endpoints, o pull sairia pela internet.

## Consequências

- Dois pushes por release.
- A API do ECR Public opera em `us-east-1`, independentemente da região da stack.
