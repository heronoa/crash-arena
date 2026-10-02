# Brief do projeto

## Objetivo

Projeto vitrine de backend em tempo real, com stack alinhado ao mercado de jogos online e iGaming: NestJS, TypeScript, MikroORM, PostgreSQL, Docker, DDD, microsserviços, REST, WebSockets e AWS (SQS, SNS, S3, Secrets Manager). O repositório precisa ser entendido por um revisor em cerca de 10 minutos de leitura do README.

Na AWS, o objetivo é **demonstrar domínio, não economizar**: load balancer com autoscaling, encolhimento em horários de pouco uso e tudo em Terraform. O custo é controlado pelo ciclo de vida (`terraform destroy` fora dos períodos de demonstração), não por cortar componentes.

## O jogo

A rodada abre para apostas, o multiplicador sobe em tempo real a partir de 1,00× e para num ponto sorteado (o crash). Quem saca antes ganha aposta × multiplicador; quem não saca perde. Só dinheiro fictício.

**Provably fair:** antes da rodada, o servidor publica o hash da seed do servidor (commit). Depois do crash, revela a seed (reveal). Qualquer pessoa recalcula o ponto de crash a partir de HMAC-SHA256 com a seed do servidor, a seed do cliente e o nonce da rodada, e confere que bate. A fórmula exata fica definida no plano do m1 e registrada em ADR.

## Domínio

| Contexto | Agregado | Responsabilidade |
|---|---|---|
| Rounds | `Round` | Estados `Betting → Running → Crashed`, commit e reveal da seed, cálculo do ponto de crash |
| Betting | `Bet` | Aposta com chave de idempotência, saque, invariante de só sacar com rodada em `Running` |
| Wallet | `Wallet` + `LedgerEntry` | Saldo derivado de lançamentos imutáveis, nunca de um campo `balance` solto |

## Serviços

| Serviço | Réplicas | Papel |
|---|---|---|
| `round-engine` | exatamente 1 | Dono do loop: commit da seed, ticks, crash. Publica ticks no Redis (pub/sub) e `round.crashed` no SNS |
| `gateway` | 1 a N | REST + WebSocket atrás do ALB. Sem estado local: assina o Redis e repassa os ticks |
| `settlement-worker` | 1 a N | Consome a fila SQS e liquida apostas de forma idempotente |

## Fluxos principais

- `POST /bets` com header `Idempotency-Key`: reserva saldo no ledger e cria a aposta.
- `POST /bets/:id/cashout`: lock otimista na aposta e checagem do estado da rodada na mesma transação, resolvendo de forma determinística a corrida entre saque e crash.
- Fim da rodada: `round.crashed` no SNS → fila SQS `settle-bets` (com DLQ) → worker liquida as apostas pendentes.
- Reveal da seed e resumo da rodada gravados no S3 como trilha de auditoria.
- `GET /rounds/:id/verify`: devolve seed, hash e o passo a passo do cálculo.
- WebSocket: `round:state`, `round:tick`, `bet:placed`, `bet:cashedOut`.
- `GET /health`: usado pelo portfólio para decidir entre mostrar o jogo ao vivo ou a simulação no navegador.

## Infraestrutura alvo

VPC em duas zonas, ALB em subnets públicas, tasks do ECS Fargate em subnets privadas, RDS PostgreSQL, ElastiCache Redis, SNS, SQS com DLQ, S3, Secrets Manager e ECR (privado para deploy, público como vitrine). Detalhes em `docs/architecture.md`.

## Contexto do autor

Experiência anterior com NestJS, TypeORM, PostgreSQL, Redis, Docker Swarm, GCP e Terraform. Este projeto amplia esse repertório para MikroORM e para o conjunto ECS, ALB, SQS e SNS na AWS. Portfólio em https://heronoa.com.br.
