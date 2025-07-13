# 🛠️ Melhorias Preventivas

---

## 🔍 Observabilidade

- Habilitar monitoramento detalhado de conexões com RDS (timeouts, pool).
- Adicionar SLOs para:
  - Tempo de resposta médio
  - Erros de conexão com banco

---

## 🧱 Resiliência

- Implementar retry para falhas em conexões com o banco.
- Ajustar timeouts de forma balanceada.
- Fallback controlado para pedidos em processamento.

---

## 📊 Alertas Proativos

- Criar alertas para:
  - Erros de conexão com RDS
  - Uso excessivo de conexões no banco (CloudWatch)

---

## 📚 Documentação

- Criar **playbook** de troubleshooting para incidentes de latência.
- Mapear e documentar todas as dependências críticas da `payment-api`.
