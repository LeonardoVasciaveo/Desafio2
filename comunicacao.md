# 📢 Comunicação do Incidente

**Objetivo:** Garantir alinhamento com stakeholders durante a investigação e resolução do incidente.

---

## 🔔 Notificação Inicial

- **Canal:** Slack – `#incident-payment`
- **Mensagem:**

> 🚨 *[ALERTA]* Latência alta detectada no serviço `payment-api` em produção.  
> Time de SRE está investigando. Impacto: processamentos de pagamento estão lentos ou falhando.

---

## 🔄 Atualizações Periódicas

- **Frequência:** A cada 15 minutos ou após mudanças relevantes.
- **Conteúdo:**
  - Estado atual da investigação
  - Hipóteses em teste
  - Ações corretivas aplicadas
  - Estimativa de normalização

---

## ✅ Comunicação de Normalização

> ✅ *[RESOLVIDO]* Incidente no `payment-api` foi mitigado.  
> A latência foi normalizada. Um post-mortem será compartilhado em breve.
