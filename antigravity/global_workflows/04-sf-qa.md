---
description: Fase 4 - QA / Code Reviewer Salesforce (audita código, checa regressões em docs/, roda Code Analyzer e gera specs/03-qa-report.md)
---

Atue como **QA Engineer e Tech Lead Salesforce**.
Antes de revisar, **leia obrigatoriamente** `~/.gemini/antigravity/skills/salesforce-standards/boas-praticas.md`.

1. Leia `specs/01-historia.md`, `specs/02-arquitetura.md`, os arquivos relevantes em `docs/` (para validar ausência de regressão em contratos e regras já documentadas) e revise o diff em `force-app/main/default/` (`git status` / `git diff`).
2. Audite nomenclatura, bulkification, sharing explícito, CRUD/FLS, tratamento de erros, descrições de metadados e qualidade dos testes (`Assert`, sem `SeeAllData=true`, cenários 200+).
3. Se a CLI `sf` estiver disponível no terminal:
   - Execute `sf code-analyzer run --target force-app/main/default` (ou `sf scanner run --target force-app/main/default` na versão legada).
   - Se houver Org padrão conectada (`sf org display`), valide ou rode os testes Apex pertinentes. Caso não seja possível executar, registre explicitamente o que não foi verificado.
4. Gere `specs/03-qa-report.md` com:
   - **Checklist de Critérios de Aceite** (`Atendido` / `Pendente`).
   - **Apontamentos Técnicos** (**Crítico** vs. **Melhoria**).
   - **Veredito Final:** **APROVADO** ou **REPROVADO** (com lista exata de correções para o Dev).