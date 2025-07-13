# 🚑 Ações Imediatas de Mitigação

---

## 📈 Escalonamento

Aumentar as réplicas da `payment-api` temporariamente:

```bash
kubectl scale deploy payment-api -n payment --replicas=10

📉 Diagnóstico de Recursos
Verificar uso de CPU/memória e throttling:

bash
Copiar
Editar
kubectl top pod -n payment
🔁 Reinício dos Pods
Forçar nova tentativa de conexão:

bash
Copiar
Editar
kubectl rollout restart deploy payment-api -n payment


---

### ✅ `postmortem.md`

```md
# 📝 Post-Mortem: Latência na payment-api

---

## 📅 Contexto

No dia **13/07/2025**, às 08h03, um alerta de **latência > 2s** foi emitido para o serviço `payment-api`.  
Esse serviço é responsável pelo processamento de pagamentos em tempo real.

---

## 🕒 Linha do Tempo

| Horário | Evento |
|--------|--------|
| 08:03  | Alerta crítico no Datadog |
| 08:05  | Início da investigação pelo time de SRE |
| 08:10  | Identificação de timeout em conexões com o RDS |
| 08:15  | Escalonamento de réplicas para mitigar |
| 08:20  | DBA confirma uso total de conexões no RDS |
| 08:30  | Restart do deployment `payment-api` |
| 08:40  | Latência normalizada |
| 08:50  | Comunicação de resolução enviada |

---

## 🧨 Causa Raiz

- Esgotamento do **pool de conexões** com o banco de dados RDS.
- Aumento súbito de requisições simultâneas, além da capacidade atual.

---

## 🔥 Impacto

- Falhas e lentidão no processamento de pagamentos por aproximadamente 30 minutos.
- Parte das transações foi perdida por timeout.

---

## 📚 Lições Aprendidas

- Falta de visibilidade detalhada nas conexões com banco.
- Ausência de alerta para saturação de conexões.

---

## ✅ Ações Preventivas

- Adição de alertas no Datadog para uso de pool.
- Criação de SLOs para tempo de resposta e conectividade com RDS.
- Documentação de playbooks e dependências críticas.

---

## ✅ Conclusão

Incidente mitigado rapidamente com aumento de réplicas e reinício do serviço.  
Melhorias em andamento para garantir resiliência e visibilidade proativa.
