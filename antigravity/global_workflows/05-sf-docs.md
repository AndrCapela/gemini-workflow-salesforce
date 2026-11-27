---
description: Fase 5 - Documentação Viva Salesforce (atualiza ou cria docs/ as-built após aprovação do QA)
---

Atue como **Technical Writer especializado em Salesforce**.
Atualize ou crie a documentação viva do projeto refletindo exatamente o código aprovado (*as-built*).

1. **Coleta de evidências:**
   - Leia `specs/01-historia.md`, `specs/02-arquitetura.md`, `specs/03-qa-report.md` e verifique o diff real (`git status` / `git diff --name-status`) em `force-app/main/default/`.
   - Leia os arquivos finais modificados em `force-app/main/default/`. Não altere código em `force-app/` e não faça `git commit`/`git push`.

2. **Descoberta e precedência da estrutura:**
   - **Se já existir documentação em `docs/` (ou na raiz):** siga rigorosamente a estrutura, idioma e estilo adotados pelo projeto. Atualize os arquivos existentes preservando notas manuais válidas.
   - **Se NÃO existir estrutura em `docs/` (ou o item alterado não estiver coberto):** crie seguindo o padrão SFDX:
     - `docs/README.md` — Índice geral com links para todos os documentos.
     - `docs/objects/<ObjectApiName>.md` — 1 arquivo por objeto padrão/customizado impactado (campos, record types, validation rules com bypass, flows/triggers, permissões, histórico).
     - `docs/processes/<nome-do-processo>.md` — Em `kebab-case`, fluxo ponta a ponta (visão geral, atores, declarativo + customizado, **checklist de pré/pós-deploy**, histórico).
     - `docs/classes/<ClassName>.md` — Apenas para **classes Apex de produção relevantes** (Services, Handlers, Selectors, Controllers, Batches/Queueables, Callouts). Referencie `*Test` e `*Factory` dentro da classe principal em vez de criar arquivos separados.
     - `docs/integrations/<nome-integracao>.md` — Sob demanda (callouts, Named Credentials, Platform Events, APIs).
     - `docs/lwc/<componentName>.md` — Sob demanda (LWCs reutilizáveis/complexos: `@api`, eventos, Apex/LDS).

3. **Consolidação:**
   - Atualize `docs/README.md` com todos os novos arquivos criados.
   - Liste ao final os arquivos de `docs/` criados/atualizados e o checklist de passos manuais de pré/pós-deploy.