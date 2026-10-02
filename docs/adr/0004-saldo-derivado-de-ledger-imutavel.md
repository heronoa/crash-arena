# ADR 0004: Saldo derivado de ledger imutável

- **Status:** Proposta
- **Data:** 2026-10-02

## Contexto

Aposta, saque, liquidação e reembolso mexem no saldo, às vezes de forma concorrente. Um campo `balance` atualizado no lugar perde rastreabilidade e facilita condições de corrida.

## Decisão

A `Wallet` não guarda saldo editável: o saldo é a soma de `LedgerEntry` imutáveis (reserva, liberação, prêmio, reembolso). Cada lançamento referencia a aposta ou rodada que o gerou.

## Alternativas consideradas

- Campo `balance` com lock pessimista: mais simples, sem trilha de auditoria.

## Consequências

- Toda mudança de saldo fica auditável.
- Leitura do saldo exige agregação ou um snapshot materializado (otimização futura, se medida necessária).
