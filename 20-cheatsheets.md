# Capítulo 20 — Cheat Sheets

> Este capítulo reúne os comandos mais utilizados ao longo do guia em um formato rápido para consulta. Use como referência durante estudos, desenvolvimento, deploys, troubleshooting e administração de servidores.

---

# Sumário

* Linux básico
* Sistema de arquivos
* Permissões
* Usuários e grupos
* Processos
* Rede
* Logs
* Systemd
* Bash
* Find, Grep, Sed e Awk
* SSH
* Git
* Docker
* Docker Compose
* Kubernetes
* Azure CLI
* CI/CD
* Monitoramento
* Troubleshooting

---

# Linux básico

## Informações do sistema

```bash
# Distribuição
cat /etc/os-release

# Kernel
uname -a
uname -r

# Hostname
hostname
hostnamectl

# Arquitetura
uname -m

# Tempo ligado
uptime

# Data e hora
date

# Usuário atual
whoami

# Informações do usuário
id

# Shell atual
echo "$SHELL"

# Diretório pessoal
echo "$HOME"

# Variáveis de ambiente
printenv
env
```

---

## Navegação

```bash
# Diretório atual
pwd

# Listar arquivos
ls

# Lista detalhada
ls -l

# Incluir arquivos ocultos
ls -la

# Tamanhos legíveis
ls -lh

# Ordenar por data
ls -lt

# Ordenar por tamanho
ls -lS

# Entrar em diretório
cd diretorio

# Voltar um nível
cd ..

# Ir para Home
cd ~

# Voltar ao diretório anterior
cd -

# Árvore de diretórios
tree
```

---

## Ajuda

```bash
# Manual
man ls

# Ajuda rápida
ls --help

# Localizar executável
which git

# Localizações relacionadas
whereis git

# Tipo do comando
type cd

# Histórico
history

# Pesquisar histórico
Ctrl+R
```

---

# Sistema de arquivos

## Criar arquivos e diretórios

```bash
# Criar arquivo vazio
touch arquivo.txt

# Criar vários arquivos
touch a.txt b.txt c.txt

# Criar diretório
mkdir projeto

# Criar vários diretórios
mkdir api banco logs

# Criar estrutura completa
mkdir -p projeto/src/controllers
```

---

## Copiar, mover e renomear

```bash
# Copiar arquivo
cp origem.txt destino.txt

# Copiar diretório
cp -r origem destino

# Copiar preservando atributos
cp -a origem destino

# Mover arquivo
mv arquivo.txt documentos/

# Renomear arquivo
mv antigo.txt novo.txt

# Mover vários arquivos
mv *.log logs/
```

---

## Remover

```bash
# Remover arquivo
rm arquivo.txt

# Pedir confirmação
rm -i arquivo.txt

# Remover diretório vazio
rmdir pasta

# Remover diretório
rm -r pasta

# Forçar remoção
rm -rf pasta
```

> ⚠️ Confira o caminho antes de executar `rm -rf`.

---

## Visualizar arquivos

```bash
# Mostrar conteúdo
cat arquivo.txt

# Visualização paginada
less arquivo.txt

# Primeiras linhas
head arquivo.txt
head -n 20 arquivo.txt

# Últimas linhas
tail arquivo.txt
tail -n 50 arquivo.txt

# Acompanhar alterações
tail -f app.log
tail -n 100 -f app.log

# Tipo do arquivo
file arquivo
```

---

## Contagem

```bash
# Linhas, palavras e bytes
wc arquivo.txt

# Apenas linhas
wc -l arquivo.txt

# Apenas palavras
wc -w arquivo.txt

# Apenas bytes
wc -c arquivo.txt
```

---

## Links

```bash
# Hard link
ln arquivo.txt copia.txt

# Link simbólico
ln -s arquivo.txt atalho.txt

# Ver destino do link
readlink atalho.txt

# Caminho absoluto do destino
readlink -f atalho.txt
```

---

## Espaço em disco

```bash
# Uso dos sistemas de arquivos
df -h

# Tipo do sistema de arquivos
df -Th

# Uso de inodes
df -i

# Tamanho do diretório atual
du -sh .

# Tamanho dos itens
du -sh *

# Ordenar por tamanho
du -sh * | sort -h

# Maiores diretórios da raiz
sudo du -xhd1 / | sort -h
```

---

## Compactação

```bash
# Criar tar.gz
tar -czf backup.tar.gz pasta/

# Extrair tar.gz
tar -xzf backup.tar.gz

# Listar conteúdo
tar -tzf backup.tar.gz

# Criar ZIP
zip -r projeto.zip projeto/

# Extrair ZIP
unzip projeto.zip

# Compactar com gzip
gzip arquivo.log

# Descompactar
gunzip arquivo.log.gz
```

---

# Permissões

## Visualizar

```bash
# Permissões de arquivos
ls -l

# Permissão do diretório
ls -ld pasta

# ACL
getfacl arquivo
```

---

## Valores numéricos

```text
r = 4
w = 2
x = 1

7 = rwx
6 = rw-
5 = r-x
4 = r--
0 = ---
```

Permissões comuns:

