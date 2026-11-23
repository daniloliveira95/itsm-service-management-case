# KPI — Chamados Recebidos vs. Chamados Resolvidos

## Objetivo

Comparar o volume de chamados recebidos pelo time de atendimento com o volume de chamados resolvidos no mesmo período.

O indicador oferece uma visão da capacidade operacional do time diante da demanda recebida, ajudando a identificar períodos em que a entrada de solicitações supera a capacidade de resolução e pode contribuir para o crescimento do backlog.

## Composição do indicador

O KPI é composto por duas séries independentes:

| Série | Critério | Significado |
|---|---|---|
| Chamados recebidos | Data de criação dentro do período | Quantidade de chamados criados no intervalo analisado |
| Chamados resolvidos | Data de resolução dentro do período | Quantidade de chamados cuja resolução ocorreu no intervalo analisado |

**Regra importante:** para a série de chamados resolvidos, considerar a data em que a resolução ocorreu (`resolutiondate`), e não apenas o status atual do chamado.

## Consultas JQL

Os exemplos abaixo consideram janeiro de 2026. Ajuste as datas conforme o período que será analisado.

### 1. Chamados recebidos

```jql
created >= "2026-01-01" AND created < "2026-02-01"
```

Seleciona os chamados criados a partir do início de 1º de janeiro até antes do início de 1º de fevereiro.

### 2. Chamados resolvidos

```jql
resolutiondate >= "2026-01-01" AND resolutiondate < "2026-02-01"
```

Seleciona os chamados cuja data de resolução ocorreu a partir do início de 1º de janeiro até antes do início de 1º de fevereiro.

> **Observação:** as consultas são exemplos parametrizados. Os campos `created` e `resolutiondate` são campos padrão do Jira, mas filtros adicionais — como projeto, tipo de solicitação ou exclusão de chamados de teste — devem ser adaptados à realidade de cada ambiente.

## Visualização recomendada

<img width="698" height="495" alt="image" src="https://github.com/user-attachments/assets/9bf4cf65-7773-479f-86ee-e3f466156578" />


**Gráfico:** colunas agrupadas.

- **Série 1:** chamados recebidos.
- **Série 2:** chamados resolvidos.
- **Eixo horizontal:** períodos de análise (por exemplo, meses).
- **Eixo vertical:** quantidade de chamados.

A comparação deve utilizar a mesma janela temporal para as duas séries.

## Indicadores complementares

Além da comparação visual, podem ser apresentados dois indicadores derivados:

### Saldo operacional

```text
Saldo operacional = Chamados recebidos - Chamados resolvidos
```

Interpretação:

- **Saldo positivo:** entraram mais chamados do que foram resolvidos no período.
- **Saldo negativo:** foram resolvidos mais chamados do que entraram no período.
- **Saldo igual a zero:** os volumes de entrada e resolução foram iguais.

### Índice de resolução do período

```text
Índice de resolução = (Chamados resolvidos / Chamados recebidos) × 100
```

Esse índice compara os volumes de resolução e entrada no período. Se não houver chamados recebidos no intervalo, o índice deve ser tratado como não aplicável, evitando divisão por zero.

## Exemplo ilustrativo

Os números abaixo são fictícios e servem apenas para demonstrar a leitura do indicador.

| Mês | Chamados recebidos | Chamados resolvidos | Saldo operacional | Índice de resolução |
|---|---:|---:|---:|---:|
| Janeiro | 120 | 110 | +10 | 91,7% |
| Fevereiro | 100 | 115 | -15 | 115% |
| Março | 140 | 125 | +15 | 89,3% |

No exemplo:

- Janeiro e março apresentaram volume de entrada superior ao de resolução.
- Em fevereiro, o time resolveu mais chamados do que recebeu no mês.
- A sequência dos períodos ajuda a observar tendências, mas não explica isoladamente as causas das variações.

## Como interpretar corretamente

Este KPI mede **volume de entrada versus volume de resolução dentro do mesmo período**. Ele é útil para acompanhar a pressão operacional e a capacidade de vazão do atendimento.

Entretanto, os chamados resolvidos em um mês não são necessariamente os mesmos chamados que foram recebidos naquele mês. Parte dos chamados resolvidos pode ter sido criada em períodos anteriores.

Portanto:

- O índice de resolução **não representa** a porcentagem dos chamados recebidos naquele mês que foram efetivamente resolvidos.
- Um índice acima de 100% indica que o volume resolvido superou o volume recebido no período; não significa, por si só, que todo o backlog foi eliminado.
- Para avaliar o backlog, é necessário acompanhar também a quantidade de chamados ainda abertos ao longo do tempo.
- Para medir o tempo de resolução ou a eficiência por coorte, é necessário outro recorte analítico.

## Configuração do relatório

A JQL define quais chamados pertencem a cada série. A apresentação em colunas agrupadas e a consolidação por mês dependem dos recursos do gadget ou da ferramenta de relatórios utilizada.

Ao configurar o relatório:

1. Defina a mesma periodicidade para as duas séries.
2. Utilize `created` para a série de recebidos.
3. Utilize `resolutiondate` para a série de resolvidos.
4. Mantenha o mesmo escopo de projeto e os mesmos critérios de inclusão nas duas consultas, quando aplicável.
5. Identifique claramente o período analisado.

---

**Classificação:** KPI operacional de volume e capacidade de atendimento.  
**Finalidade:** acompanhar a relação entre demanda recebida e volume resolvido pelo time.
