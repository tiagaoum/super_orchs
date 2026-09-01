# Integração orquestrador↔agente é assíncrona, baseada em fila

Agentes podem levar mais de 15-20 minutos para concluir uma tarefa e têm infraestrutura heterogênea (ver ADR-0002), então uma chamada síncrona request/response não é viável de forma genérica. O orquestrador publica cada job num único tópico SNS de entrada; cada time de agente assina esse tópico com uma fila SQS própria, filtrada pelo atributo `raia`, então cada time só recebe os jobs que são dele. O resultado volta por uma única fila de saída compartilhada, já que o orquestrador é o único consumidor desse lado.

Isso desacopla o orquestrador da tecnologia e da duração de cada agente, mantém um único ponto de publicação (simplicidade pedida para o MVP) e evita que um time consuma por engano o job de outro.