```text
755 = rwxr-xr-x
644 = rw-r--r--
700 = rwx------
600 = rw-------
400 = r--------
```

---

## chmod

```bash
# Permissão numérica
chmod 755 script.sh

# Tornar executável
chmod +x script.sh

# Remover execução
chmod -x script.sh

# Dono: adicionar execução
chmod u+x script.sh

# Grupo: adicionar escrita
chmod g+w arquivo.txt

# Outros: remover leitura
chmod o-r arquivo.txt

# Recursivo
chmod -R 755 pasta
```

---

## chown e chgrp

```bash
# Alterar proprietário
sudo chown usuario arquivo

# Alterar proprietário e grupo
sudo chown usuario:grupo arquivo

# Recursivo
sudo chown -R usuario:grupo pasta

# Alterar grupo
sudo chgrp grupo arquivo
```

---

## umask

```bash
# Ver valor
umask

# Alterar temporariamente
umask 022
```

---

## Permissões especiais

```bash
# SUID
chmod u+s arquivo

# SGID
chmod g+s diretorio

# Sticky Bit
chmod +t diretorio
```

---

## ACL

```bash
# Permissão para usuário
setfacl -m u:usuario:rwx pasta

# Permissão para grupo
setfacl -m g:grupo:rx pasta

# ACL padrão
setfacl -d -m g:grupo:rwx pasta

# Remover ACL
setfacl -b pasta
```

---

# Usuários e grupos

## Usuários

```bash
# Usuário atual
whoami

# Informações
id

# Usuários conectados
who
w

# Criar usuário no Ubuntu
sudo adduser usuario

# Criar usuário
sudo useradd -m -s /bin/bash usuario

# Alterar senha
sudo passwd usuario

# Bloquear
sudo passwd -l usuario

# Desbloquear
sudo passwd -u usuario

# Renomear
sudo usermod -l novo antigo

# Alterar Home
sudo usermod -d /home/novo -m usuario

# Alterar Shell
sudo usermod -s /bin/bash usuario

# Excluir usuário
sudo userdel usuario

# Excluir com Home
sudo userdel -r usuario
```

---

## Grupos

```bash
# Ver grupos do usuário atual
groups

# Ver grupos de outro usuário
groups usuario

# Criar grupo
sudo groupadd devops

# Excluir grupo
sudo groupdel devops

# Adicionar usuário ao grupo
sudo usermod -aG devops usuario

# Trocar grupo atual
newgrp devops
```

---

## Trocar usuário

```bash
# Trocar usuário
su usuario

# Abrir sessão completa
su - usuario

# Abrir shell de root
sudo -i

# Executar como outro usuário
sudo -u postgres psql

# Sair
exit
```

---

## Arquivos importantes

```text
/etc/passwd
/etc/shadow
/etc/group
/etc/sudoers
/home/usuario
~/.bashrc
~/.profile
```

---

# Processos

## Listagem

```bash
# Processos da sessão
ps

# Todos os processos
ps aux
ps -ef

# Árvore
pstree -p

# Buscar processo
pgrep -a nginx
ps aux | grep dotnet

# Detalhes por PID
ps -p PID -f
```

---

## Monitoramento

```bash
# Tempo real
top

# Interface avançada
htop

# Memória
free -h

# CPUs
nproc

# Informações da CPU
lscpu

# Top CPU
ps aux --sort=-%cpu | head

# Top memória
ps aux --sort=-%mem | head
```

---

## Encerrar processos

```bash
# SIGTERM
kill PID

# SIGKILL
kill -9 PID

# Pelo nome
killall nginx
pkill dotnet

# Por usuário
pkill -u usuario

# Listar sinais
kill -l
```

---

## Jobs

```bash
# Executar em background
comando &

# Listar Jobs
jobs

# Suspender
Ctrl+Z

# Enviar para background
bg

# Trazer para foreground
fg

# Continuar após logout
nohup comando > app.log 2>&1 &
```

---

## Prioridade

```bash
# Executar com prioridade
nice -n 10 comando

# Alterar prioridade
renice 5 -p PID
```

---

## Arquivos e portas

```bash
# Arquivos abertos
lsof

# Por processo
lsof -p PID

# Porta
sudo lsof -i :8080

# PID usando porta
sudo fuser 8080/tcp
```

---

# Rede

## Interfaces e IP

```bash
# Interfaces
ip a
ip addr

# Estado das interfaces
ip link

# IPs do host
hostname -I

# Ativar interface
sudo ip link set eth0 up

# Desativar interface
sudo ip link set eth0 down
```

---

## Rotas

```bash
# Rotas
ip route

# Gateway padrão
ip route | grep default

# Adicionar rota
sudo ip route add 10.10.0.0/24 via 192.168.1.1

# Remover rota
sudo ip route del 10.10.0.0/24
```

---

## Testes

```bash
# Ping
ping google.com
ping -c 4 google.com

# Rota até o destino
traceroute google.com

# Diagnóstico contínuo
mtr google.com

# Testar porta
nc -vz servidor 5432

# Testar HTTP
curl https://api.exemplo.com

# Cabeçalhos
curl -I https://api.exemplo.com

# Verbose
curl -v https://api.exemplo.com
```

---

## DNS

