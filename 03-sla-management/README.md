# Gestão de SLAs — Jira Service Management

## 1. Visão geral

Este case apresenta a implementação de gerenciamento de Acordos de Nível de Serviço (SLAs) em uma plataforma Jira Service Management.

A solução foi estruturada para estabelecer prazos de atendimento, organizar as demandas da equipe, acompanhar os tempos de resposta e oferecer maior visibilidade sobre o cumprimento dos compromissos operacionais.

A implementação integra configurações nativas de SLA, fluxos de trabalho, automações e consultas JQL para construir um modelo de acompanhamento dos chamados durante seu ciclo de vida.

### Objetivos da implementação

- Estabelecer prazos de atendimento alinhados às necessidades do cliente.
- Padronizar a classificação de prioridade dos chamados.
- Monitorar o cumprimento dos prazos de atendimento.
- Organizar a fila de trabalho conforme a situação dos SLAs.
- Identificar chamados com prazos vencidos.
- Automatizar etapas específicas do processo.
- Disponibilizar informações para acompanhamento e análise operacional.

---

## 2. Arquitetura dos SLAs

A estrutura foi organizada em três categorias principais:

| Categoria | Finalidade |
|---|---|
| Primeiro Atendimento | Acompanhar o prazo para a primeira atuação da equipe de suporte. |
| Retorno do Demandante | Monitorar o período em que o chamado permanece aguardando uma resposta do solicitante. |
| Retornos da Equipe | Acompanhar os prazos das interações posteriores realizadas pela equipe de atendimento. |

### Componentes utilizados

A solução combina diferentes recursos do Jira Service Management:

- **SLAs:** definição de metas, condições de início, pausa e conclusão da contagem.
- **Workflows:** controle dos estados e das transições dos chamados.
- **Automações:** execução automática de transições em situações específicas.
- **JQL:** criação de filtros para identificação e monitoramento dos chamados.
- **Filas:** organização das demandas conforme critérios operacionais e estados dos SLAs.

Esses componentes trabalham em conjunto para conectar as regras de negócio à operação diária da equipe.

---

## 3. SLA de Primeiro Atendimento

### 3.1 Objetivo

O SLA de Primeiro Atendimento estabelece o prazo para a primeira atuação da equipe de suporte após a ativação do processo de atendimento.

Seu objetivo é oferecer uma referência mensurável para a priorização dos chamados e permitir o acompanhamento dos compromissos de resposta acordados com o cliente.

### 3.2 Regras de negócio

O modelo foi definido com duas categorias de prioridade:

| Prioridade | Prazo acordado |
|---|---:|
| Urgente | 48 horas |
| Padrão | 72 horas |

A utilização de apenas duas categorias busca simplificar a classificação dos chamados e estimular uma avaliação mais criteriosa sobre a urgência de cada demanda.

### 3.3 Lógica de início da contagem

A contagem do SLA não é iniciada necessariamente no momento da criação do chamado.

Foi estabelecido um status intermediário, utilizado antes da ativação do processo de primeiro atendimento.

O fluxo operacional funciona da seguinte maneira:

1. O demandante registra um chamado na plataforma.
2. O chamado permanece inicialmente em um status intermediário.
3. Uma automação agendada verifica os chamados que aguardam o início do atendimento.
4. A automação realiza a transição para o status `PRIMEIRO ATENDIMENTO`.
5. A entrada nesse status inicia a contagem do SLA de Primeiro Atendimento.

A automação foi definida para execução às 9h dos dias úteis.

Esse mecanismo estabelece um ponto padronizado para ativação da contagem do SLA, conforme a regra operacional acordada.

### 3.4 Priorização operacional

A equipe utiliza os prazos dos SLAs como referência para organizar o atendimento das demandas padrão.

A priorização considera a proximidade do vencimento do SLA, permitindo que chamados com menor tempo restante recebam atenção antes de outros com maior prazo disponível.

Entretanto, a prioridade do SLA não substitui a avaliação da criticidade operacional.

Incidentes críticos, como indisponibilidade de internet ou falhas em bancos de dados, podem exigir intervenção imediata, independentemente da ordem normal da fila.

É importante distinguir:

- **Prioridade do SLA:** classificação do chamado conforme o prazo de resposta acordado.
- **Criticidade operacional:** avaliação do impacto de um incidente sobre o negócio e da necessidade de intervenção imediata.

---

## 4. SLA de Retorno do Demandante

### 4.1 Objetivo

O SLA de Retorno do Demandante acompanha o período em que um chamado permanece aguardando uma resposta do solicitante.

Seu propósito é permitir o monitoramento dessas pendências e oferecer um critério para identificar chamados que permanecem sem movimentação por períodos prolongados.

### 4.2 Configuração registrada

A documentação técnica da implementação registra a seguinte configuração:

| Prioridade | Prazo configurado |
|---|---:|
| Urgente | 15 dias |
| Padrão | 15 dias |

Esse SLA é utilizado como referência para acompanhar os chamados que dependem de retorno do demandante.

### 4.3 Automação de ausência de resposta

Foi criada uma automação específica para tratar chamados sem resposta do demandante.

A regra movimenta automaticamente os chamados que permanecem 30 dias sem retorno para o status `SEM RESPOSTA`.

Esse status permite que o atendente prossiga com o encerramento do chamado.

A automação contribui para padronizar o tratamento das solicitações inativas e evitar que permaneçam indefinidamente na fila de pendências.

---

## 5. SLAs de Retorno da Equipe

### 5.1 Objetivo

Além do primeiro atendimento, foram configurados quatro SLAs destinados ao acompanhamento dos retornos realizados pela equipe de suporte.

Essa estrutura permite monitorar diferentes etapas de interação durante o ciclo de vida de um chamado.

Cada etapa possui seu próprio SLA, possibilitando a identificação individual de prazos em andamento, pausados, concluídos ou vencidos.

### 5.2 Estrutura dos SLAs

| SLA | Prazo configurado |
|---|---:|
| 1º Retorno | 24 horas |
| 2º Retorno | 24 horas |
| 3º Retorno | 24 horas |
| 4º Retorno | 24 horas |

### 5.3 Monitoramento individual

Cada SLA de retorno possui condições próprias de funcionamento e um filtro específico para acompanhamento.

Os principais estados considerados são:

- `running()`: indica que a contagem do SLA está em andamento.
- `paused()`: indica que a contagem está pausada.
- `completed()`: identifica um ciclo de SLA concluído.
- `breached()`: identifica um SLA cujo prazo foi ultrapassado.

A separação dos quatro retornos permite identificar em qual etapa do atendimento existe uma pendência ou um descumprimento de prazo.

---

## 6. Filas de Atendimento e Monitoramento

### 6.1 Objetivo

Foram configuradas filas operacionais para organizar os chamados conforme sua situação e os critérios de acompanhamento definidos para a equipe.

As filas utilizam filtros JQL para apresentar automaticamente os chamados que atendem às condições estabelecidas.

### 6.2 Filas implementadas

| Fila | Finalidade |
|---|---|
| Meus Chamados | Exibe os chamados não resolvidos atribuídos ao usuário atual. |
| Todos os Chamados | Exibe todos os chamados não resolvidos. |
| Chamados Criados | Exibe chamados não resolvidos que permanecem no status intermediário `CRIADO`. |
| Primeiro Atendimento | Identifica chamados com SLA de primeiro atendimento em andamento ou pausado. |
| Retorno 1 | Identifica chamados com SLA do primeiro retorno em andamento ou pausado. |
| Retorno 2 | Identifica chamados com SLA do segundo retorno em andamento ou pausado. |
| Retorno 3 | Identifica chamados com SLA do terceiro retorno em andamento ou pausado. |
| Retorno 4 | Identifica chamados com SLA do quarto retorno em andamento ou pausado. |
| Aguardando Demandante | Exibe chamados não resolvidos que aguardam resposta do solicitante. |
| Pronto para Concluir | Identifica chamados que atendem às condições definidas para conclusão. |

### 6.3 Monitoramento de chamados com SLA vencido

Foi desenvolvida uma consulta consolidada para identificar chamados que apresentam algum dos SLAs de atendimento ou retorno vencidos.

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

Os identificadores do projeto e o período de consulta foram anonimizados.

A consulta permite centralizar a identificação de chamados com SLAs vencidos, facilitando o acompanhamento das pendências pela equipe.

### 6.4 Filtros das filas baseadas em SLA

As filas de acompanhamento utilizam os estados running() e paused() para identificar os chamados associados a cada etapa.

Primeiro Atendimento
```jql
"SLA Primeiro Atendimento" = running()
OR "SLA Primeiro Atendimento" = paused()
```
1º Retorno
```jql
"SLA 1º Retorno" = running()
OR "SLA 1º Retorno" = paused()
```
2º Retorno
```jql
"SLA 2º Retorno" = running()
OR "SLA 2º Retorno" = paused()
```
3º Retorno
```jql
"SLA 3º Retorno" = running()
OR "SLA 3º Retorno" = paused()
```
4º Retorno
```jql
"SLA 4º Retorno" = running()
OR "SLA 4º Retorno" = paused()
```
Esses filtros permitem acompanhar separadamente cada etapa do processo de atendimento.

### 7. Consultas JQL e Indicadores

Além das filas operacionais, foram criadas consultas JQL para apoiar o acompanhamento do cumprimento dos SLAs.

