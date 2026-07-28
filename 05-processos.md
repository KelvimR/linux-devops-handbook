# Capítulo 5 — Processos no Linux

> Tudo o que está em execução no Linux é um processo. Entender como criá-los, monitorá-los e controlá-los é uma das habilidades mais importantes para administradores de sistemas, DevOps, SRE e desenvolvedores.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender o ciclo de vida de um processo
- Identificar processos em execução
- Monitorar consumo de CPU e memória
- Encerrar processos corretamente
- Trabalhar com processos em foreground e background
- Gerenciar prioridades de execução
- Utilizar Jobs
- Resolver problemas comuns em servidores Linux

---

# O que é um Processo?

Um processo é uma instância de um programa em execução.

Exemplos:

```
Google Chrome

Docker

Nginx

Redis

PostgreSQL

Java

dotnet

Python
```

Cada processo possui:

- PID
- PPID
- Usuário
- Prioridade
- Estado
- Consumo de memória
- Consumo de CPU

---

# PID

PID significa:

```
Process ID
```

Cada processo possui um identificador único.

Exemplo

```
PID 1

PID 327

PID 1845

PID 8521
```

---

# PPID

PPID significa

```
Parent Process ID
```

Todo processo nasce de outro processo.

Exemplo

```
systemd
   │
   ├── sshd
   │      └── bash
   │             └── vim
```

---

# Ver processos

```bash
ps
```

Resultado

```
PID TTY TIME CMD
```

---

# Todos os processos

```bash
ps -e
```

ou

```bash
ps -A
```

---

# Formato completo

```bash
ps -ef
```

---

# Formato BSD

```bash
ps aux
```

Muito utilizado em produção.

Exemplo

```text
USER PID %CPU %MEM COMMAND
```

---

# Procurando um processo

```bash
ps aux | grep nginx
```

Outro exemplo

```bash
ps aux | grep dotnet
```

---

# Árvore de processos

```bash
pstree
```

Caso não exista

```bash
sudo apt install psmisc
```

Mostrar PID

```bash
pstree -p
```

---

# Processo inicial

O primeiro processo do sistema é

```
systemd
```

ou

```
init
```

Verificar

```bash
ps -p 1
```

---

# Monitorando processos

## top

```bash
top
```

Mostra em tempo real

- CPU
- Memória
- Swap
- Load Average
- Processos

Sair

```
q
```

---

# htop

Mais moderno.

```bash
htop
```

Instalação

```bash
sudo apt install htop
```

Recursos

- Interface colorida
- Busca
- Ordenação
- Encerramento de processos
- Scroll

---

# Entendendo o TOP

```
PID

USER

PR

NI

VIRT

RES

SHR

S

%CPU

%MEM

TIME

COMMAND
```

---

# Estados do processo

| Estado | Significado |
|---------|-------------|
| R | Running |
| S | Sleeping |
| D | Esperando I/O |
| T | Parado |
| Z | Zombie |

---

# Zombie Process

Um processo zombie terminou sua execução, mas ainda possui uma entrada na tabela de processos.

Verificar

```bash
ps aux | grep Z
```

---

# Memória

Ver uso

```bash
free -h
```

Resultado

```
Total

Used

Free

Shared

Cache
```

---

# CPU

Quantidade de CPUs

```bash
nproc
```

Informações

```bash
lscpu
```

---

# Encerrando processos

Pelo PID

```bash
kill 1234
```

---

Forçar encerramento

```bash
kill -9 1234
```

SIGKILL.

---

Encerrar vários

```bash
kill PID1 PID2 PID3
```

---

# killall

Pelo nome

```bash
killall nginx
```

Outro exemplo

```bash
killall chrome
```

---

# pkill

Mais flexível.

```bash
pkill dotnet
```

Por usuário

```bash
pkill -u joao
```

---

# Sinais (Signals)

Ver todos

```bash
kill -l
```

Principais

| Sinal | Nome | Descrição |
|--------|------|-----------|
| 1 | SIGHUP | Reiniciar |
| 2 | SIGINT | Ctrl+C |
| 9 | SIGKILL | Força encerramento |
| 15 | SIGTERM | Encerramento elegante |
| 18 | SIGCONT | Continua execução |
| 19 | SIGSTOP | Pausa |

---

# SIGTERM

Padrão do comando

```bash
kill PID
```

Permite que a aplicação finalize corretamente.

---

# SIGKILL

```bash
kill -9 PID
```

Encerra imediatamente.

Use apenas quando necessário.

---

# Foreground

Programa ocupa o terminal.

```bash
python app.py
```

Enquanto executa, o terminal fica ocupado.

---

# Background

Adicionar

