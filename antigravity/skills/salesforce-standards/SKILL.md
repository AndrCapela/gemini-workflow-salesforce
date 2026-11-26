---
name: salesforce-standards
description: Padrões de arquitetura, desenvolvimento, nomenclatura e governança Salesforce. Ativar ao trabalhar em projetos SFDX, Apex, LWC, Flows, Validation Rules ou metadados Salesforce.
---

# Padrões Salesforce (Arquitetura, Desenvolvimento e Governança)

## Precedência de Regras
1. **Padrões do Cliente / Repositório Local** (ex: `.agent/rules/` ou documentação no repo) têm prioridade máxima.
2. **Diretrizes Globais abaixo e o guia complementar `boas-praticas.md`** aplicam-se sempre que não houver regra conflitante do cliente.

## Referência Obrigatória
Antes de projetar arquiteturas, gerar código (Apex, LWC, Flows, Metadados) ou executar Code Review/QA, **leia obrigatoriamente** o arquivo complementar:
- `~/.gemini/antigravity/skills/salesforce-standards/boas-praticas.md`

Siga integralmente as convenções de nomenclatura, tabelas de prefixos de Flow, diretrizes de segurança, testes e checklists definidos nele.

## Pilares Essenciais (Resumo Rápido)
1. **Regra 80/20 (Declarativo Primeiro):** Priorize sempre objetos padrão, Dynamic Forms, Flows e Custom Metadata Types antes de propor Apex ou LWC. Justifique tecnicamente qualquer customização em código.
2. **Governor Limits & Bulkification:** Zero SOQL/DML em loops. Use SOQL for loops, DML consolidado no final e testes em massa (200+ registros).
3. **Segurança Explícita:** Toda classe Apex deve declarar explicitamente `with sharing`, `inherited sharing` (padrão para utilitárias/DAO) ou `without sharing` (somente isolado em classes dedicadas).
4. **Qualidade de Testes:** Mínimo de 85% de cobertura, uso obrigatório da classe `Assert` (nunca `System.assert`), `TestDataFactory` anotada com `@isTest` e sem asserts internos, e testes Jest para LWC.