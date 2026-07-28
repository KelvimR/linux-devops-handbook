# Capítulo 6 — Redes no Linux

> Redes são a base de praticamente toda aplicação moderna. APIs, bancos de dados, Docker, Kubernetes, SSH e serviços em nuvem dependem de comunicação pela rede.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Compreender o modelo TCP/IP
- Identificar interfaces de rede
- Configurar IPs
- Consultar rotas
- Diagnosticar problemas de conectividade
- Trabalhar com DNS
- Testar APIs utilizando curl
- Copiar arquivos utilizando SSH
- Entender portas TCP e UDP
- Resolver problemas comuns em servidores Linux

---

# O que é uma Rede?

Uma rede permite que computadores troquem informações.

Exemplos:

- Internet
- Rede da empresa
- Cluster Kubernetes
- Containers Docker
- Máquinas Virtuais

---

# Modelo TCP/IP

O modelo TCP/IP possui quatro camadas.

```
+----------------------+
| Aplicação            |
+----------------------+
| Transporte           |
+----------------------+
| Internet             |
+----------------------+
| Acesso à Rede        |
+----------------------+
```

---

# Protocolos mais utilizados

| Protocolo | Porta Padrão |
|-----------|-------------:|
| HTTP | 80 |
| HTTPS | 443 |
| SSH | 22 |
| FTP | 21 |
| SMTP | 25 |
| DNS | 53 |
| PostgreSQL | 5432 |
| MySQL | 3306 |
| SQL Server | 1433 |
| MongoDB | 27017 |
| Redis | 6379 |
| RabbitMQ | 5672 |
| Kubernetes API | 6443 |

---

# Descobrindo seu endereço IP

```bash
ip addr
```

ou

```bash
ip a
```

Resultado

```text
2: eth0:
inet 192.168.1.25/24
```

---

# Apenas o IP

```bash
hostname -I
```

---

# Informações detalhadas

```bash
ip address show
```

---

# Interfaces de Rede

Listar interfaces

```bash
ip link
```

Exemplo

```
lo

eth0

ens33

wlan0

docker0
```

---

# Ativar interface

```bash
sudo ip link set eth0 up
```

---

# Desativar interface

```bash
sudo ip link set eth0 down
```

---

# Descobrindo a rota padrão

```bash
ip route
```

Resultado

```text
default via 192.168.1.1
```

---

# Gateway

Consultar

```bash
ip route
```

---

# Adicionar rota

```bash
sudo ip route add 192.168.10.0/24 via 192.168.1.1
```

---

# Remover rota

```bash
sudo ip route del 192.168.10.0/24
```

---

# DNS

Consultar

```bash
cat /etc/resolv.conf
```

Exemplo

```text
nameserver 8.8.8.8
```

---

# Testando conectividade

```bash
ping google.com
```

Enviar apenas quatro pacotes

```bash
ping -c 4 google.com
```

---

# Ping para IP

```bash
ping 8.8.8.8
```

Se funciona por IP, mas não por nome, provavelmente existe problema de DNS.

---

# Traceroute

Descobrir o caminho até um servidor.

Ubuntu

```bash
sudo apt install traceroute
```

Executar

```bash
traceroute google.com
```

---

# MTR

Ferramenta que combina ping e traceroute.

```bash
mtr google.com
```

Instalação

```bash
sudo apt install mtr
```

---

# Consultando DNS

## nslookup

```bash
nslookup google.com
```

---

## dig

```bash
dig google.com
```

Consultar registro MX

```bash
dig gmail.com MX
```

Consultar registro A

```bash
dig openai.com A
```

Consultar registro TXT

```bash
dig google.com TXT
```

---

# Resolvendo nomes

```bash
host google.com
```

---

# Verificando portas abertas

```bash
ss -tulpn
```

Resultado

```
Netid

Local Address

Port

Process
```

---

# Filtrar uma porta

```bash
ss -tulpn | grep 5432
```

---

# netstat

Algumas distribuições ainda utilizam.

```bash
netstat -tulpn
```

Instalar

```bash
sudo apt install net-tools
```

---

# Descobrindo quem utiliza uma porta

```bash
sudo lsof -i :8080
```

Outro exemplo

```bash
sudo lsof -i :5000
```

---

# Testando APIs

## curl

Requisição GET

```bash
curl https://api.github.com
```

---

Mostrar apenas cabeçalhos

```bash
curl -I https://google.com
```

---

Ver resposta completa

```bash
curl -v https://google.com
```

---

Ignorar certificado (apenas para testes)

```bash
curl -k https://localhost
```

---

Requisição POST

```bash
curl -X POST https://api.exemplo.com/login \
-H "Content-Type: application/json" \
-d '{"usuario":"admin","senha":"123"}'
```

---

Enviar Bearer Token

```bash
curl https://api.exemplo.com \
-H "Authorization: Bearer TOKEN"
```

---

Salvar resposta

