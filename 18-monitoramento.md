# Capítulo 18 — Monitoramento e Observabilidade

> Monitorar uma aplicação não significa apenas saber se ela está "no ar". O objetivo é entender **o que aconteceu**, **quando aconteceu**, **por que aconteceu** e **como resolver rapidamente**.

---

# Objetivos

Ao final deste capítulo você será capaz de:

* Entender a diferença entre monitoramento e observabilidade
* Trabalhar com métricas
* Trabalhar com logs
* Trabalhar com traces distribuídos
* Implementar Health Checks
* Utilizar Prometheus
* Utilizar Grafana
* Utilizar OpenTelemetry
* Monitorar aplicações .NET
* Utilizar Azure Monitor e Application Insights
* Criar dashboards
* Configurar alertas
* Monitorar Docker
* Monitorar Kubernetes
* Diagnosticar problemas em produção

---

# O que é Monitoramento?

Monitoramento consiste em acompanhar continuamente o estado de um sistema.

Exemplos:

* CPU
* Memória
* Disco
* Rede
* Disponibilidade
* Tempo de resposta
* Erros
* Uso de recursos

Pergunta principal:

```text
"O sistema está funcionando?"
```

---

# O que é Observabilidade?

Observabilidade é a capacidade de entender **por que** um problema ocorreu.

Pergunta principal:

```text
"Por que o sistema parou?"
```

Ela é baseada em três pilares:

```
Métricas

Logs

Traces
```

---

# Os 3 pilares

```
                 Aplicação

      ┌──────────┬──────────┬──────────┐

      ▼          ▼          ▼

   Métricas     Logs      Traces
```

---

# Métricas

São dados numéricos ao longo do tempo.

Exemplos:

```
CPU %

RAM

Requests por segundo

Latência

Quantidade de erros

Número de usuários
```

---

# Logs

São registros de eventos.

Exemplo:

```text
09:10:15 Login realizado

09:10:18 Pedido criado

09:10:20 Falha conexão SQL
```

---

# Traces

Mostram o caminho percorrido por uma requisição.

Exemplo:

```
API

↓

RabbitMQ

↓

Worker

↓

Banco

↓

Redis

↓

Resposta
```

---

# Golden Signals

Os quatro sinais mais importantes:

```
Latência

Tráfego

Erros

Saturação
```

---

# RED Method

Muito utilizada para APIs.

```
Rate

Errors

Duration
```

---

# USE Method

Muito utilizada para infraestrutura.

```
Utilization

Saturation

Errors
```

---

# Ferramentas Populares

```
Prometheus

Grafana

OpenTelemetry

Application Insights

Azure Monitor

Elastic Stack

Loki

Jaeger

Zipkin
```

---

# Health Checks

Health Checks verificam se a aplicação está saudável.

Exemplo:

```
GET /health
```

Resposta:

```json
{
  "status": "Healthy"
}
```

---

# Health Checks no .NET

Instalação:

```bash
dotnet add package Microsoft.AspNetCore.Diagnostics.HealthChecks
```

Program.cs

```csharp
builder.Services.AddHealthChecks();

app.MapHealthChecks("/health");
```

Teste:

```
http://localhost:5000/health
```

---

# Health Checks personalizados

```csharp
builder.Services.AddHealthChecks()
    .AddSqlServer(connectionString)
    .AddRedis(redisConnection)
    .AddMongoDb(mongoConnection);
```

---

# Health Check UI

Pacotes:

```bash
dotnet add package AspNetCore.HealthChecks.UI
```

Permite visualizar os estados em uma interface web.

---

# Logging

No .NET:

```csharp
private readonly ILogger<PedidoService> _logger;
```

Registrar informação:

```csharp
_logger.LogInformation("Pedido criado.");
```

Erro:

```csharp
_logger.LogError(ex, "Erro ao salvar pedido.");
```

Warning:

```csharp
_logger.LogWarning("Fila indisponível.");
```

---

# Níveis de Log

```
Trace

Debug

Information

Warning

Error

Critical
```

---

# Structured Logging

Evite:

```csharp
_logger.LogInformation("Pedido " + id);
```

Prefira:

```csharp
_logger.LogInformation(
    "Pedido {PedidoId} criado.",
    id);
```

---

# Serilog

Pacotes:

```bash
dotnet add package Serilog.AspNetCore
```

Configuração:

```csharp
builder.Host.UseSerilog();
```

---

# Logs em arquivo

```json
"Serilog": {

  "WriteTo": [

    {

      "Name": "File"

    }

  ]
}
```

---

# Logs JSON

Exemplo:

```json
{
  "Timestamp": "...",
  "Level": "Information",
  "Message": "Pedido criado",
  "PedidoId": 10
}
```

---

# Correlation ID

Permite acompanhar uma requisição entre vários serviços.

```
Cliente

↓

API

↓

RabbitMQ

↓

Worker

↓

Banco
```

Mesmo Correlation ID:

```
A1B2C3
```

---

# OpenTelemetry

Padrão aberto para observabilidade.

Coleta:

* Métricas
* Logs
* Traces

Fluxo:

```
Aplicação

↓

OpenTelemetry

↓

Collector

↓

Prometheus

↓

Grafana
```

---

# Configuração OpenTelemetry

Pacote:

```bash
dotnet add package OpenTelemetry.Extensions.Hosting
```

Exemplo:

```csharp
builder.Services.AddOpenTelemetry();
```

---

# Prometheus

Banco de métricas.

