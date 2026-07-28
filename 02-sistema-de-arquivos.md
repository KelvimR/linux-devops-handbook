# Capítulo 2 — Sistema de Arquivos Linux

> "No Linux, tudo é um arquivo."

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender como o sistema de arquivos funciona
- Navegar pela estrutura de diretórios
- Criar e remover arquivos
- Criar e remover diretórios
- Copiar, mover e renomear arquivos
- Trabalhar com links simbólicos e links físicos
- Localizar arquivos
- Descobrir espaço em disco
- Compreender o FHS (Filesystem Hierarchy Standard)

---

# O que é um Sistema de Arquivos?

O sistema de arquivos (Filesystem) é responsável por organizar e armazenar os dados em um disco.

Ele controla:

- Arquivos
- Diretórios
- Permissões
- Espaço em disco
- Metadados
- Links

Sem um sistema de arquivos, o sistema operacional não conseguiria localizar nenhum arquivo.

---

# O padrão FHS (Filesystem Hierarchy Standard)

A maioria das distribuições Linux segue um padrão chamado **Filesystem Hierarchy Standard (FHS)**.

Isso significa que praticamente todos os servidores Linux possuem a mesma estrutura de diretórios.

```
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

---

# Diretórios mais importantes

## /

Raiz do sistema.

Todos os arquivos estão abaixo deste diretório.

Nunca existe outro diretório acima dele.

---

## /home

Armazena os usuários.

Exemplo:

```
/home/joao

/home/maria
```

---

## /root

Diretório do usuário root.

Não confunda:

```
/

com

/root
```

---

## /etc

Arquivos de configuração.

Exemplos:

```
/etc/passwd

/etc/hosts

/etc/fstab

/etc/systemd

/etc/nginx
```

---

## /var

Arquivos que mudam constantemente.

Exemplos:

```
logs

cache

mail

spool
```

Muito utilizado para troubleshooting.

```
/var/log
```

---

## /tmp

Arquivos temporários.

Podem ser apagados automaticamente após reinicialização.

---

## /usr

Aplicações instaladas.

```
/usr/bin

/usr/lib

/usr/share
```

---

## /opt

Programas de terceiros.

Exemplo:

```
/opt/sonarqube

/opt/postman
```

---

## /dev

Dispositivos do sistema.

Exemplos:

```
/dev/sda

/dev/null

/dev/random
```

---

## /proc

Informações sobre processos.

Exemplo:

```
/proc/cpuinfo

/proc/meminfo
```

---

# Caminhos Absolutos

Começam pela raiz.

```
/home/kelvim/projetos/api
```

Sempre iniciam com:

```
/
```

---

# Caminhos Relativos

Dependem do diretório atual.

```
documentos

api

../projetos
```

---

# Comandos de Navegação

## Mostrar diretório atual

```bash
pwd
```

Resultado

```
/home/kelvim
```

---

## Listar arquivos

```bash
ls
```

---

## Lista detalhada

```bash
ls -l
```

---

## Mostrar ocultos

```bash
ls -la
```

---

## Formato legível

```bash
ls -lh
```

---

## Ordenar por data

```bash
ls -lt
```

---

## Ordenar por tamanho

```bash
ls -lS
```

---

## Árvore de diretórios

```bash
tree
```

Caso não exista:

```bash
sudo apt install tree
```

---

# Criando Arquivos

Criar arquivo vazio

```bash
touch arquivo.txt
```

Criar vários

```bash
touch a.txt b.txt c.txt
```

---

# Criando Diretórios

```bash
mkdir projetos
```

Criar múltiplos

```bash
mkdir backend frontend banco
```

Criar estrutura completa

```bash
mkdir -p empresa/api/src/controllers
```

---

# Copiar Arquivos

```bash
cp origem.txt destino.txt
```

Copiar pasta

```bash
cp -r backend backup
```

Modo verboso

```bash
cp -rv origem destino
```

---

# Mover Arquivos

Mover

```bash
mv arquivo.txt documentos/
```

Renomear

```bash
mv antigo.txt novo.txt
```

Mover vários

```bash
mv *.log logs/
```

---

# Remover Arquivos

```bash
rm arquivo.txt
```

Perguntar antes

```bash
rm -i arquivo.txt
```

---

# Remover Diretórios

```bash
rm -r pasta
```

Forçar

```bash
rm -rf pasta
```

⚠️ Muito cuidado:

```bash
rm -rf /
```

Pode destruir completamente um sistema Linux.

---

# Visualizando Arquivos

Mostrar tudo

```bash
cat arquivo.txt
```

Primeiras linhas

```bash
head arquivo.txt
```

10 primeiras

```bash
head -10 arquivo.txt
```

Últimas linhas

```bash
tail arquivo.txt
```

20 últimas

```bash
tail -20 arquivo.txt
```

Acompanhar logs

```bash
tail -f app.log
```

Visualização paginada

```bash
less arquivo.txt
```

Pesquisar dentro do less

```
/erro
```

Sair

```
q
```

---

# Contar Linhas

```bash
wc arquivo.txt
```

Somente linhas

```bash
wc -l arquivo.txt
```

Palavras

```bash
wc -w arquivo.txt
```

Caracteres

```bash
wc -m arquivo.txt
```

---

# Localizando Arquivos

Buscar nome

```bash
find . -name "*.cs"
```

Buscar diretórios

```bash
find . -type d
```

Buscar arquivos

```bash
find . -type f
```

Arquivos maiores que 500 MB

```bash
find . -size +500M
```

Arquivos modificados hoje

```bash
find . -mtime -1
```

---

# Localizando Programas

```bash
which git
```

Resultado

```
/usr/bin/git
```

Mais detalhes

```bash
whereis git
```

---

# Espaço em Disco

Mostrar discos

```bash
df -h
```

Resultado

```
Filesystem      Size Used Avail Use%
```

---

# Espaço por Diretório

```bash
du -sh .
```

Todos

```bash
du -sh *
```

Ordenado

```bash
du -sh * | sort -h
```

---

# Tipos de Arquivos

Mostrar tipo

```bash
file arquivo
```

Exemplos

```
PDF