```bash
# Consulta
dig google.com

# Servidor específico
dig @8.8.8.8 google.com

# Outros
nslookup google.com
host google.com

# Configuração DNS
cat /etc/resolv.conf
```

---

## Portas locais

```bash
# TCP e UDP
ss -tulpn

# TCP em escuta
ss -ltnp

# Filtrar porta
ss -ltnp | grep :8080

# Alternativa antiga
netstat -tulpn
```

---

## Download

```bash
# wget
wget URL

# Salvar com nome
wget -O arquivo.zip URL

# curl
curl -O URL

# Seguir redirecionamentos
curl -L -O URL
```

---

## Firewall

```bash
# Status UFW
sudo ufw status verbose

# Ativar
sudo ufw enable

# Permitir SSH
sudo ufw allow ssh

# Permitir porta
sudo ufw allow 8080/tcp

# Excluir regra
sudo ufw delete allow 8080/tcp

# nftables
sudo nft list ruleset
```

---

# Logs

## Arquivos

```bash
# Diretório principal
cd /var/log

# Últimas linhas
tail -n 100 app.log

# Tempo real
tail -f app.log

# Paginação
less app.log

# Erros
grep -i error app.log

# Múltiplos termos
grep -Ei "error|exception|failed|critical" app.log

# Contexto
grep -n -B 5 -A 10 "ERROR" app.log
```

---

## Journalctl

```bash
# Todos
journalctl

# Último boot
journalctl -b

# Boot anterior
journalctl -b -1

# Últimas linhas
journalctl -n 100

# Tempo real
journalctl -f

# Por serviço
journalctl -u nginx

# Por serviço em tempo real
journalctl -fu nginx

# Desde hoje
journalctl --since today

# Última hora
journalctl --since "1 hour ago"

# Intervalo
journalctl \
  --since "2026-07-28 10:00:00" \
  --until "2026-07-28 11:00:00"

# Apenas erros
journalctl -p err

# Kernel
journalctl -k
```

---

## Logs compactados

```bash
# Ler
zcat app.log.gz

# Pesquisar
zgrep ERROR app.log.gz

# Paginar
zless app.log.gz
```

---

## Logins

```bash
# Histórico
last

# Falhas
sudo lastb

# Sessões atuais
who
w

# Autenticação
sudo tail -f /var/log/auth.log
```

---

# Systemd

## Serviços

```bash
# Status
systemctl status nginx

# Iniciar
sudo systemctl start nginx

# Parar
sudo systemctl stop nginx

# Reiniciar
sudo systemctl restart nginx

# Recarregar
sudo systemctl reload nginx

# Habilitar no boot
sudo systemctl enable nginx

# Desabilitar
sudo systemctl disable nginx

# Está ativo?
systemctl is-active nginx

# Está habilitado?
systemctl is-enabled nginx
```

---

## Listagem

```bash
# Serviços ativos
systemctl list-units --type=service

# Arquivos de unit
systemctl list-unit-files

# Falhas
systemctl --failed

# Timers
systemctl list-timers
```

---

## Após editar serviço

```bash
sudo systemctl daemon-reload

sudo systemctl restart minha-api
```

---

## Sistema

```bash
# Target padrão
systemctl get-default

# Reiniciar
sudo systemctl reboot

# Desligar
sudo systemctl poweroff

# Suspender
sudo systemctl suspend
```

---

## Diagnóstico

```bash
# Validar unit
systemd-analyze verify \
  /etc/systemd/system/minha-api.service

# Tempo de boot
systemd-analyze

# Serviços lentos
systemd-analyze blame

# Cadeia crítica
systemd-analyze critical-chain
```

---

# Bash

## Estrutura básica

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

echo "Olá"
```

---

## Variáveis

```bash
nome="Kelvim"

echo "$nome"

readonly AMBIENTE="production"

export ASPNETCORE_ENVIRONMENT="Production"
```

---

## Entrada

```bash
read -r nome

read -r -p "Nome: " nome

read -r -s -p "Senha: " senha
echo
```

---

## Parâmetros

```bash
$0    # Nome do script
$1    # Primeiro parâmetro
$2    # Segundo parâmetro
$#    # Quantidade
$@    # Todos
$?    # Código de saída
$$    # PID do script
```

---

## Condicionais

```bash
if [[ -f arquivo.txt ]]
then
  echo "Existe"
else
  echo "Não existe"
fi
```

---

## Comparações

```text
Números:
-eq -ne -gt -lt -ge -le

Strings:
== != -z -n

Arquivos:
-f -d -e -r -w -x
```

---

## Case

```bash
case "$1" in
  start)
    echo "Iniciando"
    ;;
  stop)
    echo "Parando"
    ;;
  *)
    echo "Uso: $0 {start|stop}"
    ;;
esac
```

---

## Loops

```bash
for arquivo in *.log
do
  echo "$arquivo"
done
```

```bash
contador=1

while [[ "$contador" -le 5 ]]
do
  echo "$contador"
  ((contador++))
done
```

---

## Funções

```bash
log()
{
  echo "[$(date)] $*"
}

log "Aplicação iniciada"
```

---

## Tratamento de erro

```bash
set -e
set -u
set -o pipefail

