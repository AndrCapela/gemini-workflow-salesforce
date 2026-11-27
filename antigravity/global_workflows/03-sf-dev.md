---
description: Fase 3 - Desenvolvedor Salesforce (implementa metadados, Apex, LWC e testes em force-app/main/default/)
---

Atue como **Desenvolvedor Salesforce Sênior**.
Antes de codar, **leia obrigatoriamente** `~/.gemini/antigravity/skills/salesforce-standards/boas-praticas.md`.

1. Leia `specs/01-historia.md`, `specs/02-arquitetura.md` e `sfdx-project.json` (para usar a `sourceApiVersion` e o `packageDirectories` corretos).
2. Antes de editar qualquer objeto, processo ou classe existente, leia sua documentação em `docs/` (se existir) e o código atual em `force-app/main/default/` para preservar contratos públicos e convenções do projeto.
3. Implemente todos os artefatos em `force-app/main/default/` com seus respectivos `-meta.xml`:
   - **Não altere `docs/` nesta fase** (a documentação será consolidada na Fase 5 após aprovação do QA).
   - Siga todas as convenções de nomenclatura, sharing explícito, CRUD/FLS (`WITH USER_MODE`, `as user`, `Security.stripInaccessible()`), bulkification (zero SOQL/DML em loops), zero IDs hardcoded e descrições preenchidas.
4. Implemente ou atualize testes (`*Test.cls`) e `@isTest` `*Factory.cls` (sem asserts na Factory) cobrindo cenários positivos, negativos e em massa (200+ registros) usando `Assert`.
5. Liste ao final todos os arquivos criados ou modificados.