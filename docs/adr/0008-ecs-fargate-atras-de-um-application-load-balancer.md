# ADR 0008: ECS Fargate atrás de um Application Load Balancer

- **Status:** Aceita
- **Data:** 2026-10-02

## Contexto

O objetivo na AWS é demonstrar domínio de operação, em especial balanceamento de carga e escala horizontal, não minimizar custo.

## Decisão

Serviços no ECS Fargate em subnets privadas, atrás de um ALB com listener HTTPS (certificado do ACM). RDS PostgreSQL e ElastiCache Redis gerenciados. Tudo em Terraform. O custo é controlado com `terraform destroy` fora dos períodos de demonstração.

## Alternativas consideradas

- EKS: custo fixo do control plane não se justifica para três serviços.
- EC2 com docker compose e túnel: mais barato, mas não demonstra balanceamento nem autoscaling.

## Consequências

- Custo fixo real (ALB, RDS, ElastiCache e saída de rede) enquanto a stack estiver no ar.
- Ciclo de destruir e recriar precisa ser testado e documentado.
