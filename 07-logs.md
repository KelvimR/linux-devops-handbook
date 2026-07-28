# Capítulo 7 — Logs no Linux

> "Logs contam a história do sistema." Aprender a interpretar logs é uma das habilidades mais importantes para profissionais de DevOps, SRE, Cloud e Desenvolvimento.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender a importância dos logs
- Navegar pelo diretório `/var/log`
- Utilizar `journalctl`
- Monitorar logs em tempo real
- Pesquisar informações em arquivos grandes
- Trabalhar com logs de aplicações
- Entender o funcionamento do `logrotate`
- Resolver incidentes através da análise de logs

---

# O que são Logs?

Logs são registros de eventos gerados pelo sistema operacional e pelas aplicações.

Eles respondem perguntas como:

- O que aconteceu?
- Quando aconteceu?
- Quem executou?
- Qual serviço apresentou erro?
- Por que uma aplicação parou?

Sem logs, encontrar a causa de um problema pode ser extremamente difícil.

---

# Onde ficam os logs?

Na maioria das distribuições Linux os logs ficam em:

```text
/var/log
```

Visualizar

```bash
cd /var/log

ls -lh
```

---

# Principais arquivos

| Arquivo | Descrição |
|----------|-----------|
| syslog | Eventos gerais do sistema (Ubuntu/Debian) |
| messages | Eventos gerais (CentOS/RHEL) |
| auth.log | Autenticação |
| kern.log | Kernel |
| dmesg | Inicialização do Kernel |
| boot.log | Boot do sistema |
| dpkg.log | Instalação de pacotes |
| apt/history.log | Histórico do APT |
| lastlog | Último login |
| faillog | Falhas de login |

---

# Visualizando arquivos

## cat

```bash
cat /var/log/syslog
```

Para arquivos pequenos.

---

## less

```bash
less /var/log/syslog
```

Atalhos

```
↓

↑

PageDown

PageUp

G

g

/q
```

Pesquisar

```
/error
```

Próxima ocorrência

```
n
```

---

## head

Primeiras linhas

```bash
head /var/log/syslog
```

20 linhas

```bash
head -20 arquivo.log
```

---

## tail

Últimas linhas

```bash
tail /var/log/syslog
```

50 linhas

```bash
tail -50 arquivo.log
```

---

# Monitorando logs

O comando mais utilizado:

```bash
tail -f arquivo.log
```

Exemplo

```bash
tail -f /var/log/syslog
```

---

Mostrar últimas 100 linhas

```bash
tail -100 arquivo.log
```

Continuar acompanhando

```bash
tail -100f arquivo.log
```

---

# Pesquisando erros

```bash
grep ERROR app.log
```

Ignorar maiúsculas

```bash
grep -i error app.log
```

Contar ocorrências

```bash
grep ERROR app.log | wc -l
```

---

# Filtrar múltiplas palavras

```bash
grep -E "ERROR|WARN" app.log
```

---

# Excluir palavras

```bash
grep -v INFO app.log
```

---

# Pesquisar recursivamente

```bash
grep -R ERROR .
```

---

# Logs do Systemd

Sistemas modernos utilizam:

```
systemd
```

Consultar logs

```bash
journalctl
```

---

Último boot

```bash
journalctl -b
```

---

Boot anterior

```bash
journalctl -b -1
```

---

Últimas linhas

```bash
journalctl -n 50
```

---

Acompanhar em tempo real

```bash
journalctl -f
```

---

Por serviço

```bash
journalctl -u nginx
```

Outro exemplo

```bash
journalctl -u docker
```

---

Hoje

```bash
journalctl --since today
```

---

Última hora

```bash
journalctl --since "1 hour ago"
```

---

Entre datas

```bash
journalctl --since "2026-07-20" --until "2026-07-21"
```

---

Somente erros

```bash
journalctl -p err
```

---

Somente críticos

```bash
journalctl -p crit
```

---

# Logs do Kernel

Ver mensagens

```bash
dmesg
```

Mais recentes

```bash
dmesg | tail
```

Erros

```bash
dmesg | grep error
```

---

# Logs de autenticação

Ubuntu

```bash
/var/log/auth.log
```

CentOS

```bash
/var/log/secure
```

Ver tentativas

```bash
grep Failed /var/log/auth.log
```

---

# Histórico de logins

Últimos logins

```bash
last
```

Usuário específico

```bash
last kelvim
```

Usuários conectados

```bash
who
```

Detalhes

```bash
w
```

---

# Docker

Logs do container

```bash
docker logs container
```

Seguir

```bash
docker logs -f container
```

Últimas 100 linhas

```bash
docker logs --tail 100 container
```

Com timestamp

```bash
docker logs -t container
```

---

# Kubernetes

Logs do Pod

```bash
kubectl logs pod
```

Seguir

```bash
kubectl logs -f pod
```

Container específico

```bash
kubectl logs pod -c api
```

Últimas linhas

