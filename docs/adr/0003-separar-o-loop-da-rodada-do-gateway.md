# ADR 0003: Separar o loop da rodada do gateway

- **Status:** Proposta
- **Data:** 2026-10-02

## Contexto

O gateway precisa escalar horizontalmente atrás do ALB. Se o loop da rodada rodasse em cada réplica, cada uma sortearia um crash diferente.

## Decisão

Três serviços: `round-engine` com exatamente uma réplica, dono da rodada; `gateway` sem estado, de 1 a N réplicas, recebendo os ticks pelo Redis pub/sub; `settlement-worker` consumindo a fila de liquidação. Se o `round-engine` cair, o ECS sobe outro e a rodada em andamento é anulada com reembolso.

## Alternativas consideradas

- Eleição de líder entre réplicas do gateway com advisory lock no PostgreSQL: mais resiliente, mais complexo de testar.
- Estado da rodada em cada réplica com sincronização: arriscado e difícil de provar correto.

## Consequências

- Ponto único de falha aceito e documentado.
- O domínio precisa da regra de anulação de rodada desde o início.
