# Banco de Queries JQL

Coleção das consultas JQL documentadas no material de implantação do Jira Service Management.

> **Observação:** os valores `XXX` e `-XXd` são parâmetros ilustrativos presentes no documento original e devem ser substituídos pelos valores do ambiente.

## 1. Chamados estourados

**Propósito:** localizar chamados do projeto informado, criados dentro do período definido, que tenham pelo menos um dos SLAs de atendimento ou retorno estourado.

```jql
project = XXX
AND created >= -XXd
AND (
    "SLA 1º Retorno" = breached()
    OR "SLA 2º Retorno" = breached()
    OR "SLA 3º Retorno" = breached()
    OR "SLA 4º Retorno" = breached()
    OR "SLA Primeiro Atendimento" = breached()
)
ORDER BY created DESC
```

## 2. Placar de relatórios

### 2.1 Primeiro atendimento — dentro do prazo

**Propósito:** retornar chamados cujo SLA de Primeiro Atendimento foi concluído dentro do prazo.

```jql
"SLA Primeiro Atendimento" = completed()
```

### 2.2 Primeiro atendimento — fora do prazo

**Propósito:** retornar chamados cujo SLA de Primeiro Atendimento foi estourado.

```jql
"SLA Primeiro Atendimento" = breached()
```

### 2.3 1º retorno atrasado

**Propósito:** localizar chamados com o SLA do primeiro retorno estourado e criados nos últimos 30 dias.

```jql
"SLA 1º Retorno" = breached()
AND created >= -30d
```

### 2.4 2º retorno atrasado

**Propósito:** localizar chamados com o SLA do segundo retorno estourado e criados nos últimos 30 dias.

```jql
"SLA 2º Retorno" = breached()
AND created >= -30d
```

### 2.5 3º retorno atrasado

**Propósito:** localizar chamados com o SLA do terceiro retorno estourado e criados nos últimos 30 dias.

```jql
"SLA 3º Retorno" = breached()
AND created >= -30d
```

### 2.6 4º retorno atrasado

**Propósito:** localizar chamados com o SLA do quarto retorno estourado e criados nos últimos 30 dias.

```jql
"SLA 4º Retorno" = breached()
AND created >= -30d
```

### 2.7 Retornos dentro do prazo

**Propósito:** retornar chamados em que pelo menos um dos quatro SLAs de retorno foi concluído dentro do prazo.

```jql
(
    "SLA 1º Retorno" = completed()
    OR "SLA 2º Retorno" = completed()
    OR "SLA 3º Retorno" = completed()
    OR "SLA 4º Retorno" = completed()
)
```

## 3. Filas de chamados

### 3.1 Meus chamados

**Propósito:** exibir chamados não resolvidos atribuídos ao usuário atual.

```jql
assignee in (currentUser())
AND resolution = Unresolved
```

### 3.2 Todos os chamados

**Propósito:** exibir todos os chamados ainda não resolvidos.

```jql
resolution = Unresolved
```

### 3.3 Chamados criados

**Propósito:** exibir chamados de tipos padrão que estejam no status `CRIADO` e ainda não tenham sido resolvidos.

```jql
issuetype in standardIssueTypes()
AND status = CRIADO
AND resolution = Unresolved
```

### 3.4 Primeiro atendimento

**Propósito:** exibir chamados cujo SLA de Primeiro Atendimento esteja em execução ou pausado.

```jql
"SLA Primeiro Atendimento" = running()
OR "SLA Primeiro Atendimento" = paused()
```

### 3.5 Retorno 1

**Propósito:** exibir chamados cujo SLA do primeiro retorno esteja em execução ou pausado.

```jql
"SLA 1º Retorno" = running()
OR "SLA 1º Retorno" = paused()
```

### 3.6 Retorno 2

**Propósito:** exibir chamados cujo SLA do segundo retorno esteja em execução ou pausado.

```jql
"SLA 2º Retorno" = running()
OR "SLA 2º Retorno" = paused()
```

### 3.7 Retorno 3

**Propósito:** exibir chamados cujo SLA do terceiro retorno esteja em execução ou pausado.

```jql
"SLA 3º Retorno" = running()
OR "SLA 3º Retorno" = paused()
```

### 3.8 Retorno 4

**Propósito:** exibir chamados cujo SLA do quarto retorno esteja em execução ou pausado.

```jql
"SLA 4º Retorno" = running()
OR "SLA 4º Retorno" = paused()
```

### 3.9 Aguardando demandante

**Propósito:** exibir chamados não resolvidos que estejam no status `AGUARDANDO DEMANDANTE`.

```jql
status = "AGUARDANDO DEMANDANTE"
AND resolution = Unresolved
```

### 3.10 Pronto para concluir

**Propósito:** localizar chamados não resolvidos que estejam em um dos status de encerramento operacional ou cujo SLA de Retorno do Demandante tenha sido estourado.

```jql
(
    status in (Concluído, Cancelado, "SEM RESPOSTA", "NÃO APROVADO")
    AND resolution = Unresolved
)
OR
(
    "Retorno do Demandante" = breached()
    AND resolution = Unresolved
)
```
