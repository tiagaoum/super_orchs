# Fluxo é linear na v1: um item não volta para uma raia anterior

Um item percorre as raias sempre para frente. Em particular, subbug aberto em "Em Testes QA" não devolve mais a história para "Em Refinamento": ele apenas segura o avanço em "Testado QA" até que o QA ajuste o que for necessário e encerre o subbug, como qualquer outro semáforo (ver ADR-0005).

Retorno de raia é a parte do fluxo com mais casos de borda — o que fazer com o update set já promovido, com o subitem da raia de destino, com a execução do Step Functions em andamento, com a contagem de WIP — e nenhum deles é necessário para provar a tese da v1, que é o orquestrador conduzir um item de ponta a ponta com revisão humana nos gates certos. Mantemos o avanço estritamente linear agora e revisitamos retorno quando o piloto mostrar com que frequência ele é de fato preciso.
