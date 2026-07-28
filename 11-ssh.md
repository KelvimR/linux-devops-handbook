# Capítulo 11 — SSH (Secure Shell)

> O **SSH (Secure Shell)** é o protocolo padrão para administração remota de servidores Linux. É através dele que profissionais de DevOps, Cloud, SRE e Desenvolvimento acessam servidores, realizam deploys, configuram aplicações e automatizam tarefas com segurança.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender o funcionamento do SSH
- Conectar-se a servidores remotos
- Utilizar autenticação por senha e chave SSH
- Gerenciar chaves públicas e privadas
- Copiar arquivos com SCP
- Sincronizar arquivos com RSYNC
- Criar aliases no arquivo SSH Config
- Configurar acesso seguro
- Resolver problemas comuns de conexão

---

# O que é SSH?

SSH significa:

```
Secure Shell
```

É um protocolo criptografado para:

- Acessar servidores remotamente
- Executar comandos
- Transferir arquivos
- Criar túneis de rede
- Automatizar deploys

Porta padrão:

```
22
```

---

# Verificando se o SSH está instalado

Cliente SSH:

```bash
ssh -V
```

Servidor SSH (Ubuntu):

```bash
sudo systemctl status ssh
```

Instalar servidor:

```bash
sudo apt update

sudo apt install openssh-server
```

---

# Conectando a um servidor

Sintaxe:

```bash
ssh usuario@ip
```

Exemplo:

```bash
ssh ubuntu@192.168.1.100
```

Outro exemplo:

```bash
ssh root@10.0.0.10
```

---

# Conectar utilizando uma porta diferente

```bash
ssh -p 2222 usuario@ip
```

---

# Primeiro acesso

Ao conectar pela primeira vez:

```text
The authenticity of host can't be established.

Are you sure you want to continue connecting?
```

Responder:

```text
yes
```

O servidor será salvo em:

```text
~/.ssh/known_hosts
```

---

# Gerando uma chave SSH

Algoritmo moderno:

```bash
ssh-keygen -t ed25519
```

Especificando arquivo:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/devops
```

Adicionar comentário:

```bash
ssh-keygen -t ed25519 -C "kelvim@github"
```

---

# RSA (compatibilidade)

```bash
ssh-keygen -t rsa -b 4096
```

---

# Arquivos gerados

```text
id_ed25519
```

Chave privada

```text
id_ed25519.pub
```

Chave pública

---

# Nunca compartilhe

❌ Chave privada

```
id_ed25519
```

---

✅ Pode compartilhar

```
id_ed25519.pub
```

---

# Copiar chave para servidor

Método recomendado:

```bash
ssh-copy-id usuario@ip
```

Manual:

```bash
cat ~/.ssh/id_ed25519.pub
```

Adicionar o conteúdo em:

```text
~/.ssh/authorized_keys
```

---

# Permissões corretas

Diretório:

```bash
chmod 700 ~/.ssh
```

Arquivo:

```bash
chmod 600 ~/.ssh/authorized_keys
```

Chave privada:

```bash
chmod 600 ~/.ssh/id_ed25519
```

---

# Conectar utilizando chave

```bash
ssh -i ~/.ssh/id_ed25519 usuario@ip
```

Outro exemplo:

```bash
ssh -i devops.pem ubuntu@IP_PUBLICO
```

---

# Utilizando arquivo PEM (AWS EC2)

Ajustar permissão:

```bash
chmod 400 devops.pem
```

Conectar:

```bash
ssh -i devops.pem ubuntu@IP_PUBLICO
```

---

# SSH Config

Arquivo:

```text
~/.ssh/config
```

Exemplo:

```text
Host api
    HostName 192.168.1.100
    User ubuntu
    IdentityFile ~/.ssh/id_ed25519
    Port 22
```

Agora basta executar:

```bash
ssh api
```

---

# Copiar arquivos com SCP

Enviar arquivo:

```bash
scp arquivo.txt usuario@ip:/home/usuario
```

Receber arquivo:

```bash
scp usuario@ip:/home/usuario/arquivo.txt .
```

Copiar diretório:

```bash
scp -r projeto usuario@ip:/home/usuario
```

---

# RSYNC

Mais eficiente que SCP.

Enviar projeto:

```bash
rsync -av projeto/ usuario@ip:/var/www/projeto
```

Excluir arquivos inexistentes no destino:

```bash
rsync -av --delete projeto/ usuario@ip:/var/www/projeto
```

---

# Executar comando remoto

```bash
ssh usuario@ip "hostname"
```

Outro exemplo:

```bash
ssh usuario@ip "uptime"
```

---

# Executar script remoto

```bash
ssh usuario@ip "bash -s" < deploy.sh
```

---

# Encaminhamento de portas (Port Forwarding)

Local para remoto:

```bash
ssh -L 8080:localhost:80 usuario@ip
```

Agora acessar:

```text
http://localhost:8080
```

---

# Túnel para PostgreSQL

```bash
ssh -L 5432:localhost:5432 usuario@ip
```

Conectar localmente:

```bash
psql -h localhost -p 5432
```

---

# Túnel para MongoDB

```bash
ssh -L 27017:localhost:27017 usuario@ip
```

---

# ProxyJump (Bastion Host)

```bash
ssh -J bastion usuario@servidor
```

---

# Agente SSH

Iniciar:

```bash
eval "$(ssh-agent -s)"
```

Adicionar chave:

```bash
ssh-add ~/.ssh/id_ed25519
```

Listar:

```bash
ssh-add -l
```

---

# Verificar impressão digital

```bash
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

