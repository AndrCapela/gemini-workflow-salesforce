# 🚀 Antigravity Multi-Agent Pipeline — Salesforce Delivery

Configuração global para o **Google Antigravity** que estrutura uma esteira multiagente especializada em **Salesforce**, utilizando habilidades sob demanda (`skills/`) e workflows globais (`global_workflows/`) sem interferir em repositórios de outras stacks (Python, Node, etc.).

## 📂 Estrutura do Repositório

Este repositório é mapeado diretamente para o diretório global `~/.gemini/` do usuário, utilizando um `.gitignore` em modo *allowlist* para versionar **apenas** skills e workflows compartilháveis. Tokens, sessões, caches locais e o arquivo pessoal `GEMINI.md` ficam intencionalmente fora do Git para não sobrescrever preferências locais de cada desenvolvedor.

```text
~/.gemini/
├── .gitignore                                  # Protege dados locais e GEMINI.md pessoal (allowlist)
├── README.md                                   # Esta documentação
└── antigravity/
    ├── skills/
    │   └── salesforce-standards/
    │       ├── SKILL.md                        # Skill ativada em contexto Salesforce
    │       └── boas-praticas.md                # Guia de padrões, nomenclatura e segurança
    └── global_workflows/
        ├── 00-sf-pipeline.md                   # Orquestrador Mestre (5 Fases)
        ├── 01-sf-po.md                         # Fase 1: PO / Especificador
        ├── 02-sf-arquiteto.md                  # Fase 2: Arquiteto Salesforce (Regra 80/20)
        ├── 03-sf-dev.md                        # Fase 3: Desenvolvedor Salesforce
        ├── 04-sf-qa.md                         # Fase 4: QA / Code Reviewer
        └── 05-sf-docs.md                       # Fase 5: Documentação Viva as-built
```

## ⚙️ Instalação em uma Nova Máquina

Siga os passos abaixo para replicar essa configuração em qualquer computador com o **Antigravity** instalado.

### 1. Instale sem sobrescrever sua sessão ou seu `GEMINI.md`

Caso você já utilize o Antigravity na máquina, a pasta `~/.gemini` já existirá com suas credenciais, cache e seu `GEMINI.md` pessoal. **Não a apague.** Clone o repositório em um diretório temporário e restaure apenas os arquivos rastreados:

#### Linux / macOS:
```bash
git clone https://github.com/AndrCapela/gemini-workflow-salesforce.git /tmp/gemini-config
mkdir -p ~/.gemini
cp -r /tmp/gemini-config/.git ~/.gemini/
cp /tmp/gemini-config/.gitignore ~/.gemini/
cd ~/.gemini
git checkout -- .
rm -rf /tmp/gemini-config
```

#### Windows (PowerShell):
```powershell
git clone https://github.com/AndrCapela/gemini-workflow-salesforce.git "$env:TEMP\gemini-config"
New-Item -ItemType Directory -Force -Path "$HOME\.gemini" | Out-Null
Copy-Item -Recurse -Force "$env:TEMP\gemini-config\.git" "$HOME\.gemini\"
Copy-Item -Force "$env:TEMP\gemini-config\.gitignore" "$HOME\.gemini\"
Set-Location "$HOME\.gemini"
git checkout -- .
Remove-Item -Recurse -Force "$env:TEMP\gemini-config"
```

### 2. Valide o isolamento do Git

Execute `git status` dentro de `~/.gemini/`. O Git deve mostrar a árvore limpa (*working tree clean*), ignorando automaticamente arquivos de autenticação, logs, sessões e seu `GEMINI.md` local.

---

## 📁 Estrutura Esperada nos Projetos Salesforce (SFDX)

O pipeline segue a estrutura padrão do Salesforce DX (`force-app/main/default/`), mantendo a documentação viva em `docs/` (versionada no Git) e os artefatos temporários da demanda em `specs/` (fora de `packageDirectories`, sem impactar deploys via `sf` CLI):

