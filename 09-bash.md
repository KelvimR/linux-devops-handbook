# Capítulo 9 — Bash Scripting

> Bash é a linguagem de automação padrão da maioria das distribuições Linux. Saber escrever scripts é uma habilidade essencial para profissionais de DevOps, SRE, Cloud e Administração de Sistemas.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender a estrutura de um script Bash
- Trabalhar com variáveis
- Receber parâmetros
- Utilizar operadores
- Criar condicionais
- Criar loops
- Criar funções
- Manipular arquivos
- Tratar erros
- Automatizar tarefas reais

---

# O que é Bash?

Bash significa:

```
Bourne Again SHell
```

É o interpretador de comandos mais utilizado no Linux.

Verificar versão:

```bash
bash --version
```

Verificar shell atual:

```bash
echo $SHELL
```

---

# Primeiro Script

Crie um arquivo:

```bash
nano hello.sh
```

Conteúdo:

```bash
#!/bin/bash

echo "Olá Mundo!"
```

Dar permissão:

```bash
chmod +x hello.sh
```

Executar:

```bash
./hello.sh
```

---

# Shebang

A primeira linha indica qual interpretador será utilizado.

```bash
#!/bin/bash
```

Outra opção:

```bash
#!/usr/bin/env bash
```

Essa abordagem é mais portátil.

---

# Comentários

```bash
# Comentário de uma linha
```

---

# Variáveis

```bash
nome="Kelvim"

echo $nome
```

Ou

```bash
echo "${nome}"
```

---

# Variáveis do Sistema

Usuário:

```bash
echo $USER
```

Home:

```bash
echo $HOME
```

Diretório atual:

```bash
echo $PWD
```

Shell:

```bash
echo $SHELL
```

---

# Entrada de Dados

```bash
read nome

echo $nome
```

Com mensagem:

```bash
read -p "Digite seu nome: " nome
```

Senha:

```bash
read -s -p "Senha: " senha

echo
```

---

# Constantes

```bash
readonly PI=3.14
```

---

# Parâmetros

Script:

```bash
./deploy.sh producao
```

No script:

```bash
echo $1
```

Mais parâmetros:

```bash
$2

$3
```

Quantidade:

```bash
$#
```

Todos:

```bash
$@
```

Nome do script:

```bash
$0
```

---

# Operações Matemáticas

```bash
((idade=30))

((idade++))

echo $idade
```

Ou

```bash
resultado=$((10+20))

echo $resultado
```

---

# Operadores

## Numéricos

```bash
-eq
-ne
-gt
-lt
-ge
-le
```

Exemplo:

```bash
if [ 10 -gt 5 ]
then
    echo "Maior"
fi
```

---

## Strings

```bash
=

!=

-z

-n
```

Exemplo:

```bash
if [ "$nome" = "Kelvim" ]
then
    echo "Bem-vindo"
fi
```

---

## Arquivos

```bash
-f

-d

-r

-w

-x

-e
```

Exemplo:

```bash
if [ -f appsettings.json ]
then
    echo "Existe"
fi
```

---

# IF

```bash
idade=20

if [ $idade -ge 18 ]
then
    echo "Maior de idade"
fi
```

---

# IF ELSE

```bash
if [ $idade -ge 18 ]
then
    echo "Adulto"
else
    echo "Menor"
fi
```

---

# IF ELSE IF

```bash
nota=8

if [ $nota -ge 9 ]
then
    echo "Excelente"

elif [ $nota -ge 7 ]
then
    echo "Aprovado"

else
    echo "Reprovado"
fi
```

---

# CASE

```bash
case $1 in

start)
    echo "Iniciando"
;;

stop)
    echo "Parando"
;;

restart)
    echo "Reiniciando"
;;

*)
    echo "Opção inválida"
;;

esac
```

---

# FOR

```bash
for i in 1 2 3 4 5
do
    echo $i
done
```

---

Loop estilo C

```bash
for ((i=1;i<=10;i++))
do
    echo $i
done
```

---

# FOR com arquivos

```bash
for arquivo in *.txt
do
    echo $arquivo
done
```

---

# WHILE

```bash
contador=1

while [ $contador -le 5 ]
do
    echo $contador
    ((contador++))
done
```

---

# UNTIL

```bash
contador=1

until [ $contador -gt 5 ]
do
    echo $contador
    ((contador++))
done
```

---

# Break

```bash
for i in {1..10}
do
    if [ $i -eq 5 ]
    then
        break
    fi

    echo $i
done
```

---

# Continue

```bash
for i in {1..5}
do
    if [ $i -eq 3 ]
    then
        continue
    fi

    echo $i
done
```

---

# Funções

```bash
saudacao(){

    echo "Olá"

}
```

Executar:

```bash
saudacao
```

---

Com parâmetro:

```bash
saudacao(){

    echo "Olá $1"

}

saudacao Kelvim
```

---

Retorno

```bash
somar(){

    echo $(($1+$2))

}

resultado=$(somar 10 20)

echo $resultado
```

---

# Arrays

Criar:

```bash
frutas=("Maçã" "Banana" "Uva")
```

Primeiro:

```bash
echo ${frutas[0]}
```

Todos:

```bash
echo ${frutas[@]}
```

Quantidade:

```bash
echo ${#frutas[@]}
```

---

# Manipulação de Arquivos

Existe?

```bash
if [ -f backup.zip ]
then
    echo "Existe"
fi
```

Criar:

```bash
touch teste.txt
```

Remover:

```bash
rm teste.txt
```

---

# Redirecionamentos

Sobrescrever:

```bash
echo "Teste" > arquivo.txt
```

Adicionar:

```bash
echo "Novo" >> arquivo.txt
```

---

# Pipes

```bash
cat app.log | grep ERROR
```

---

# Tratamento de Erros

Parar na primeira falha:

```bash
set -e
```

Parar em variável inexistente:

```bash
set -u
```

Debug:

```bash
set -x
```

Desativar:

```bash
set +x
```

---

# Código de Retorno

Todo comando retorna um código.

Consultar:

```bash
echo $?
```

```
0
```

Sucesso.

Qualquer outro valor:

Erro.

---

# Trap

Executar ação ao finalizar.

```bash
trap "echo Encerrando..." EXIT
```

---

# Script de Backup

```bash
#!/bin/bash

ORIGEM="/home/usuario/projetos"

DESTINO="/backup"

DATA=$(date +%Y%m%d)

tar -czf "$DESTINO/backup-$DATA.tar.gz" "$ORIGEM"

echo "Backup concluído."
```

---

# Script de Deploy (.NET)

```bash
#!/bin/bash

set -e

echo "Publicando..."

dotnet publish -c Release

echo "Copiando arquivos..."

cp -R bin/Release/net8.0/publish/* /var/www/api/

sudo systemctl restart minha-api

echo "Deploy concluído!"
```

---

# Script de Monitoramento

```bash
#!/bin/bash

uso=$(df -h / | awk 'NR==2 {print $5}')

echo "Uso do disco: $uso"
```

---

# Script para Verificar Serviço

```bash
#!/bin/bash

SERVICO="nginx"

if systemctl is-active --quiet $SERVICO
then
    echo "Serviço ativo"
else
    echo "Serviço parado"
fi
```

---

# Script para Verificar Porta

```bash
#!/bin/bash

PORTA=8080

if ss -tulpn | grep ":$PORTA" > /dev/null
then
    echo "Porta aberta"
else
    echo "Porta fechada"
fi
```

---

# Script de Limpeza

```bash
#!/bin/bash

find /tmp -type f -mtime +7 -delete

echo "Arquivos temporários removidos."
```

---

# Script para Rotacionar Logs

```bash
#!/bin/bash

ARQUIVO="/var/log/app.log"

cp "$ARQUIVO" "$ARQUIVO.$(date +%Y%m%d)"

> "$ARQUIVO"
```

---

# Laboratório

Crie um script chamado:

```bash
monitor.sh
```

Ele deve:

- Mostrar data atual
- Mostrar usuário
- Mostrar hostname
- Mostrar uso do disco
- Mostrar memória disponível
- Mostrar tempo de atividade

Solução:

```bash
#!/bin/bash

echo "===== Monitoramento ====="

date

echo

echo "Usuário: $USER"

echo "Host: $(hostname)"

echo

df -h /

echo

free -h

echo

uptime
```

---

# Problemas Comuns

## Permission denied

```bash
chmod +x script.sh
```

---

## bad interpreter

Verifique o Shebang.

```bash
#!/bin/bash
```

ou

```bash
#!/usr/bin/env bash
```

---

## command not found

Verifique:

```bash
which comando
```

---

## syntax error near unexpected token

Confira:

- `if`
- `then`
- `fi`
- `do`
- `done`
- aspas
- parênteses

---

# Boas Práticas

✅ Sempre utilize:

```bash
set -e
```

---

✅ Utilize nomes descritivos para variáveis.

---

✅ Coloque comentários apenas quando agregarem contexto.

---

✅ Utilize funções para reutilizar código.

---

✅ Sempre coloque variáveis entre aspas.

Exemplo:

```bash
"$ARQUIVO"
```

---

✅ Utilize ShellCheck para validar scripts.

Instalação:

```bash
sudo apt install shellcheck
```

Analisar:

```bash
shellcheck deploy.sh
```

---

# Cheat Sheet

```bash
# Executar
./script.sh

# Permissão
chmod +x script.sh

# Debug
bash -x script.sh

# Variáveis
echo $VAR

# Entrada
read nome

# IF
if [ condição ]; then fi

# FOR
for i in {1..10}; do done

# WHILE
while condição; do done

# CASE
case $1 in esac

# Função
minha_funcao(){ }

# Código retorno
echo $?

# Debug
set -x

# Parar em erro
set -e

# ShellCheck
shellcheck script.sh
```

---

# Resumo

Neste capítulo você aprendeu:

- Estrutura de um script Bash
- Variáveis
- Parâmetros
- Operadores
- Condicionais
- Loops
- Funções
- Arrays
- Manipulação de arquivos
- Tratamento de erros
- Scripts de backup, deploy e monitoramento
- Debug de scripts
- Boas práticas para automação

---
