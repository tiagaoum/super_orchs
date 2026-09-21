# "Em Implantação" tem agente, mas quem libera o avanço é uma pessoa

A raia "Em Implantação" tem agente — é ele que monta a GMUD a partir dos update sets/fix scripts do item. Ainda assim, o semáforo dessa raia é encerrado por uma pessoa, não pelo agente: sair de "Em Implantação" significa que a mudança foi para produção, e implantar em produção continua sendo ato humano. A pessoa implanta, encerra o subitem, e só então o orquestrador move o item para "Implementado" — onde a execução daquele item termina.

É a única raia em que agente e revisão humana convivem, e é de propósito: o trabalho repetitivo (preencher a GMUD) é o que queremos tirar da mão primeiro, enquanto o ato irreversível (tocar produção) fica com quem responde por ele. É uma escolha de v1 — "nesse momento faremos assim" — revisitável quando o piloto der confiança sobre o que o agente entrega aqui.
