# Capítulo 8 — Gerenciamento de Serviços com Systemd

> O **systemd** é o sistema de inicialização (init system) utilizado pela maioria das distribuições Linux modernas. Ele é responsável por iniciar o sistema operacional, controlar serviços, registrar logs e automatizar tarefas.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender o funcionamento do systemd
- Gerenciar serviços utilizando `systemctl`
- Criar serviços personalizados
- Configurar inicialização automática
- Trabalhar com Targets
- Criar Timers
- Diagnosticar falhas em serviços
- Integrar aplicações .NET, Docker e Nginx ao systemd

---

# O que é o Systemd?

O **systemd** é o primeiro processo iniciado pelo Linux.

Seu PID é sempre:

```bash
1
```

Verificar:

```bash
ps -p 1
```

Resultado:

```text
PID TTY          TIME CMD
1 ?        00:00:02 systemd
```

---

# Responsabilidades

O systemd controla:

- Inicialização do Linux
- Serviços
- Montagem de discos
- Logs (journalctl)
- Timers
- Sessões de usuários
- Dependências entre serviços

---

# O que é um Serviço?

Um serviço é um processo executado em segundo plano (daemon).

Exemplos:

- nginx
- docker
- ssh
- postgresql
- redis
- rabbitmq
- mongod
- kestrel (.NET)

---

# O comando systemctl

É a principal ferramenta para administrar serviços.

Sintaxe:

```bash
systemctl [comando] [serviço]
```

---

# Verificar status

```bash
systemctl status nginx
```

Resultado:

```text
● nginx.service - nginx web server
Active: active (running)
```

---

# Iniciar serviço

```bash
sudo systemctl start nginx
```

---

# Parar serviço

```bash
sudo systemctl stop nginx
```

---

# Reiniciar

```bash
sudo systemctl restart nginx
```

---

# Recarregar configuração

```bash
sudo systemctl reload nginx
```

Utilize quando o serviço suporta recarga sem reiniciar.

---

# Verificar se está ativo

```bash
systemctl is-active nginx
```

Resultado:

```text
active
```

---

# Verificar se inicia automaticamente

```bash
systemctl is-enabled nginx
```

---

# Habilitar inicialização automática

```bash
sudo systemctl enable nginx
```

---

# Desabilitar

```bash
sudo systemctl disable nginx
```

---

# Listar serviços

Todos:

```bash
systemctl list-units
```

Somente serviços:

```bash
systemctl list-units --type=service
```

---

Somente ativos:

```bash
systemctl list-units --type=service --state=running
```

---

Serviços instalados:

```bash
systemctl list-unit-files
```

---

# Targets

Os Targets substituem os antigos Runlevels.

| Runlevel | Target |
|-----------|---------------------------|
| 0 | poweroff.target |
| 1 | rescue.target |
| 3 | multi-user.target |
| 5 | graphical.target |
| 6 | reboot.target |

---

Ver Target atual:

```bash
systemctl get-default
```

---

Alterar para modo texto:

```bash
sudo systemctl set-default multi-user.target
```

---

Modo gráfico:

```bash
sudo systemctl set-default graphical.target
```

---

# Reiniciar sistema

```bash
sudo systemctl reboot
```

---

# Desligar

```bash
sudo systemctl poweroff
```

---

# Suspender

```bash
sudo systemctl suspend
```

---

# Hibernar

```bash
sudo systemctl hibernate
```

---

# Criando um Serviço

Os arquivos ficam em:

```text
/etc/systemd/system/
```

Criar:

```bash
sudo nano /etc/systemd/system/minha-api.service
```

Conteúdo:

```ini
[Unit]
Description=Minha API .NET
After=network.target

[Service]
WorkingDirectory=/opt/minha-api
ExecStart=/usr/bin/dotnet MinhaApi.dll
Restart=always
RestartSec=5
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
```

---

# Atualizar configuração

Sempre execute:

```bash
sudo systemctl daemon-reload
```

---

# Habilitar

```bash
sudo systemctl enable minha-api
```

---

# Iniciar

```bash
sudo systemctl start minha-api
```

---

# Verificar

```bash
systemctl status minha-api
```

---

# Logs do serviço

```bash
journalctl -u minha-api
```

Acompanhar em tempo real:

```bash
journalctl -fu minha-api
```

---

Últimas 100 linhas:

```bash
journalctl -u minha-api -n 100
```

---

# Reinício Automático

Opções comuns:

```ini
Restart=no
Restart=always
Restart=on-failure
Restart=on-abnormal
```

Mais utilizado:

```ini
Restart=always
```

---

# Dependências

Esperar rede:

```ini
After=network.target
```

Esperar Docker:

```ini
After=docker.service
```

Exigir Docker:

```ini
Requires=docker.service
```

---

# Variáveis de Ambiente

Arquivo:

```ini
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=ConnectionStrings__Default=Server=...
```

Ou:

```ini
EnvironmentFile=/etc/minha-api.env
```

---

# Timers

Os Timers substituem o cron em diversos cenários.

Arquivo:

```text
backup.timer
```

Exemplo:

```ini
[Unit]
Description=Backup Diário

[Timer]
OnCalendar=daily

[Install]
WantedBy=timers.target
```

Ativar:

```bash
sudo systemctl enable backup.timer
sudo systemctl start backup.timer
```

---

# Listar Timers

```bash
systemctl list-timers
```