```bash
python app.py &
```

O terminal continua livre.

---

# Jobs

Ver jobs

```bash
jobs
```

Resultado

```
[1] Running
```

---

Enviar para background

Durante execução

```
Ctrl + Z
```

Depois

```bash
bg
```

---

Trazer para frente

```bash
fg
```

---

# nohup

Executar sem depender do terminal.

```bash
nohup python app.py &
```

Saída

```
nohup.out
```

Muito usado para:

- APIs
- Scripts
- Serviços temporários

---

# nice

Executar com prioridade.

```bash
nice -n 10 python app.py
```

---

# renice

Alterar prioridade

```bash
renice 5 -p PID
```

---

# Descobrindo quem usa uma porta

Porta 80

```bash
sudo lsof -i :80
```

Porta 5000

```bash
sudo lsof -i :5000
```

---

Outra opção

```bash
ss -tulpn
```

---

# Ver arquivos utilizados

```bash
lsof
```

Por processo

```bash
lsof -p PID
```

---

# Tempo de execução

```bash
time ls
```

Resultado

```
real

user

sys
```

---

# Agendando processos

Executar depois

```bash
at 18:00
```

Agendar tarefas recorrentes

```bash
crontab -e
```

(Será detalhado em um capítulo específico.)

---

# Cenários Reais

## Porta ocupada

Erro

```
Address already in use
```

Descobrir

```bash
sudo lsof -i :5000
```

Encerrar

```bash
kill PID
```

---

## API .NET travada

Descobrir

```bash
ps aux | grep dotnet
```

Finalizar

```bash
kill PID
```

---

## Docker consumindo muita CPU

Ver

```bash
docker stats
```

Relacionar com

```bash
top
```

---

## Processo travado

Primeiro

```bash
kill PID
```

Se não resolver

```bash
kill -9 PID
```

---

## Memória alta

Ver

```bash
free -h
```

Depois

```bash
top
```

Ou

```bash
htop
```

---

# Laboratório

Criar processo

```bash
sleep 300
```

Outro terminal

```bash
ps aux | grep sleep
```

Finalizar

```bash
kill PID
```

---

Executar em background

```bash
sleep 600 &
```

Ver

```bash
jobs
```

Trazer novamente

```bash
fg
```

Cancelar

```
Ctrl+C
```

---

Executar com nohup

```bash
nohup sleep 600 &
```

Verificar

```bash
ps aux | grep sleep
```

---

# Problemas Comuns

## Permission denied

Encerrar usando

```bash
sudo kill PID
```

---

## No such process

O processo já terminou.

Verificar

```bash
ps aux
```

---

## Address already in use

Encontrar processo

```bash
lsof -i :PORTA
```

---

## CPU 100%

Ver

```bash
top
```

Ou

```bash
htop
```

---

# Boas Práticas

✅ Sempre tente `kill` antes de `kill -9`.

---

✅ Utilize `htop` para análise visual.

---

✅ Nunca finalize processos sem identificar sua função.

---

✅ Monitore consumo de CPU e memória regularmente.

---

✅ Utilize `nohup` para scripts longos.

---

✅ Evite executar aplicações críticas diretamente no terminal.

---

# Cheat Sheet

```bash
# Listar processos
ps
ps aux
ps -ef

# Monitoramento
top
htop

# Buscar processo
ps aux | grep nginx

# Árvore
pstree

# Encerrar
kill PID

# Forçar
kill -9 PID

# Pelo nome
killall nginx
pkill dotnet

# Jobs
jobs
bg
fg

# Background
comando &

# Nohup
nohup comando &

# CPU
lscpu

# Memória
free -h

# Arquivos abertos
lsof

# Porta utilizada
lsof -i :5000
ss -tulpn

# Prioridade
nice
renice

# Tempo
time comando
```

---

# Resumo

Neste capítulo você aprendeu:

- O que é um processo
- PID e PPID
- Estados dos processos
- `ps`, `top` e `htop`
- `kill`, `killall` e `pkill`
- Sinais do Linux
- Foreground e Background
- Jobs
- `nohup`
- `nice` e `renice`
- `lsof`
- Troubleshooting de processos
- Boas práticas para ambientes de produção

---

# Próximo capítulo

No **Capítulo 6 — Redes no Linux**, aprenderemos:

- Modelo TCP/IP
- Interfaces de rede
- IP, Gateway e DNS
- `ip`
- `ss`
- `netstat`
- `ping`
- `traceroute`
- `dig`
- `nslookup`
- `curl`
- `wget`
- `scp`
- `rsync`
- Diagnóstico de problemas de conectividade
- Ferramentas utilizadas diariamente por profissionais de DevOps e Cloud.