```bash
kubectl logs --tail=100 pod
```

---

# Nginx

Logs

```
/var/log/nginx/
```

Arquivos

```
access.log

error.log
```

Visualizar

```bash
tail -f /var/log/nginx/access.log
```

Erros

```bash
tail -f /var/log/nginx/error.log
```

---

# Apache

```
/var/log/apache2/
```

ou

```
/var/log/httpd/
```

---

# PostgreSQL

Ubuntu

```
/var/log/postgresql/
```

---

# MySQL

```
/var/log/mysql/
```

---

# Redis

```
/var/log/redis/
```

---

# MongoDB

```
/var/log/mongodb/
```

---

# Logrotate

Evita que logs cresçam indefinidamente.

Configuração

```text
/etc/logrotate.conf
```

Arquivos adicionais

```text
/etc/logrotate.d/
```

Executar manualmente

```bash
sudo logrotate -f /etc/logrotate.conf
```

---

# Compactando logs

```bash
gzip app.log
```

Resultado

```
app.log.gz
```

Visualizar

```bash
zcat app.log.gz
```

Pesquisar

```bash
zgrep ERROR app.log.gz
```

---

# Cenários Reais

## API .NET retornando HTTP 500

Verifique:

```bash
journalctl -u kestrel
```

ou

```bash
docker logs api
```

---

## Nginx retorna 502

Consultar

```bash
tail -f /var/log/nginx/error.log
```

---

## SSH recusando login

```bash
grep Failed /var/log/auth.log
```

---

## Docker não inicia

```bash
journalctl -u docker
```

---

## Servidor reiniciou sozinho

Consultar

```bash
journalctl -b -1
```

---

## Disco cheio

Encontrar logs grandes

```bash
du -sh /var/log/*
```

---

# Laboratório

Criar pasta

```bash
mkdir logs-lab

cd logs-lab
```

Criar arquivo

```bash
touch app.log
```

Adicionar conteúdo

```bash
echo "INFO Aplicação iniciada" >> app.log

echo "WARN Cache cheio" >> app.log

echo "ERROR Banco indisponível" >> app.log
```

Pesquisar

```bash
grep ERROR app.log
```

Monitorar

```bash
tail -f app.log
```

Em outro terminal

```bash
echo "INFO Nova conexão" >> app.log
```

---

# Problemas Comuns

## Arquivo não encontrado

```text
No such file or directory
```

Verifique

```bash
pwd

ls
```

---

## Permission denied

Utilize

```bash
sudo
```

Quando necessário.

---

## journalctl vazio

Verifique se o serviço utiliza o `systemd`.

---

## Arquivo muito grande

Utilize

```bash
less
```

ou

```bash
tail
```

Nunca:

```bash
cat arquivo_gigante.log
```

---

# Boas Práticas

✅ Utilize `tail -f` para monitoramento em tempo real.

---

✅ Pesquise utilizando `grep` antes de abrir arquivos enormes.

---

✅ Nunca exclua logs antes de investigar um incidente.

---

✅ Configure `logrotate` em aplicações próprias.

---

✅ Mantenha timestamps consistentes (UTC em servidores distribuídos).

---

✅ Utilize níveis de log (`INFO`, `WARN`, `ERROR`, `DEBUG`) corretamente.

---

# Cheat Sheet

```bash
# Ver logs
cat arquivo.log
less arquivo.log

# Últimas linhas
tail arquivo.log
tail -f arquivo.log

# Primeiras linhas
head arquivo.log

# Pesquisar
grep ERROR arquivo.log
grep -i error arquivo.log

# Journal
journalctl
journalctl -f
journalctl -u nginx
journalctl -b

# Kernel
dmesg

# Login
last
who
w

# Docker
docker logs container
docker logs -f container

# Kubernetes
kubectl logs pod
kubectl logs -f pod

# Espaço
du -sh /var/log/*

# Logrotate
logrotate -f /etc/logrotate.conf

# Compactados
zcat arquivo.gz
zgrep ERROR arquivo.gz
```

---

# Resumo

Neste capítulo você aprendeu:

- Estrutura de logs do Linux
- Diretório `/var/log`
- Ferramentas `cat`, `less`, `head` e `tail`
- Pesquisa com `grep`
- Uso do `journalctl`
- Logs do Kernel (`dmesg`)
- Logs de autenticação
- Logs do Docker, Kubernetes, Nginx e bancos de dados
- Rotação de logs com `logrotate`
- Diagnóstico de incidentes utilizando logs
- Boas práticas de monitoramento

---

# Próximo capítulo

No **Capítulo 8 — Gerenciamento de Serviços com Systemd**, aprenderemos:

- O que é o `systemd`
- Conceitos de Units
- Targets
- Serviços (`.service`)
- Timers
- Inicialização do sistema
- `systemctl`
- Habilitar e desabilitar serviços
- Criar serviços personalizados
- Troubleshooting de serviços em produção
- Integração com aplicações .NET, Docker e Nginx