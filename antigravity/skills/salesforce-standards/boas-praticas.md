# Melhores Práticas de Desenvolvimento Salesforce

> **Regra de Precedência:** Caso o cliente ou projeto atual possua padrões próprios de implementação ou diretrizes específicas (ex: em `.agent/rules/`), **os padrões do cliente têm prioridade máxima** sobre este documento.

---

## 1. Regras Principais (Todos os Metadados)

- **SEMPRE** preencha o campo `Descrição` em objetos, campos, flows, validation rules, custom labels, custom metadata e demais metadados.
- **SEMPRE** trate erros retornando mensagens claras e significativas.
- **SEMPRE** utilize a versão mais recente da API suportada pelo projeto (`sfdx-project.json`).
- **SEMPRE** utilize nomes significativos para classes, variáveis, campos, métodos e objetos (exceção: iteradores de loop como `i`, `j`, `k`).
- **NUNCA** faça hardcode de IDs no código ou em metadados.
- **NUNCA** execute SOQL ou DML dentro de loops `for`.
- **EVITE** acrônimos e abreviações sempre que possível.
- **EVITE** palavras vazias em nomes (ex: prefira `accounts` em vez de `accountList`, pois o tipo da coleção é irrelevante ou pode mudar).
- Consulte e respeite o *Salesforce Pattern & Anti-Pattern Explorer*.

---

## 2. Nomenclatura Geral (Apex e LWC)

- **Métodos:** devem sempre começar com um verbo.
  - Errado: `id()` | Correto: `getId()`
- **Classes:** não podem ser nomeadas como ações.
  - Errado: `ValidateAccount` | Correto: `AccountValidator`
- **Mapas:** siga o padrão `{chave}To{Valor}`.
  - Ex: `idToWrapper`, `accountIdToAccount`
- **Booleanos:** inicie com `is`, `has` ou `can`.
  - Ex: `isFieldShown`, `hasException`, `canAccessScreen`

---

## 3. Apex

### Diretrizes de Código
- Mantenha formatação consistente (padrão Prettier). Evite escrever código via Developer Console.
- Utilize **SOQL for loops** sempre que possível para economizar memória (heap).
- Agrupe instruções **DML no final** da execução lógica.
- **Triggers** não devem conter lógica de negócio (delegue para classes Handler):
  - `before save`: atualize apenas o próprio registro que acionou a trigger.
  - `after save`: utilize quando precisar fazer DML em outros objetos.

### Segurança, Compartilhamento (Sharing) e CRUD/FLS
- **SEMPRE** declare explicitamente o modo de compartilhamento (`with sharing`, `without sharing` ou `inherited sharing`) no cabeçalho de toda classe Apex.
- **EVITE** omitir a declaração de compartilhamento (*Omission Bias*).
- **SEMPRE** prefira `inherited sharing` para classes utilitárias, seletores (DAO) e componentes reutilizáveis.
- **NUNCA** use `without sharing` de forma indiscriminada; restrinja apenas a blocos específicos que precisem deliberadamente ignorar regras de compartilhamento (ex: integrações de sistema, rotinas de background), isolando essa lógica em classes dedicadas.
- **CRUD / FLS (Segurança de Objeto e Campo):** **SEMPRE** valide permissões de leitura e escrita em queries e DMLs utilizando **`WITH USER_MODE`** no SOQL, **`as user`** em operações DML (ex: `insert as user records;`) ou **`Security.stripInaccessible()`**, exceto em processos estritamente de sistema devidamente justificados.

### Apex Assíncrono e Integrações
- **Prefira `Queueable` em vez de `@future`:** `Queueable` suporta tipos não primitivos, encadeamento (*chaining*), `Finalizers` e monitoramento via `AsyncApexJob`.
- **Grandes volumes:** utilize `Batch Apex` para processar grandes massas de dados (acima de 50.000 registros).
- **Integrações HTTP (Callouts):** utilize **Named Credentials** em conjunto com **External Credentials** para gerenciar endpoints e autenticação; nunca faça hardcode de URLs ou tokens.
- **Separação DML + Callout:** nunca execute operações DML antes de um callout HTTP na mesma transação (evita `CalloutException: You have uncommitted work pending`). Isole o callout em execução assíncrona (`Queueable` com `Database.AllowsCallouts`) ou utilize Platform Events.

### Testes Unitários
- **SEMPRE** inclua asserts relevantes que validem comportamento real.
- **SEMPRE** cubra cenários de volumetria (*bulkification* / processamento em lote).
- **EVITE** `System.assert` e `System.assertEquals`; utilize a classe nativa moderna **`Assert`** (ex: `Assert.areEqual`, `Assert.isTrue`).
- **Classes Factory:**
  - Use o sufixo `Factory` (ex: `AccountTestDataFactory`, `OpportunityFactory`).
  - **SEMPRE** anote com `@isTest` no topo da classe para não consumir limite de armazenamento de código da Org.
  - **NUNCA** coloque instruções de `Assert` dentro de classes Factory.