trap 'echo "Erro na linha $LINENO"' ERR
trap 'echo "Finalizando"' EXIT
```

---

## Debug

```bash
bash -x script.sh

set -x
set +x

shellcheck script.sh
```

---

# Find, Grep, Sed e Awk

## Find

```bash
# Por nome
find . -name "*.cs"

# Ignorar caixa
find . -iname "readme.md"

# Arquivos
find . -type f

# Diretórios
find . -type d

# Maiores que 100 MB
find . -size +100M

# Modificados nas últimas 24h
find . -mtime -1

# Mais antigos que 30 dias
find . -mtime +30

# Vazios
find . -empty

# Por usuário
find . -user usuario

# Executar comando
find . -name "*.tmp" -exec rm {} \;

# Excluir
find . -name "*.tmp" -delete
```

---

## Grep

```bash
# Buscar texto
grep "ERROR" app.log

# Ignorar caixa
grep -i error app.log

# Número da linha
grep -n ERROR app.log

# Recursivo
grep -R "TODO" .

# Recursivo com linha
grep -Rn "TODO" .

# Não correspondente
grep -v INFO app.log

# Expressão estendida
grep -E "ERROR|WARN" app.log

# Contar
grep -c ERROR app.log

# Antes e depois
grep -B 5 -A 10 ERROR app.log
```

---

## Sed

```bash
# Substituição
sed 's/dev/prod/' arquivo.txt

# Todas as ocorrências
sed 's/dev/prod/g' arquivo.txt

# Editar arquivo
sed -i 's/dev/prod/g' arquivo.txt

# Backup antes de alterar
sed -i.bak 's/dev/prod/g' arquivo.txt

# Excluir linha 5
sed '5d' arquivo.txt

# Mostrar linha 10
sed -n '10p' arquivo.txt

# Mostrar intervalo
sed -n '10,20p' arquivo.txt
```

---

## Awk

```bash
# Primeira coluna
awk '{print $1}' arquivo.txt

# Primeira e terceira
awk '{print $1, $3}' arquivo.txt

# Separador CSV
awk -F',' '{print $2}' dados.csv

# Contar linhas
awk 'END {print NR}' arquivo.txt

# Somar coluna
awk '{soma += $2} END {print soma}' dados.txt

# Filtrar valor
awk '$3 > 80 {print $1, $3}' dados.txt
```

---

## Sort, Uniq, Cut e Xargs

```bash
# Ordenar
sort arquivo.txt

# Numérico
sort -n numeros.txt

# Reverso
sort -r arquivo.txt

# Duplicados
sort arquivo.txt | uniq

# Contar duplicados
sort arquivo.txt | uniq -c

# Campo
cut -d':' -f1 /etc/passwd

# Executar em lote
find . -name "*.log" -print0 \
  | xargs -0 rm
```

---

## Pipelines úteis

```bash
# Contar erros
grep ERROR app.log | wc -l

# IPs únicos
awk '{print $1}' access.log \
  | sort \
  | uniq

# Top 10 IPs
awk '{print $1}' access.log \
  | sort \
  | uniq -c \
  | sort -nr \
  | head -10

# Top processos por memória
ps aux \
  | sort -k4 -nr \
  | head
```

---

# SSH

## Conexão

```bash
# Padrão
ssh usuario@ip

# Porta específica
ssh -p 2222 usuario@ip

# Com chave
ssh -i ~/.ssh/id_ed25519 usuario@ip

# Arquivo PEM
chmod 400 chave.pem
ssh -i chave.pem ubuntu@ip
```

---

## Chaves

```bash
# Gerar Ed25519
ssh-keygen -t ed25519

# Com comentário
ssh-keygen -t ed25519 \
  -C "email@exemplo.com"

# Copiar chave
ssh-copy-id usuario@ip

# Ver fingerprint
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

---

## Permissões

```bash
chmod 700 ~/.ssh

chmod 600 ~/.ssh/authorized_keys

chmod 600 ~/.ssh/id_ed25519

chmod 400 chave.pem
```

---

## SSH Config

```text
Host producao
    HostName 192.168.1.100
    User deploy
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

Uso:

```bash
ssh producao
```

---

## Transferência

```bash
# Enviar arquivo
scp arquivo.txt usuario@ip:/destino

# Baixar arquivo
scp usuario@ip:/origem/arquivo.txt .

# Diretório
scp -r projeto usuario@ip:/destino

# Rsync
rsync -av projeto/ usuario@ip:/var/www/projeto/

# Excluir no destino
rsync -av --delete \
  projeto/ usuario@ip:/var/www/projeto/
```

---

## Comando remoto e túnel

```bash
# Comando remoto
ssh usuario@ip "hostname"

# Script remoto
ssh usuario@ip "bash -s" < deploy.sh

# Túnel local
ssh -L 5432:localhost:5432 usuario@ip

# Bastion
ssh -J bastion usuario@servidor
```

---

## Diagnóstico

```bash
ssh -v usuario@ip

ssh -vvv usuario@ip

ssh-keygen -R IP_DO_SERVIDOR

systemctl status ssh

ss -ltnp | grep :22
```

---

# Git

## Configuração

```bash
git config --global user.name "Seu Nome"

