# ADR 0009: Autoscaling do gateway por conexões ativas e por horário

- **Status:** Proposta
- **Data:** 2026-10-02

## Contexto

O gateway serve principalmente WebSocket. Uma conexão aberta é uma requisição longa que quase não usa CPU, então CPU e contagem de requisições do ALB não refletem a carga real. O projeto também precisa mostrar encolhimento em horários de pouco uso.

## Decisão

Target tracking numa métrica customizada de conexões WebSocket ativas por task, publicada pelo gateway no CloudWatch, mais ações agendadas que mudam mínimo e máximo de réplicas por horário. Scale-in com drenagem: deregistration delay no ALB, aviso de reconexão aos clientes no SIGTERM e `stopTimeout` calibrado. Só transporte WebSocket.

## Alternativas consideradas

- Escala por CPU: não reage à carga de conexões.
- Sticky session com long polling: complica o balanceamento e a drenagem.

## Consequências

- O gateway precisa publicar a métrica e tratar SIGTERM corretamente.
- O teste de carga do m7 mede conexões perdidas no scale-in.
