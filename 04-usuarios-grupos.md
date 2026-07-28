# Capítulo 4 — Usuários e Grupos no Linux

> Todo processo no Linux é executado por um usuário e pertence a um grupo. Compreender esse modelo é essencial para administrar servidores com segurança.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender como funciona a autenticação no Linux
- Criar, modificar e remover usuários
- Criar e gerenciar grupos
- Trabalhar com sudo
- Utilizar `su` e `sudo`
- Entender os arquivos `/etc/passwd`, `/etc/shadow` e `/etc/group`
- Bloquear e desbloquear usuários
- Alterar senhas
- Resolver problemas comuns relacionados a permissões de usuários

---

# Como funciona o controle de usuários?

Cada usuário possui:

- Um nome de usuário (username)
- Um UID (User ID)
- Um grupo principal (GID)
- Um diretório Home
- Um Shell padrão

Exemplo:

```
Usuário: kelvim

UID: 1000

Grupo: developers

Home: /home/kelvim

Shell: /bin/bash
```

---

# Usuários do Sistema

Nem todos os usuários representam pessoas.

Exemplo:

```
root

www-data

postgres

mysql

redis

nginx

nobody
```

Esses usuários executam serviços do sistema.

---

# UID

Cada usuário possui um identificador numérico.

Visualizar

```bash
id
```

Resultado

```
uid=1000(kelvim)
gid=1000(kelvim)
groups=1000(kelvim),27(sudo)
```

---

# GID

Representa o grupo principal do usuário.

Exemplo

```
developers

docker

sudo
```

---

# Usuário Root

O usuário root possui:

```
UID = 0
```

Ele possui acesso total ao sistema.

Verificar

```bash
id root
```

Resultado

```
uid=0(root)
```

---

# Arquivos importantes

## /etc/passwd

Contém informações dos usuários.

Visualizar

```bash
cat /etc/passwd
```

Exemplo

```
kelvim:x:1000:1000:Kelvim:/home/kelvim:/bin/bash
```

Campos

```
Usuário

Senha (x)

UID

GID

Descrição

Home

Shell
```

---

## /etc/shadow

Armazena as senhas criptografadas.

Visualizar

```bash
sudo cat /etc/shadow
```

Exemplo

```
kelvim:$6$....
```

Somente o root pode acessar.

---

## /etc/group

Lista os grupos.

Visualizar

```bash
cat /etc/group
```

---

# Descobrindo o usuário atual

```bash
whoami
```

---

# Informações completas

```bash
id
```

---

# Sessões abertas

```bash
who
```

---

Mais detalhes

```bash
w
```

---

# Criando usuários

```bash
sudo useradd joao
```

Esse comando cria apenas o usuário.

---

Criando Home

```bash
sudo useradd -m joao
```

---

Definindo Shell

```bash
sudo useradd -m -s /bin/bash joao
```

---

Definindo comentário

```bash
sudo useradd -m -c "João Silva" joao
```

---

# Ubuntu

Normalmente utiliza-se:

```bash
sudo adduser joao
```

O sistema solicitará:

- Senha
- Nome
- Informações opcionais

---

# Definindo senha

```bash
sudo passwd joao
```

Alterar própria senha

```bash
passwd
```

---

# Bloquear usuário

```bash
sudo passwd -l joao
```

---

# Desbloquear

```bash
sudo passwd -u joao
```

---

# Alterar Shell

Ver shells disponíveis

```bash
cat /etc/shells
```

Alterar

```bash
sudo usermod -s /bin/zsh joao
```

---

# Alterar Home

```bash
sudo usermod -d /home/novo joao
```

Mover arquivos

```bash
sudo usermod -d /home/novo -m joao
```

---

# Renomear usuário

```bash
sudo usermod -l novo_nome antigo_nome
```

---

# Excluir usuário

```bash
sudo userdel joao
```

Excluir Home

```bash
sudo userdel -r joao
```

---

# Trabalhando com grupos

Criar grupo

```bash
sudo groupadd devops
```

---

Excluir grupo

```bash
sudo groupdel devops
```

---

Adicionar usuário ao grupo

```bash
sudo usermod -aG devops joao
```

Muito importante:

Sempre utilize:

```
-aG
```

Nunca apenas:

```
-G
```

Caso contrário os outros grupos serão removidos.

---

# Ver grupos

```bash
groups
```

Outro usuário

```bash
groups joao
```

---

# Grupo atual

