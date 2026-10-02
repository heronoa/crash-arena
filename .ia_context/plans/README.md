# Planos

Um arquivo por etapa de trabalho, nomeado `<feature>-m<N>-<etapa>.plan.md`, por exemplo `round-m1-domain.plan.md`.

Front-matter mínimo:

```yaml
---
feature: round
milestone: m1
etapa: domain
executor: claude        # roteamento de modelo
issue: 12               # issue do GitHub que o PR vai fechar
branch: feature/m1-round-aggregate
status: proposto        # proposto | aprovado | em-execucao | concluido
---
```

Corpo: objetivo, critérios de aceite, testes a escrever primeiro, arquivos que serão tocados e o que fica fora do escopo. Desvios durante a execução são registrados no próprio plano.
