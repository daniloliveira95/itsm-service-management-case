# KPIs — Ranking de Áreas e Colaboradores Demandantes

## Objetivo

Identificar as principais origens da demanda de atendimento por meio de dois rankings:

1. **Top 5 áreas demandantes:** mostra quais áreas da organização mais acionam o time de atendimento.
2. **Top 5 colaboradores demandantes:** mostra quais solicitantes registram o maior volume de chamados.

Os rankings ajudam a compreender a distribuição da demanda, identificar áreas com maior necessidade de suporte e apoiar ações de comunicação, prevenção, treinamento e planejamento da capacidade de atendimento.

A referência visual é o relatório gerencial, que apresenta os rankings separados por prioridade — **Urgente** e **Padrão** — e uma coluna com o total de chamados por área ou colaborador.

---

# 1. Top 5 áreas demandantes

## O que o indicador mede

Apresenta as cinco áreas com maior volume de chamados registrados no período analisado.

A tabela deve apresentar:

| Coluna | Conteúdo |
|---|---|
| Área solicitante | Área à qual o chamado está associado |
| Urgente | Quantidade de chamados urgentes |
| Padrão | Quantidade de chamados padrão |
| Total | Quantidade total de chamados da área |

O ranking deve ser ordenado pelo total de chamados, do maior para o menor, exibindo as cinco primeiras áreas.

## Consulta JQL base

```jql
created >= -90d
AND created < now()
```

A consulta considera os chamados criados nos últimos 90 dias. O período é ilustrativo e pode ser alterado conforme a janela de análise desejada.

### Variação — somente chamados urgentes

```jql
created >= -90d
AND created < now()
AND priority = "Urgente"
```

### Variação — somente chamados padrão

```jql
created >= -90d
AND created < now()
AND priority = "Padrão"
```

> **Importante:** a JQL seleciona os chamados que compõem o relatório. O ranking por área, a contagem por prioridade, o cálculo do total e a ordenação Top 5 são realizados na configuração da tabela ou do gadget de relatórios.

## Configuração recomendada da tabela

- **Filtro de origem:** JQL base do período.
- **Agrupamento / linhas:** campo de área solicitante.
- **Colunas ou séries:** prioridade.
- **Métrica:** contagem de chamados (issues).
- **Total:** soma das contagens por prioridade.
- **Ordenação:** total decrescente.
- **Limite de exibição:** 5 áreas.

O campo de área pode ser um campo personalizado do Jira. O nome utilizado na consulta ou na configuração deve corresponder ao cadastro real do ambiente.

---

# 2. Top 5 colaboradores demandantes

## O que o indicador mede

Apresenta os cinco colaboradores que mais registraram chamados no período analisado.

A tabela deve apresentar:

| Coluna | Conteúdo |
|---|---|
| Colaborador | Solicitante que registrou o chamado |
| Urgente | Quantidade de chamados urgentes |
| Padrão | Quantidade de chamados padrão |
| Total | Quantidade total de chamados do colaborador |

O ranking deve ser ordenado pelo total de chamados, do maior para o menor, exibindo os cinco primeiros colaboradores.

## Consulta JQL base

```jql
created >= -90d
AND created < now()
```

O campo padrão `reporter` representa o usuário que registrou o chamado. O ranking pode utilizar esse campo como dimensão de agrupamento.

### Variação — somente chamados urgentes

```jql
created >= -90d
AND created < now()
AND priority = "Urgente"
```

### Variação — somente chamados padrão

```jql
created >= -90d
AND created < now()
AND priority = "Padrão"
```

> **Importante:** a consulta base é a mesma utilizada no ranking de áreas. A diferença está no campo de agrupamento: neste caso, o solicitante (`reporter`). A JQL não calcula diretamente o Top 5 nem agrupa os resultados por colaborador.

## Configuração recomendada da tabela

- **Filtro de origem:** JQL base do período.
- **Agrupamento / linhas:** `reporter` (solicitante).
- **Colunas ou séries:** prioridade.
- **Métrica:** contagem de chamados (issues).
- **Total:** soma das contagens por prioridade.
- **Ordenação:** total decrescente.
- **Limite de exibição:** 5 colaboradores.

---

# 3. Leitura gerencial dos rankings

Os rankings oferecem perspectivas complementares sobre a origem da demanda:

| Análise | O que ajuda a identificar |
|---|---|
| Ranking por área | Áreas que concentram maior volume de solicitações |
| Ranking por colaborador | Solicitantes com maior frequência de abertura de chamados |
| Distribuição por prioridade | Participação de chamados urgentes e padrão em cada grupo |
| Total por grupo | Volume geral de demanda associado a cada área ou colaborador |

Possíveis aplicações gerenciais:

- Identificar áreas que podem se beneficiar de treinamentos ou orientações preventivas.
- Investigar concentração de incidentes ou solicitações recorrentes em determinados setores.
- Apoiar o planejamento da capacidade e a distribuição de recursos do time de atendimento.
- Avaliar se o volume elevado está associado a processos específicos, mudanças organizacionais ou necessidades de suporte.
- Direcionar ações de comunicação e autoatendimento para grupos com maior recorrência de solicitações.

## Cuidados de interpretação

- **Volume não é sinônimo de problema:** uma área maior ou com mais usuários pode naturalmente registrar mais chamados. Quando possível, compare o volume com o número de colaboradores da área.
- **Volume não mede qualidade do atendimento:** o ranking mostra origem e quantidade de solicitações, não satisfação, complexidade ou desempenho individual do suporte.
- **Prioridade deve ser analisada no contexto:** uma concentração de chamados urgentes pode merecer investigação, mas não comprova isoladamente falha operacional.
- **Evite exposição desnecessária:** rankings nominais de colaboradores devem ser compartilhados somente com públicos autorizados e com finalidade gerencial legítima. Em portfólios públicos, substitua nomes por dados fictícios ou anonimizados.

---

# 4. Considerações técnicas

1. **JQL versus visualização:** a consulta filtra os chamados; agrupamento, contagem, soma, ordenação e limite Top 5 dependem do gadget ou da ferramenta de relatórios.
2. **Campo de área:** o relatório de referência utiliza a dimensão “Área Solicitante”. É necessário confirmar o nome exato do campo personalizado no ambiente Jira antes de configurar o relatório.
3. **Solicitante:** `reporter` é o campo padrão do Jira para identificar quem registrou o chamado. Se o processo utilizar outro campo para representar o colaborador demandante, o agrupamento deve ser ajustado.
4. **Prioridades:** os exemplos utilizam `Urgente` e `Padrão`, conforme a classificação apresentada no relatório de referência. Os valores precisam corresponder exatamente às opções cadastradas no Jira.
5. **Período:** os exemplos utilizam uma janela móvel de 90 dias. Para reproduzir um relatório histórico, substitua o intervalo por datas específicas.
6. **Escopo consistente:** os dois rankings devem utilizar o mesmo período e os mesmos critérios de inclusão para que os resultados possam ser comparados.

## Resumo da configuração

| Ranking | Campo de agrupamento | Segmentação | Métrica | Exibição |
|---|---|---|---|---|
| Top 5 áreas demandantes | Área solicitante (campo personalizado) | Urgente / Padrão | Contagem de chamados | 5 primeiras áreas |
| Top 5 colaboradores demandantes | `reporter` | Urgente / Padrão | Contagem de chamados | 5 primeiros colaboradores |

---

**Classificação:** KPIs de distribuição e concentração da demanda.  
**Finalidade:** identificar as principais origens dos chamados e apoiar decisões de prevenção, comunicação e planejamento operacional.
