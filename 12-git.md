# Capítulo 12 — Git

> O **Git** é o sistema de controle de versão mais utilizado no mundo. Ele permite acompanhar alterações em arquivos, trabalhar em equipe, manter histórico completo do projeto e realizar integrações com plataformas como GitHub, GitLab e Azure DevOps.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender o funcionamento do Git
- Criar repositórios
- Trabalhar com commits
- Utilizar branches
- Resolver conflitos
- Trabalhar com repositórios remotos
- Utilizar GitHub no dia a dia
- Aplicar boas práticas em projetos profissionais

---

# O que é o Git?

Git é um Sistema de Controle de Versão Distribuído (DVCS - Distributed Version Control System).

Ele permite:

- Controlar alterações no código
- Recuperar versões anteriores
- Trabalhar em equipe
- Criar ramificações (branches)
- Integrar pipelines de CI/CD

---

# Instalação

## Ubuntu

```bash
sudo apt update

sudo apt install git
```

## Verificar instalação

```bash
git --version
```

Resultado

```text
git version 2.x.x
```

---

# Primeira Configuração

Definir nome

```bash
git config --global user.name "Kelvim Rodrigues"
```

Definir e-mail

```bash
git config --global user.email "seuemail@email.com"
```

Visualizar configurações

```bash
git config --list
```

Arquivo de configuração global

```text
~/.gitconfig
```

---

# Criando um Repositório

Criar pasta

```bash
mkdir meu-projeto

cd meu-projeto
```

Inicializar

```bash
git init
```

Resultado

```text
Initialized empty Git repository
```

---

# Status

Consultar alterações

```bash
git status
```

Possíveis estados:

- Untracked
- Modified
- Staged
- Committed

---

# Criando Arquivos

```bash
touch README.md

touch app.py
```

Consultar

```bash
git status
```

---

# Adicionando Arquivos

Adicionar um arquivo

```bash
git add README.md
```

Adicionar todos

```bash
git add .
```

Adicionar por extensão

```bash
git add *.md
```

---

# Commit

Criar commit

```bash
git commit -m "Adiciona README inicial"
```

---

Adicionar e commitar arquivos rastreados

```bash
git commit -am "Atualiza documentação"
```

> **Importante:** `git commit -am` não adiciona arquivos novos (untracked).

---

# Histórico

Visualizar commits

```bash
git log
```

Formato resumido

```bash
git log --oneline
```

Com gráfico

```bash
git log --oneline --graph --all
```

---

# Diferenças

Antes do commit

```bash
git diff
```

Arquivos em stage

```bash
git diff --cached
```

Entre commits

```bash
git diff HASH1 HASH2
```

---

# Ignorando Arquivos

Criar:

```text
.gitignore
```

Exemplo para .NET

```text
bin/

obj/

.vs/

*.user

*.log

appsettings.Development.json
```

---

# Remover Arquivo

Excluir do Git

```bash
git rm arquivo.txt
```

Remover apenas do repositório

```bash
git rm --cached arquivo.txt
```

---

# Renomear Arquivo

```bash
git mv antigo.txt novo.txt
```

---

# Branches

Listar

```bash
git branch
```

Criar

```bash
git branch develop
```

Trocar

```bash
git switch develop
```

Ou

```bash
git checkout develop
```

---

Criar e trocar

```bash
git switch -c feature/login
```

---

# Merge

Voltar para main

```bash
git switch main
```

Mesclar

```bash
git merge feature/login
```

---

# Deletar Branch

Local

```bash
git branch -d feature/login
```

Forçar remoção

```bash
git branch -D feature/login
```

---

# Branch Remota

Excluir

```bash
git push origin --delete feature/login
```

---

# Repositório Remoto

Adicionar origem

```bash
git remote add origin https://github.com/usuario/repositorio.git
```

Visualizar

```bash
git remote -v
```

---

# Primeiro Push

```bash
git push -u origin main
```

Próximos pushes

```bash
git push
```

---

# Atualizar Repositório

Buscar alterações

```bash
git fetch
```

Baixar e integrar

```bash
git pull
```

---

# Clonar Projeto

HTTPS

```bash
git clone https://github.com/usuario/projeto.git
```

SSH

```bash
git clone git@github.com:usuario/projeto.git
```

---

# Tags

Criar

```bash
git tag v1.0.0
```

Enviar

```bash
git push origin v1.0.0
```

Listar

```bash
git tag
```

---

# Stash

Salvar alterações temporariamente

```bash
git stash
```

Listar

```bash
git stash list
```

Restaurar

```bash
git stash pop
```

Remover

```bash
git stash drop
```

---

# Desfazendo Alterações

Descartar alterações

```bash
git restore arquivo.txt
```

Remover do stage

```bash
git restore --staged arquivo.txt
```

---

# Reset

Voltar commit

```bash
git reset --soft HEAD~1
```

Manter alterações.

---

Remover commit

```bash
git reset --hard HEAD~1
```

⚠️ Remove definitivamente as alterações locais.

---

# Revert

Criar commit desfazendo outro

```bash
git revert HASH
```

