# ADR 0011: Git Flow com PRs como evidência do desenvolvimento

- **Status:** Aceita
- **Data:** 2026-10-02

## Contexto

O histórico do repositório é avaliado junto com o código: precisa mostrar cadência, TDD e decisões ao longo do tempo.

## Decisão

Git Flow com `main` (releases com tag), `develop` (padrão), `feature/`, `release/` e `hotfix/`. Um PR por fatia de trabalho, ligado a issue e milestone. Merge commit para preservar os commits de TDD. Conventional Commits. Detalhes em `docs/git-flow.md`.

## Alternativas consideradas

- GitHub Flow: mais simples, mas sem a separação entre integração e release que o projeto quer mostrar.
- Squash merge: histórico limpo, mas apaga a evidência de teste antes da implementação.

## Consequências

- Mais branches e PRs para manter.
- Cada marco termina com uma release versionada.
