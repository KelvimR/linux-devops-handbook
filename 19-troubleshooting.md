# Capítulo 19 — Troubleshooting no Linux e DevOps

> Troubleshooting é o processo de identificar, analisar e corrigir problemas em sistemas, aplicações, redes, containers e serviços. Um bom diagnóstico evita tentativas aleatórias e reduz o tempo de indisponibilidade.

---

# Objetivos

Ao final deste capítulo você será capaz de:

* Aplicar uma metodologia de troubleshooting
* Identificar problemas de CPU, memória e disco
* Diagnosticar processos travados
* Investigar falhas de rede e DNS
* Resolver problemas com portas
* Analisar serviços do systemd
* Investigar logs
* Diagnosticar containers Docker
* Resolver problemas no Docker Compose
* Investigar falhas no Kubernetes
* Diagnosticar aplicações .NET
* Analisar bancos de dados, Redis e RabbitMQ
* Coletar evidências antes de alterar o ambiente
* Criar um relatório de incidente

---

# O que é Troubleshooting?

Troubleshooting é uma abordagem organizada para resolver problemas.

O objetivo não é apenas fazer o sistema voltar a funcionar.

Também é necessário descobrir:

* O que aconteceu
* Quando aconteceu
* Qual componente falhou
* Qual foi a causa
* Qual foi o impacto
* Como evitar que aconteça novamente

---

# Sintoma, problema e causa raiz

Esses conceitos não são iguais.

## Sintoma

É o que o usuário ou o monitoramento percebe.

Exemplos:

```text
A API está lenta.

O site não abre.

O container reinicia.

O banco não conecta.
```

---

## Problema

É a falha técnica encontrada.

Exemplos:

```text
CPU em 100%.

Porta 8080 ocupada.

Disco sem espaço.

Serviço PostgreSQL parado.
```

---

## Causa raiz

É o motivo original do problema.

Exemplos:

```text
Consulta sem índice.

Loop infinito na aplicação.

Logs sem rotação.

Configuração incorreta no deploy.
```

---

# Metodologia de troubleshooting

Uma sequência recomendada:

```text
1. Identificar o sintoma
2. Confirmar o problema
3. Determinar o impacto
4. Coletar evidências
5. Formular hipóteses
6. Testar uma hipótese por vez
7. Aplicar a correção
8. Validar o resultado
9. Documentar
10. Prevenir recorrência
```

---

# Regra principal

> Não altere vários componentes ao mesmo tempo.

Caso contrário, será difícil saber qual ação resolveu ou piorou o problema.

---

# Perguntas iniciais

Antes de executar comandos, pergunte:

```text
O problema afeta todos os usuários?

Quando começou?

Houve deploy recente?

Houve mudança de configuração?

O problema é constante ou intermitente?

Qual ambiente está afetado?

Existe mensagem de erro?

O serviço funcionava anteriormente?
```

---

# Fluxo de diagnóstico

```text
Usuário relata problema
         │
         ▼
Confirmar o sintoma
         │
         ▼
Verificar disponibilidade
         │
         ▼
Verificar logs
         │
         ▼
Verificar recursos
         │
         ▼
Verificar rede
         │
         ▼
Verificar dependências
         │
         ▼
Aplicar correção
         │
         ▼
Validar
```

---

# Coletando informações do sistema

## Distribuição Linux

```bash
cat /etc/os-release
```

---

## Kernel

```bash
uname -a
```

Ou:

```bash
uname -r
```

---

## Hostname

```bash
hostname
```

Mais detalhes:

```bash
hostnamectl
```

---

## Tempo ligado

```bash
uptime
```

---

## Data e horário

```bash
date
```

---

## Usuário atual

```bash
whoami
```

---

## Informações do usuário

```bash
id
```

---

# Checklist inicial

```bash
date

hostname

uptime

whoami

df -h

free -h

top

systemctl --failed

journalctl -p err -n 50
```

Esse conjunto oferece uma visão inicial do servidor.

---

# Diagnóstico de CPU

## Ver uso em tempo real

```bash
top
```

Ou:

```bash
htop
```

---

## Ordenar por CPU

No `top`, pressione:

```text
P
```

Também é possível usar:

```bash
ps aux --sort=-%cpu | head
```

---

## Top processos por CPU

```bash
ps -eo pid,user,comm,%cpu,%mem \
  --sort=-%cpu | head
```

---

## Informações da CPU

```bash
lscpu
```

---

## Quantidade de CPUs

```bash
nproc
```

---

# Load Average

Ver:

```bash
uptime
```

Exemplo:

```text
load average: 1.20, 0.90, 0.75
```

Os valores representam aproximadamente:

```text
1 minuto

5 minutos

15 minutos
```

O Load Average precisa ser interpretado em relação à quantidade de CPUs disponíveis.

Ver quantidade:

```bash
nproc
```

---

# Cenário: CPU em 100%

Passos:

```bash
top
```

Depois:

```bash
ps aux --sort=-%cpu | head
```

Identifique o processo:

```bash
ps -p PID -f
```

Veja arquivos usados:

```bash
sudo lsof -p PID
```

Veja as threads:

```bash
top -H -p PID
```

---

# Possíveis causas de CPU alta

* Loop infinito
* Consulta pesada
* Compactação de arquivos
* Build em execução
* Muitos requests
* Processo mal configurado
* Container sem limite
* Malware
* Job agendado
* Garbage Collection excessivo