Essas consultas permitem identificar chamados concluídos dentro do prazo e chamados que ultrapassaram suas metas.

### 7.1 Primeiro Atendimento

Dentro do prazo
```jql
"SLA Primeiro Atendimento" = completed()
```
Fora do prazo
```jql
"SLA Primeiro Atendimento" = breached()
```
### 7.2 Retornos atrasados

Foram criados filtros individuais para cada etapa de retorno.

1º Retorno atrasado
```jql
"SLA 1º Retorno" = breached()
AND created >= -30d
```
2º Retorno atrasado
```jql
"SLA 2º Retorno" = breached()
AND created >= -30d
```
3º Retorno atrasado
```jql
"SLA 3º Retorno" = breached()
AND created >= -30d
```
4º Retorno atrasado
```jql
"SLA 4º Retorno" = breached()
AND created >= -30d
```
### 7.3 Retornos concluídos

Também foi criada uma consulta para identificar chamados que possuem pelo menos um dos SLAs de retorno concluído.

```jql
    (
    "SLA 1º Retorno" = completed()
    OR
    "SLA 2º Retorno" = completed()
    OR
    "SLA 3º Retorno" = completed()
    OR
    "SLA 4º Retorno" = completed()
    )
```

Essas consultas podem ser utilizadas na composição de filtros e relatórios para acompanhamento operacional.

### 8. Automações e Integração com Workflows

A implementação utiliza automações para executar transições específicas e reduzir a necessidade de intervenção manual em determinadas etapas.

### 8.1 Automação — Primeiro Atendimento

Responsável por movimentar os chamados para o status PRIMEIRO ATENDIMENTO, que inicia a contagem do SLA correspondente.

A regra está integrada ao processo de ativação do primeiro atendimento.

### 8.2 Automação — Sem Resposta do Demandante

Responsável por identificar chamados que permanecem 30 dias sem resposta do solicitante e movimentá-los para o status SEM RESPOSTA.

Essa transição permite que o atendente prossiga com o encerramento da solicitação.

### 8.3 Integração com fluxos de trabalho

A gestão dos SLAs está relacionada aos fluxos de trabalho utilizados pelas diferentes áreas da operação.

Foram documentados workflows específicos para:

* Recrutamento.
* TI / Administrativo.
* Financeiro.

Os status e as transições dos workflows são elementos importantes para controlar os eventos de início, pausa e conclusão dos ciclos de SLA.

### 9. Resultados e Benefícios Operacionais

A implementação foi estruturada para proporcionar melhorias na organização e no acompanhamento dos serviços.

### 9.1 Padronização do atendimento

* Definição de metas de resposta.
* Simplificação da classificação de prioridades.
* Estabelecimento de critérios comuns de acompanhamento.

### 9.2 Visibilidade operacional

* Identificação de chamados com prazos vencidos.
* Acompanhamento individual dos retornos.
* Organização das demandas por meio de filas específicas.
* Maior visibilidade sobre chamados aguardando interação.

### 9.3 Controle de processos

* Automatização de transições específicas.
* Padronização do tratamento de chamados sem resposta.
* Redução da dependência de verificações manuais para identificar determinadas pendências.
* Estruturação de filtros para acompanhamento dos compromissos de atendimento.

### 9.4 Apoio à tomada de decisão

A combinação de SLAs, filas e consultas JQL disponibiliza informações que podem apoiar a gestão da carga de trabalho, a identificação de gargalos e o acompanhamento do desempenho operacional.

### 10. Evidências Técnicas

A documentação original da implementação reúne registros das configurações realizadas no Jira Service Management.

As evidências contemplam:

* Configuração do SLA de Primeiro Atendimento.
* Configuração do SLA de Retorno do Demandante.
* Configurações dos SLAs de 1º, 2º, 3º e 4º Retornos.
* Consultas JQL para identificação de chamados vencidos.
* Filtros utilizados nas filas operacionais.
* Estrutura dos workflows.
* Automações relacionadas ao primeiro atendimento e à ausência de resposta do demandante.
  
Informações específicas do cliente e identificadores internos foram anonimizados para preservar a confidencialidade do ambiente.

### 11. Considerações finais

Este case demonstra a aplicação de recursos do Jira Service Management para estruturar um modelo de gestão de níveis de serviço integrado à operação de atendimento.

A solução não se limita à configuração de prazos. Ela combina regras de negócio, condições de SLA, workflows, automações e consultas JQL para estabelecer um processo de acompanhamento mais organizado e mensurável.

O principal objetivo da implementação foi transformar os compromissos de atendimento em critérios operacionais que pudessem ser acompanhados diretamente na plataforma.

Tecnologias e conceitos: Jira Service Management · ITSM · SLA Management · JQL · Workflow Management · Automação · Gestão de Serviços de TI · Gestão de Processos.