```text
<projeto-salesforce>/
├── sfdx-project.json
├── force-app/main/default/           # Metadados SFDX (classes, triggers, flows, lwc, objects...)
├── specs/                            # Artefatos temporários da demanda (recomendado no .gitignore)
│   ├── 01-historia.md
│   ├── 02-arquitetura.md
│   └── 03-qa-report.md
└── docs/                             # Documentação viva mantida automaticamente pelo 05-sf-docs
    ├── README.md                     # Índice geral do projeto
    ├── objects/                      # 1 arquivo por objeto impactado (ex: Case.md)
    ├── processes/                    # 1 arquivo por processo ponta a ponta (ex: case-management.md)
    ├── classes/                      # 1 arquivo por classe Apex de produção relevante
    ├── integrations/                 # Sob demanda (callouts, Named Credentials, Platform Events)
    └── lwc/                          # Sob demanda (LWCs reutilizáveis ou complexos)
```

> **Respeito a repositórios existentes:** Caso o projeto já possua uma pasta `docs/` com outra estrutura, todos os workflows respeitam automaticamente a organização e o idioma já adotados no repositório.

---

## 🛠️ Como Usar nos Projetos Salesforce

Não é necessário copiar nenhum arquivo de configuração para dentro dos seus repositórios SFDX. A skill e os workflows ficam disponíveis globalmente em qualquer projeto aberto na IDE.

### Modo 1: Pipeline Completo Automatizado (Recomendado)

No chat do Antigravity, dentro de um repositório Salesforce, acione o orquestrador mestre passando o requisito de negócio:

```text
/00-sf-pipeline Preciso validar o limite de crédito da Conta antes de mover uma Oportunidade para Closed Won, bloqueando o fechamento se o valor ultrapassar o saldo disponível.
```

#### O que acontece em cada fase:

1. **`FASE 1 — 01-sf-po`:** Lê a documentação existente em `docs/` e inspeciona `force-app/` antes de analisar o pedido. **Pausa** para fazer perguntas caso existam ambiguidades. Assim que você responde, gera `specs/01-historia.md`.
2. **`FASE 2 — 02-sf-arquiteto`:** Lê `specs/01-historia.md`, `docs/` e `force-app/`, aplica a **Regra 80/20** (priorizando Flows, Dynamic Forms e Custom Metadata antes de Apex/LWC) e gera `specs/02-arquitetura.md` incluindo o plano de impacto em `docs/`. **Pausa** aguardando seu *"Aprovado"*.
3. **`FASE 3 — 03-sf-dev`:** Implementa todos os metadados XML, Flows, classes Apex (`bulkified`, sharing explícito, `WITH USER_MODE`), LWCs e classes de teste (`*Test.cls` com `Assert` e cenários bulk 200+) em `force-app/main/default/`.
4. **`FASE 4 — 04-sf-qa`:** Revisa o `git diff` contra `boas-praticas.md` e `docs/` (para evitar regressões), executa o Salesforce Code Analyzer (`sf code-analyzer run` / `sf scanner run`) no terminal e gera `specs/03-qa-report.md`. Se encontrar falhas críticas, **retorna automaticamente para a Fase 3** para correção (até 2 ciclos).
5. **`FASE 5 — 05-sf-docs`:** Após aprovação do QA, cruza o plano com o código final aprovado (*as-built*) e atualiza os arquivos existentes em `docs/` (ou cria novos seguindo o padrão SFDX), entregando também o checklist de passos manuais de pré/pós-deploy.

### Modo 2: Execução Individual de Workflows

Você pode invocar qualquer etapa isoladamente via comando `/` no chat:

| Comando | Quando usar |
| :--- | :--- |
| `/01-sf-po <requisito>` | Apenas para levantar dúvidas e gerar User Stories + Gherkin. |
| `/02-sf-arquiteto` | Para desenhar a arquitetura técnica a partir de `specs/01-historia.md`. |
| `/03-sf-dev` | Para gerar código e testes a partir de `specs/02-arquitetura.md`. |
| `/04-sf-qa` | Para auditar códigos ou PRs escritos manualmente. |
| `/05-sf-docs` | Para gerar ou atualizar a documentação viva em `docs/` com base no `git diff` atual. |

---

## 🔄 Atualizando as Regras no GitHub

Sempre que evoluir um workflow ou regra em `boas-praticas.md`:

```bash
cd ~/.gemini
git status
git add .
git commit -m "chore: atualiza workflows e diretrizes Salesforce"
git push
```

> **Nota sobre Precedência:** Se um projeto/cliente específico possuir regras próprias definidas em `.agent/rules/` ou em um `GEMINI.md` local na raiz do repositório, os padrões do cliente terão prioridade automática sobre as diretrizes globais.