---

# Arquivos importantes

```text
~/.ssh/
```

Contém:

```
id_ed25519

id_ed25519.pub

authorized_keys

known_hosts

config
```

---

# Configuração do servidor

Arquivo:

```text
/etc/ssh/sshd_config
```

Alterações comuns:

Desabilitar login root:

```text
PermitRootLogin no
```

Desabilitar senha:

```text
PasswordAuthentication no
```

Alterar porta:

```text
Port 2222
```

Após alterar:

```bash
sudo systemctl restart ssh
```

---

# Testando conexão

Modo detalhado:

```bash
ssh -v usuario@ip
```

Mais detalhes:

```bash
ssh -vvv usuario@ip
```

---

# Cenários Reais

## Deploy de aplicação .NET

```bash
scp -r publish/ deploy@servidor:/var/www/api
```

Depois:

```bash
ssh deploy@servidor "sudo systemctl restart minha-api"
```

---

## Conectar em EC2

```bash
chmod 400 devops.pem

ssh -i devops.pem ubuntu@IP
```

---

## Acessar PostgreSQL remoto

```bash
ssh -L 5432:localhost:5432 usuario@ip
```

Depois conectar pelo DBeaver ou psql.

---

## Acessar servidor pelo alias

Arquivo:

```text
~/.ssh/config
```

Depois:

```bash
ssh producao
```

---

## Automatizar backup

```bash
rsync -av backup/ usuario@backup:/dados
```

---

# Laboratório

Gerar chave:

```bash
ssh-keygen -t ed25519
```

Listar:

```bash
ls ~/.ssh
```

Criar configuração:

```bash
nano ~/.ssh/config
```

Adicionar:

```text
Host laboratorio
    HostName 192.168.1.100
    User ubuntu
    IdentityFile ~/.ssh/id_ed25519
```

Testar:

```bash
ssh laboratorio
```

---

# Problemas Comuns

## Permission denied (publickey)

Verifique:

- Chave correta
- `authorized_keys`
- Permissões (`700` e `600`)
- Usuário correto

---

## Host key verification failed

Remover chave antiga:

```bash
ssh-keygen -R IP_DO_SERVIDOR
```

---

## Connection refused

Verifique:

```bash
sudo systemctl status ssh
```

E confirme se a porta está aberta:

```bash
ss -tulpn | grep ssh
```

---

## Connection timed out

Verifique:

- Firewall
- Security Groups (AWS)
- IP correto
- Porta correta

---

## WARNING: UNPROTECTED PRIVATE KEY FILE!

Corrigir:

```bash
chmod 600 ~/.ssh/id_ed25519
```

Ou para arquivos `.pem`:

```bash
chmod 400 chave.pem
```

---

# Boas Práticas

✅ Utilize autenticação por chave SSH.

---

✅ Nunca compartilhe sua chave privada.

---

✅ Desabilite login por senha em servidores de produção.

---

✅ Desabilite login remoto do usuário `root`.

---

✅ Utilize usuários específicos para deploy.

---

✅ Faça backup da pasta:

```text
~/.ssh
```

---

✅ Utilize aliases no arquivo `~/.ssh/config`.

---

# Cheat Sheet

```bash
# Conectar
ssh usuario@ip

# Porta personalizada
ssh -p 2222 usuario@ip

# Gerar chave
ssh-keygen -t ed25519

# Copiar chave
ssh-copy-id usuario@ip

# Usar chave
ssh -i ~/.ssh/id_ed25519 usuario@ip

# SCP
scp arquivo.txt usuario@ip:/destino

# RSYNC
rsync -av origem/ usuario@ip:/destino

# Executar comando remoto
ssh usuario@ip "hostname"

# Agente SSH
ssh-add ~/.ssh/id_ed25519

# Debug
ssh -vvv usuario@ip

# Remover host conhecido
ssh-keygen -R IP

# Reiniciar SSH
sudo systemctl restart ssh
```

---

# Resumo

Neste capítulo você aprendeu:

- O que é o SSH
- Como conectar a servidores remotos
- Como gerar e utilizar chaves SSH
- Como configurar aliases com `~/.ssh/config`
- Como copiar arquivos com `scp`
- Como sincronizar arquivos com `rsync`
- Como criar túneis SSH
- Como configurar o servidor SSH com segurança
- Como diagnosticar problemas de conexão
- Boas práticas para ambientes de produção

---

# Próximo capítulo

No **Capítulo 12 — Docker para DevOps**, aprenderemos:

- O que são containers
- Diferença entre imagem e container
- Instalação do Docker
- Comandos essenciais
- Dockerfile
- Docker Compose
- Volumes
- Redes
- Logs
- Boas práticas
- Deploy de aplicações .NET com Docker