### Convenção de Nomenclatura (Apex)
| Elemento | Padrão | Observação |
| :--- | :--- | :--- |
| Classes | `PascalCase` | Sem acrônimos, sublinhados, hífens ou palavras vazias (`Classe`, `Valor`, `Componente`) |
| Métodos | `camelCase` | Iniciar com verbo |
| Variáveis | `camelCase` | Sem palavras vazias |
| Constantes | `SCREAMING_SNAKE_CASE` | — |
| Test Data Factories | `PascalCase` + `Factory` | Ex: `AccountTestDataFactory` |

---

## 4. Custom Labels (Rótulos Personalizados)

- **NUNCA** armazene credenciais, tokens ou segredos (para integrações HTTP, utilize **Named Credentials**).
- **EVITE** usar para lógica ou configuração (não são auditáveis e não possuem relacionamentos).
- Utilize para textos traduzíveis no front-end e crie as traduções correspondentes sempre que possível.
- Preencha **Categorias** indicando qual aplicativo, processo ou componente utiliza o rótulo (ex: `LWC`, `Apex`, `HTTP`, `URL`, `NomeDaClasse`).
- Verifique existência de tradução via classe `Label` quando aplicável.
- **Nomenclatura:** `PascalCase` + descrição concisa da finalidade.
- **Sintaxe de uso:**
  - Apex: `System.Label.NomeDoRotulo`
  - LWC: `import labelName from '@salesforce/label/c.NomeDoRotulo';`
  - Flow / Visualforce: `$Label`
  - Aura: `$Label.c.NomeDoRotulo`
  - Lightning App Builder: `{!$Label.NomeDoRotulo}`

---

## 5. Custom Metadata Types (Tipos de Metadados Personalizados)

- Utilize para configurações que precisam ser **deployadas entre Orgs** (mapeamentos de regras, matrizes, tabelas de referência, *feature flags*).
- Permitem relacionamentos para configuração.
- **Nomenclatura:** `PascalCase` + descrição clara e concisa.

---

## 6. Custom Settings (Configurações Personalizadas)

- Utilize apenas quando precisar de configurações específicas de Org ou usuário editáveis **sem deploy**.
- **Hierarchy:** varia por usuário ou perfil (uso válido).
- **List:** **não recomendado** pela Salesforce — prefira **Custom Metadata Types**.
- **Nomenclatura:** `PascalCase` + descrição clara e concisa.

---

## 7. Custom Objects (Objetos Personalizados)

- **SEMPRE** verifique se existe um **Objeto Padrão** que atenda ao requisito antes de criar um objeto personalizado.
- **SEMPRE** forneça descrição clara indicando finalidade e contexto de negócio.
- **Record Types:** crie Record Types quando houver previsão de múltiplos processos de negócio, ciclos de vida ou layouts distintos (evite criar sem necessidade real para não gerar manutenção extra em permissões e queries).
- **Nomenclatura:** `PascalCase`, nomes significativos, sem acrônimos, abreviações ou palavras vazias.

---

## 8. Flows (Fluxos)

### Diretrizes
- **Before Save:** use para atualizar o próprio registro acionador utilizando elementos de **Atribuição** (sem DML).
- **After Save:** use quando precisar consultar/atualizar outros sObjects ou enviar alertas por e-mail.
- **Bypass de Automação:** em Record-Triggered Flows, considere verificar uma **Custom Permission** de bypass (ex: `!$Permission.BypassFlows`) nos critérios de entrada para permitir cargas de dados e integrações controladas.
- **Histórico na Descrição:** ao editar um fluxo, registre a modificação na descrição (ex: *"Descrição original. Edição: alterado critério de filtro de conta."*).
- **Fault Paths (Caminhos com Falha):** adicione obrigatoriamente em **TODOS** os elementos DML/Get. Apresente tela de erro em Screen Flows e mensagens de erro nos demais.
- **DML no final:** acumule alterações em variáveis/coleções de registro e execute DML apenas ao final do fluxo.
- **Mensagens de erro consolidadas:** reúna todas as validações/erros e apresente uma única vez ao usuário.
- **Screen Flows:** não faça Get Records do registro acionador; crie uma variável do tipo Registro chamada `recordId` para receber seus valores diretamente.
- **Testes:** crie **Flow Tests** sempre que possível e execute-os após cada deploy.

### Convenção de Nomenclatura de Flows
| Tipo de Fluxo | Rótulo (Label) | API Name | Exemplo |
| :--- | :--- | :--- | :--- |
| Record-Triggered (Before Save) | `{Objeto}: Before Save` | `{Objeto}_Before_Save` | `Account: Before Save` |
| Record-Triggered (After Save) | `{Objeto}: After Save` | `{Objeto}_After_Save` | `Account: After Save` |
| Screen Flow | `{Objeto}: Descrição Concisa` | `{Objeto}_DescricaoConcisa` | `Account: Determinar Recursos de País` |