---

# Editando um serviço

Nunca altere diretamente um serviço instalado por pacotes.

Crie um override:

```bash
sudo systemctl edit nginx
```

Exemplo:

```ini
[Service]
Restart=always
```

---

# Arquivos importantes

| Caminho | Descrição |
|---------|-----------|
| /etc/systemd/system | Serviços personalizados |
| /usr/lib/systemd/system | Serviços do sistema |
| /run/systemd/system | Serviços temporários |

---

# Diagnóstico

Verificar falhas:

```bash
systemctl --failed
```

---

Detalhes:

```bash
systemctl status nome-servico
```

---

Logs:

```bash
journalctl -u nome-servico
```

---

Validar arquivo:

```bash
systemd-analyze verify minha-api.service
```

---

Tempo de inicialização

```bash
systemd-analyze
```

Exemplo:

```text
Startup finished in 4.7s
```

---

Serviços mais lentos:

```bash
systemd-analyze blame
```

---

Dependências:

```bash
systemd-analyze critical-chain
```

---

# Cenários Reais

## API .NET não inicia

Verificar:

```bash
systemctl status minha-api
```

Depois:

```bash
journalctl -u minha-api
```

---

## Docker inicia depois da API

Adicionar:

```ini
After=docker.service
Requires=docker.service
```

---

## Serviço reinicia continuamente

Verificar:

```bash
journalctl -u servico
```

Normalmente ocorre por:

- DLL inexistente
- Caminho incorreto
- Porta ocupada
- Permissões
- Variáveis de ambiente ausentes

---

## Alterou o arquivo .service

Executar:

```bash
sudo systemctl daemon-reload
```

Depois:

```bash
sudo systemctl restart servico
```

---

## Serviço não inicia no boot

Verificar:

```bash
systemctl is-enabled servico
```

Caso necessário:

```bash
sudo systemctl enable servico
```

---

# Laboratório

Criar diretório:

```bash
sudo mkdir -p /opt/scripts
```

Criar script:

```bash
sudo nano /opt/scripts/hello.sh
```

Conteúdo:

```bash
#!/bin/bash

echo "Serviço executado em $(date)" >> /tmp/hello.log
```

Permissão:

```bash
sudo chmod +x /opt/scripts/hello.sh
```

Criar serviço:

```bash
sudo nano /etc/systemd/system/hello.service
```

Conteúdo:

```ini
[Unit]
Description=Hello Service

[Service]
Type=oneshot
ExecStart=/opt/scripts/hello.sh

[Install]
WantedBy=multi-user.target
```

Executar:

```bash
sudo systemctl daemon-reload
sudo systemctl start hello.service
```

Verificar:

```bash
cat /tmp/hello.log
```

---

# Problemas Comuns

## Unit not found

O arquivo `.service` não existe.

Verifique:

```bash
ls /etc/systemd/system
```

---

## Failed to start

Consultar:

```bash
journalctl -u servico
```

---

## Permission denied

Verifique:

```bash
chmod +x script.sh
```

ou

```bash
ls -l
```

---

## Exec format error

O script provavelmente não possui o interpretador correto.

Adicione:

```bash
#!/bin/bash
```

na primeira linha.

---

# Boas Práticas

✅ Utilize um usuário dedicado para aplicações.

---

✅ Nunca execute aplicações como `root`, exceto quando realmente necessário.

---

✅ Configure `Restart=on-failure` ou `Restart=always` para aplicações críticas.

---

✅ Utilize `journalctl` antes de reiniciar um serviço.

---

✅ Armazene aplicações em diretórios como:

```text
/opt
```

ou

```text
/var/www
```

---

✅ Utilize arquivos `EnvironmentFile` para variáveis sensíveis.

---

# Cheat Sheet

```bash
# Status
systemctl status nginx

# Iniciar
sudo systemctl start nginx

# Parar
sudo systemctl stop nginx

# Reiniciar
sudo systemctl restart nginx

# Recarregar configuração
sudo systemctl reload nginx

# Habilitar boot
sudo systemctl enable nginx

# Desabilitar
sudo systemctl disable nginx

# Ativo?
systemctl is-active nginx

# Inicializa no boot?
systemctl is-enabled nginx

# Listar serviços
systemctl list-units --type=service

# Serviços com falha
systemctl --failed

# Logs
journalctl -u nginx
journalctl -fu nginx

# Atualizar serviços
sudo systemctl daemon-reload

# Validar
systemd-analyze verify minha-api.service

# Tempo de boot
systemd-analyze

# Serviços lentos
systemd-analyze blame

# Timers
systemctl list-timers
```

---

# Resumo

Neste capítulo você aprendeu:

- O que é o systemd
- Como gerenciar serviços com `systemctl`
- Como habilitar e desabilitar serviços
- Como criar serviços personalizados
- Como criar Timers
- Como diagnosticar falhas
- Como visualizar logs com `journalctl`
- Como configurar aplicações .NET para iniciar automaticamente
- Boas práticas para ambientes de produção

---

# Próximo capítulo

No **Capítulo 9 — Bash Scripting**, aprenderemos:

- Variáveis
- Operadores
- Condições (`if`, `case`)
- Laços (`for`, `while`, `until`)
- Funções
- Entrada e saída de dados
- Manipulação de arquivos
- Tratamento de erros
- Scripts de automação para DevOps
- Exemplos reais de backup, deploy e monitoramento