Modelo:

```
Prometheus

↓

Scraping

↓

API
```

Endpoint:

```
/metrics
```

---

# Métrica

```
http_requests_total
```

---

# Docker Compose

```yaml
services:

  prometheus:

    image: prom/prometheus

    ports:
      - "9090:9090"
```

---

# Prometheus.yml

```yaml
scrape_configs:

  - job_name: api

    static_configs:

      - targets:
        - api:8080
```

---

# Grafana

Ferramenta de dashboards.

```
Prometheus

↓

Grafana

↓

Dashboard
```

---

# Docker

```yaml
grafana:

  image: grafana/grafana

  ports:
    - "3000:3000"
```

---

# Dashboards

Exemplos:

```
CPU

RAM

Requests

Tempo resposta

Erros

Docker

Kubernetes
```

---

# Alertas

Exemplos:

```
CPU > 80%

RAM > 90%

Erro > 5%

API fora do ar

Disco cheio
```

---

# Docker

Listar uso:

```bash
docker stats
```

---

# Logs

```bash
docker logs api
```

Tempo real

```bash
docker logs -f api
```

---

# Kubernetes

Pods

```bash
kubectl get pods
```

---

Logs

```bash
kubectl logs api
```

---

Tempo real

```bash
kubectl logs -f api
```

---

Eventos

```bash
kubectl get events
```

---

Describe

```bash
kubectl describe pod api
```

---

# Application Insights

Serviço do Azure.

Permite:

* Logs
* Traces
* Métricas
* Dependências
* Exceções
* Performance

---

# Azure Monitor

Monitora:

* Máquinas Virtuais
* App Service
* Kubernetes
* Banco
* Storage
* Containers

---

# Dashboards importantes

Backend:

```
Requests

Erros

Latência

CPU

RAM

Banco

RabbitMQ

Redis
```

---

Docker:

```
Containers

CPU

RAM

Restart

Volumes
```

---

Kubernetes:

```
Pods

Nodes

Deployments

ReplicaSets

Namespaces

CPU

RAM
```

---

# RabbitMQ

Monitorar:

```
Filas

Consumers

Mensagens

Dead Letter

Ack

Unacked
```

Interface:

```
15672
```

---

# PostgreSQL

Monitorar:

```
Conexões

Locks

Queries

CPU

Disco
```

---

# Redis

Monitorar:

```
Memória

Hits

Miss

Evictions
```

---

# MongoDB

Monitorar:

```
Conexões

Queries

Replica

Memória
```

---

# Latência

Exemplo:

```
API

20 ms

↓

Banco

15 ms

↓

RabbitMQ

5 ms

↓

Total

40 ms
```

---

# SLI

Indicador.

Exemplo:

```
Disponibilidade

99,9%
```

---

# SLO

Objetivo.

```
Disponibilidade

99,95%
```

---

# SLA

Contrato.

```
99,9%
```

---

# Observabilidade em Microsserviços

```
API Gateway

↓

Pedido

↓

Pagamento

↓

Estoque

↓

Notificação
```

Todos compartilham:

```
Trace ID

Correlation ID
```

---

# Diagnóstico

Perguntas:

```
Quando ocorreu?

Qual serviço?

Qual usuário?

Qual exceção?

Qual banco?

Qual fila?

Qual container?
```

---

# Cenário Real

Usuário reclama:

```
Sistema lento.
```

Verificar:

```
Grafana

↓

CPU

↓

RAM

↓

Requests

↓

Logs

↓

Trace

↓

Banco
```

---

# Laboratório

Criar:

```
API .NET

↓

Health Check

↓

Prometheus

↓

Grafana
```

Docker Compose:

```yaml
services:

  api:

    build: .

  prometheus:

    image: prom/prometheus

  grafana:

    image: grafana/grafana
```

---

# Troubleshooting

## API lenta

Verificar:

```
CPU

RAM

Banco

Logs
```

---

## Muitos erros

Verificar:

```
Logs

Exceptions

RabbitMQ

Banco
```

---

## Container reiniciando

```bash
docker logs CONTAINER
```

---

## Pod reiniciando

```bash
kubectl describe pod
```

---

## Falha RabbitMQ

Verificar:

```
Consumers

Mensagens

Dead Letter
```

---

# Boas Práticas

✅ Nunca utilize apenas logs.

---

✅ Monitore métricas.

---

✅ Utilize Health Checks.

---

✅ Utilize Correlation ID.

---

✅ Utilize Structured Logging.

---

✅ Centralize logs.

---

✅ Crie dashboards.

---

✅ Configure alertas.

---

✅ Monitore infraestrutura e aplicação.

---

✅ Utilize OpenTelemetry sempre que possível.

---

# Cheat Sheet

```bash
# Docker
docker stats

docker logs api

docker logs -f api

# Kubernetes
kubectl get pods

kubectl logs api

kubectl logs -f api

kubectl describe pod api

kubectl get events

# Health
GET /health

# Prometheus
http://localhost:9090

# Grafana
http://localhost:3000

# RabbitMQ
http://localhost:15672
```

---

# Resumo

Neste capítulo você aprendeu:

* Monitoramento
* Observabilidade
* Métricas
* Logs
* Traces
* Health Checks
* Structured Logging
* Serilog
* OpenTelemetry
* Prometheus
* Grafana
* Azure Monitor
* Application Insights
* Docker
* Kubernetes
* Dashboards
* Alertas
* Diagnóstico de problemas

---