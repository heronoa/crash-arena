# ADR 0001: Registrar decisões de arquitetura em ADR

- **Status:** Aceita
- **Data:** 2026-10-02

## Contexto

O projeto é avaliado também pelas decisões tomadas, não só pelo código. Sem registro, o porquê de cada escolha se perde, e quem lê o repositório vê só o resultado.

## Decisão

Toda decisão de arquitetura relevante vira um ADR curto em `docs/adr/`, numerado e escrito no mesmo PR que introduz a mudança. Formato: contexto, decisão, alternativas consideradas e consequências. ADRs não são editados depois de aceitos; uma decisão nova substitui a anterior e aponta para ela.

## Alternativas consideradas

- Documentação livre em wiki: fica fora do histórico do código e não acompanha os PRs.
- Só comentários no código: não registram alternativas descartadas.

## Consequências

- Cada PR com decisão nova carrega um arquivo a mais.
- O índice em `docs/adr/README.md` precisa ser mantido.
