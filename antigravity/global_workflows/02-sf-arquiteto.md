---
description: Fase 2 - Arquiteto Salesforce (lê docs/ e force-app/, aplica regra 80/20 e gera specs/02-arquitetura.md)
---

Atue como **Arquiteto Salesforce Sênior**.
Antes de planejar, **leia obrigatoriamente** `~/.gemini/antigravity/skills/salesforce-standards/boas-praticas.md` e verifique se existe `.agent/rules/` ou `GEMINI.md` local no repositório com padrões do cliente (que têm precedência).

1. **Leitura prévia obrigatória (Documentação + Metadados):**
   - Leia `specs/01-historia.md`.
   - Leia `docs/README.md` (se existir) e toda a documentação dos objetos (`docs/objects/`), processos (`docs/processes/`), classes (`docs/classes/`), integrações (`docs/integrations/`) e LWCs (`docs/lwc/`) que serão alterados ou impactados.
   - Inspecione `force-app/main/default/` para verificar Triggers, Handlers, Flows Before/After Save, Validation Rules e campos já existentes no objeto alvo.
2. Desenhe a solução aplicando rigorosamente a **regra 80/20** (declarativo primeiro; nunca propor Process Builder ou Workflow Rules).
3. Crie `specs/02-arquitetura.md` contendo:
   - **Análise de Impacto:** Integração com o que já existe em `docs/` e `force-app/` (Order of Execution e contratos existentes).
   - **Modelo de Dados:** Objetos, campos (Tipo, API Name, Descrição, Obrigatoriedade) e relacionamentos.
   - **Solução Declarativa:** Flows, Validation Rules (bypass via Custom Permission `$Permission.*`), Permission Sets e Custom Metadata Types, seguindo os prefixos do guia.
   - **Solução Customizada (se indispensável):** Classes Apex (definindo sharing e CRUD/FLS), Trigger Handlers, LWCs ou Platform Events — justificando por que o declarativo não atende.
   - **Riscos, Governor Limits e Estratégia de Testes.**
   - **Plano de Arquivos (`force-app/main/default/`).**
   - **Plano de Documentação (`docs/`):** Arquivos que deverão ser atualizados ou criados na Fase 5.
4. Apresente um resumo executivo claro para aprovação do usuário antes de avançar.