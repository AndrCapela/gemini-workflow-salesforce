# 🚀 Antigravity Multi-Agent Pipeline — Salesforce Delivery

Configuração global para o **Google Antigravity** que transforma a IDE em uma esteira multiagente especializada em **Salesforce**, garantindo padronização de arquitetura (regra 80/20), nomenclatura, segurança e qualidade de código sem poluir repositórios de outras stacks (Python, Node, etc.).

---

## 📂 Estrutura do Repositório

Este repositório é mapeado diretamente para a pasta global `~/.gemini/` do usuário, utilizando um `.gitignore` em modo *allowlist* para rastrear apenas as regras e workflows (mantendo tokens, sessões e caches locais 100% privados).

```text
~/.gemini/
├── .gitignore                                     # Protege dados sensíveis locais (allowlist)
├── README.md                                      # Esta documentação
└── antigravity/
    ├── skills/
    │   └── salesforce-standards/
    │       ├── SKILL.md                           # Skill ativada apenas em contexto Salesforce
    │       └── boas-praticas.md                   # Guia completo de padrões, nomenclatura e segurança
    └── global_workflows/
        ├── 00-sf-pipeline.md                      # Orquestrador Mestre (executa o fluxo completo)
        ├── 01-sf-po.md                            # Agente 1: PO / Especificador
        ├── 02-sf-arquiteto.md                     # Agente 2: Arquiteto Salesforce (80/20)
        ├── 03-sf-dev.md                           # Agente 3: Desenvolvedor Salesforce
        └── 04-sf-qa.md                            # Agente 4: QA / Code Reviewer
```

---

## ⚙️ Instalação em uma Nova Máquina

Siga os passos abaixo para replicar essa configuração em qualquer computador com o **Google Antigravity** instalado.

### 1. Faça backup da pasta `.gemini` atual (se existir)
Caso você já tenha feito login no Antigravity na máquina nova, a pasta `~/.gemini` já existirá com seus arquivos de sessão. **Não a apague.** Apenas clone este repositório em uma pasta temporária e mova os arquivos rastreados para dentro de `~/.gemini/`:

#### Linux / macOS:
```bash
git clone https://github.com/AndrCapela/antigravity-config.git /tmp/antigravity-config
mkdir -p ~/.gemini
cp -r /tmp/antigravity-config/.git ~/.gemini/
cp -r /tmp/antigravity-config/.gitignore ~/.gemini/
cd ~/.gemini
git checkout -- .
rm -rf /tmp/antigravity-config
```

#### Windows (PowerShell):
```powershell
git clone https://github.com/AndrCapela/antigravity-config.git "$env:TEMP\antigravity-config"
New-Item -ItemType Directory -Force -Path "$HOME\.gemini" | Out-Null
Copy-Item -Recurse -Force "$env:TEMP\antigravity-config\.git" "$HOME\.gemini\"
Copy-Item -Force "$env:TEMP\antigravity-config\.gitignore" "$HOME\.gemini\"
Set-Location "$HOME\.gemini"
git checkout -- .
Remove-Item -Recurse -Force "$env:TEMP\antigravity-config"
```

### 2. Valide o isolamento do Git
Execute `git status` dentro de `~/.gemini/`. O Git deve mostrar a árvore limpa (*working tree clean*), ignorando automaticamente arquivos de autenticação e cache do Antigravity.

---

## 🛠️ Como Usar nos Projetos Salesforce

Não é necessário copiar nenhum arquivo de configuração para dentro dos seus repositórios SFDX. Os workflows e a skill ficam disponíveis globalmente.

*(Opcional)* Adicione `specs/` ao `.gitignore` do seu projeto Salesforce caso não queira commitar os artefatos intermediários de planejamento no repositório do cliente.

### Modo 1: Pipeline Completo Automatizado (Recomendado)

No chat do Antigravity, invoque o orquestrador mestre passando o requisito de negócio:

```text
/00-sf-pipeline Preciso validar o limite de crédito da Conta antes de mover uma Oportunidade para Closed Won, bloqueando o fechamento se o valor ultrapassar o saldo disponível.
```

#### O que acontece em cada etapa:
1. **`[FASE 1/4] Agente PO / Especificador`:** Analisa o pedido e o repositório atual. **Pausa** para fazer perguntas caso existam ambiguidades. Assim que você responde no chat, ele gera `specs/01-historia.md`.
2. **`[FASE 2/4] Agente Arquiteto Salesforce`:** Lê a história, aplica a **Regra 80/20** (priorizando Flows, Dynamic Forms e Custom Metadata antes de Apex/LWC) e gera `specs/02-arquitetura.md`. **Pausa** aguardando seu *"Aprovado"*.
3. **`[FASE 3/4] Agente Desenvolvedor`:** Implementa todos os metadados, classes Apex (`bulkified`, `WITH USER_MODE`), LWCs e classes de teste (`*Test.cls` com `Assert` e cobertura >= 85%) em `force-app/main/default/`.
4. **`[FASE 4/4] Agente QA / Code Reviewer`:** Revisa o código contra o guia `boas-praticas.md`, executa o Salesforce Code Analyzer (`sf scanner run`, se instalado) e gera `specs/03-qa-report.md`. Se encontrar falhas críticas, **aciona o Dev automaticamente** para corrigir antes de encerrar.

---

### Modo 2: Execução Individual por Papel

Você também pode chamar qualquer agente isoladamente a qualquer momento:

| Comando | Quando usar |
| :--- | :--- |
| `/01-sf-po <descrição>` | Apenas para refinar um requisito e gerar User Stories + Gherkin. |
| `/02-sf-arquiteto` | Para desenhar a solução técnica a partir de uma história já existente. |
| `/03-sf-dev` | Para gerar código e testes a partir de uma arquitetura já definida. |
| `/04-sf-qa` | Para auditar e revisar códigos ou PRs que você mesmo escreveu manualmente. |

---

## 🔄 Atualizando as Regras no GitHub

Sempre que você evoluir um prompt de workflow ou adicionar uma nova regra em `boas-praticas.md`:

```bash
cd ~/.gemini
git status
git add .
git commit -m "chore: atualiza diretrizes de desenvolvimento Salesforce"
git push
```

> **Nota sobre Precedência:** Se um projeto/cliente específico possuir regras próprias definidas em `.agent/rules/` na raiz do repositório, o Antigravity dará prioridade automática às regras do cliente sobre as diretrizes globais.