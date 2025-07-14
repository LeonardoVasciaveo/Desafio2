# Post-Mortem: Incidente de Latência na payment-api

---

## Contexto

Em **13/07/2025 às 08:03**, um alerta foi disparado no **Datadog** indicando que a **latência média da `payment-api`** estava acima de 2 segundos por mais de 10 minutos.

Esse serviço é crítico para o processamento de pagamentos no e-commerce da empresa.

---

## Linha do Tempo

| Horário | Evento |
|--------|--------|
| 08:03  | Alerta crítico emitido no Datadog |
| 08:05  | Início da investigação pelo time de SRE |
| 08:10  | Identificados timeouts em conexões com o banco RDS |
| 08:15  | Escalonamento das réplicas de 6 para 10 |
| 08:20  | DBA confirma esgotamento de conexões simultâneas no RDS |
| 08:30  | Realizado rollout restart do deployment `payment-api` |
| 08:40  | Latência normalizada |
| 08:50  | Comunicação de resolução enviada no canal de incidentes |

---

## Causa Raiz

- O **pool de conexões com o RDS foi esgotado**, gerando timeouts na aplicação.
- O aumento inesperado de requisições simultâneas não foi absorvido pelo número atual de réplicas.

---

## Impacto

- Serviço de pagamentos com falhas e lentidão durante aproximadamente **30 minutos**.
- Parte das transações foi **cancelada automaticamente por timeout**.
- Perda de receita estimada não foi mensurada no momento do post-mortem.

---

## Lições Aprendidas

- Conexões com banco de dados são um **gargalo crítico** e devem ser monitoradas com mais granularidade.
- Alertas proativos para **uso de conexões e saturação de pool** estavam ausentes.
- SREs responderam rapidamente com boas práticas de mitigação.

---

## Ações Preventivas

- [x] Criar alertas específicos para **erros de conexão** com o RDS.
- [x] Monitorar `DatabaseConnections` e `DatabaseConnectionsLimit` via CloudWatch e Datadog.
- [x] Definir **SLOs para latência e disponibilidade da `payment-api`**.
- [x] Adicionar métricas customizadas na aplicação via OpenTelemetry.
- [ ] Revisar capacidade do RDS e escalabilidade horizontal.
- [ ] Atualizar documentação com **playbooks de resposta a incidentes**.

---

## Conclusão

O incidente foi **rapidamente mitigado** com o aumento das réplicas e reinício do serviço.  
Foram identificados **pontos cegos na observabilidade** e no controle de conexões com o banco, que já estão sendo tratados com ações estruturais.

Esse aprendizado será compartilhado com os times de SRE e desenvolvimento para **evitar reincidência**.

---

*Post-mortem elaborado de forma blameless, com foco em melhoria contínua.*
