# KPIs — Tempo de Vida e Primeiro Atendimento dos Chamados

## Objetivo

Consolidar dois indicadores temporais do atendimento:

1. **Tempo de vida dos chamados:** acompanhar quanto tempo, em média, os chamados levam desde a criação até a resolução.
2. **Tempo de primeiro atendimento:** acompanhar quanto tempo, em média, o time leva para realizar o primeiro atendimento.

Os dois indicadores são apresentados separadamente para chamados **Padrão (não urgentes)** e **Urgentes**, permitindo comparar o comportamento do atendimento conforme a prioridade.

O modelo toma como referência os gráficos do relatório gerencial: ambos analisam chamados resolvidos nos últimos 90 dias, com agrupamento mensal.

---

# 1. Tempo de vida dos chamados

## O que o indicador mede

O tempo de vida representa o intervalo entre a criação do chamado e sua resolução.

A análise considera os chamados cuja **data de resolução** ocorreu dentro do período selecionado. O tempo é então calculado a partir da diferença entre a data de criação e a data de resolução.

**Unidade de apresentação:** dias.

**Recorte temporal de referência:** últimos 90 dias, agrupados mensalmente.

## Consultas JQL

As consultas abaixo são propostas parametrizadas para selecionar os chamados resolvidos nos últimos 90 dias. Os valores de prioridade devem corresponder exatamente aos nomes cadastrados no ambiente Jira.

### Chamados Padrão (não urgentes)

```jql
resolutiondate >= -90d
AND priority = "Padrão"
```

### Chamados Urgentes

```jql
resolutiondate >= -90d
AND priority = "Urgente"
```

> **Importante:** a JQL seleciona os chamados resolvidos no intervalo e separados por prioridade. Ela não calcula, por si só, a média entre criação e resolução. O cálculo do tempo de vida e a agregação mensal precisam ser realizados pelo gadget ou pela ferramenta de relatórios, utilizando as datas `created` e `resolutiondate`.

## Visualização recomendada

**Gráfico:** colunas ou linha, conforme o recurso de relatório disponível.

- **Série 1:** tempo médio de vida dos chamados Padrão.
- **Série 2:** tempo médio de vida dos chamados Urgentes.
- **Eixo horizontal:** mês da resolução.
- **Eixo vertical:** tempo médio em dias.

Para manter a comparação consistente, as duas séries devem utilizar o mesmo período, a mesma unidade e o mesmo critério de cálculo.

## Interpretação

- Aumento do tempo médio pode indicar maior complexidade das demandas, dependências externas, acúmulo de trabalho ou dificuldades no fluxo de resolução.
- Redução do tempo médio pode indicar maior vazão operacional, mas deve ser analisada em conjunto com volume, prioridade e complexidade dos chamados.
- A comparação entre Urgentes e Padrão ajuda a verificar se a prioridade está refletindo diferenças no tratamento e na velocidade de resolução.

---

# 2. Tempo de primeiro atendimento

## O que o indicador mede

O tempo de primeiro atendimento representa o intervalo entre o início da contagem do SLA de primeiro atendimento e o primeiro atendimento realizado pela equipe.

No relatório gerencial de referência, o indicador é apresentado como **tempo médio em dias**, separado por prioridade e considerando os chamados resolvidos nos últimos 90 dias, com agrupamento mensal.

### Metas apresentadas no relatório de referência

| Prioridade | Meta registrada no gráfico |
|---|---:|
| Padrão (não urgente) | 4 dias úteis |
| Urgente | 2 dias úteis |

Essas são as metas exibidas nos gráficos do relatório gerencial utilizado como referência. Elas devem ser tratadas como configuração histórica daquele relatório, não como confirmação automática de que continuam vigentes em qualquer ambiente.

## Consultas JQL

As consultas abaixo são propostas para selecionar chamados com SLA de primeiro atendimento concluído e resolução dentro dos últimos 90 dias.

### Chamados Padrão (não urgentes)

```jql
resolutiondate >= -90d
AND priority = "Padrão"
AND "SLA Primeiro Atendimento" = completed()
```

### Chamados Urgentes

```jql
resolutiondate >= -90d
AND priority = "Urgente"
AND "SLA Primeiro Atendimento" = completed()
```

