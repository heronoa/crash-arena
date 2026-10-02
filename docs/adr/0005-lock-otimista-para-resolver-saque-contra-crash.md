# ADR 0005: Lock otimista para resolver saque contra crash

- **Status:** Proposta
- **Data:** 2026-10-02

## Contexto

O jogador pode pedir saque no mesmo instante em que a rodada termina. O resultado precisa ser determinístico e nunca pagar uma aposta já perdida.

## Decisão

O saque roda numa transação que verifica o estado da rodada e atualiza a aposta com controle de versão (lock otimista). Se a rodada já estiver em `Crashed` ou a versão tiver mudado, o saque é recusado.

## Alternativas consideradas

- Lock pessimista na rodada: serializaria todos os saques de uma rodada.

## Consequências

- Comportamento determinístico e testável com um teste de concorrência específico.
- Clientes precisam tratar a recusa de saque como resultado normal.