git config --global user.email "email@exemplo.com"

git config --list
```

---

## Fluxo básico

```bash
git init

git status

git add .

git commit -m "Mensagem"

git log --oneline
```

---

## Repositório remoto

```bash
git remote add origin URL

git remote -v

git push -u origin main

git pull

git fetch

git clone URL
```

---

## Branches

```bash
# Listar
git branch

# Criar e trocar
git switch -c feature/login

# Trocar
git switch main

# Merge
git merge feature/login

# Excluir local
git branch -d feature/login

# Excluir remota
git push origin --delete feature/login
```

---

## Diferenças e histórico

```bash
git diff

git diff --cached

git log

git log --oneline --graph --all

git show HASH

git blame Program.cs

git grep "ConnectionString"
```

---

## Desfazer

```bash
# Descartar alteração
git restore arquivo.txt

# Remover do stage
git restore --staged arquivo.txt

# Reverter commit compartilhado
git revert HASH

# Remover último commit mantendo alterações
git reset --soft HEAD~1

# Remover commit e alterações
git reset --hard HEAD~1
```

---

## Stash

```bash
git stash

git stash list

git stash pop

git stash drop
```

---

## Tags

```bash
git tag v1.0.0

git push origin v1.0.0

git tag
```

---

## Limpeza

```bash
# Simular
git clean -n

# Remover
git clean -fd
```

---

# Docker

## Informações

```bash
docker version

docker info

docker system df
```

---

## Imagens

```bash
docker images

docker image ls

docker pull nginx

docker rmi nginx

docker image inspect nginx
```

---

## Containers

```bash
# Executar
docker run nginx

# Background
docker run -d nginx

# Nome e porta
docker run -d \
  --name web \
  -p 8080:80 \
  nginx

# Interativo
docker run -it ubuntu bash

# Ativos
docker ps

# Todos
docker ps -a

# Parar
docker stop web

# Iniciar
docker start web

# Reiniciar
docker restart web

# Remover
docker rm web

# Forçar
docker rm -f web
```

---

## Logs e execução

```bash
# Logs
docker logs web

# Tempo real
docker logs -f web

# Últimas linhas
docker logs --tail 100 web

# Entrar
docker exec -it web bash

# Shell alternativo
docker exec -it web sh

# Executar comando
docker exec web ls -la

# Processos internos
docker top web
```

---

## Copiar arquivos

```bash
# Host para container
docker cp arquivo.txt web:/tmp/

# Container para host
docker cp web:/etc/nginx/nginx.conf .
```

---

## Volumes

```bash
docker volume ls

docker volume create dados

docker volume inspect dados

docker volume rm dados
```

Uso:

```bash
docker run -d \
  -v dados:/var/lib/postgresql/data \
  postgres:17
```

---

## Redes

```bash
docker network ls

docker network create backend

docker network inspect backend

docker network connect backend web

docker network disconnect backend web
```

---

## Build

```bash
# Build
docker build -t minha-api:1.0.0 .

# Dockerfile específico
docker build \
  -f src/MinhaApi/Dockerfile \
  -t minha-api:1.0.0 \
  .

# Sem cache
docker build --no-cache -t minha-api .
```

---

## Tags e Registry

```bash
docker login

docker tag \
  minha-api:1.0.0 \
  usuario/minha-api:1.0.0

docker push usuario/minha-api:1.0.0

docker pull usuario/minha-api:1.0.0
```

---

## Estatísticas e inspeção

```bash
docker stats

docker inspect web

docker port web

docker inspect \
  --format='{{.State.ExitCode}}' \
  web
```

---

## Limpeza

```bash
docker container prune

docker image prune

docker volume prune

docker network prune

docker system prune

docker system prune -a
```

> ⚠️ Use `prune` com cuidado, principalmente com volumes.

---

# Docker Compose

## Operação básica

```bash
# Subir
docker compose up

# Background
docker compose up -d

# Rebuild
docker compose up -d --build

# Serviço específico
docker compose up -d api

# Parar e remover
docker compose down

# Remover volumes
docker compose down -v
```

---

## Status e logs

```bash
docker compose ps

docker compose logs

docker compose logs api

docker compose logs -f api

docker compose top
```

---

## Execução

```bash
docker compose exec api bash

docker compose exec api sh

docker compose run --rm api comando

docker compose restart api

docker compose stop api

docker compose start api
```

---

## Build e imagens

```bash
docker compose build

docker compose build --no-cache api

docker compose pull

docker compose push
```

---

## Validação

```bash
docker compose config

docker compose config --services
```

---

## Profiles e escala

```bash
docker compose \
  --profile dev \
  up -d

docker compose up -d --scale api=3
```

---

## Arquivos múltiplos

```bash
docker compose \
  -f compose.yaml \
  -f compose.prod.yaml \
  up -d
```

---

# Kubernetes

## Cluster e contexto

```bash
kubectl cluster-info

kubectl get nodes

kubectl config get-contexts

kubectl config current-context

kubectl config use-context CONTEXTO
```

---

## Namespaces

```bash
kubectl get namespaces

kubectl create namespace dev

kubectl get pods -n dev

kubectl config set-context \
  --current \
  --namespace=dev