> **Importante:** a condição `completed()` seleciona SLAs concluídos. Ela não garante, isoladamente, que o SLA foi cumprido dentro da meta. Para separar atendimentos dentro e fora do prazo, é necessário consultar também o estado `breached()` e confirmar como o gadget utilizado calcula a média.
>
> A JQL seleciona os chamados. O valor médio do tempo de primeiro atendimento deve ser obtido do campo de SLA correspondente no relatório/gadget, e não de uma contagem simples de issues.

## Visualização recomendada

**Gráfico:** linha temporal.

- **Série 1:** tempo médio de primeiro atendimento — Padrão.
- **Série 2:** tempo médio de primeiro atendimento — Urgente.
- **Eixo horizontal:** mês de referência.
- **Eixo vertical:** tempo médio em dias.

Quando a ferramenta permitir, incluir linhas de referência para as metas de 4 dias úteis (Padrão) e 2 dias úteis (Urgente). A unidade da meta deve permanecer identificada como **dias úteis**, sem compará-la diretamente a valores calculados em dias corridos.

## Interpretação

- A média deve ser analisada em conjunto com a meta aplicável a cada prioridade.
- Um aumento pode indicar demora na triagem, fila elevada, indisponibilidade de equipe ou falhas na automação que inicia a contagem do SLA.
- A média pode esconder casos extremos. Por isso, é recomendável acompanhar também a quantidade de SLAs violados e a distribuição dos tempos, quando o Jira permitir.

---

# 3. Comparação entre os indicadores

| Dimensão | Tempo de vida | Primeiro atendimento |
|---|---|---|
| O que mede | Tempo entre criação e resolução | Tempo até o primeiro atendimento |
| Início da contagem | Criação do chamado | Início da contagem do SLA |
| Encerramento da medição | Resolução do chamado | Primeiro atendimento |
| Unidade de referência | Dias | Dias |
| Segmentação | Padrão e Urgente | Padrão e Urgente |
| Agrupamento | Mensal | Mensal |
| Janela do relatório de referência | Últimos 90 dias | Últimos 90 dias |

## Leitura gerencial conjunta

A leitura combinada permite distinguir dois momentos diferentes da experiência de atendimento:

- **Primeiro atendimento:** indica a velocidade com que a equipe começa a atuar sobre a solicitação.
- **Tempo de vida:** indica quanto tempo o chamado permanece em aberto até ser resolvido.

Um primeiro atendimento rápido não significa necessariamente uma resolução rápida. Um chamado pode receber retorno inicial dentro da meta e continuar em andamento por mais tempo devido à complexidade, dependências ou necessidade de interações adicionais.

Por isso, os indicadores devem ser analisados em conjunto, mas não tratados como medidas equivalentes.

---

# 4. Observações técnicas e limitações

1. **JQL e cálculo são etapas diferentes.** As consultas filtram os chamados que compõem cada série. A média temporal, o agrupamento mensal e a renderização do gráfico dependem do gadget ou da ferramenta de relatórios.
2. **Prioridades são valores do ambiente.** Os termos `Padrão` e `Urgente` são utilizados conforme a classificação descrita no relatório. A consulta deve ser ajustada caso os nomes ou o campo de prioridade sejam diferentes no Jira.
3. **Tempo de vida não é tempo de trabalho ativo.** A diferença entre criação e resolução representa o tempo decorrido do ciclo do chamado. Não deve ser interpretada automaticamente como horas efetivamente trabalhadas pela equipe.
4. **SLA concluído não significa necessariamente SLA cumprido.** Um SLA pode ser concluído depois de ultrapassar a meta. Para medir violações, utilizar também o estado `breached()`.
5. **Calendários precisam ser considerados.** O relatório de primeiro atendimento apresenta metas em dias úteis. A comparação com o tempo médio só é válida se a ferramenta utilizar calendário e unidade compatíveis.
6. **Amostras pequenas exigem cautela.** Médias mensais calculadas com poucos chamados podem oscilar bastante e não representar uma tendência consistente.

## Resumo da configuração proposta

| Gráfico | Filtros principais | Métrica calculada | Segmentação |
|---|---|---|---|
| Tempo de vida dos chamados | Resolvidos nos últimos 90 dias | Média entre `created` e `resolutiondate` | Padrão / Urgente |
| Tempo de primeiro atendimento | Resolvidos nos últimos 90 dias e SLA de primeiro atendimento concluído | Média do SLA de primeiro atendimento | Padrão / Urgente |

---

**Classificação:** KPIs operacionais de desempenho temporal.  
**Finalidade:** acompanhar a velocidade de início do atendimento e o tempo total de resolução, comparando chamados Padrão e Urgentes.