```bash
curl https://site.com > resposta.html
```

---

# Download de arquivos

## wget

```bash
wget https://site.com/arquivo.zip
```

Salvar com outro nome

```bash
wget -O backup.zip https://site.com/arquivo.zip
```

---

## curl

```bash
curl -O https://site.com/arquivo.zip
```

---

# SSH

Conectar

```bash
ssh usuario@192.168.1.20
```

Especificar porta

```bash
ssh -p 2222 usuario@host
```

Usar chave privada

```bash
ssh -i chave.pem ubuntu@IP
```

---

# SCP

Copiar arquivo

```bash
scp arquivo.txt usuario@IP:/home/usuario
```

Copiar diretório

```bash
scp -r projeto usuario@IP:/home/usuario
```

---

# RSYNC

Muito utilizado em backups e deploys.

```bash
rsync -av origem destino
```

Servidor remoto

```bash
rsync -av projeto usuario@IP:/var/www
```

---

# Arquivo Hosts

Editar

```bash
sudo nano /etc/hosts
```

Exemplo

```
127.0.0.1 api.local
```

---

# Nome da máquina

Consultar

```bash
hostname
```

Alterar

```bash
sudo hostnamectl set-hostname servidor01
```

---

# Firewall (UFW)

Status

```bash
sudo ufw status
```

Ativar

```bash
sudo ufw enable
```

Permitir SSH

```bash
sudo ufw allow ssh
```

Permitir porta

```bash
sudo ufw allow 8080
```

Remover regra

```bash
sudo ufw delete allow 8080
```

---

# OpenSSL

Ver certificado

```bash
openssl s_client -connect google.com:443
```

Ver validade

```bash
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
```

---

# Cenários Reais

## API não responde

Verificar

```bash
ss -tulpn
```

Depois

```bash
curl http://localhost:5000
```

---

## Porta ocupada

```bash
sudo lsof -i :5000
```

Encerrar

```bash
kill PID
```

---

## Docker não acessa Internet

Verificar DNS

```bash
cat /etc/resolv.conf
```

---

## Erro de DNS

Testar

```bash
nslookup google.com
```

Depois

```bash
dig google.com
```

---

## Servidor inacessível

Executar

```bash
ping servidor
```

Depois

```bash
traceroute servidor
```

---

## Erro SSL

Testar

```bash
openssl s_client -connect dominio:443
```

---

# Laboratório

Consultar IP

```bash
ip a
```

Consultar rota

```bash
ip route
```

Testar Internet

```bash
ping google.com
```

Consultar DNS

```bash
dig github.com
```

Ver portas

```bash
ss -tulpn
```

Testar API pública

```bash
curl https://api.github.com
```

Baixar arquivo

```bash
wget https://speed.hetzner.de/1MB.bin
```

---

# Problemas Comuns

## Connection refused

O serviço não está em execução ou não está escutando na porta esperada.

Verifique

```bash
ss -tulpn
```

---

## No route to host

Problema de rota ou firewall.

Verifique

```bash
ip route
```

---

## Temporary failure in name resolution

Problema de DNS.

Verifique

```bash
cat /etc/resolv.conf
```

---

## Connection timed out

Firewall, rota incorreta ou serviço indisponível.

---

# Boas Práticas

✅ Utilize `ss` em vez de `netstat` em distribuições modernas.

✅ Teste APIs com `curl` antes de depurar a aplicação.

✅ Utilize `rsync` para sincronização de arquivos.

✅ Não desabilite o firewall sem necessidade.

✅ Utilize autenticação por chave SSH.

✅ Monitore portas expostas em servidores.

---

# Cheat Sheet

```bash
# Interfaces
ip a
ip link

# Rotas
ip route

# IP
hostname -I

# Ping
ping google.com
ping -c 4 google.com

# DNS
dig google.com
nslookup google.com
host google.com

# Portas
ss -tulpn
netstat -tulpn
lsof -i :8080

# API
curl https://api.github.com
curl -I https://google.com
curl -v https://google.com

# Download
wget URL
curl -O URL

# SSH
ssh usuario@IP
ssh -i chave.pem usuario@IP

# SCP
scp arquivo usuario@IP:/destino

# RSYNC
rsync -av origem destino

# Firewall
sudo ufw status
sudo ufw allow 22

# Hostname
hostname
hostnamectl
```

---

# Resumo

Neste capítulo você aprendeu:

- Fundamentos de redes no Linux
- Modelo TCP/IP
- Interfaces de rede
- Endereçamento IP
- Rotas e Gateway
- DNS
- Ferramentas de diagnóstico (`ping`, `traceroute`, `mtr`)
- Testes de APIs com `curl`
- Download de arquivos com `wget`
- Transferência de arquivos com `scp` e `rsync`
- Gerenciamento de firewall com `ufw`
- Diagnóstico de certificados SSL
- Boas práticas para ambientes de produção

---
