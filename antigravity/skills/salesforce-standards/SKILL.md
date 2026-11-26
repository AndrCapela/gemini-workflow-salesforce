---
name: salesforce-standards
description: Padrões de arquitetura, desenvolvimento e governança Salesforce. Ativar ao trabalhar em projetos SFDX, Apex, LWC, Flows ou metadados Salesforce.
---

# Padrões Salesforce (Arquitetura e Código)

1. **Regra 80/20 (Declarativo Primeiro):** Priorize sempre recursos padrão e declarativos (Flows, Validation Rules, Formula Fields, objetos padrão) antes de propor Apex ou LWC. Justifique tecnicamente qualquer uso de código customizado.
2. **Governor Limits & Bulkification:** Todo código Apex (especialmente Triggers e métodos chamados por Flow) deve ser 100% bulkified. Nunca execute SOQL ou DML dentro de loops.
3. **Segurança:** Use `with sharing` por padrão nas classes Apex. Aplique `WITH USER_MODE` em queries SOQL ou `Security.stripInaccessible()` antes de DML.
4. **Arquitetura de Triggers:** Uma única Trigger por objeto, delegando lógica para classes Handler (Trigger Handler Pattern).
5. **Testes:** Classes `@isTest` devem ter no mínimo 85% de cobertura, testar cenários positivos, negativos e em massa (200+ registros), usando `Assert` (não `System.assert`) e `TestDataFactory` sem `SeeAllData=true`.