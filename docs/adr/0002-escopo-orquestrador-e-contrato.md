# Escopo do projeto é o orquestrador e o contrato de integração, não os agentes

O squad_autonoma constrói e opera apenas o orquestrador e o contrato de integração orquestrador↔agente. A implementação de cada agente especializado (Refinamento, Execução, QAI, GMUD etc.) é responsabilidade de times externos ao projeto, que podem escolher sua própria stack e provedor de LLM.

Consequência direta: o orquestrador não faz nenhuma chamada de LLM própria — sua lógica é determinística (ler xClick, aplicar a regra do semáforo, montar/despachar jobs, escrever resultados de volta). Decisões de framework de agente e procurement de LLM (Bedrock, proxy interno, etc.) ficam fora do escopo deste projeto e são de cada time dono de agente.