### Convenção de Elementos e Recursos do Flow
| Elemento / Recurso | Tipo de Dados | Padrão API Name | Exemplo |
| :--- | :--- | :--- | :--- |
| Decisão (Rótulo) | — | Uma pergunta? | `País da Conta é Válido?` |
| Decisão (Resultado) | — | Uma resposta | `Sim` / `Não` |
| Tela (Screen) | — | `scr_nome` | `scr_introducao` |
| Fórmula | Booleano / Moeda / Data / Texto / Hora / Número | `frmB_`, `frmC_`, `frmD_`, `frmTx_`, `frmTm_`, `frmN_` | `frmB_usuarioTemAcesso`, `frmTx_erroDinamico` |
| Variável | Booleano / Moeda / Data / Data-Hora / Texto / Hora / Número | `varB_`, `varC_`, `varD_`, `varDt_`, `varTx_`, `varTm_`, `varN_` | `varB_temAcesso`, `varDt_inicioEvento` |
| Variável | Registro / Picklist / Multi-Picklist / Apex-Defined | `varR_`, `varPk_`, `varMPk_`, `varApx_` | `varR_contasBrasileiras`, `varApx_contasCalculadas` |
| Constante | Booleano / Moeda / Data / Texto / Hora / Número | `conB_`, `conC_`, `conD_`, `conTx_`, `conTm_`, `conN_` | `conTx_erroCampo`, `conTm_inicioEvento` |
| Escolha (Choice) | — | `chc_nome` | `chc_tiposConta` |
| Coleção (Collection) | — | `col_nome` | `col_contas` |
| Collection Choice Set | — | `recCh_nome` / `pklCh_nome` | `recCh_contasFiltradas` |
| Picklist Choice Set | — | `pklV_nome` | `pklV_nomeParaAPI` |
| Estágio (Stage) | — | `stg_nome` | `stg_inicio` |
| Text Template | — | `txtT_nome` | `txtT_erroEmail` |

---

## 9. Lightning Web Components (LWC)

### Diretrizes
- **SEMPRE** utilize a biblioteca base de componentes Salesforce e o **SLDS** (Salesforce Lightning Design System).
- **SEMPRE** verifique se é possível usar **Lightning Data Service (LDS)** antes de chamar Apex.
- **EVITE** deixar `console.log()` ativos no código (risco de vazamento de dados).
- Minimize chamadas ao servidor: prefira que o componente pai busque os dados e passe para os filhos via propriedades.
- Desenvolva componentes **modulares, reutilizáveis e polimórficos** (ex: datatable genérica que aceita qualquer sObject e lista de colunas).
- Exponha propriedades configuráveis (`@api` / `targetConfigs`) para facilitar o uso por administradores.
- Comente apenas lógicas complexas; nomes de variáveis e métodos devem ser autoexplicativos.
- Aplique princípios **SOLID** e atributos de **acessibilidade (a11y)**.
- Crie testes unitários com **Jest**.
- Para chamadas assíncronas de longa duração, utilize a classe **Continuation**.

### Convenção de Nomenclatura (LWC)
- Utilize `camelCase` para nomes de componentes.
- Não use caracteres vazios (`_`, `-`) nem palavras vazias (`Component`, `Cmp`).
- Evite sufixos `Pai`/`Filho`; use termos semânticos do framework/UI (ex: `accountPanel`, `accountPanelItem`, `accountHeader`).

---

## 10. Static Resources (Recursos Estáticos)

- Utilize para imagens, stylesheets e scripts JS referenciados em LWC, Apex, Visualforce ou Aura.
- **Limites:** máximo de **5 MB** por arquivo e **250 MB** por Org (consome cota de armazenamento de dados).
- Utilize versionamento para facilitar rollback.
- **Nomenclatura:** `camelCase`.

---

## 11. Validation Rules (Regras de Validação)

### Diretrizes
- Mantenha regras **modulares e específicas**; se complexa, documente com comentários.
- **Bypass de Validação:** **SEMPRE** crie exceções para usuários de integração e administradores de sistema utilizando **Custom Permissions** (ex: `NOT($Permission.BypassValidationRules)`). **NUNCA** faça hardcode de `$Profile.Name` (quebra em Orgs com idiomas diferentes).
- Use como **último recurso**: prefira Screen Flows, Dynamic Forms ou configurações de Page Layout (regras de validação disparam por campo e pioram a UX).
- Para validações complexas, prefira **Record-Triggered Flow (Custom Error)** ou **Trigger Apex (`addError`)**.

### Convenção de Nomenclatura e Mensagens
- **Nomenclatura:** `PascalCase`, sem palavras vazias (`Verificar`, `Validar`). Ex: `AccountCountryMandatory`.
- **Descrição:** obrigatória e detalhada — responda *o quê*, *para quem* e *por quê*.
- **Mensagem de Erro:** informe claramente **qual campo** acionou e **qual critério** foi violado.