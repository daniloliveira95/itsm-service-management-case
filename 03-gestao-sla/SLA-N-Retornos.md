# ⏱️ SLA de 1º Retorno

> Documentação de um SLA configurado no **Jira Service Management** para monitorar o tempo de resposta da equipe após a primeira manifestação do demandante.

---

## 📑 Sumário

1. [Visão geral](#1-visão-geral)
2. [Parâmetros de configuração](#2-parâmetros-de-configuração)
3. [Aplicabilidade](#3-aplicabilidade)
4. [Ciclo de vida do SLA](#4-ciclo-de-vida-do-sla)
5. [Decisões técnicas](#5-decisões-técnicas)
6. [Relação com o processo operacional](#6-relação-com-o-processo-operacional)
7. [Resumo técnico](#7-resumo-técnico)
8. [Considerações finais](#8-considerações-finais)

---

## 1. Visão geral

O **SLA de 1º Retorno** monitora o tempo que a equipe leva para dar continuidade ao atendimento depois que o chamado entra na etapa `RETORNO 1`.

Seu objetivo é garantir previsibilidade nas interações posteriores ao primeiro atendimento, respeitando o calendário de trabalho das áreas responsáveis e desconsiderando os períodos em que o chamado depende de agendamentos, outras áreas ou terceiros.

---

## 2. Parâmetros de configuração

| Parâmetro | Configuração |
|---|---|
| **Calendário (com meta)** | Calendário Áreas Centrais |
| **Prioridade Urgente** | 29 horas |
| **Prioridade Padrão** | 29 horas |
| **Demais prioridades** | Sem meta — 24/7 Calendário (Padrão) |
| **Demais itens** | Sem meta — 24/7 Calendário (Padrão) |
| **Status de início** | `RETORNO 1` |
| **Condições de pausa** | 3 status configurados |
| **Condições de encerramento** | 5 condições configuradas |
| **Formato de exibição** | Foco na data de entrega |

---

## 3. Aplicabilidade

O SLA é aplicado somente aos tipos de solicitação definidos para esse processo.

No ambiente original, a aplicabilidade é determinada por uma condição **JQL** que seleciona os tipos de solicitação e, em seguida, aplica metas conforme a prioridade do chamado. Para preservar informações internas, os tipos de solicitação e demais identificadores específicos não são reproduzidos nesta documentação.

### Metas por prioridade

| Prioridade | Calendário | Meta de tempo |
|---|---|---|
| 🔺 Urgente | Calendário Áreas Centrais | **29h** |
| 🟰 Padrão | Calendário Áreas Centrais | **29h** |
| Todas as prioridades restantes | 24/7 Calendário (Padrão) | Sem meta |
| Todos os itens remanescentes | 24/7 Calendário (Padrão) | Sem meta |

> [!TIP]
> Os itens são verificados **de cima para baixo**, e a meta de tempo é atribuída com base na **primeira instrução JQL correspondente**. Chamados fora do escopo da condição caem na regra final, **sem meta**.

---

## 4. Ciclo de vida do SLA

### 4.1 Início da contagem

A contagem começa quando o chamado entra no status:

`RETORNO 1`

### 4.2 Fluxo operacional

```mermaid
flowchart TD
    A["Chamado entra em<br/>RETORNO 1"] --> B["Início da contagem do SLA"]
    B --> C{"Qual é o estado<br/>atual do chamado?"}
    C -->|"Status de pausa"| D["Contagem pausada"]
    D -->|"Sai do status de pausa"| B
    C -->|"Condição de encerramento"| F["SLA encerrado"]
    C -->|"Nenhuma das anteriores"| E["Contagem permanece ativa"]
    E --> C
    F --> G["Chamado segue o workflow"]

    style B fill:#238636,color:#ffffff
    style D fill:#d29922,color:#ffffff
    style F fill:#8957e5,color:#ffffff
```

### 4.3 Condições de pausa

A contagem é **pausada** enquanto o chamado permanecer em um dos status abaixo:

| Tipo | Condição |
|---|---|
| Status | `ATENDIMENTO AGENDADO` |
| Status | `Pendente Outra Área` |
| Status | `Pendente Terceirizado` |

Nesses status, o avanço do chamado depende de um agendamento, de outra área ou de um terceiro, portanto o tempo não é contabilizado contra o SLA.

### 4.4 Condições de encerramento

| Tipo | Condição |
|---|---|
| Resolução | Resolução definida |
| Status inserido | `AGUARDANDO DEMANDANTE` |
| Status inserido | `Cancelado` |
| Status inserido | `Concluído` |
| Status inserido | `RETORNO 2` |

A contagem é encerrada quando uma resolução é definida, quando o chamado é finalizado (concluído ou cancelado) ou quando ele avança para uma etapa em que a espera passa a ser do demandante ou de um novo retorno.

---

## 5. Decisões técnicas

### 5.1 Meta única para Urgente e Padrão

Ambas as prioridades utilizam a mesma meta de **29 horas** no **Calendário Áreas Centrais**, que representa o período efetivo de atuação das equipes. Os itens **sem meta** utilizam o calendário padrão 24/7, pois não há prazo a ser cumprido.

### 5.2 Conjunto de pausas mais enxuto

Este SLA possui **três** status de pausa. Em comparação com o [SLA de Primeiro Atendimento](./SLA-Primeiro-Atendimento.md), os status `CRIADO` e `Pendente Aprovação` não pausam a contagem aqui, o que mantém o foco nas dependências que ainda podem ocorrer depois do primeiro atendimento: agendamentos, outras áreas e terceiros.

### 5.3 Encerramento por mudança de responsabilidade

O SLA é encerrado não apenas por conclusão, mas também quando a responsabilidade pelo próximo passo muda. Ao entrar em `AGUARDANDO DEMANDANTE`, por exemplo, a espera passa a ser do solicitante, e não mais da equipe.

### 5.4 Exibição focada na data de entrega

O formato de exibição prioriza a **data e hora de vencimento**, facilitando a leitura pelos analistas. O indicador aparece, por exemplo, como horário restante, em destaque quando o prazo está próximo de estourar, ou como data de entrega com ícone de sucesso quando cumprido.

---

## 6. Relação com o processo operacional

O SLA de 1º Retorno acompanha as interações que acontecem **depois** do primeiro atendimento.

Ele se conecta aos demais indicadores da seguinte forma:

- O [SLA de Primeiro Atendimento](./SLA-Primeiro-Atendimento.md) é encerrado quando o chamado entra em `RETORNO 1`, que é exatamente o status que **inicia** este SLA.
- Ao entrar em `AGUARDANDO DEMANDANTE`, este SLA é encerrado e passa a valer o [SLA de Retorno do Demandante](./SLA-Retorno-do-Demandante.md).
- A transição para `RETORNO 2` encerra este ciclo e dá sequência ao acompanhamento das interações seguintes.

Essa separação permite distinguir:

- ⚙️ Tempo até a primeira atuação da equipe
- 🔁 Tempo de execução de retornos e revisões
- ⏳ Tempo aguardando o demandante

---

## 7. Resumo técnico

| Elemento | Regra |
|---|---|
| **Início** | Entrada em `RETORNO 1` |
| **Pausa** | `ATENDIMENTO AGENDADO`, `Pendente Outra Área`, `Pendente Terceirizado` |
| **Encerramento** | Resolução definida ou entrada em `AGUARDANDO DEMANDANTE`, `Cancelado`, `Concluído` ou `RETORNO 2` |
| **Meta Urgente** | 29 horas |
| **Meta Padrão** | 29 horas |
| **Calendário das metas** | Calendário Áreas Centrais |
| **Demais casos** | Sem meta — 24/7 Calendário (Padrão) |

---

## 8. Considerações finais

O SLA de 1º Retorno estabelece um mecanismo objetivo para acompanhar o tempo de resposta da equipe nas interações posteriores ao primeiro atendimento.

A combinação de uma meta por prioridade, calendário específico, pausas por dependências externas e múltiplas condições de encerramento permite medir apenas o tempo que realmente depende da equipe, mantendo o indicador coerente com o restante do ciclo de vida do chamado.
