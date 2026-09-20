# Orquestrador centraliza toda comunicação com o xClick

Vários agentes especializados, tocados por frentes de desenvolvimento diferentes (ver ADR-0002), participam do fluxo de uma história/bug. Decidimos que só o orquestrador acessa o xClick (via MCP, chamado programaticamente pelo orquestrador) — os agentes recebem conteúdo já extraído e devolvem conteúdo puro, sem tocar o xClick diretamente. Abrir o subitem de cada raia (ADR-0005) também é ato do orquestrador, nunca do agente.

Isso centraliza autenticação/credenciais do xClick num único componente, cria um ponto único de auditoria (relevante dado o contexto de change management/GMUD) e mantém os agentes desacoplados dos detalhes de integração do board — o que importa ainda mais porque cada agente pode ser implementado com uma stack diferente.
