# Registros de decisão (ADRs)

Cada decisão de arquitetura relevante tem um registro curto aqui. **Aceita** foi decidida e vale; **Proposta** é a direção atual, a confirmar no PR que a implementar (pode virar Aceita, ser alterada ou substituída).

| ADR | Decisão | Status |
|---|---|---|
| [0001](0001-registrar-decisoes-de-arquitetura-em-adr.md) | Registrar decisões de arquitetura em ADR | Aceita |
| [0002](0002-monorepo-com-backend-frontend-e-infraestrutura.md) | Monorepo com backend, frontend e infraestrutura | Aceita |
| [0003](0003-separar-o-loop-da-rodada-do-gateway.md) | Separar o loop da rodada do gateway | Proposta |
| [0004](0004-saldo-derivado-de-ledger-imutavel.md) | Saldo derivado de ledger imutável | Proposta |
| [0005](0005-lock-otimista-para-resolver-saque-contra-crash.md) | Lock otimista para resolver saque contra crash | Proposta |
| [0006](0006-liquidacao-assincrona-via-sns-e-sqs.md) | Liquidação assíncrona via SNS e SQS | Proposta |
| [0007](0007-resultado-verificavel-por-commit-e-reveal-com-hmac.md) | Resultado verificável por commit e reveal com HMAC | Proposta |
| [0008](0008-ecs-fargate-atras-de-um-application-load-balancer.md) | ECS Fargate atrás de um Application Load Balancer | Aceita |
| [0009](0009-autoscaling-do-gateway-por-conexoes-ativas-e-por-horario.md) | Autoscaling do gateway por conexões ativas e por horário | Proposta |
| [0010](0010-imagens-no-ecr-publico-e-privado-sem-credenciais-fixas.md) | Imagens no ECR público e privado, sem credenciais fixas | Aceita |
| [0011](0011-git-flow-com-prs-como-evidencia-do-desenvolvimento.md) | Git Flow com PRs como evidência do desenvolvimento | Aceita |
| [0012](0012-frontend-estatico-na-cloudflare-via-wrangler.md) | Frontend estático na Cloudflare via wrangler | Aceita |

## Como escrever um novo ADR

Copie `template.md`, use o próximo número e escreva no mesmo PR que introduz a mudança. Um ADR aceito não é editado: se a decisão mudar, um novo ADR substitui o anterior e os dois apontam um para o outro.
