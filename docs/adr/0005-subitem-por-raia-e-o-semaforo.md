# Subitem aberto em cada raia é o semáforo de avanço

Ao mover um item para uma raia, o orquestrador abre nele um subitem (e, em "Testado QA", carrega junto os subbugs que o QAI encontrou) e passa a tratar o status desses filhos como o único sinal de avanço: enquanto houver um Pendente, o item fica parado; quando todos estiverem Concluído/Encerrado ou Cancelado, o orquestrador move o item para a próxima raia. Quem conclui muda conforme a raia — nas raias de revisão humana (ver ADR-0006) é a pessoa responsável, nas demais é o próprio agente ao terminar com sucesso.

Isso dá um mecanismo único para gates humanos e automáticos, e o coloca dentro do xClick: a pessoa não precisa de ferramenta nova para liberar o fluxo — encerrar o subitem no board que ela já usa é o aceite. Como o subitem também carrega o conteúdo produzido pelo agente, revisão e liberação acontecem no mesmo lugar, e o registro de quem liberou o quê fica no próprio item, o que importa no contexto de change management/GMUD.
