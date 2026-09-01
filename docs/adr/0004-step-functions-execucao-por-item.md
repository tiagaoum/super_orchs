# Núcleo do orquestrador é uma execução do Step Functions por item

A lógica do orquestrador é determinística (ver ADR-0002), então em vez de um Lambda fazendo polling manual de cada raia com locks em DynamoDB, cada item que entra no fluxo (saindo do Backlog) ganha sua própria execução do AWS Step Functions (Standard), representando seu ciclo de vida completo através das raias. Um Lambda leve, disparado por EventBridge Scheduler, só detecta itens novos no Backlog e inicia uma execução por item.

Isso resolve de graça vários requisitos que já tínhamos: a própria execução funciona como lock de idempotência (evita reprocessar o mesmo item), o histórico de execução é o rastro de auditoria por item, e o timeout por raia é nativo do serviço. O custo é desprezível no volume do piloto (poucos centavos por item), e o histórico de execuções também serve de base para o control plane futuro.