```

---

## Recursos

```bash
# Tudo
kubectl get all

# Pods
kubectl get pods

# Mais detalhes
kubectl get pods -o wide

# Deployments
kubectl get deployments

# Services
kubectl get services

# ConfigMaps
kubectl get configmaps

# Secrets
kubectl get secrets

# Ingress
kubectl get ingress

# Volumes
kubectl get pv
kubectl get pvc
```

---

## Aplicar e excluir

```bash
kubectl apply -f arquivo.yaml

kubectl delete -f arquivo.yaml

kubectl delete pod POD

kubectl delete deployment API
```

---

## Diagnóstico

```bash
kubectl describe pod POD

kubectl logs POD

kubectl logs -f POD

kubectl logs POD --previous

kubectl logs POD -c CONTAINER

kubectl get events \
  --sort-by=.metadata.creationTimestamp
```

---

## Exec e port-forward

```bash
kubectl exec -it POD -- bash

kubectl exec -it POD -- sh

kubectl port-forward \
  deployment/minha-api \
  8080:8080
```

---

## Deployments

```bash
# Criar
kubectl create deployment nginx \
  --image=nginx

# Escalar
kubectl scale deployment nginx \
  --replicas=3

# Atualizar imagem
kubectl set image \
  deployment/minha-api \
  api=minha-api:2.0.0

# Reiniciar
kubectl rollout restart \
  deployment/minha-api

# Status
kubectl rollout status \
  deployment/minha-api

# Histórico
kubectl rollout history \
  deployment/minha-api

# Rollback
kubectl rollout undo \
  deployment/minha-api
```

---

## Services

```bash
kubectl expose deployment nginx \
  --type=NodePort \
  --port=80

kubectl get svc

kubectl get endpoints
```

---

## ConfigMaps e Secrets

```bash
# ConfigMap
kubectl create configmap api-config \
  --from-literal=AMBIENTE=DEV

# Secret
kubectl create secret generic api-secret \
  --from-literal=SENHA=valor

# Ver YAML
kubectl get configmap api-config -o yaml

kubectl get secret api-secret -o yaml
```

> ⚠️ Secrets exibidos em YAML podem revelar valores codificados em Base64.

---

## Autoscaling

```bash
kubectl autoscale deployment api \
  --cpu-percent=70 \
  --min=2 \
  --max=10

kubectl get hpa
```

---

# Azure CLI

## Autenticação e conta

```bash
az version

az login

az logout

az account list -o table

az account show -o table

az account set \
  --subscription "NOME_OU_ID"
```

---

## Regiões

```bash
az account list-locations \
  --query "[].{Nome:name,Descricao:displayName}" \
  -o table
```

---

## Resource Groups

```bash
az group create \
  --name rg-devops \
  --location brazilsouth

az group list -o table

az group show \
  --name rg-devops

az group exists \
  --name rg-devops

az group delete \
  --name rg-devops \
  --yes \
  --no-wait
```

---

## Recursos

```bash
az resource list -o table

az resource list \
  --resource-group rg-devops \
  -o table
```

---

## Máquinas virtuais

```bash
az vm list \
  --show-details \
  -o table

az vm create \
  --resource-group rg-devops \
  --name vm-linux \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys

az vm start \
  -g rg-devops \
  -n vm-linux

az vm stop \
  -g rg-devops \
  -n vm-linux

az vm deallocate \
  -g rg-devops \
  -n vm-linux

az vm restart \
  -g rg-devops \
  -n vm-linux

az vm delete \
  -g rg-devops \
  -n vm-linux \
  --yes
```

---

## Storage

```bash
az storage account create \
  --name storageunico123 \
  --resource-group rg-devops \
  --location brazilsouth \
  --sku Standard_LRS \
  --kind StorageV2

az storage account list -o table

az storage container create \
  --name arquivos \
  --account-name storageunico123 \
  --auth-mode login

az storage blob upload \
  --account-name storageunico123 \
  --container-name arquivos \
  --name arquivo.txt \
  --file arquivo.txt \
  --auth-mode login
```

---

## ACR

```bash
az acr create \
  --resource-group rg-devops \
  --name acrdevops123 \
  --sku Basic

az acr list -o table

az acr login \
  --name acrdevops123

az acr build \
  --registry acrdevops123 \
  --image minha-api:1.0.0 \
  .

az acr repository list \
  --name acrdevops123 \
  -o table

az acr repository show-tags \
  --name acrdevops123 \
  --repository minha-api \
  -o table
```

---

## App Service

```bash
az appservice plan create \
  --name plan-devops \
  --resource-group rg-devops \
  --sku B1 \
  --is-linux

az webapp create \
  --resource-group rg-devops \
  --plan plan-devops \
  --name app-devops-unico \
  --runtime "DOTNETCORE:8.0"

az webapp list -o table

az webapp restart \
  -g rg-devops \
  -n app-devops-unico

az webapp log tail \
  -g rg-devops \
  -n app-devops-unico

az webapp deploy \
  -g rg-devops \
  -n app-devops-unico \
  --src-path publish.zip \
  --type zip
```

---

## AKS

```bash
az aks create \
  --resource-group rg-devops \
  --name aks-devops \
  --node-count 2 \
  --generate-ssh-keys