---

# Diagnóstico de memória

## Ver memória

```bash
free -h
```

---

## Processos que mais consomem memória

```bash
ps aux --sort=-%mem | head
```

---

## Formato personalizado

```bash
ps -eo pid,user,comm,%mem,rss \
  --sort=-%mem | head
```

---

## Informações detalhadas

```bash
cat /proc/meminfo
```

---

# Swap

Ver:

```bash
swapon --show
```

Ou:

```bash
free -h
```

---

# Cenário: servidor sem memória

Verifique:

```bash
free -h
```

Depois:

```bash
ps aux --sort=-%mem | head
```

Procure eventos do OOM Killer:

```bash
journalctl -k | grep -i "out of memory"
```

Ou:

```bash
dmesg | grep -i "killed process"
```

---

# OOM Killer

Quando a memória acaba, o Kernel pode finalizar processos para proteger o sistema.

Mensagens comuns:

```text
Out of memory

Killed process

OOM Killer
```

Investigue com:

```bash
journalctl -k
```

---

# Possíveis causas de memória alta

* Memory leak
* Cache sem limite
* Muitos processos
* Container sem limite
* Consultas muito grandes
* Objetos mantidos na memória
* Uploads grandes
* Logs acumulados em buffers
* Má configuração da JVM ou .NET

---

# Diagnóstico de disco

## Espaço disponível

```bash
df -h
```

---

## Tipo do sistema de arquivos

```bash
df -Th
```

---

## Uso por diretório

```bash
du -sh /*
```

Esse comando pode gerar mensagens de permissão.

Utilize:

```bash
sudo du -sh /* 2>/dev/null
```

---

## Pastas maiores

```bash
sudo du -xhd1 / | sort -h
```

---

## Arquivos maiores

```bash
sudo find / \
  -xdev \
  -type f \
  -size +500M \
  -exec ls -lh {} \; \
  2>/dev/null
```

---

## Top 20 maiores arquivos

```bash
sudo find / \
  -xdev \
  -type f \
  -printf '%s %p\n' \
  2>/dev/null \
  | sort -nr \
  | head -20
```

---

# Inodes

Um disco pode ter espaço livre e ainda assim não aceitar novos arquivos por falta de inodes.

Verificar:

```bash
df -i
```

---

# Cenário: No space left on device

Verifique espaço:

```bash
df -h
```

Depois inodes:

```bash
df -i
```

Encontre diretórios grandes:

```bash
sudo du -xhd1 / | sort -h
```

Verifique logs:

```bash
sudo du -sh /var/log/*
```

Verifique Docker:

```bash
docker system df
```

---

# Arquivo removido ainda ocupando espaço

Um processo pode manter um arquivo excluído aberto.

Verificar:

```bash
sudo lsof +L1
```

A saída pode mostrar arquivos marcados como:

```text
deleted
```

Reiniciar corretamente o processo libera o espaço.

---

# Diagnóstico de I/O

## Ver estatísticas

```bash
iostat
```

Instalação:

```bash
sudo apt install sysstat
```

Executar:

```bash
iostat -xz 1
```

---

## Processos que usam disco

```bash
sudo iotop
```

Instalação:

```bash
sudo apt install iotop
```

---

# Sintomas de I/O alto

* Aplicação lenta
* Banco lento
* Load Average elevado
* CPU nem sempre alta
* Processos em estado `D`
* Tempo de resposta inconsistente

---

# Diagnóstico de processos

## Listar processos

```bash
ps aux
```

---

## Buscar processo

```bash
ps aux | grep nginx
```

Uma alternativa mais limpa:

```bash
pgrep -a nginx
```

---

## Árvore de processos

```bash
pstree -p
```

---

## Detalhes de um processo

```bash
ps -p PID -f
```

---

## Arquivos abertos

```bash
sudo lsof -p PID
```

---

## Diretório de trabalho

```bash
readlink -f /proc/PID/cwd
```

---

## Executável

```bash
readlink -f /proc/PID/exe
```

---

## Variáveis de ambiente

```bash
sudo tr '\0' '\n' < /proc/PID/environ
```

> A saída pode conter informações sensíveis.

---

# Encerrando processos

Tente primeiro:

```bash
kill PID
```

Esse comando envia `SIGTERM`.

Se não responder:

```bash
kill -9 PID
```

> Utilize `SIGKILL` apenas quando necessário, pois o processo não poderá finalizar corretamente.

---

# Processos Zombie

Verificar:

```bash
ps aux | awk '$8 ~ /Z/'
```

Um processo Zombie precisa ser tratado pelo processo pai.

Descubra o PPID:

```bash
ps -o pid,ppid,state,cmd -p PID
```

---

# Porta ocupada

Erro comum:

```text
Address already in use
```

Verificar:

```bash
sudo lsof -i :8080
```

Ou:

```bash
sudo ss -ltnp | grep :8080
```

Outra opção:

```bash
sudo fuser 8080/tcp
```

---

# Liberar porta

Identifique o PID e finalize corretamente:

```bash
kill PID
```

Não finalize o processo sem entender sua função.

---

# Diagnóstico de serviços systemd

## Status

```bash
systemctl status nginx
```

---

## Serviços com falha

```bash
systemctl --failed
```

---

## Logs

```bash
journalctl -u nginx
```

