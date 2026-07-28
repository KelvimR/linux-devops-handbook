# Capítulo 15 — Kubernetes

> O **Kubernetes (K8s)** é a principal plataforma de orquestração de containers do mercado. Ele automatiza o deploy, escalabilidade, recuperação de falhas e gerenciamento de aplicações distribuídas.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender a arquitetura do Kubernetes
- Trabalhar com Pods
- Criar Deployments
- Expor aplicações utilizando Services
- Trabalhar com ConfigMaps e Secrets
- Utilizar Volumes Persistentes
- Realizar Rolling Updates
- Escalar aplicações
- Fazer troubleshooting em clusters
- Implantar aplicações .NET em Kubernetes

---

# O que é Kubernetes?

Kubernetes é uma plataforma open source criada originalmente pelo Google para orquestrar containers.

Ele resolve problemas como:

- Alta disponibilidade
- Escalabilidade
- Balanceamento de carga
- Auto recuperação
- Atualizações sem downtime
- Gerenciamento de configuração

---

# Arquitetura

```
                 Kubernetes Cluster
+---------------------------------------------------+

             Control Plane

+-----------------------------------------------+
| API Server                                    |
| Scheduler                                     |
| Controller Manager                            |
| ETCD                                          |
+-----------------------------------------------+

        │

        ▼

+----------------+     +----------------+
| Worker Node 1  |     | Worker Node 2  |
|----------------|     |----------------|
| kubelet        |     | kubelet        |
| kube-proxy     |     | kube-proxy     |
| Pods           |     | Pods           |
+----------------+     +----------------+
```

---

# Componentes do Control Plane

## API Server

Ponto de entrada do cluster.

Todo comando `kubectl` passa por ele.

---

## ETCD

Banco de dados distribuído.

Armazena:

- Deployments
- Pods
- Secrets
- ConfigMaps
- Services

---

## Scheduler

Decide em qual Worker Node cada Pod será executado.

---

## Controller Manager

Garante que o estado atual seja igual ao estado desejado.

Exemplo:

```
Desejado:

3 Pods

Atual:

2 Pods

↓

Cria automaticamente o terceiro.
```

---

# Worker Node

Cada Worker possui:

```
kubelet

kube-proxy

Container Runtime

Pods
```

---

# Container Runtime

Pode ser:

- containerd
- CRI-O

O Docker já não é mais utilizado diretamente como runtime padrão.

---

# Kubectl

Ferramenta oficial para administrar clusters.

Verificar versão

```bash
kubectl version
```

---

Informações do cluster

```bash
kubectl cluster-info
```

---

Nós

```bash
kubectl get nodes
```

---

Detalhes

```bash
kubectl describe node NOME
```

---

# Namespaces

Listar

```bash
kubectl get ns
```

Criar

```bash
kubectl create namespace desenvolvimento
```

Executar

```bash
kubectl get pods -n desenvolvimento
```

---

# Pods

Menor unidade do Kubernetes.

Um Pod pode conter:

```
1 container

ou

vários containers
```

---

Criar

```bash
kubectl run nginx \
--image=nginx
```

---

Listar

```bash
kubectl get pods
```

Mais detalhes

```bash
kubectl get pods -o wide
```

---

Descrever

```bash
kubectl describe pod nginx
```

---

Excluir

```bash
kubectl delete pod nginx
```

---

# YAML

Exemplo

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx

spec:

  containers:

  - name: nginx

    image: nginx
```

Aplicar

```bash
kubectl apply -f pod.yaml
```

---

# Deployments

Controlam Pods.

Exemplo

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:

  name: api

spec:

  replicas: 3

  selector:

    matchLabels:

      app: api

  template:

    metadata:

      labels:

        app: api

    spec:

      containers:

      - name: api

        image: minha-api:v1
```

---

Aplicar

```bash
kubectl apply -f deployment.yaml
```

---

Listar

```bash
kubectl get deployments
```

---

Escalar

```bash
kubectl scale deployment api \
--replicas=5
```

---

# ReplicaSet

Responsável por manter a quantidade de Pods.

Normalmente é gerenciado pelo Deployment.

---

# Services

Expondo aplicações.

Tipos:

- ClusterIP
- NodePort
- LoadBalancer
- ExternalName

---

ClusterIP

Apenas interno.

```yaml
type: ClusterIP
```

---

NodePort

Exposto em todos os Workers.

```yaml
type: NodePort
```

---

LoadBalancer

Muito utilizado na Cloud.

```yaml
type: LoadBalancer
```

---

Criar

```bash
kubectl expose deployment api \
--port=80
```

---

Listar

```bash
kubectl get svc
```

---

# ConfigMaps

Guardar configurações.

Criar

```bash
kubectl create configmap api-config \
--from-literal=AMBIENTE=DEV
```

---

Listar

```bash
kubectl get configmaps
```

---

# Secrets

Guardar informações sensíveis.

Criar

```bash
kubectl create secret generic api-secret \
--from-literal=SENHA=123
```

---

Listar

```bash
kubectl get secrets
```

---

Descrever

```bash
kubectl describe secret api-secret
```

---

# Persistent Volume

Persistência de dados.

```
PVC

↓

PV

↓

Storage
```

---

Listar

```bash
kubectl get pv
```

```bash
kubectl get pvc
```

