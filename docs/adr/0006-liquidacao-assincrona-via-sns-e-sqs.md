# ADR 0006: Liquidação assíncrona via SNS e SQS

- **Status:** Proposta
- **Data:** 2026-10-02

## Contexto

Ao fim de cada rodada, todas as apostas pendentes precisam ser liquidadas. Fazer isso de forma síncrona no `round-engine` atrasaria a rodada seguinte e acoplaria os serviços.

## Decisão

O `round-engine` publica `round.crashed` num tópico SNS, assinado por uma fila SQS `settle-bets` com DLQ. O `settlement-worker` consome com processamento idempotente e só remove a mensagem depois de processada.

## Alternativas consideradas

- Chamada direta do round-engine ao worker: acoplamento e perda de mensagens em falha.
- Fila sem tópico: impede outros consumidores futuros do mesmo evento.

## Consequências

- Entrega pelo menos uma vez exige idempotência real no worker.
- Em desenvolvimento e testes, SNS e SQS rodam no LocalStack.
