# ADR 0002: Monorepo com backend, frontend e infraestrutura

- **Status:** Aceita
- **Data:** 2026-10-02

## Contexto

O projeto tem três serviços de backend que compartilham o mesmo domínio, um cliente web mínimo e toda a infraestrutura em Terraform. Mudanças costumam atravessar mais de uma parte (um evento novo mexe no domínio, no worker e na fila).

## Decisão

Um repositório com `backend/` (monorepo NestJS com `apps/` e `libs/`), `frontend/`, `terraform/`, `loadtest/`, `docs/` e `.ia_context/`. No backend, `libs/domain` concentra o domínio em TypeScript puro e é compartilhado pelos três serviços.

## Alternativas consideradas

- Um repositório por serviço: duplicaria o domínio ou exigiria publicar pacotes internos, e espalharia o histórico de PRs.
- Um único app NestJS: impediria escalar o gateway independentemente do loop da rodada (ver ADR 0003).

## Consequências

- Um único histórico de PRs mostra a evolução completa.
- O CI precisa de filtros por pasta para não rodar tudo em toda mudança.