az aks list -o table

az aks get-credentials \
  --resource-group rg-devops \
  --name aks-devops

az aks scale \
  --resource-group rg-devops \
  --name aks-devops \
  --node-count 3

az aks update \
  --resource-group rg-devops \
  --name aks-devops \
  --attach-acr acrdevops123

az aks delete \
  --resource-group rg-devops \
  --name aks-devops \
  --yes
```

---

## Key Vault

```bash
az keyvault create \
  --name kv-devops-unico \
  --resource-group rg-devops \
  --location brazilsouth

az keyvault secret set \
  --vault-name kv-devops-unico \
  --name DatabasePassword \
  --value "VALOR"

az keyvault secret list \
  --vault-name kv-devops-unico \
  -o table
```

---

## Consultas

```bash
az account show \
  --query "{Nome:name,ID:id}" \
  -o table

az group list \
  --query "[].{Nome:name,Regiao:location}" \
  -o table

az resource list \
  --query "[].{Nome:name,Tipo:type,Regiao:location}" \
  -o table
```

---

# CI/CD

## Comandos .NET

```bash
dotnet restore

dotnet build \
  --configuration Release \
  --no-restore

dotnet test \
  --configuration Release \
  --no-build

dotnet publish \
  src/MinhaApi/MinhaApi.csproj \
  --configuration Release \
  --output ./publish
```

---

## Docker em pipeline

```bash
docker build \
  -t registry/minha-api:"$VERSION" \
  .

docker push \
  registry/minha-api:"$VERSION"
```

---

## GitHub Actions — estrutura mínima

```yaml
name: CI

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

      - uses: actions/checkout@v4

      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: "10.0.x"

      - run: dotnet restore

      - run: >
          dotnet build
          --configuration Release
          --no-restore

      - run: >
          dotnet test
          --configuration Release
          --no-build
```

---

## Artefatos

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: app
    path: ./publish

- uses: actions/download-artifact@v4
  with:
    name: app
    path: ./publish
```

---

## Variáveis e Secrets

```yaml
env:
  APP_NAME: minha-api

run: echo "${{ env.APP_NAME }}"
```

```yaml
${{ secrets.AZURE_CLIENT_ID }}
```

---

## Dependências e condições

```yaml
needs: build
```

```yaml
if: github.ref == 'refs/heads/main'
```

```yaml
if: always()
```

```yaml
environment: production
```

---

## Azure Login

```yaml
permissions:
  id-token: write
  contents: read
```

```yaml
- uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

---

## Atualizar AKS

```bash
kubectl set image \
  deployment/minha-api \
  api=acr.azurecr.io/minha-api:"$VERSION"

kubectl rollout status \
  deployment/minha-api

kubectl rollout undo \
  deployment/minha-api
```

---

# Monitoramento

## Linux

```bash
top

htop

free -h

df -h

iostat -xz 1

iotop

uptime
```

---

## Docker

```bash
docker stats

docker logs -f CONTAINER

docker inspect CONTAINER

docker system df
```

---

## Kubernetes

```bash
kubectl get pods

kubectl top nodes

kubectl top pods

kubectl logs -f POD

kubectl describe pod POD

kubectl get events
```

> `kubectl top` depende do Metrics Server.

---

## Health Checks

```bash
curl --fail \
  http://localhost:8080/health
```

Com tempo:

```bash
curl \
  -o /dev/null \
  -s \
  -w "Status: %{http_code}\nTempo: %{time_total}s\n" \
  http://localhost:8080/health
```

---

## Prometheus e Grafana

```text
Prometheus:
http://localhost:9090

Grafana:
http://localhost:3000

RabbitMQ Management:
http://localhost:15672
```

---

## RabbitMQ

```bash
rabbitmq-diagnostics ping

rabbitmq-diagnostics status

rabbitmqctl list_queues \
  name \
  messages \
  messages_ready \
  messages_unacknowledged \
  consumers
```

---

## Redis

```bash
redis-cli ping

redis-cli info

redis-cli info memory

redis-cli client list
```

---

## PostgreSQL

```bash
pg_isready

psql \
  -h localhost \
  -p 5432 \
  -U admin \
  -d database
```

---

# Troubleshooting

## Checklist rápido

```bash
date

hostname

uptime

free -h

df -h

df -i

top

systemctl --failed

journalctl -p err -n 50
```

---

## CPU alta

```bash
top

ps aux --sort=-%cpu | head

ps -p PID -f

top -H -p PID
```

---

## Memória alta

```bash
free -h

ps aux --sort=-%mem | head

journalctl -k | grep -i oom

dmesg | grep -i "killed process"
```

---

## Disco cheio

```bash
df -h

df -i

sudo du -xhd1 / | sort -h

