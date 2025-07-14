# Incidente - Alta Latência no Serviço `payment-api`

##  Contexto

No dia **13/07/2025**, às **08h03**, um alerta crítico foi disparado no **Datadog**:

> **ALERTA:** Latência média da API acima de 2 segundos por 10 minutos  
> **Ambiente:** Produção  
> **Cluster:** EKS-prod  
> **Namespace:** payment  
> **Serviço:** payment-api

Este serviço é responsável pelo processamento de pagamentos em tempo real. A sua indisponibilidade impacta diretamente o faturamento da empresa.

---

##  Ações Imediatas de Mitigação

### 1. Escalonamento Temporário

```bash
kubectl scale deployment payment-api -n payment --replicas=10
```

Distribuir a carga entre mais réplicas para reduzir a latência enquanto o diagnóstico é realizado.

---

### 2. Diagnóstico de Recursos

```bash
kubectl top pod -n payment
```

Verifica o consumo de CPU e memória dos pods.

```bash
kubectl describe pod <pod-name> -n payment
```

Mostra eventos, falhas de probes, erros de container ou throttling.

---

### 3. Reinício Controlado do Deployment

```bash
kubectl rollout restart deployment payment-api -n payment
```

Renova as conexões com dependências externas como o banco de dados, elimina pods travados ou em estado inconsistente.

---

### 4. Verificação do Banco de Dados (RDS)

- Checar número de conexões ativas.
- Validar tempo de resposta.
- Conferir limites de conexão da aplicação e do banco.
- Verificar métricas de saturação via CloudWatch ou Datadog.

---

### 5. Observabilidade

- Validar dashboards no Datadog.
- Confirmar coleta de métricas de latência, erros 5xx, uso de conexões, CPU e memória.
- Conferir instrumentação com OpenTelemetry.

---

### 6. Comunicação do Incidente

- Atualizações a cada 10 minutos no canal de incidentes.
- Registro de status atual, ações tomadas e próximos passos.
- Comunicação clara para stakeholders técnicos e não técnicos.

---

##  Linha do Tempo

| Horário | Evento |
|--------|--------|
| 08:03  | Alerta crítico de latência no Datadog |
| 08:05  | Início da investigação pelo time de SRE |
| 08:10  | Identificação de timeouts com RDS nos logs |
| 08:15  | Escalonamento das réplicas para 10 |
| 08:20  | Time de banco confirma esgotamento de conexões no RDS |
| 08:30  | Reinício do deployment `payment-api` |
| 08:40  | Latência normalizada |
| 08:50  | Comunicação de resolução enviada |

---

##  Causa Raiz

- Esgotamento do **pool de conexões com o RDS**, causado por:
  - Aumento abrupto de requisições simultâneas.
  - Limites inadequados de conexões no backend e/ou banco.
  - Falta de escalonamento automático para esse cenário.

---

##  Impacto

- Interrupção parcial do serviço por cerca de **30 minutos**.
- Perda de transações devido a timeouts no processamento de pagamentos.

---

##  Lições Aprendidas

- Ausência de alertas para saturação do pool de conexões.
- Falta de visibilidade detalhada da dependência com o banco.
- HPA não configurado para responder automaticamente à sobrecarga.
- Playbooks de resposta a incidentes não estavam completos.

---

##  Ações Preventivas

- **Monitoramento:**
  - Criar alertas no Datadog para:
    - Uso de pool de conexões
    - Tempo médio de resposta
    - Taxa de erros HTTP 5xx

- **SLOs/SLIs:**
  - Definir SLOs para:
    - Latência da `payment-api`
    - Conectividade com RDS

- **Autoescalabilidade:**
  - Implementar **Horizontal Pod Autoscaler (HPA)** baseado em CPU, QPS e conexões.

- **Capacidade e tuning:**
  - Reavaliar limites de conexão simultânea no banco e na aplicação.

- **Documentação:**
  - Criar playbooks claros de resposta a incidentes.
  - Incluir checklists e fluxos de comunicação para incidentes similares.

---

##  Conclusão

O incidente foi mitigado com ações rápidas de escalonamento e reinício do serviço.  
A normalização ocorreu em aproximadamente 40 minutos após o disparo do alerta.  

Já estão em andamento melhorias estruturais para aumentar a resiliência, visibilidade e automação da resposta a incidentes na plataforma.