---

# Ingress

Gerencia acesso HTTP.

Exemplo

```
api.meusite.com

↓

API
```

---

Listar

```bash
kubectl get ingress
```

---

# Logs

Pod

```bash
kubectl logs api
```

Tempo real

```bash
kubectl logs -f api
```

Últimas linhas

```bash
kubectl logs --tail=100 api
```

---

Container específico

```bash
kubectl logs api \
-c nginx
```

---

# Executar comandos

```bash
kubectl exec -it api \
-- bash
```

Caso não exista bash

```bash
kubectl exec -it api \
-- sh
```

---

# Port Forward

```bash
kubectl port-forward pod/api \
8080:80
```

Agora acessar

```
localhost:8080
```

---

# Rollout

Atualizar imagem

```bash
kubectl set image deployment/api \
api=minha-api:v2
```

---

Ver andamento

```bash
kubectl rollout status deployment/api
```

---

Histórico

```bash
kubectl rollout history deployment/api
```

---

Rollback

```bash
kubectl rollout undo deployment/api
```

---

# Recursos

CPU

```yaml
resources:

  requests:

    cpu: "250m"

    memory: "256Mi"

  limits:

    cpu: "500m"

    memory: "512Mi"
```

---

# Autoscaling

Criar

```bash
kubectl autoscale deployment api \
--cpu-percent=70 \
--min=2 \
--max=10
```

---

# Troubleshooting

Ver tudo

```bash
kubectl get all
```

---

Eventos

```bash
kubectl get events
```

---

Descrever Pod

```bash
kubectl describe pod api
```

---

Logs

```bash
kubectl logs api
```

---

Entrar

```bash
kubectl exec -it api -- bash
```

---

# Cenários Reais

## API não sobe

```bash
kubectl describe pod api
```

Depois

```bash
kubectl logs api
```

---

## CrashLoopBackOff

Verificar

```bash
kubectl logs api
```

Normalmente:

- aplicação falhando
- variável ausente
- banco indisponível

---

## ImagePullBackOff

Verificar:

- nome da imagem
- autenticação
- Docker Registry

---

## Pod Pending

Verificar

```bash
kubectl describe pod
```

Pode ser:

- falta de memória
- falta de CPU
- PVC

---

## Atualizar aplicação

```bash
kubectl set image deployment/api \
api=minha-api:v2
```

---

# Laboratório

Criar Deployment

```bash
kubectl create deployment nginx \
--image=nginx
```

Escalar

```bash
kubectl scale deployment nginx \
--replicas=3
```

Expor

```bash
kubectl expose deployment nginx \
--type=NodePort \
--port=80
```

Verificar

```bash
kubectl get all
```

Logs

```bash
kubectl logs deployment/nginx
```

Excluir

```bash
kubectl delete deployment nginx

kubectl delete service nginx
```

---

# Problemas Comuns

## Pod Pending

Verifique:

```bash
kubectl describe pod
```

---

## CrashLoopBackOff

Verifique:

```bash
kubectl logs POD
```

---

## ImagePullBackOff

Verifique:

- imagem
- registry
- secret

---

## Connection refused

Verifique:

```bash
kubectl get svc
```

---

## ConfigMap não atualiza

Reinicie os Pods:

```bash
kubectl rollout restart deployment api
```

---

# Boas Práticas

✅ Utilize Deployments em vez de Pods isolados.

---

✅ Defina `requests` e `limits` de CPU e memória.

---

✅ Utilize ConfigMaps para configurações.

---

✅ Utilize Secrets para dados sensíveis.

---

✅ Faça Rolling Updates.

---

✅ Nunca utilize a tag `latest` em produção.

Exemplo:

```yaml
image: minha-api:1.0.3
```

---

✅ Organize aplicações por Namespace.

---

# Cheat Sheet

```bash
# Cluster
kubectl cluster-info

# Nodes
kubectl get nodes

# Pods
kubectl get pods
kubectl describe pod POD
kubectl delete pod POD

# Deployments
kubectl get deployments
kubectl scale deployment api --replicas=3

# Services
kubectl get svc

# Namespaces
kubectl get ns

# Logs
kubectl logs POD
kubectl logs -f POD

# Exec
kubectl exec -it POD -- bash

# Apply
kubectl apply -f arquivo.yaml

# Delete
kubectl delete -f arquivo.yaml

# Rollout
kubectl rollout status deployment/api
kubectl rollout undo deployment/api

# Eventos
kubectl get events

# Tudo
kubectl get all
```

---

# Resumo

Neste capítulo você aprendeu:

- Arquitetura do Kubernetes
- Control Plane e Worker Nodes
- Pods
- Deployments
- ReplicaSets
- Services
- ConfigMaps
- Secrets
- Persistent Volumes
- Ingress
- Rolling Updates
- Autoscaling
- Troubleshooting
- Boas práticas para ambientes de produção

---

# Próximo capítulo

No **Capítulo 16 — CI/CD com GitHub Actions**, aprenderemos:

- O que é CI/CD
- Estrutura dos Workflows
- GitHub Actions
- Jobs e Steps
- Secrets
- Cache
- Build de aplicações .NET
- Testes automatizados
- Docker Build e Push
- Deploy automatizado para servidores Linux e Kubernetes
- Integração com Azure e Docker Hub