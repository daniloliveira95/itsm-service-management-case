# KPI — Contador Operacional por JQL

## Objetivo

Documentar o uso de um gadget de dashboard que contabiliza chamados a partir de filtros JQL. Cada contador apresenta a quantidade de itens que atendem à consulta configurada e permite acessar a relação de chamados correspondente ao selecionar o número.

> **Nome funcional nesta documentação:** Contador Operacional. O nome exato do gadget pode variar conforme a implementação ou aplicativo instalado no Jira.

## Como funciona

1. Cada indicador é associado a uma consulta JQL.
2. O gadget executa o filtro e apresenta a quantidade de chamados encontrados.
3. Ao clicar no número, o usuário acessa os chamados que compõem aquele resultado.
4. A consulta pode ser ajustada para representar diferentes situações operacionais.

Esse comportamento combina monitoramento quantitativo com acesso direto aos registros que justificam o indicador.

## Exemplo — Urgente vs. Padrão

<img width="698" height="110" alt="image" src="https://github.com/user-attachments/assets/5d8f0795-a6ea-4299-b452-01ba3fbb1897" />


Os números abaixo são ilustrativos e não representam dados reais.

| Contador | Resultado ilustrativo |
|---|---:|
| Chamados urgentes | 77 |
| Chamados padrão | 80 |

### Chamados urgentes

**Propósito:** contar os chamados classificados como urgentes, criados nos últimos 90 dias.

```jql
project = XXX
AND priority = Urgente
AND created >= -90d
```

### Chamados padrão

**Propósito:** contar os chamados classificados como padrão, criados nos últimos 90 dias.

```jql
project = XXX
AND priority = Padrão
AND created >= -90d
```

> Substitua `XXX` pelo identificador do projeto. Os valores `Urgente` e `Padrão` devem corresponder exatamente aos nomes das prioridades cadastradas no ambiente.

## Aplicações alternativas

O mesmo recurso pode ser reutilizado para monitorar diferentes condições do atendimento.

### 1. Backlog — chamados não resolvidos

**Propósito:** acompanhar o volume total de chamados ainda não resolvidos.

```jql
project = XXX
AND resolution = Unresolved
```

### 2. Violações de SLA

**Propósito:** contar chamados com pelo menos um SLA de atendimento ou retorno estourado.

```jql
project = XXX
AND (
    "SLA Primeiro Atendimento" = breached()
    OR "SLA 1º Retorno" = breached()
    OR "SLA 2º Retorno" = breached()
    OR "SLA 3º Retorno" = breached()
    OR "SLA 4º Retorno" = breached()
)
AND resolution = Unresolved
```

### 3. Aguardando o demandante

**Propósito:** acompanhar chamados aguardando uma ação ou resposta do solicitante.

```jql
project = XXX
AND status = "AGUARDANDO DEMANDANTE"
AND resolution = Unresolved
```

### 4. Chamados sem responsável

**Propósito:** identificar chamados ainda não atribuídos a um atendente.

```jql
project = XXX
AND assignee is EMPTY
AND resolution = Unresolved
```

### 5. Pendentes de aprovação

**Propósito:** acompanhar chamados que aguardam aprovação.

```jql
project = XXX
AND status = "PENDENTE APROVAÇÃO"
AND resolution = Unresolved
```

> O status deve corresponder ao nome efetivamente utilizado no workflow. Caso o ambiente utilize outro nome para a etapa de aprovação, ajuste a consulta.

## Aplicação no dashboard

| Elemento | Configuração |
|---|---|
| Componente | Gadget de contagem baseado em filtros |
| Fonte | Consultas JQL salvas no Jira |
| Exibição | Quantidade de chamados por condição |
| Interação | Clique no contador para abrir os chamados correspondentes |
| Reutilização | Um contador para cada filtro ou condição monitorada |

## Valor gerencial

- **Monitoramento:** acompanhamento rápido do volume por prioridade, situação ou condição de SLA.
- **Investigação:** acesso aos chamados que formam cada número, facilitando a análise da causa.
- **Gestão operacional:** identificação de backlog, dependências, ausência de responsável e gargalos de aprovação.
- **Rastreabilidade:** ligação direta entre o indicador exibido e os registros operacionais que o compõem.

## Cuidados de interpretação

- A contagem representa volume de chamados que satisfazem a JQL; não representa, isoladamente, esforço, produtividade ou qualidade do atendimento.
- Para comparar períodos, mantenha critérios equivalentes de projeto, data, status e resolução.
- Evite somar contadores cujas consultas possam incluir os mesmos chamados, pois as categorias podem se sobrepor.
- Revise os nomes de projetos, prioridades, status e campos de SLA antes de reutilizar as consultas em outro ambiente.
