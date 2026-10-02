# Git Flow

O histórico de PRs é parte da entrega: mostra a cadência do trabalho, o TDD e as decisões tomadas ao longo do caminho. Por isso, ele precisa ser o retrato real do desenvolvimento: PRs pequenos, abertos no ritmo do trabalho, sem reescrita de histórico.

## Branches

| Branch | Papel | Sai de | Entra em |
|---|---|---|---|
| `main` | Só releases, cada uma com tag `vX.Y.Z` | | |
| `develop` | Integração. Branch padrão do repositório | `main` | |
| `feature/m<N>-<descricao>` | Uma fatia de trabalho | `develop` | `develop` por PR |
| `release/vX.Y.Z` | Ajustes finais e changelog de um marco | `develop` | `main` e `develop` |
| `hotfix/<descricao>` | Correção de algo publicado | `main` | `main` e `develop` |

Cada marco fechado vira uma release: `v0.1.0` no m1, `v0.2.0` no m2, e assim por diante. O setup (m0) sai como `v0.0.1`.

## Pull requests

- Um PR por fatia de trabalho. Um marco costuma ter de três a cinco PRs.
- Todo PR fecha uma issue (`Closes #N`) e pertence ao milestone do marco.
- Corpo segue o template: contexto, o que mudou, como testar, checklist.
- **Merge commit, nunca squash.** Os commits de red, green e refactor ficam no histórico como evidência de que o teste veio antes.
- Auto-revisão registrada no próprio PR: comentários nos trechos que merecem explicação.

## Commits

Conventional Commits: `feat`, `fix`, `test`, `refactor`, `docs`, `chore`, `ci`, `build`. Exemplo de sequência TDD num PR:

```
test(domain): round rejects cashout after crash
feat(domain): enforce running state on cashout
refactor(domain): extract round state transitions
```

## Proteções

- `main` e `develop`: só por PR, com o CI passando. Sem exigência de aprovação, porque o GitHub não permite aprovar o próprio PR.
- Push direto e force push bloqueados nas duas.

## Organização no GitHub

- **Milestones:** m0 a m8.
- **Issues:** uma por tarefa, dentro do milestone.
- **Project:** quadro com A fazer, Em andamento, Em revisão e Feito.
- **Releases:** uma por tag, com changelog gerado a partir dos commits.