JPEG

ASCII

ELF

ZIP
```

---

# Links

Linux possui dois tipos.

## Hard Link

Compartilha o mesmo inode.

```bash
ln arquivo.txt copia.txt
```

---

## Symbolic Link

Semelhante a um atalho.

```bash
ln -s arquivo.txt atalho.txt
```

Muito utilizado para:

- Nginx
- Apache
- Docker
- Configurações

Exemplo

```
sites-available

↓

sites-enabled
```

---

# Inodes

Cada arquivo possui um identificador único chamado inode.

Visualizar

```bash
ls -i
```

Exemplo

```
128954 arquivo.txt
```

---

# Arquivos Ocultos

Começam com ponto.

```
.gitignore

.bashrc

.profile

.env
```

Mostrar

```bash
ls -la
```

---

# Caracteres Coringa (Wildcards)

Todos arquivos txt

```bash
*.txt
```

Todos arquivos log

```bash
*.log
```

Todos iniciados com app

```bash
app*
```

Todos terminados com .conf

```bash
*.conf
```

---

# Redirecionamento

Salvar saída

```bash
ls > arquivos.txt
```

Adicionar

```bash
echo "novo" >> arquivo.txt
```

Erro

```bash
comando 2> erro.log
```

Tudo

```bash
comando > saida.log 2>&1
```

Descartar saída

```bash
comando > /dev/null
```

Descartar saída e erros

```bash
comando > /dev/null 2>&1
```

---

# Pipes

Enviar saída para outro comando

```bash
cat arquivo.txt | wc -l
```

Ordenar

```bash
cat nomes.txt | sort
```

Buscar

```bash
cat app.log | grep ERROR
```

---

# Laboratório

Crie a estrutura abaixo:

```
empresa
├── backend
├── frontend
├── banco
├── logs
└── backup
```

Passos

```bash
mkdir empresa

cd empresa

mkdir backend frontend banco logs backup

touch backend/api.cs

touch frontend/index.html

touch banco/schema.sql

touch logs/app.log

ls -la
```

Agora:

- copie `schema.sql` para `backup`;
- renomeie `api.cs` para `Program.cs`;
- crie um link simbólico chamado `ultimo-log` apontando para `logs/app.log`;
- liste todos os arquivos da árvore.

---

# Boas Práticas

✅ Utilize nomes sem espaços.

```
api-client

docker-compose

backup-2026
```

Evite

```
Meu Projeto Final
```

---

✅ Prefira letras minúsculas.

---

✅ Utilize diretórios organizados.

---

✅ Nunca utilize `rm -rf` sem conferir o caminho.

---

✅ Utilize caminhos absolutos em scripts.

---

# Erros Comuns

## No such file or directory

O caminho informado não existe.

Verifique com:

```bash
pwd

ls
```

---

## Permission denied

Você não possui permissão.

Verifique:

```bash
ls -l
```

---

## Is a directory

Tentou abrir um diretório como arquivo.

Use:

```bash
cd diretorio
```

---

## Device or resource busy

O arquivo está sendo utilizado por outro processo.

Descubra:

```bash
lsof arquivo
```

---

# Resumo

Neste capítulo você aprendeu:

- Estrutura do sistema de arquivos
- FHS
- Diretórios do Linux
- Caminhos absolutos e relativos
- Criar arquivos e diretórios
- Copiar, mover e excluir
- Visualizar arquivos
- Localizar arquivos
- Links simbólicos e hard links
- Inodes
- Espaço em disco
- Redirecionamentos
- Pipes
- Boas práticas

---