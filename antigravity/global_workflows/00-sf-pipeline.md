---
description: Orquestrador completo do pipeline Salesforce (PO -> Arquiteto -> Dev -> QA -> Docs)
---

Execute o pipeline de entrega Salesforce em 5 fases para o requisito informado pelo usuário.
Antes de iniciar, verifique se existe `.agent/rules/` ou `GEMINI.md` local no repositório; em caso de conflito com a skill global, siga o padrão local do cliente.

1. **FASE 1 (PO):** Siga as instruções de `01-sf-po.md` (lendo `docs/` e `force-app/`).
   - **CHECKPOINT 1:** Se houver dúvidas de negócio, apresente-as ao usuário e **PARE** aguardando resposta antes de gerar `specs/01-historia.md`.
2. **FASE 2 (Arquiteto):** Siga `02-sf-arquiteto.md` (lendo `specs/01-historia.md`, `docs/` e `force-app/`) e gere `specs/02-arquitetura.md`.
   - **CHECKPOINT 2:** Apresente o resumo executivo da arquitetura e o plano de impacto em `docs/`, e **PARE** pedindo aprovação do usuário antes de codar.
3. **FASE 3 (Dev):** Após aprovação, siga `03-sf-dev.md` para implementar tudo em `force-app/main/default/`. Avance automaticamente para a Fase 4.
4. **FASE 4 (QA):** Siga `04-sf-qa.md` e gere `specs/03-qa-report.md`.
   - **Auto-correção:** Se o veredito for **REPROVADO** por erros críticos, retorne à Fase 3 (`03-sf-dev.md`) corrigindo os apontamentos e rode a Fase 4 novamente (máximo 2 ciclos). Se continuar reprovado após 2 ciclos, **PARE** e reporte os bloqueios ao usuário.
5. **FASE 5 (Docs):** Assim que o QA emitir **APROVADO**, siga `05-sf-docs.md` para atualizar/criar a documentação viva em `docs/`.
   - Encerre com o resumo final: arquivos alterados em `force-app/`, resultado das validações de QA (e o que não pôde ser executado), arquivos atualizados/criados em `docs/` e passos manuais de pré/pós-deploy.