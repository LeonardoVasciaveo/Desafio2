# Diagnóstico - Incidente Latência API Payment

##  Sintomas observados

- Alerta no Datadog: Latência média > 2s por 10min
- Logs: Timeout ao acessar banco RDS
- ArgoCD: Aplicação `Healthy` e `Synced`

##  Ferramentas utilizadas

- Datadog
- ArgoCD
- kubectl
- AWS CloudWatch
- RDS Console

##  Comandos executados

```bash
kubectl get pods -n payment
kubectl logs -f -n payment deploy/payment-api
kubectl describe pod <pod-name> -n payment
kubectl top pod -n payment