Últimas linhas:

```bash
journalctl -u nginx -n 100
```

Tempo real:

```bash
journalctl -fu nginx
```

---

## Reiniciar

```bash
sudo systemctl restart nginx
```

---

## Verificar se está ativo

```bash
systemctl is-active nginx
```

---

## Verificar inicialização automática

```bash
systemctl is-enabled nginx
```

---

# Cenário: serviço não inicia

Execute:

```bash
systemctl status SERVICO
```

Depois:

```bash
journalctl -u SERVICO -n 100
```

Valide a configuração, quando aplicável.

Nginx:

```bash
sudo nginx -t
```

SSH:

```bash
sudo sshd -t
```

Systemd:

```bash
systemd-analyze verify \
  /etc/systemd/system/minha-api.service
```

---

# Possíveis causas

* Caminho incorreto
* Arquivo inexistente
* Permissão negada
* Porta ocupada
* Variável ausente
* Usuário inválido
* Dependência indisponível
* Configuração com erro
* Executável não encontrado

---

# Diagnóstico de logs

## Últimos erros do sistema

```bash
journalctl -p err -n 100
```

---

## Logs desde o boot

```bash
journalctl -b
```

---

## Boot anterior

```bash
journalctl -b -1
```

---

## Período específico

```bash
journalctl \
  --since "2026-07-28 10:00:00" \
  --until "2026-07-28 11:00:00"
```

---

## Buscar erros

```bash
grep -i "error" app.log
```

Múltiplos termos:

```bash
grep -Ei "error|exception|failed|critical" app.log
```

---

## Contexto ao redor do erro

```bash
grep -n -B 5 -A 10 "ERROR" app.log
```

---

## Acompanhar log

```bash
tail -100f app.log
```

---

# Diagnóstico de rede

## Interfaces

```bash
ip a
```

---

## Rotas

```bash
ip route
```

---

## Gateway

```bash
ip route | grep default
```

---

## Testar conectividade por IP

```bash
ping -c 4 8.8.8.8
```

---

## Testar nome de domínio

```bash
ping -c 4 google.com
```

---

# Diagnóstico por camadas

```text
1. Interface está ativa?
2. Existe endereço IP?
3. Existe rota padrão?
4. O Gateway responde?
5. A Internet responde por IP?
6. O DNS resolve nomes?
7. A porta do destino responde?
8. A aplicação responde?
```

---

# Testar gateway

Descubra:

```bash
ip route
```

Depois:

```bash
ping -c 4 IP_DO_GATEWAY
```

---

# DNS

## Consultar resolução

```bash
dig google.com
```

Ou:

```bash
nslookup google.com
```

---

## Ver servidores DNS

```bash
cat /etc/resolv.conf
```

---

## Testar servidor DNS específico

```bash
dig @8.8.8.8 google.com
```

---

# Cenário: funciona por IP, mas não por nome

Se:

```bash
ping 8.8.8.8
```

funciona, mas:

```bash
ping google.com
```

não funciona, o problema provavelmente está no DNS.

---

# Testar porta remota

Com `nc`:

```bash
nc -vz servidor 5432
```

Outro exemplo:

```bash
nc -vz api.exemplo.com 443
```

---

# Testar HTTP

```bash
curl -I https://api.exemplo.com
```

Modo detalhado:

```bash
curl -v https://api.exemplo.com
```

---

# Medir tempo de resposta

```bash
curl \
  -o /dev/null \
  -s \
  -w "Status: %{http_code}\nTempo: %{time_total}s\n" \
  https://api.exemplo.com/health
```

---

# Testar certificado TLS

```bash
openssl s_client \
  -connect api.exemplo.com:443 \
  -servername api.exemplo.com
```

---

# Ver validade do certificado

```bash
echo \
  | openssl s_client \
      -connect api.exemplo.com:443 \
      -servername api.exemplo.com \
      2>/dev/null \
  | openssl x509 \
      -noout \
      -dates
```

---

# Traceroute

```bash
traceroute api.exemplo.com
```

---

# MTR

```bash
mtr api.exemplo.com
```

---

# Diagnóstico de firewall

## UFW

```bash
sudo ufw status verbose
```

---

## Regras atuais

```bash
sudo nft list ruleset
```

Em sistemas antigos:

```bash
sudo iptables -L -n -v
```

---

# Cenário: Connection refused

Significa geralmente:

* Host alcançável
* Porta fechada
* Serviço parado
* Serviço escutando apenas em localhost
* Porta incorreta

Verifique no servidor:

```bash
ss -ltnp
```

---

# Cenário: Connection timed out

Possíveis causas:

* Firewall
* Security Group
* Rota
* Host indisponível
* Porta filtrada
* Rede privada
* IP incorreto

---

# Cenário: No route to host

Verifique:

```bash
ip route
```

Depois:

```bash
traceroute HOST
```

---

# Diagnóstico de HTTP

## Código 400

Possíveis causas:

* Payload inválido
* Campo obrigatório ausente
* Tipo incorreto
* JSON malformado

Teste com:

```bash
curl -v \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"nome":"teste"}' \
  http://localhost:8080/api
```

---

## Código 401

Possíveis causas:

* Token ausente
* Token expirado
* Credencial inválida
* Configuração de autenticação

---

## Código 403

Possíveis causas:

* Usuário autenticado sem permissão
* Policy
* Role
* Firewall de aplicação
* Regra de acesso

---

## Código 404

Possíveis causas:

* Rota incorreta
* Base path incorreto
* Aplicação não publicada
* Proxy configurado incorretamente

---

## Código 500

Possíveis causas:

* Exceção não tratada
* Banco indisponível
* Variável ausente
* Falha de dependência
* Erro de código

Investigue os logs da aplicação.

---

## Código 502

Normalmente ocorre quando um proxy não consegue se comunicar com a aplicação.

Exemplo:

```text
Nginx → API indisponível
```

Verifique:

```bash
tail -f /var/log/nginx/error.log
```

E:

```bash
curl http://localhost:PORTA
```

---

## Código 503

Possíveis causas:

* Serviço indisponível
* Aplicação em manutenção
* Pods sem capacidade
* Health Check falhando
* Dependência indisponível

---

## Código 504

Normalmente indica timeout entre proxy e aplicação.

Investigue:

* Latência da API
* Banco
* Serviço externo
* Timeout do proxy
* Thread bloqueada

---

# Diagnóstico do Nginx

## Validar configuração

```bash
sudo nginx -t
```

---

## Status

```bash
systemctl status nginx
```

---

## Logs

```bash
tail -f /var/log/nginx/access.log
```

```bash
tail -f /var/log/nginx/error.log
```

---

## Portas

```bash
ss -ltnp | grep nginx
```

---

# Diagnóstico do SSH

## Serviço

```bash
systemctl status ssh
```

---

## Porta

```bash
ss -ltnp | grep :22
```

---

## Debug do cliente

```bash
ssh -vvv usuario@servidor
```

---

## Logs de autenticação

```bash
sudo tail -f /var/log/auth.log
```

---

## Permissões

```bash
chmod 700 ~/.ssh

chmod 600 ~/.ssh/authorized_keys

chmod 600 ~/.ssh/id_ed25519
```

Para arquivos `.pem`:

```bash
chmod 400 chave.pem
```

---

# Diagnóstico de Docker

## Serviço Docker

```bash
systemctl status docker
```

---

## Informações

```bash
docker info
```

---

## Containers ativos

```bash
docker ps
```

Todos:

```bash
docker ps -a
```

---

## Logs

```bash
docker logs CONTAINER
```

Tempo real:

```bash
docker logs -f CONTAINER
```

Últimas linhas:

```bash
docker logs --tail 100 CONTAINER
```

---

## Inspecionar container

```bash
docker inspect CONTAINER
```

---

## Processos internos

```bash
docker top CONTAINER
```

---

## Uso de recursos

```bash
docker stats
```

---

## Entrar no container

```bash
docker exec -it CONTAINER bash
```

Ou:

```bash
docker exec -it CONTAINER sh
```

---

# Container parou

Verifique:

```bash
docker ps -a
```

Depois:

```bash
docker logs CONTAINER
```

Consultar código de saída:

```bash
docker inspect \
  --format='{{.State.ExitCode}}' \
  CONTAINER
```

---

# Códigos de saída comuns

| Código | Possível significado                                 |
| -----: | ---------------------------------------------------- |
|    `0` | Finalização normal                                   |
|    `1` | Erro genérico                                        |
|  `126` | Comando encontrado, mas não executável               |
|  `127` | Comando não encontrado                               |
|  `137` | Processo recebeu SIGKILL, frequentemente por memória |
|  `139` | Segmentation fault                                   |
|  `143` | Processo recebeu SIGTERM                             |

---

# Container com Exit Code 137

Verifique memória:

```bash
docker inspect CONTAINER \
  --format='{{.State.OOMKilled}}'
```

Também:

```bash
journalctl -k | grep -i oom
```

---

# Porta do Docker não funciona

Verifique:

```bash
docker port CONTAINER
```

Depois:

```bash
docker inspect CONTAINER
```

Confirme o mapeamento:

```text
HOST:CONTAINER
```

Exemplo:

```text
5000:8080
```

Acesse a porta do host:

```text
http://localhost:5000
```

---

# Rede Docker

Listar:

```bash
docker network ls
```

Inspecionar:

```bash
docker network inspect REDE
```

---

# Resolver nomes entre containers

Containers na mesma rede devem usar o nome do serviço ou container.

Correto:

```text
Host=postgres
```

Incorreto dentro do container:

```text
Host=localhost
```

`localhost` aponta para o próprio container.

---

# Volume Docker

Listar:

```bash
docker volume ls
```

Inspecionar:

```bash
docker volume inspect VOLUME
```

---

# Permissão em volume

Verifique:

```bash
ls -la CAMINHO
```

Dentro do container:

```bash
docker exec -it CONTAINER id
```

Compare UID e GID.

---

# Limpeza Docker

Ver espaço:

```bash
docker system df
```

Containers parados:

```bash
docker container prune
```

Imagens não utilizadas:

```bash
docker image prune
```

Volumes não utilizados:

```bash
docker volume prune
```

Limpeza ampla:

```bash
docker system prune
```

> Verifique cuidadosamente antes de remover imagens, volumes ou dados.

---

# Diagnóstico do Docker Compose

## Validar configuração

```bash
docker compose config
```

---

## Status

```bash
docker compose ps
```

---

## Logs

```bash
docker compose logs
```

Serviço específico:

```bash
docker compose logs api
```

