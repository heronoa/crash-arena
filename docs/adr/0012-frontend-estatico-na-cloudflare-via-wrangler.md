# ADR 0012: Frontend estático na Cloudflare via wrangler

- **Status:** Aceita
- **Data:** 2026-10-02

## Contexto

O frontend é um cliente mínimo e estático. Hospedá-lo na AWS não demonstra nada além do que o backend já demonstra.

## Decisão

Frontend publicado na Cloudflare com wrangler: prévia a cada merge em `develop`, produção a cada tag. Token de API restrito à conta e à zona, com expiração, guardado como secret de ambiente.

## Alternativas consideradas

- S3 com CloudFront: funciona, mas adiciona custo e infraestrutura sem ganho de demonstração.

## Consequências

- Único segredo de longa duração do pipeline é o token da Cloudflare.
- O frontend precisa liberar o domínio da API na CSP.
