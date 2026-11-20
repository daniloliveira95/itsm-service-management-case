# Workflow Genérico de Atendimento — TI / Administrativo

> Documentação do workflow de atendimento utilizado para organizar demandas gerais no Jira Service Management. Embora tenha sido originalmente identificado como **TI / Administrativo**, sua estrutura utiliza status genéricos de atendimento, retorno, dependência e encerramento, podendo ser aplicada a solicitações de diferentes áreas.

---

## 📑 Sumário

1. [Visão geral](#1-visão-geral)
2. [Objetivos do workflow](#2-objetivos-do-workflow)
3. [Classificação visual dos status](#3-classificação-visual-dos-status)
4. [Ciclo de vida da solicitação](#4-ciclo-de-vida-da-solicitação)
5. [Descrição dos status](#5-descrição-dos-status)
6. [Retornos da equipe](#6-retornos-da-equipe)
7. [Estados de pendência](#7-estados-de-pendência)
8. [Transições globais e encerramento](#8-transições-globais-e-encerramento)
9. [Integração com SLAs](#9-integração-com-slas)
10. [Diagrama do workflow](#10-diagrama-do-workflow)
11. [Decisões de desenho e considerações operacionais](#11-decisões-de-desenho-e-considerações-operacionais)

---

## 1. Visão geral

Este workflow controla o ciclo de vida de solicitações gerais registradas no Jira Service Management.

A estrutura organiza o atendimento desde a criação do chamado, passando pelo primeiro atendimento, pelos retornos da equipe e por eventuais estados de dependência, até a conclusão ou o encerramento da solicitação.

Apesar de ter sido originalmente denominado **TI / Administrativo**, os status apresentados não são exclusivos dessas áreas. Eles representam situações comuns a diferentes operações de serviço, como:

- Recebimento e análise de uma solicitação.
- Primeiro contato ou atuação da equipe responsável.
- Retornos sucessivos durante o tratamento.
- Dependência de aprovação ou de outra área.
- Aguardando manifestação do solicitante.
- Atendimento agendado.
- Dependência de fornecedor ou terceiro.
- Encerramento da solicitação.

Por essa razão, este documento trata o fluxo como um **modelo genérico de atendimento**, que pode ser aplicado a demandas de diferentes áreas desde que as regras operacionais e os SLAs correspondentes sejam compatíveis.

---

## 2. Objetivos do workflow

O workflow foi estruturado para:

- Padronizar o ciclo de vida das solicitações.
- Identificar quando o atendimento foi iniciado.
- Registrar os retornos realizados pela equipe.
- Diferenciar atividades em execução de situações de espera ou dependência.
- Dar visibilidade aos impedimentos que afetam a continuidade do atendimento.
- Integrar os estados do chamado à medição dos SLAs.
- Permitir o encerramento da solicitação quando o tratamento for concluído ou quando não houver condições para sua continuidade.

A separação entre atividades, pendências e estados finais facilita a leitura operacional da fila e permite compreender por que determinado chamado permanece em uma etapa.

---

## 3. Classificação visual dos status

As cores dos status indicam o comportamento operacional do chamado e sua relação com a contagem dos SLAs.

| Cor | Classificação | Comportamento do SLA | Interpretação |
|---|---|---|---|
| Cinza | Pendência | Pausado | O atendimento depende de uma ação, validação, agenda ou condição externa para avançar. |
| Azul | Atividade | Em andamento | Existe atuação da equipe ou uma atividade de atendimento em curso. |
| Verde | Encerramento | Encerrado | A solicitação foi concluída ou encerrada, deixando o fluxo ativo. |

### 3.1 Status cinza — Pendência

Os status cinza representam situações em que o andamento depende de uma condição ou de uma ação necessária para a continuidade.

Durante a permanência nesses estados, o SLA fica pausado.

### 3.2 Status azul — Atividade

Os status azuis representam etapas de atuação da equipe. Enquanto o chamado permanece nesses estados, o SLA continua contando.

### 3.3 Status verde — Encerramento

Os status verdes representam estados finais. Ao entrar em um deles, o chamado deixa o ciclo regular de atendimento e o SLA é encerrado.

> A cor representa o comportamento configurado para o status no workflow. Ela não substitui a análise do histórico do chamado nem identifica, sozinha, quem é responsável pela próxima ação.

---

## 4. Ciclo de vida da solicitação

O ciclo começa quando a solicitação é criada e encaminhada para o primeiro atendimento.

A partir daí, o chamado pode seguir por diferentes caminhos, conforme a natureza da demanda e as dependências identificadas durante o tratamento.

### 4.1 Macroetapas

| Fase | Status relacionados | Comportamento do SLA |
|---|---|---|
| Entrada | `CRIADO` | Pausado |
| Atendimento inicial | `PRIMEIRO ATENDIMENTO` | Em andamento |
| Continuidade do atendimento | `RETORNO 1` a `RETORNO 4` | Em andamento |
| Dependências | `PENDENTE APROVAÇÃO`, `AGUARDANDO DEMANDANTE`, `PENDENTE OUTRA ÁREA`, `PENDENTE TERCEIRIZADO` | Pausado |
| Agendamento | `ATENDIMENTO AGENDADO` | Pausado |
| Encerramento | `NÃO APROVADO`, `CONCLUÍDO`, `CANCELADO`, `SEM RESPOSTA` | Encerrado |

### 4.2 Leitura do ciclo

O fluxo não deve ser interpretado necessariamente como uma sequência linear. Durante o atendimento, a equipe pode identificar dependências, solicitar informações, agendar uma intervenção ou registrar novos retornos.

O status representa a situação atual da solicitação. As transições permitem ajustar essa situação conforme o trabalho evolui.

---

## 5. Descrição dos status

### 5.1 `CRIADO`

**Classificação:** Cinza — Pendência  
**SLA:** Pausado

Estado inicial da solicitação. Indica que o chamado foi registrado e ainda não ingressou no status que marca o primeiro atendimento.

### 5.2 `PRIMEIRO ATENDIMENTO`

**Classificação:** Azul — Atividade  
**SLA:** Em andamento

Representa o início do atendimento pela equipe responsável. É o status associado ao início da medição do SLA de primeiro atendimento.

A entrada nesse status sinaliza que a solicitação passou da etapa de registro para a etapa de atuação da equipe.

### 5.3 `RETORNO 1`, `RETORNO 2`, `RETORNO 3` e `RETORNO 4`

**Classificação:** Azul — Atividade  
**SLA:** Em andamento

Os status de retorno registram etapas adicionais de atuação da equipe ao longo do tratamento da solicitação.

Eles permitem distinguir e acompanhar diferentes momentos de retorno, em vez de concentrar todo o histórico de interação em um único status genérico.

Exemplos de situações que podem exigir um retorno:

- Solicitação de revisão de uma entrega.
- Reincidência imediata de um problema após uma atuação.
- Continuidade de uma análise ou intervenção ainda não finalizada.
- Necessidade de registrar uma nova etapa de atendimento antes do encerramento.

Os retornos também permitem manter o chamado em acompanhamento quando ainda existe trabalho a realizar ou quando a solicitação não está pronta para ser concluída.

> Os nomes `RETORNO 1` a `RETORNO 4` identificam etapas distintas do processo. A imagem do workflow não comprova, por si só, que toda solicitação precise percorrer os quatro retornos em sequência. A utilização depende da evolução de cada chamado.

### 5.4 `PENDENTE APROVAÇÃO`

**Classificação:** Cinza — Pendência  
**SLA:** Pausado

Representa uma solicitação que depende de aprovação para continuar.

A permanência nesse status pausa a contagem do SLA enquanto a autorização necessária não é obtida.

O workflow também apresenta uma transição desse estado para `NÃO APROVADO`, permitindo registrar o encerramento quando a aprovação não for concedida.

### 5.5 `AGUARDANDO DEMANDANTE`

**Classificação:** Cinza — Pendência  
**SLA:** Pausado

Indica que a continuidade do atendimento depende de uma manifestação, informação, validação ou ação do solicitante.

Enquanto o chamado aguarda o demandante, o SLA fica pausado. O atendimento pode ser retomado quando houver retorno e a equipe puder dar continuidade ao tratamento.

### 5.6 `PENDENTE OUTRA ÁREA`

**Classificação:** Cinza — Pendência  
**SLA:** Pausado

Representa uma dependência de outra área da organização.

O status permite identificar que a continuidade da solicitação depende de uma ação ou retorno que não está sob atuação direta da equipe responsável pelo chamado.

### 5.7 `PENDENTE TERCEIRIZADO`

**Classificação:** Cinza — Pendência  
**SLA:** Pausado

Indica que o atendimento depende de fornecedor, prestador de serviço ou terceiro envolvido na resolução da solicitação.

A contagem do SLA fica pausada durante essa dependência, conforme a classificação do status.

### 5.8 `ATENDIMENTO AGENDADO`

**Classificação:** Cinza — Pendência  
**SLA:** Pausado

Representa uma situação em que o atendimento foi programado para uma data ou horário posterior.

Como a execução está condicionada ao agendamento, o SLA permanece pausado enquanto o chamado estiver nesse estado.

### 5.9 `NÃO APROVADO`

**Classificação:** Verde — Encerramento  
**SLA:** Encerrado

Estado final utilizado quando uma aprovação necessária não é concedida e a solicitação não deve prosseguir.

### 5.10 `CONCLUÍDO`

**Classificação:** Verde — Encerramento  
**SLA:** Encerrado

Estado final que representa a conclusão do atendimento ou da solicitação.

### 5.11 `CANCELADO`

**Classificação:** Verde — Encerramento  
**SLA:** Encerrado

Estado final utilizado quando a solicitação é cancelada e não deve continuar no fluxo regular de atendimento.

### 5.12 `SEM RESPOSTA`

**Classificação:** Verde — Encerramento  
**SLA:** Encerrado

Estado final utilizado quando não há resposta necessária para a continuidade da solicitação, conforme o procedimento operacional aplicável.

---

## 6. Retornos da equipe

A existência de quatro status específicos de retorno permite registrar diferentes momentos de atuação durante o ciclo de vida de uma solicitação.

Essa estrutura é útil quando o primeiro atendimento não representa a resolução definitiva e o chamado precisa continuar em acompanhamento.

### 6.1 Finalidade dos retornos

Os retornos podem representar:

- Uma nova atuação após a resposta inicial.
- Uma revisão de entrega ou de solução.
- Uma reincidência do problema.
- Uma etapa adicional de análise.
- A continuidade de uma atividade que impede o encerramento do chamado.

### 6.2 Relação com o encerramento

O registro de um retorno não significa que o chamado foi concluído. Pelo contrário: esses status permitem identificar que houve uma nova etapa de atuação e que a solicitação continua em tratamento.

O chamado deve permanecer no fluxo enquanto houver atividade ou condição operacional que impeça sua conclusão.

### 6.3 Acompanhamento

A separação entre `RETORNO 1`, `RETORNO 2`, `RETORNO 3` e `RETORNO 4` permite acompanhar os retornos em etapas distintas e relacioná-los às regras de SLA configuradas para cada uma.

Os detalhes das metas e condições de cada SLA estão documentados na seção de gestão de SLAs do repositório.

---

## 7. Estados de pendência

Os estados de pendência representam situações diferentes e, por isso, não devem ser tratados como um único status genérico.

| Status | Tipo de dependência | Efeito operacional |
|---|---|---|
| `PENDENTE APROVAÇÃO` | Autorização | Aguarda aprovação para prosseguir. |
| `AGUARDANDO DEMANDANTE` | Solicitante | Aguarda informação, manifestação ou validação do demandante. |
| `PENDENTE OUTRA ÁREA` | Dependência interna | Aguarda atuação de outra área da organização. |
| `PENDENTE TERCEIRIZADO` | Dependência externa | Aguarda atuação de fornecedor ou terceiro. |
| `ATENDIMENTO AGENDADO` | Programação | Aguarda a data ou o horário definido para atendimento. |

A distinção permite identificar a natureza do impedimento e facilita a leitura operacional dos chamados que estão com o SLA pausado.

---

## 8. Transições globais e encerramento

O diagrama original apresenta a indicação `Tudo` associada a transições de determinados status.

Essa indicação representa uma transição global disponível a partir dos estados contemplados pela configuração do workflow. Na documentação visual, ela aparece associada aos status de retorno, pendência e encerramento.

### 8.1 Encerramentos

O workflow apresenta quatro estados finais:

- `NÃO APROVADO`
- `CONCLUÍDO`
- `CANCELADO`
- `SEM RESPOSTA`

Os estados `CONCLUÍDO`, `CANCELADO` e `SEM RESPOSTA` aparecem associados à indicação de transição global `Tudo`, permitindo o encerramento a partir de diferentes etapas do processo.

O status `NÃO APROVADO` aparece conectado diretamente a `PENDENTE APROVAÇÃO` no diagrama.

### 8.2 Importância operacional

A possibilidade de encerramento a partir de diferentes etapas evita que um chamado precise percorrer todos os status intermediários quando a solicitação já não deve continuar.

Isso permite lidar com situações como:

- Cancelamento da necessidade original.
- Ausência de resposta que inviabiliza a continuidade.
- Conclusão antecipada do atendimento.
- Reprovação de uma aprovação necessária.

> A indicação `Tudo` foi preservada conforme apresentada no diagrama de origem. A disponibilidade exata de cada transição deve ser conferida na configuração do Jira, incluindo eventuais condições e permissões.

---

## 9. Integração com SLAs

Os status do workflow são utilizados como eventos para controlar a contagem dos SLAs.

A integração permite que o indicador acompanhe o atendimento quando há atuação da equipe e pause quando o chamado depende de uma ação ou condição externa.

### 9.1 Relação entre status e indicadores

| Indicador / SLA | Status relacionado |
|---|---|
| Primeiro Atendimento | `PRIMEIRO ATENDIMENTO` |
| Retorno do Demandante | `AGUARDANDO DEMANDANTE` |
| Primeiro Retorno | `RETORNO 1` |
| Segundo Retorno | `RETORNO 2` |
| Terceiro Retorno | `RETORNO 3` |
| Quarto Retorno | `RETORNO 4` |

### 9.2 Comportamento por categoria

| Categoria | Status | Comportamento |
|---|---|---|
| Entrada | `CRIADO` | Pausado |
| Atividade | `PRIMEIRO ATENDIMENTO` | Em andamento |
| Atividade | `RETORNO 1` a `RETORNO 4` | Em andamento |
| Pendência | Status cinzas | Pausado |
| Encerramento | Status verdes | Encerrado |

### 9.3 Separação entre workflow e SLA

O workflow determina o estado atual da solicitação e as transições disponíveis.

O SLA mede o tempo conforme as condições configuradas para início, pausa e encerramento.

Portanto, a mudança de status pode ter impacto direto na medição do atendimento. A documentação detalhada das metas, calendários e condições de cada indicador deve ser consultada no case de gestão de SLAs.

---

## 10. Diagrama do workflow

O diagrama abaixo representa os estados e as relações visíveis na configuração apresentada.

<img width="1030" height="299" alt="image" src="https://github.com/user-attachments/assets/3ddb31fd-8ab3-49ec-9088-34539f047f56" />


> **Nota:** o diagrama acima destaca os estados e o encadeamento inicial identificados na imagem. As transições globais indicadas por `Tudo` não foram desenhadas individualmente para evitar sugerir uma sequência obrigatória entre todos os status. A configuração efetiva das transições deve ser consultada no Jira.

---

## 11. Decisões de desenho e considerações operacionais

### 11.1 Workflow reutilizável

Embora originalmente identificado como TI / Administrativo, o fluxo utiliza estados genéricos que podem atender demandas de diferentes áreas.

A reutilização deve considerar se o processo da área possui etapas compatíveis com atendimento, retorno, pendência e encerramento.

### 11.2 Pendências com significado específico

A separação dos estados de pendência permite identificar se o chamado aguarda o solicitante, outra área, um terceiro, uma aprovação ou uma data agendada.

Isso melhora a leitura do motivo pelo qual o atendimento está pausado.

### 11.3 Retornos rastreáveis

Os quatro status de retorno permitem registrar etapas sucessivas de atuação sem confundir retorno, pendência e conclusão.

### 11.4 Encerramento flexível

Os estados finais permitem encerrar solicitações sem exigir a passagem por todas as etapas intermediárias, respeitando as transições e permissões configuradas.

### 11.5 Integração com indicadores

A classificação por cores conecta a leitura visual do workflow ao comportamento dos SLAs, facilitando a identificação de atividades em andamento, pendências e chamados encerrados.

---

## Considerações finais

O Workflow Genérico de Atendimento organiza solicitações de diferentes naturezas por meio de um conjunto padronizado de estados de entrada, atividade, retorno, pendência e encerramento.

Sua principal característica é separar claramente o trabalho em execução das situações que dependem de terceiros ou de ações externas, refletindo essa distinção diretamente na contagem dos SLAs.

A estrutura também permite registrar retornos sucessivos e encerrar solicitações a partir de diferentes etapas, oferecendo flexibilidade para acompanhar o ciclo de vida real de cada chamado.

**Tecnologias e conceitos:** Jira Service Management · ITSM · Workflow Management · Gestão de Serviços · SLA Management · Gestão de Processos · Atendimento e Suporte.