Mais seguro para projetos compartilhados.

---

# Conflitos

Exemplo

```text
<<<<<<< HEAD
Código atual
=======
Novo código
>>>>>>> feature/login
```

Resolver:

- Editar arquivo
- Remover marcadores
- Salvar

Depois

```bash
git add .

git commit
```

---

# Histórico de Arquivo

```bash
git log arquivo.cs
```

Quem alterou uma linha?

```bash
git blame Program.cs
```

---

# Buscar Texto

```bash
git grep "ConnectionString"
```

---

# Mostrar Alterações

```bash
git show HASH
```

---

# Limpeza

Arquivos não rastreados

```bash
git clean -n
```

Remover

```bash
git clean -fd
```

---

# Submódulos

Adicionar

```bash
git submodule add URL
```

Atualizar

```bash
git submodule update --init --recursive
```

---

# GitHub com SSH

Gerar chave

```bash
ssh-keygen -t ed25519
```

Adicionar chave pública ao GitHub.

Testar

```bash
ssh -T git@github.com
```

---

# Fluxo Git (Git Flow Simplificado)

```text
main
│
├── develop
│
├── feature/login
│
├── feature/api
│
├── hotfix/erro-login
│
└── release/v1.2
```

---

# Fluxo Diário

```bash
git pull

git switch -c feature/nova-feature

git add .

git commit -m "Implementa nova feature"

git push -u origin feature/nova-feature
```

Abrir Pull Request.

---

# Cenários Reais

## Corrigir erro urgente

```bash
git switch -c hotfix/login
```

Após corrigir

```bash
git commit -m "Corrige autenticação"

git push
```

---

## Recuperar arquivo removido

```bash
git restore README.md
```

---

## Encontrar quem alterou

```bash
git blame Program.cs
```

---

## Ver histórico resumido

```bash
git log --oneline --graph --all
```

---

## Publicar projeto

```bash
git init

git add .

git commit -m "Primeiro commit"

git remote add origin URL

git push -u origin main
```

---

# Laboratório

Criar projeto

```bash
mkdir git-lab

cd git-lab

git init
```

Criar README

```bash
echo "# Git Lab" > README.md
```

Adicionar

```bash
git add README.md
```

Commit

```bash
git commit -m "Primeiro commit"
```

Criar branch

```bash
git switch -c feature/teste
```

Modificar README

```bash
echo "Nova linha" >> README.md
```

Commit

```bash
git add .

git commit -m "Atualiza README"
```

Voltar

```bash
git switch main
```

Merge

```bash
git merge feature/teste
```

---

# Problemas Comuns

## Author identity unknown

Configure:

```bash
git config --global user.name "Seu Nome"

git config --global user.email "email@email.com"
```

---

## Permission denied (publickey)

Configure uma chave SSH e adicione-a ao GitHub.

Teste:

```bash
ssh -T git@github.com
```

---

## Merge conflict

Resolva os conflitos manualmente.

Depois

```bash
git add .

git commit
```

---

## Nothing to commit

Nenhuma alteração pendente.

Verifique

```bash
git status
```

---

# Boas Práticas

✅ Faça commits pequenos e frequentes.

---

✅ Escreva mensagens de commit claras.

Exemplos:

```text
Adiciona autenticação JWT

Corrige erro na API de pagamentos

Atualiza documentação Docker
```

---

✅ Nunca faça commit de:

- Senhas
- Tokens
- Chaves privadas
- Arquivos `.env`
- Certificados

---

✅ Utilize `.gitignore`.

---

✅ Atualize sua branch (`git pull`) antes de abrir um Pull Request.

---

✅ Prefira `git revert` em ambientes compartilhados em vez de `git reset --hard`.

---

# Cheat Sheet

```bash
# Inicializar
git init

# Status
git status

# Adicionar
git add .
git add arquivo

# Commit
git commit -m "Mensagem"

# Histórico
git log
git log --oneline --graph --all

# Diferenças
git diff

# Branch
git branch
git switch -c feature/api
git switch main

# Merge
git merge feature/api

# Remoto
git remote -v

# Push
git push
git push -u origin main

# Pull
git pull

# Clone
git clone URL

# Stash
git stash
git stash pop

# Restore
git restore arquivo

# Reset
git reset --soft HEAD~1

# Revert
git revert HASH

# Limpeza
git clean -fd
```

---

# Resumo

Neste capítulo você aprendeu:

- Conceitos fundamentais do Git
- Criação e gerenciamento de repositórios
- Commits e histórico
- Branches e Merge
- Repositórios remotos
- GitHub com HTTPS e SSH
- Resolução de conflitos
- Stash, Reset e Revert
- Fluxo de trabalho profissional
- Boas práticas para desenvolvimento colaborativo

---

# Próximo capítulo

No **Capítulo 13 — Docker**, aprenderemos:

- O que são containers
- Arquitetura do Docker
- Imagens e Containers
- Dockerfile
- Volumes
- Redes
- Docker Compose
- Registry (Docker Hub)
- Logs
- Boas práticas
- Deploy de aplicações .NET utilizando Docker