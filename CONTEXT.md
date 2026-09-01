# Squad Autônoma

Orquestração de agentes de IA especializados que executam o ciclo de vida de desenvolvimento de software (SDLC) de itens de trabalho no xClick, com humano no loop para validação de cada etapa.

## Language

**xClick**:
Board ágil interno da empresa (tipo Jira simplificado) usado para gerenciar o SDLC. Expõe API e um servidor MCP para consulta e movimentação de itens.

**Raia**:
Uma coluna/status de um item dentro do xClick, representando um estágio do SDLC. Sequência fixa: Backlog → Em Refinamento → Execução → Peer Review → Pronto para QA → Em Testes QA → Testado QA → Em Implantação → Implementado → Ativado.
_Avoid_: Status, coluna, etapa (usar "raia" para manter consistência com o vocabulário do usuário)

**Item**:
Unidade de trabalho rastreada no xClick. Os tipos principais são História e Bug; existem outros tipos e subtipos.
_Avoid_: Card, ticket, work item

**Subitem**:
Item filho criado pelo orquestrador/agente e anexado a uma História ou Bug, contendo o conteúdo produzido por IA para uma raia específica (ex.: resultado do refinamento, update sets, resultado de QA). Possui status próprio: Pendente, Concluído ou Cancelado.

**Subbug**:
Bug filho criado durante "Em Testes QA" quando um defeito é encontrado; sua existência em aberto força o item pai a retornar para "Em Refinamento".

**Orquestrador**:
Componente central que lê periodicamente as raias do xClick, obtém o conteúdo dos itens, delega para o agente especializado correspondente à raia atual, e movimenta os itens entre raias conforme a regra do semáforo de subitem.

**Agente especializado**:
Agente de IA responsável por uma etapa específica do SDLC (ex.: refinamento, execução/desenvolvimento, QAI, implantação/GMUD).

**Semáforo de subitem**:
Regra de governança que decide se um item pode avançar de raia: avança somente quando existir ao menos um subitem Concluído e todos os demais estiverem Cancelado. Qualquer subitem Pendente bloqueia o avanço, mesmo havendo outros Concluído. Concluído normalmente representa validação humana, mas em raias automatizáveis (ex.: Pronto para QA) pode ser setado pelo próprio agente ao concluir com sucesso — o semáforo em si não pressupõe origem humana, só que o gate foi satisfeito.
_Avoid_: Flag, gate (usar "semáforo" — termo do usuário)

**"Para IA Ler"**:
Seção dedicada dentro de um item do xClick que contém o conteúdo destinado a ser consumido pelos agentes de IA.

**QAI**:
Agente especializado responsável pelos testes automatizados/assistidos por IA na raia "Em Testes QA".

**GMUD** (Gestão de Mudança):
Documento de change management necessário para autorizar a implantação de update sets/fix scripts em produção. Preenchido pelo agente da raia "Em Implantação". A execução da implantação em si é sempre humana.

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
Interface técnica, definida e mantida pelo squad_autonoma, que qualquer agente especializado deve implementar para participar do fluxo — independente de framework/LLM/infra escolhidos pelo time dono do agente. Ver ADR-0002 e ADR-0003.

**Job**:
Unidade de trabalho que o orquestrador publica na fila de entrada de um agente, contendo o conteúdo já extraído do xClick necessário para aquela raia (conteúdo do item, seção "Para IA Ler", subbugs quando existirem).

**Dependência declarada**:
Referência a outro item, declarada com a convenção `Depende de: <ID>` (um ou mais IDs, separados por vírgula) dentro da seção "Para IA Ler". Caso raro. Considerada resolvida quando o item referenciado atinge a raia "Em Implantação". O orquestrador checa essa referência uma única vez, ao decidir iniciar a execução de um item saído do Backlog (não é reavaliada em raias posteriores); enquanto não resolvida, o item é pulado — sem consumir vaga do limite de WIP — e recebe um comentário automático no xClick sinalizando a espera, até a próxima verificação.
