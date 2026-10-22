# Monitoramento de Prazos e SLAs com JQL

## 1. Objetivo

Este documento apresenta um exemplo de utilização da Jira Query Language (JQL) para identificar solicitações que ultrapassaram os prazos definidos em seus respectivos indicadores de nível de serviço (SLA).

A abordagem pode ser aplicada a ambientes que utilizam o Jira Service Management para gerenciar solicitações e processos de diferentes áreas organizacionais.

## 2. Cenário de utilização

Em uma organização, diferentes departamentos podem utilizar uma plataforma compartilhada para registrar, acompanhar e resolver solicitações.

Entre os possíveis contextos de utilização estão:

- Tecnologia da Informação;
- Recursos Humanos;
- Administração;
- Jurídico;
- Outras áreas responsáveis pelo atendimento de solicitações internas.

Dependendo da configuração da plataforma, cada processo pode possuir etapas, responsáveis, prazos e critérios de atendimento próprios.

Uma consulta consolidada permite identificar solicitações com indicadores de SLA vencidos, apoiando o acompanhamento operacional e a priorização de demandas.

## 3. Consulta JQL de exemplo

A consulta abaixo representa um modelo genérico de monitoramento. Os identificadores de projeto e os nomes dos campos devem ser adaptados à configuração do ambiente de destino.

    project = PROJECT_KEY
    AND created >= -30d
    AND (
        "SLA First Response" = breached()
        OR "SLA Second Response" = breached()
        OR "SLA Third Response" = breached()
        OR "SLA Fourth Response" = breached()
        OR "SLA First Assignment" = breached()
    )
    ORDER BY created DESC

## 4. Explicação da consulta

- `project = PROJECT_KEY`: restringe os resultados ao projeto especificado.
- `created >= -30d`: considera solicitações criadas nos últimos 30 dias.
- `breached()`: verifica se o indicador de SLA ultrapassou a meta configurada.
- `OR`: permite retornar solicitações que atendam a pelo menos uma das condições de SLA.
- `ORDER BY created DESC`: ordena os resultados da solicitação mais recente para a mais antiga.

## 5. Aplicações organizacionais

Uma consulta desse tipo pode apoiar diferentes necessidades de acompanhamento, conforme os processos e SLAs configurados.

Exemplos de aplicação:

- Identificação de solicitações com prazos excedidos;
- Acompanhamento de pendências entre departamentos;
- Apoio à priorização de demandas;
- Monitoramento de indicadores operacionais;
- Preparação de filtros para acompanhamento e relatórios.

A consulta, isoladamente, não determina a causa do atraso nem substitui a análise do processo responsável pela solicitação.

## 6. Adaptação ao ambiente

Antes de utilizar a consulta, é necessário verificar:

- A chave do projeto;
- Os nomes exatos dos campos de SLA;
- A disponibilidade das funções JQL utilizadas;
- Os critérios de início, pausa e encerramento dos SLAs;
- O período de análise desejado;
- As permissões de acesso aos projetos e às solicitações.

Os nomes dos campos apresentados neste exemplo são ilustrativos e precisam ser validados no ambiente de destino.

## 7. Limitações

O resultado depende da configuração dos SLAs, dos campos disponíveis e das condições definidas para cada processo.

A existência de um SLA vencido indica que uma meta configurada não foi cumprida, mas não explica, por si só, o motivo do atraso.

Este documento apresenta um exemplo técnico para fins de estudo e portfólio. A consulta deve ser validada antes de qualquer utilização operacional.

## 8. Confidencialidade

Este exemplo utiliza identificadores e nomes genéricos.

Não devem ser publicados nomes de clientes, chaves reais de projetos, URLs internas, dados de usuários, informações de solicitações ou configurações confidenciais.

A documentação tem como objetivo demonstrar conhecimentos técnicos e princípios de gestão de processos sem expor informações proprietárias.