sudo du -sh /var/log/*

docker system df

sudo lsof +L1
```

---

## Porta ocupada

```bash
sudo lsof -i :8080

sudo ss -ltnp | grep :8080

sudo fuser 8080/tcp
```

---

## Serviço falhando

```bash
systemctl status SERVICO

journalctl -u SERVICO -n 100

systemctl is-active SERVICO

systemctl is-enabled SERVICO
```

---

## Rede

```bash
ip a

ip route

ping -c 4 HOST

dig HOST

nc -vz HOST PORTA

curl -v URL

traceroute HOST
```

---

## Docker

```bash
docker ps -a

docker logs CONTAINER

docker inspect CONTAINER

docker stats

docker network inspect REDE

docker volume inspect VOLUME
```

---

## Docker Compose

```bash
docker compose config

docker compose ps

docker compose logs

docker compose logs -f SERVICO

docker compose exec SERVICO sh
```

---

## Kubernetes

```bash
kubectl get pods -o wide

kubectl describe pod POD

kubectl logs POD

kubectl logs POD --previous

kubectl get events \
  --sort-by=.metadata.creationTimestamp

kubectl get svc

kubectl get endpoints
```

---

## API .NET

```bash
ps aux | grep dotnet

dotnet --info

dotnet --list-runtimes

ss -ltnp | grep dotnet

curl -v http://localhost:8080/health
```

---

## PostgreSQL

```bash
pg_isready \
  -h localhost \
  -p 5432

psql \
  -h localhost \
  -U admin \
  -d database
```

---

## Redis

```bash
redis-cli ping

redis-cli info memory
```

---

## RabbitMQ

```bash
rabbitmq-diagnostics ping

rabbitmq-diagnostics status

rabbitmqctl list_queues \
  name \
  messages_ready \
  messages_unacknowledged \
  consumers
```

---

# Atalhos do terminal

| Atalho   | Função                     |
| -------- | -------------------------- |
| `Ctrl+C` | Interromper comando        |
| `Ctrl+Z` | Suspender processo         |
| `Ctrl+D` | Encerrar sessão ou entrada |
| `Ctrl+L` | Limpar tela                |
| `Ctrl+A` | Início da linha            |
| `Ctrl+E` | Fim da linha               |
| `Ctrl+R` | Pesquisar histórico        |
| `Ctrl+U` | Apagar antes do cursor     |
| `Ctrl+K` | Apagar depois do cursor    |
| `Ctrl+W` | Apagar palavra anterior    |
| `Tab`    | Autocompletar              |
| `↑`      | Comando anterior           |
| `↓`      | Próximo comando            |
| `!!`     | Repetir último comando     |
| `!123`   | Executar item do histórico |

---

# Comandos perigosos

Use com atenção:

```bash
rm -rf pasta

sudo chown -R usuario:grupo /

sudo chmod -R 777 /

docker compose down -v

docker system prune -a --volumes

kubectl delete namespace producao

az group delete \
  --name GRUPO \
  --yes

git reset --hard

git clean -fd
```

Antes de executar:

```text
1. Confira o ambiente.
2. Confira o caminho.
3. Confira o nome do recurso.
4. Faça backup quando necessário.
5. Simule a operação quando possível.
```

---

# Fluxo diário recomendado

## Desenvolvimento

```bash
git pull

git switch -c feature/nova-feature

docker compose up -d

dotnet restore

dotnet build

dotnet test
```

---

## Antes do commit

```bash
git status

git diff

dotnet build \
  --configuration Release

dotnet test \
  --configuration Release
```

---

## Publicação

```bash
git add .

git commit -m "Implementa nova funcionalidade"

git push -u origin feature/nova-feature
```

---

## Diagnóstico local

```bash
docker compose ps

docker compose logs -f api

curl http://localhost:8080/health

ss -ltnp | grep 8080
```

---

# Checklist de servidor

```text
[ ] CPU normal

[ ] Memória disponível

[ ] Disco disponível

[ ] Inodes disponíveis

[ ] Serviços ativos

[ ] Portas corretas

[ ] Logs sem erros críticos

[ ] Health Checks saudáveis

[ ] Backups válidos

[ ] Certificados válidos

[ ] Containers saudáveis

[ ] Pods saudáveis

[ ] Alertas configurados
```

---

# Checklist antes do deploy

```text
[ ] Build concluído

[ ] Testes aprovados

[ ] Imagem versionada

[ ] Configurações revisadas

[ ] Secrets disponíveis

[ ] Migrações avaliadas

[ ] Health Check configurado

[ ] Estratégia de rollback definida

[ ] Ambiente correto confirmado

[ ] Monitoramento ativo
```

---

# Checklist após o deploy

```text
[ ] Aplicação iniciou

[ ] Health Check responde

[ ] Logs foram verificados

[ ] Banco está acessível

[ ] Filas estão funcionando

[ ] Cache está acessível

[ ] Endpoints principais respondem

[ ] Métricas permanecem normais

[ ] Nenhum alerta crítico foi disparado

[ ] Versão implantada foi registrada
```

---

# Resumo

Neste capítulo você encontrou uma referência rápida para:

* Navegação e arquivos
* Permissões
* Usuários e grupos
* Processos
* Redes
* Logs
* Systemd
* Bash
* Find, Grep, Sed e Awk
* SSH
* Git
* Docker
* Docker Compose
* Kubernetes
* Azure CLI
* CI/CD
* Monitoramento
* Troubleshooting

Este Cheat Sheet deve ser atualizado junto com seus estudos e com as tecnologias utilizadas nos seus projetos.

---