Tempo real:

```bash
docker compose logs -f api
```

---

## Reconstruir serviço

```bash
docker compose up -d --build api
```

---

## Reiniciar

```bash
docker compose restart api
```

---

## Ver processos

```bash
docker compose top
```

---

## Executar comando

```bash
docker compose exec api sh
```

---

# Depends On não garante disponibilidade

Exemplo:

```yaml
depends_on:
  - postgres
```

Isso não garante que o banco já esteja pronto para receber conexões.

Utilize Health Check:

```yaml
healthcheck:
  test:
    - CMD-SHELL
    - pg_isready -U admin
  interval: 10s
  timeout: 5s
  retries: 5
```

E:

```yaml
depends_on:
  postgres:
    condition: service_healthy
```

---

# Diagnóstico de Kubernetes

## Visão geral

```bash
kubectl get all
```

---

## Pods

```bash
kubectl get pods
```

Mais detalhes:

```bash
kubectl get pods -o wide
```

---

## Descrever Pod

```bash
kubectl describe pod POD
```

---

## Logs

```bash
kubectl logs POD
```

Tempo real:

```bash
kubectl logs -f POD
```

---

## Logs do container anterior

Muito útil em `CrashLoopBackOff`:

```bash
kubectl logs POD --previous
```

---

## Container específico

```bash
kubectl logs POD -c CONTAINER
```

---

## Eventos

```bash
kubectl get events \
  --sort-by=.metadata.creationTimestamp
```

---

## Entrar no Pod

```bash
kubectl exec -it POD -- sh
```

---

## Variáveis de ambiente

```bash
kubectl exec POD -- env
```

---

## Serviços

```bash
kubectl get svc
```

---

## Endpoints

```bash
kubectl get endpoints
```

---

## Deployments

```bash
kubectl get deployments
```

---

## Rollout

```bash
kubectl rollout status deployment/API
```

Histórico:

```bash
kubectl rollout history deployment/API
```

---

# Pod Pending

Verifique:

```bash
kubectl describe pod POD
```

Possíveis causas:

* Recursos insuficientes
* PVC indisponível
* Node Selector
* Taints e Tolerations
* Image Pull Secret
* Scheduler sem Node disponível

---

# CrashLoopBackOff

Passos:

```bash
kubectl describe pod POD
```

Depois:

```bash
kubectl logs POD
```

E:

```bash
kubectl logs POD --previous
```

Possíveis causas:

* Aplicação encerra
* Variável ausente
* Banco indisponível
* Porta incorreta
* Comando de inicialização inválido
* Health Check incorreto

---

# ImagePullBackOff

Verifique:

```bash
kubectl describe pod POD
```

Possíveis causas:

* Nome da imagem incorreto
* Tag inexistente
* Registry privado
* Secret ausente
* Permissão negada
* ACR não anexado ao AKS

---

# Pod OOMKilled

Verifique:

```bash
kubectl describe pod POD
```

Procure:

```text
Reason: OOMKilled
```

Verifique limites:

```bash
kubectl get pod POD -o yaml
```

---

# Readiness Probe falhando

Sintoma:

```text
Pod Running, mas não recebe tráfego
```

Verifique:

```bash
kubectl describe pod POD
```

Teste internamente:

```bash
kubectl exec POD -- \
  wget -qO- http://localhost:8080/health
```

---

# Liveness Probe falhando

Sintoma:

```text
Container reiniciando continuamente
```

Verifique o endpoint, porta, tempo inicial e timeout.

---

# Service não encontra Pod

Compare os labels do Pod:

```bash
kubectl get pods --show-labels
```

Com o selector do Service:

```bash
kubectl describe service SERVICO
```

Verifique endpoints:

```bash
kubectl get endpoints SERVICO
```

---

# DNS no Kubernetes

Teste:

```bash
kubectl exec -it POD -- \
  nslookup NOME_DO_SERVICO
```

---

# Port Forward

```bash
kubectl port-forward \
  deployment/minha-api \
  8080:8080
```

Depois:

```bash
curl http://localhost:8080/health
```

---

# Diagnóstico de aplicações .NET

## Ver processo

```bash
ps aux | grep dotnet
```

---

## Versão do .NET

```bash
dotnet --info
```

---

## Listar runtimes

```bash
dotnet --list-runtimes
```

---

## Listar SDKs

```bash
dotnet --list-sdks
```

---

# Erro de runtime ausente

Mensagem comum:

```text
You must install or update .NET to run this application
```

Verifique:

```bash
dotnet --list-runtimes
```

Compare com o `TargetFramework`.

---

# Logs com systemd

```bash
journalctl -fu minha-api
```

---

# Logs Docker

```bash
docker logs -f minha-api
```

---

# Variáveis de ambiente

```bash
printenv | sort
```

Para processo do systemd:

```bash
systemctl show minha-api \
  --property=Environment
```

---

# Ambiente ASP.NET Core

```bash
echo "$ASPNETCORE_ENVIRONMENT"
```

---

# Porta da aplicação

```bash
ss -ltnp | grep dotnet
```

---

# Testar Health Check

```bash
curl -v http://localhost:8080/health
```

---

# Erro de Connection String

Verifique:

* Nome da variável
* Separador `__`
* Host
* Porta
* Banco
* Usuário
* Senha
* Rede
* Certificado

Exemplo:

```text
ConnectionStrings__Default
```

---

# Diagnóstico de PostgreSQL

## Serviço

```bash
systemctl status postgresql
```

---

## Porta

```bash
ss -ltnp | grep 5432
```

---

## Teste de prontidão

```bash
pg_isready
```

Com host:

```bash
pg_isready -h localhost -p 5432
```

---

## Conectar

```bash
psql \
  -h localhost \
  -p 5432 \
  -U admin \
  -d database
```

---

## Conexões

```sql
SELECT *
FROM pg_stat_activity;
```

---

## Queries demoradas

```sql
SELECT
    pid,
    now() - query_start AS duration,
    state,
    query
FROM pg_stat_activity
WHERE state <> 'idle'
ORDER BY duration DESC;
```

---

## Locks

```sql
SELECT *
FROM pg_locks;
```

---

# Diagnóstico de SQL Server

## Container

```bash
docker logs sqlserver
```

---

## Porta

```bash
ss -ltnp | grep 1433
```

---

## Testar conexão

```bash
sqlcmd \
  -S localhost,1433 \
  -U sa \
  -P 'SENHA'
```

---

# Diagnóstico de MongoDB

## Porta

```bash
ss -ltnp | grep 27017
```

---

## Container

```bash
docker logs mongodb
```

---

## Testar

```bash
mongosh \
  "mongodb://usuario:senha@localhost:27017"
```

---

## Ping

```javascript
db.adminCommand({ ping: 1 })
```

---

# Diagnóstico de Redis

## Porta

```bash
ss -ltnp | grep 6379
```

---

## Testar

```bash
redis-cli ping
```

Resultado esperado:

```text
PONG
```

---

## Informações

```bash
redis-cli info
```

---

## Memória

```bash
redis-cli info memory
```

---

## Clientes

```bash
redis-cli client list
```

---

# Diagnóstico de RabbitMQ

## Serviço

```bash
systemctl status rabbitmq-server
```

---

## Portas

```bash
ss -ltnp | grep -E '5672|15672'
```

---

## Container

```bash
docker logs rabbitmq
```

---

## Status

```bash
rabbitmq-diagnostics status
```

---

## Ping

```bash
rabbitmq-diagnostics ping
```

---

## Filas

```bash
rabbitmqctl list_queues
```

Com detalhes:

```bash
rabbitmqctl list_queues \
  name \
  messages \
  messages_ready \
  messages_unacknowledged \
  consumers
```

---

# RabbitMQ com Connection String

Uma senha com caracteres especiais pode precisar de URL Encoding.

Exemplo conceitual:

```text
@ → %40
```

Uma URI deve possuir apenas um `@` separando credenciais e host.

---

# Mensagens acumuladas

Verifique:

```bash
rabbitmqctl list_queues \
  name \
  messages_ready \
  messages_unacknowledged \
  consumers
```

Possíveis causas:

* Consumer parado
* Consumer lento
* Erro ao processar mensagem
* Falta de ACK
* Dead Letter ausente
* Prefetch incorreto

---

# Diagnóstico de Git

## Status

```bash
git status
```

---

## Remotos

```bash
git remote -v
```

---

## Branch atual

```bash
git branch --show-current
```

---

## Histórico

```bash
git log --oneline --graph --all
```

---

## Conectividade com GitHub

```bash
ssh -T git@github.com
```

---

## Debug SSH

```bash
GIT_SSH_COMMAND="ssh -vvv" \
git fetch
```

---

# Diagnóstico de pipelines CI/CD

## Reproduzir localmente

Antes de alterar o pipeline, execute localmente:

```bash
dotnet restore

dotnet build --configuration Release

dotnet test --configuration Release
```

---

## Verificar caminhos

```bash
find . -name "*.sln"

find . -name "*.slnx"

find . -name "*.csproj"

find . -name "Dockerfile"
```

---

## Verificar versão

```bash
dotnet --info
```

---

## Docker Build

```bash
docker build \
  --progress=plain \
  -t teste .
```

---

## Docker Compose

```bash
docker compose config
```

---

# Erros comuns de pipeline

## Project file does not exist

Verifique o caminho do arquivo.

---

## SDK não encontrado

Compare:

* `global.json`
* Versão do Runner
* `setup-dotnet`
* Target Framework

---

## Secret vazio

Verifique:

* Nome
* Environment
* Escopo
* Permissão
* Evento que iniciou o workflow

---

## Push negado

Verifique:

* Login
* Registry
* Token
* Role
* Nome da imagem
* Tag

---

# Comandos de diagnóstico rápido

## Sistema

```bash
uptime

free -h

df -h

df -i

top
```

---

## Processos

```bash
ps aux --sort=-%cpu | head

ps aux --sort=-%mem | head

pstree -p
```

---

## Serviços

```bash
systemctl --failed

systemctl status SERVICO

journalctl -u SERVICO -n 100
```

---

## Rede

```bash
ip a

ip route

ss -ltnp

ping -c 4 HOST

dig HOST

curl -v URL
```

---

## Docker

```bash
docker ps -a

docker logs CONTAINER

docker inspect CONTAINER

docker stats

docker system df
```

---

## Kubernetes

```bash
kubectl get pods -o wide

kubectl describe pod POD

kubectl logs POD

kubectl logs POD --previous

kubectl get events
```

---

# Cenário 1 — API não responde

