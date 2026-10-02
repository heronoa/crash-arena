# ADR 0007: Resultado verificável por commit e reveal com HMAC

- **Status:** Proposta
- **Data:** 2026-10-02

## Contexto

O jogador precisa poder confirmar que o ponto de crash não foi escolhido depois das apostas.

## Decisão

Antes da rodada, o servidor publica o hash da seed do servidor. Depois do crash, revela a seed. O ponto de crash é derivado de HMAC-SHA256 com a seed do servidor, a seed do cliente e o nonce da rodada. `GET /rounds/:id/verify` devolve os insumos e o passo a passo, e o frontend recalcula no navegador.

## Alternativas consideradas

- Sorteio com gerador aleatório comum: não verificável.

## Consequências

- A fórmula exata e a margem da casa ficam definidas no plano do m1 e registradas aqui antes do primeiro PR de domínio.
- A seed do servidor nunca pode vazar antes do reveal.