```bash
id
```

---

# Trocar grupo atual

```bash
newgrp devops
```

---

# sudo

Permite executar comandos como administrador.

```bash
sudo apt update
```

---

# Quem pode usar sudo?

Verificar

```bash
groups
```

Se aparecer:

```
sudo
```

Você possui privilégios administrativos.

---

Adicionar usuário ao sudo

Ubuntu

```bash
sudo usermod -aG sudo joao
```

RHEL/CentOS

```bash
sudo usermod -aG wheel joao
```

---

# su

Trocar de usuário

```bash
su joao
```

Trocar para root

```bash
su -
```

ou

```bash
sudo su
```

---

# Sair do usuário

```bash
exit
```

---

# Variáveis relacionadas

Usuário atual

```bash
echo $USER
```

Home

```bash
echo $HOME
```

Shell

```bash
echo $SHELL
```

---

# Diretório Home

Criado automaticamente em

```
/home/usuario
```

Arquivos importantes

```
.bashrc

.profile

.ssh

.gitconfig
```

---

# Arquivo .bashrc

Executado sempre que um novo terminal é aberto.

Exemplo

```bash
alias ll="ls -la"
```

Atualizar

```bash
source ~/.bashrc
```

---

# Diretório .ssh

Contém

```
id_rsa

id_ed25519

authorized_keys

known_hosts
```

Gerar chave

```bash
ssh-keygen
```

---

# Cenários Reais

## Docker

Permissão negada

```
permission denied while trying to connect to the Docker daemon socket
```

Resolver

```bash
sudo usermod -aG docker $USER
```

Atualizar sessão

```bash
newgrp docker
```

---

## Deploy

Criar usuário exclusivo

```bash
sudo adduser deploy
```

Adicionar SSH

```
~/.ssh/authorized_keys
```

Sem permitir login como root.

---

## PostgreSQL

Executar comando como postgres

```bash
sudo -u postgres psql
```

---

## Nginx

Executar

```bash
sudo -u www-data comando
```

---

# Laboratório

Criar usuário

```bash
sudo adduser desenvolvedor
```

Criar grupo

```bash
sudo groupadd backend
```

Adicionar usuário

```bash
sudo usermod -aG backend desenvolvedor
```

Verificar

```bash
groups desenvolvedor
```

Trocar para ele

```bash
su - desenvolvedor
```

Voltar

```bash
exit
```

Excluir

```bash
sudo userdel -r desenvolvedor
```

---

# Problemas Comuns

## user already exists

Usuário já existe.

Verificar

```bash
cat /etc/passwd
```

---

## Permission denied

Verifique se pertence ao grupo correto.

```bash
groups
```

---

## sudo: command not found

Instalar

Ubuntu

```bash
apt install sudo
```

---

## user is not in the sudoers file

Adicionar

```bash
sudo usermod -aG sudo usuario
```

---

# Boas Práticas

✅ Nunca trabalhe diariamente como root.

---

✅ Utilize sudo apenas quando necessário.

---

✅ Crie usuários específicos para serviços.

---

✅ Utilize grupos para controlar permissões.

---

✅ Nunca compartilhe contas de usuário.

---

✅ Utilize autenticação por chave SSH em servidores.

---

✅ Desabilite login remoto do root quando possível.

---

# Cheat Sheet

```bash
# Usuário atual
whoami

# Informações
id

# Sessões
who
w

# Criar usuário
sudo adduser usuario

# Remover usuário
sudo userdel -r usuario

# Alterar senha
sudo passwd usuario

# Bloquear
sudo passwd -l usuario

# Desbloquear
sudo passwd -u usuario

# Criar grupo
sudo groupadd grupo

# Excluir grupo
sudo groupdel grupo

# Adicionar ao grupo
sudo usermod -aG grupo usuario

# Ver grupos
groups

# Trocar usuário
su -

# Executar como root
sudo comando

# Atualizar bashrc
source ~/.bashrc

# Gerar chave SSH
ssh-keygen
```

---

# Resumo

Neste capítulo você aprendeu:

- Estrutura de usuários e grupos
- UID e GID
- Arquivos `/etc/passwd`, `/etc/shadow` e `/etc/group`
- Criação e remoção de usuários
- Gerenciamento de grupos
- Uso de `sudo` e `su`
- Administração de senhas
- Organização do diretório Home
- Arquivos de configuração do usuário
- Cenários reais de administração de servidores

---