Sequência:

```bash
curl -v http://localhost:8080/health
```

Verifique a porta:

```bash
ss -ltnp | grep 8080
```

Verifique o processo:

```bash
ps aux | grep dotnet
```

Verifique o serviço:

```bash
systemctl status minha-api
```

Verifique os logs:

```bash
journalctl -u minha-api -n 100
```

Verifique dependências:

```bash
nc -vz postgres 5432

nc -vz rabbitmq 5672

nc -vz redis 6379
```

---

# Cenário 2 — Docker Compose não sobe

Validar:

```bash
docker compose config
```

Subir:

```bash
docker compose up
```

Ver logs:

```bash
docker compose logs
```

Ver status:

```bash
docker compose ps
```

Verificar portas:

```bash
ss -ltnp
```

Verificar espaço:

```bash
df -h

docker system df
```

---

# Cenário 3 — PostgreSQL retorna Connection Refused

Verificar container:

```bash
docker compose ps postgres
```

Logs:

```bash
docker compose logs postgres
```

Health Check:

```bash
docker compose exec postgres \
  pg_isready -U admin
```

Verificar Connection String:

```text
Host=postgres
```

Dentro da rede Docker, não utilize:

```text
Host=localhost
```

---

# Cenário 4 — Kubernetes CrashLoopBackOff

```bash
kubectl get pods
```

```bash
kubectl describe pod POD
```

```bash
kubectl logs POD
```

```bash
kubectl logs POD --previous
```

Verifique:

* Variáveis
* Secrets
* ConfigMaps
* Banco
* Porta
* Probes
* Limites de memória
* Comando de inicialização

---

# Cenário 5 — Servidor está lento

```bash
uptime
```

```bash
top
```

```bash
free -h
```

```bash
df -h
```

```bash
iostat -xz 1
```

```bash
ps aux --sort=-%cpu | head
```

```bash
ps aux --sort=-%mem | head
```

```bash
journalctl -p err -n 100
```

---

# Cenário 6 — Disco cheio

```bash
df -h
```

```bash
df -i
```

```bash
sudo du -xhd1 / | sort -h
```

```bash
sudo du -sh /var/log/*
```

```bash
docker system df
```

```bash
sudo lsof +L1
```

---

# Cenário 7 — Nginx retorna 502

Validar:

```bash
sudo nginx -t
```

Logs:

```bash
tail -f /var/log/nginx/error.log
```

Testar backend:

```bash
curl -v http://localhost:8080/health
```

Verificar porta:

```bash
ss -ltnp | grep 8080
```

Verificar serviço:

```bash
systemctl status minha-api
```

---

# Cenário 8 — RabbitMQ acumula mensagens

```bash
rabbitmqctl list_queues \
  name \
  messages_ready \
  messages_unacknowledged \
  consumers
```

Verifique consumers:

```bash
docker compose ps
```

Logs:

```bash
docker compose logs -f notifications
```

Investigue:

* Erros no Consumer
* ACK
* Retentativas
* Dead Letter Queue
* Conexão com banco
* Prefetch
* Concorrência

---

# Cenário 9 — Pipeline falha e local funciona

Compare:

```text
Sistema operacional

Versão do SDK

Variáveis

Secrets

Caminhos

Arquivos commitados

Permissões

Serviços externos
```

No pipeline, exiba informações não sensíveis:

```bash
pwd

find . -maxdepth 3 -type f | sort

dotnet --info

docker version
```

---

# Coleta de evidências

Antes de reiniciar ou excluir recursos, colete:

```text
Horário

Logs

Status do serviço

Uso de CPU

Uso de memória

Uso de disco

Processos

Portas

Versão da aplicação

Versão da imagem

Commit implantado

Último deploy
```

---

# Script básico de diagnóstico

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

OUTPUT="diagnostico-$(date +%Y%m%d-%H%M%S).txt"

{
  echo "===== DATA ====="
  date

  echo
  echo "===== HOST ====="
  hostnamectl

  echo
  echo "===== UPTIME ====="
  uptime

  echo
  echo "===== DISCO ====="
  df -h

  echo
  echo "===== INODES ====="
  df -i

  echo
  echo "===== MEMÓRIA ====="
  free -h

  echo
  echo "===== CPU ====="
  lscpu

  echo
  echo "===== PROCESSOS CPU ====="
  ps aux --sort=-%cpu | head -15

  echo
  echo "===== PROCESSOS MEMÓRIA ====="
  ps aux --sort=-%mem | head -15

  echo
  echo "===== PORTAS ====="
  ss -ltnp

  echo
  echo "===== SERVIÇOS COM FALHA ====="
  systemctl --failed

  echo
  echo "===== ERROS RECENTES ====="
  journalctl -p err -n 100 --no-pager

} > "$OUTPUT" 2>&1

echo "Diagnóstico salvo em: $OUTPUT"
```

---

# Segurança durante o diagnóstico

Alguns comandos podem exibir:

* Senhas
* Tokens
* Connection Strings
* Variáveis sensíveis
* Chaves
* Dados pessoais

Evite compartilhar saídas completas sem revisar.

Comandos que exigem cuidado:

```bash
printenv

docker inspect

kubectl get secret -o yaml

systemctl show

