# Squad Agêntica

Orquestração de agentes de IA especializados que conduzem o ciclo de vida de desenvolvimento de software (SDLC) de itens de trabalho no xClick, com revisão humana obrigatória nas raias de gate.

## Language

**xClick**:
Board ágil interno da empresa (tipo Jira simplificado) usado para gerenciar o SDLC. Expõe API e um servidor MCP para consulta e movimentação de itens.

**Raia**:
Uma coluna/status de um item dentro do xClick, representando um estágio do SDLC. Sequência fixa: Backlog → Em Refinamento → Refinado → Execução → Peer Review → Pronto para QA → Em Testes QA → Testado QA → Em Implantação → Implantado → Ativado.
_Avoid_: Status, coluna, etapa (usar "raia" para manter consistência com o vocabulário do usuário)

**Item**:
Unidade de trabalho rastreada no xClick. Os tipos principais são História e Bug; existem outros tipos e subtipos.
_Avoid_: Card, ticket, work item

**Subitem**:
Item filho que o orquestrador abre na História ou Bug a cada raia em que ela entra, carregando o conteúdo produzido pelo agente daquela raia (ex.: resultado do refinamento, update sets, resultado de QA). Tem status próprio — Pendente, Concluído ou Cancelado — e é ele que funciona como semáforo da raia (ver ADR-0005).

**Subbug**:
Bug filho aberto pelo agente QAI durante "Em Testes QA" quando um defeito é encontrado. Faz o papel de semáforo junto com o subitem da raia seguinte, "Testado QA": enquanto houver subbug em aberto o item não avança; o QA ajusta o que for necessário e encerra os subbugs para liberar o avanço. Na v1 subbug não devolve o item para uma raia anterior (ver ADR-0007).

**Orquestrador**:
Componente central que lê periodicamente as raias do xClick, obtém o conteúdo dos itens, delega para o agente especializado correspondente à raia atual, abre o subitem daquela raia e movimenta os itens conforme a regra do semáforo de subitem.

**Agente especializado**:
Agente de IA responsável por uma etapa específica do SDLC (ex.: refinamento, execução/desenvolvimento, QAI, implantação/GMUD). Construído pelo próprio time de modernização de jornadas, mas em frente separada da orquestração: cada agente é plugado ao fluxo conforme fica pronto para consumo (ver ADR-0002).

**Semáforo de subitem**:
Regra de governança que decide se um item pode avançar de raia: ao entrar na raia o orquestrador abre o subitem e só avança quando todos os filhos abertos para aquela raia — o subitem e, em "Testado QA", os subbugs — estiverem Concluído/Encerrado ou Cancelado. Qualquer um Pendente bloqueia o avanço. Quem conclui muda conforme a raia: nas raias de revisão humana é a pessoa responsável, nas demais é o próprio agente ao terminar com sucesso.
_Avoid_: Flag, gate (usar "semáforo" — termo do usuário)

**Raia de revisão humana**:
Raia em que o semáforo só é liberado por uma pessoa. São quatro: **Refinado** (o refinamento entregue pelo agente é ajustado e concluído por um humano antes da Execução, resolvendo as dúvidas que o agente apontou), **Peer Review** (revisão do que o agente implementou no ambiente DEV, com ajuste manual do que ficou incorreto ou faltando), **Testado QA** (revisão dos testes do agente e ajuste/encerramento dos subbugs pelo QA) e **Em Implantação** (o agente monta a GMUD, mas quem implanta em produção e encerra o subitem é uma pessoa — ver ADR-0008).
_Avoid_: Gate manual, aprovação (usar "raia de revisão humana")

**"Para IA Ler"**:
Seção dedicada dentro de um item do xClick que contém o conteúdo destinado a ser consumido pelos agentes de IA.

**QAI**:
Agente especializado responsável pelos testes automatizados/assistidos por IA na raia "Em Testes QA".

**GMUD** (Gestão de Mudança):
Documento de change management necessário para autorizar a implantação de update sets/fix scripts em produção. Preenchido pelo agente da raia "Em Implantação". A execução da implantação em si é sempre humana: a pessoa implanta em produção e encerra o subitem da raia, e só então o orquestrador move o item para "Implantado" (ver ADR-0008).

**Update Set**:
Artefato do ServiceNow que empacota mudanças de configuração/código. Criado e aplicado pelo agente de Execução diretamente no ambiente DEV do ServiceNow, e depois promovido para QA; a promoção final para produção é sempre manual.

**Fix Script**:
Script do ServiceNow executado como parte de uma implantação. Mesma dinâmica do Update Set: criado/aplicado pelo agente em DEV/QA; execução em produção é manual.

**Ambiente (ServiceNow)**:
Instância ou escopo lógico do ServiceNow onde uma mudança existe em um dado momento: DEV (onde o agente de Execução desenvolve), QA (onde o agente QAI testa) ou PROD (produção, alcançada só por ação humana).

**Promoção**:
Ato de mover um Update Set/mudança de um ambiente do ServiceNow para o próximo (DEV → QA → PROD). As promoções DEV→QA são feitas por agente; a promoção para PROD é sempre humana.

**Squad**:
Time de desenvolvimento com seu próprio board no xClick. O piloto começa com 2 squads, cada uma com um board independente monitorado pelo orquestrador.

**Board**:
Instância do xClick pertencente a uma squad específica, contendo suas próprias raias e itens.

**Control plane**:
(Planejado, ainda não detalhado) Interface de observação/controle do próprio orquestrador — não confundir com o xClick, que é a interface de trabalho dos itens.

**Contrato de integração (agente)**:
Interface técnica, definida e mantida pelo squad_agentica, que qualquer agente especializado deve implementar para participar do fluxo — independente de framework/LLM/infra escolhidos por quem constrói o agente. Ver ADR-0002 e ADR-0003.

**Job**:
Unidade de trabalho que o orquestrador publica na fila de entrada de um agente, contendo o conteúdo já extraído do xClick necessário para aquela raia (conteúdo do item, seção "Para IA Ler", subbugs quando existirem).

**Dependência declarada**:
Referência a outro item, declarada com a convenção `Depende de: <ID>` (um ou mais IDs, separados por vírgula) dentro da seção "Para IA Ler". Caso raro. Considerada resolvida quando o item referenciado atinge a raia "Em Implantação". O orquestrador checa essa referência uma única vez, ao decidir iniciar a execução de um item saído do Backlog (não é reavaliada em raias posteriores); enquanto não resolvida, o item é pulado — sem consumir vaga do limite de WIP — e recebe um comentário automático no xClick sinalizando a espera, até a próxima verificação.
