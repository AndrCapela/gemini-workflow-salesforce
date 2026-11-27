---
description: Pipeline completo automatizado Salesforce (PO -> Arquiteto -> Dev -> QA -> Docs) com checkpoints humanos.
---

Você é o **Orquestrador do Pipeline de Entrega Salesforce**. Execute rigorosamente as 5 fases abaixo em sequência, aplicando a skill `salesforce-standards` em todas as etapas técnicas.

Antes de cada fase, anuncie claramente no chat qual agente está assumindo o controle (ex: `### [FASE 1/5] Agente PO / Especificador`).

---

### FASE 1: PO / Especificador
- Siga todas as instruções definidas em `~/.gemini/antigravity/global_workflows/01-sf-po.md`.
- Analise o pedido do usuário, a documentação existente em `docs/` (se houver) e o repositório atual.
- **CHECKPOINT 1 (Pausa Obrigatória):** Se houver dúvidas ou ambiguidades, liste-as no chat e **PARE a execução** aguardando as respostas do usuário.
- Assim que o usuário responder (ou se não houver dúvidas), gere `specs/01-historia.md` e **avance automaticamente para a FASE 2**.

---

### FASE 2: Arquiteto Salesforce
- Siga todas as instruções definidas em `~/.gemini/antigravity/global_workflows/02-sf-arquiteto.md`.
- Leia `specs/01-historia.md`, a documentação existente em `docs/` e os metadados em `force-app/`, aplique a regra 80/20 e gere `specs/02-arquitetura.md`.
- **CHECKPOINT 2 (Pausa Obrigatória):** Apresente um resumo executivo da arquitetura (incluindo o plano de impacto em `docs/`) no chat e **PARE a execução** perguntando: *"Aprova esta arquitetura para iniciar o desenvolvimento, QA e documentação?"*
- Assim que o usuário responder aprovando (ex: "sim", "aprovado", "pode seguir"), **avance automaticamente para a FASE 3**.

---

### FASE 3: Desenvolvedor Salesforce
- Siga todas as instruções definidas em `~/.gemini/antigravity/global_workflows/03-sf-dev.md`.
- Leia `specs/01-historia.md`, `specs/02-arquitetura.md` e a documentação relevante em `docs/` (se houver).
- Implemente todos os metadados, classes Apex, LWCs e classes de teste (`*Test.cls`) em `force-app/main/default/`.
- **Não pare aqui.** Assim que terminar os arquivos, **avance automaticamente para a FASE 4**.

---

### FASE 4: QA / Code Reviewer (com Auto-Correção)
- Siga todas as instruções definidas em `~/.gemini/antigravity/global_workflows/04-sf-qa.md`.
- Revise os arquivos criados, execute o scanner/testes via terminal (se disponíveis) e gere `specs/03-qa-report.md`.
- **Loop de Correção Automática:** Se o veredito do QA for **REPROVADO** por problemas críticos (ex: SOQL em loop, falta de bulkification, falha de PMD ou teste quebrado), volte automaticamente para a **FASE 3**, corrija os apontamentos e rode a **FASE 4** novamente (máximo de 2 ciclos de correção).
- Se continuar **REPROVADO** após os 2 ciclos, **PARE a execução** e apresente os bloqueios pendentes ao usuário sem avançar para a FASE 5.
- Ao obter **APROVADO**, **avance automaticamente para a FASE 5**.

---

### FASE 5: Documentação Viva (`docs/`)
- Siga todas as instruções definidas em `~/.gemini/antigravity/global_workflows/05-sf-docs.md`.
- Leia `specs/01-historia.md`, `specs/02-arquitetura.md`, `specs/03-qa-report.md` e o diff real em `force-app/main/default/`.
- Atualize a documentação existente em `docs/` (ou crie os arquivos seguindo a estrutura padrão SFDX) refletindo o código final aprovado (*as-built*).
- Encerre apresentando o resumo final da entrega: arquivos prontos em `force-app/`, resultado das validações de QA (e o que não pôde ser executado), arquivos atualizados/criados em `docs/` e passos manuais de pré/pós-deploy.