cat /proc/PID/environ
```

---

# Não faça durante um incidente

❌ Não utilize `kill -9` sem investigar.

❌ Não reinicie todos os serviços ao mesmo tempo.

❌ Não execute `docker compose down -v` em produção.

❌ Não remova volumes sem confirmar o conteúdo.

❌ Não utilize `docker system prune -a --volumes` sem planejamento.

❌ Não delete Pods antes de coletar logs.

❌ Não altere firewall sem registrar as regras anteriores.

❌ Não faça deploy de várias correções simultâneas.

❌ Não compartilhe Secrets em chats ou tickets.

---

# Boas práticas

✅ Confirme o problema antes de agir.

✅ Registre o horário do incidente.

✅ Colete evidências antes de reiniciar.

✅ Analise primeiro os logs.

✅ Teste uma hipótese por vez.

✅ Comece pelas verificações mais simples.

✅ Compare o estado atual com o estado esperado.

✅ Utilize Health Checks.

✅ Mantenha dashboards e alertas.

✅ Documente a correção.

✅ Crie uma ação preventiva.

✅ Automatize verificações repetitivas.

✅ Mantenha um Runbook para incidentes conhecidos.

---

# Runbook

Um Runbook é um procedimento documentado para resolver ou investigar um problema.

Exemplo:

```text
Problema:
API retorna 502.

Verificações:
1. Conferir Nginx.
2. Testar backend localmente.
3. Verificar porta.
4. Verificar serviço.
5. Consultar logs.

Correção:
Reiniciar aplicação somente se necessário.

Validação:
Health Check responde HTTP 200.
```

---

# Relatório de incidente

Estrutura sugerida:

```text
Título:

Data e horário:

Ambiente:

Serviço afetado:

Impacto:

Sintoma:

Linha do tempo:

Causa raiz:

Correção aplicada:

Tempo de recuperação:

Ações preventivas:

Responsáveis:
```

---

# Exemplo de incidente

```text
Título:
API de pedidos indisponível

Ambiente:
Produção

Impacto:
Usuários não conseguiram finalizar pedidos

Sintoma:
Nginx retornava HTTP 502

Causa raiz:
Aplicação .NET encerrou por falta de memória

Correção:
Aplicação reiniciada e limite de memória revisado

Ação preventiva:
Criar alerta de memória e revisar consumo da aplicação
```

---

# Checklist de troubleshooting

```text
[ ] Confirmei o sintoma

[ ] Identifiquei o ambiente

[ ] Registrei o horário

[ ] Verifiquei logs

[ ] Verifiquei CPU

[ ] Verifiquei memória

[ ] Verifiquei disco

[ ] Verifiquei processos

[ ] Verifiquei portas

[ ] Verifiquei rede e DNS

[ ] Verifiquei dependências

[ ] Verifiquei mudanças recentes

[ ] Testei uma hipótese por vez

[ ] Validei a correção

[ ] Documentei a causa

[ ] Criei ação preventiva
```

---

# Cheat Sheet

```bash
# Sistema
uptime
hostnamectl
cat /etc/os-release
uname -a

# CPU
top
htop
ps aux --sort=-%cpu | head
lscpu
nproc

# Memória
free -h
ps aux --sort=-%mem | head
journalctl -k | grep -i oom

# Disco
df -h
df -i
du -sh *
sudo du -xhd1 / | sort -h
sudo lsof +L1

# Processos
ps aux
pgrep -a PROCESSO
pstree -p
lsof -p PID
kill PID
kill -9 PID

# Portas
ss -ltnp
lsof -i :8080
fuser 8080/tcp

# Serviços
systemctl status SERVICO
systemctl --failed
journalctl -u SERVICO
journalctl -fu SERVICO

# Logs
journalctl -p err -n 100
tail -100f app.log
grep -Ei "error|failed|exception" app.log

# Rede
ip a
ip route
ping -c 4 HOST
dig HOST
nc -vz HOST PORTA
curl -v URL
traceroute HOST

# Docker
docker ps -a
docker logs CONTAINER
docker inspect CONTAINER
docker stats
docker system df

# Docker Compose
docker compose config
docker compose ps
docker compose logs
docker compose logs -f SERVICO

# Kubernetes
kubectl get pods -o wide
kubectl describe pod POD
kubectl logs POD
kubectl logs POD --previous
kubectl get events

# PostgreSQL
pg_isready
psql -h HOST -U USUARIO -d BANCO

# Redis
redis-cli ping
redis-cli info

# RabbitMQ
rabbitmq-diagnostics ping
rabbitmq-diagnostics status
rabbitmqctl list_queues
```

---

# Resumo

Neste capítulo você aprendeu:

* Metodologia de troubleshooting
* Diferença entre sintoma, problema e causa raiz
* Diagnóstico de CPU
* Diagnóstico de memória
* Diagnóstico de disco e inodes
* Diagnóstico de processos
* Diagnóstico de serviços
* Análise de logs
* Diagnóstico de rede e DNS
* Diagnóstico de HTTP
* Diagnóstico de Nginx e SSH
* Troubleshooting de Docker
* Troubleshooting de Docker Compose
* Troubleshooting de Kubernetes
* Diagnóstico de aplicações .NET
* Diagnóstico de PostgreSQL, SQL Server, MongoDB, Redis e RabbitMQ
* Diagnóstico de pipelines
* Coleta de evidências
* Criação de Runbooks
* Documentação de incidentes
* Boas práticas para ambientes de produção

---
