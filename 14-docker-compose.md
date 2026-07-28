# Capítulo 14 — Docker Compose

> O **Docker Compose** permite definir e executar aplicações compostas por múltiplos containers utilizando um único arquivo YAML. Em vez de iniciar cada container manualmente, você descreve toda a infraestrutura como código.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender o funcionamento do Docker Compose
- Criar arquivos `compose.yaml`
- Trabalhar com múltiplos serviços
- Configurar redes e volumes
- Utilizar variáveis de ambiente
- Criar Health Checks
- Controlar dependências entre serviços
- Utilizar Profiles
- Criar ambientes completos para aplicações .NET

---

# O que é Docker Compose?

O Docker Compose é uma ferramenta para definir aplicações multicontainer.

Exemplo:

```
API
│
├── PostgreSQL
├── Redis
├── RabbitMQ
├── MongoDB
└── Nginx
```

Tudo pode ser iniciado com um único comando.

```bash
docker compose up
```

> **Observação:** Nas versões atuais do Docker, o comando recomendado é `docker compose` (sem hífen).

---

# Estrutura Básica

Arquivo:

```text
compose.yaml
```

Também é aceito:

```text
docker-compose.yml
```

Exemplo:

```yaml
services:

  nginx:
    image: nginx
    ports:
      - "8080:80"
```

Executar:

```bash
docker compose up
```

---

# Estrutura Completa

```yaml
services:

  api:
    build: .

  postgres:
    image: postgres:17

volumes:

networks:
```

---

# Serviço

```yaml
services:

  api:
    image: minha-api
```

---

# Nome do Container

```yaml
container_name: api
```

---

# Build

```yaml
build:
  context: .
```

Dockerfile personalizado:

```yaml
build:
  context: .
  dockerfile: Dockerfile.dev
```

---

# Imagem

```yaml
image: nginx:latest
```

Outro exemplo

```yaml
image: redis:8
```

---

# Portas

```yaml
ports:
  - "5000:8080"
```

Formato:

```
HOST:CONTAINER
```

---

# Variáveis de Ambiente

```yaml
environment:
  ASPNETCORE_ENVIRONMENT: Development
```

Mais de uma:

```yaml
environment:
  ASPNETCORE_ENVIRONMENT: Production
  ConnectionStrings__Default: Host=postgres
```

---

# Arquivo .env

Estrutura:

```text
POSTGRES_USER=admin

POSTGRES_PASSWORD=admin123
```

Compose:

```yaml
env_file:
  - .env
```

---

# Utilizando Variáveis

```yaml
environment:
  POSTGRES_USER: ${POSTGRES_USER}
  POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

---

# Volumes

Volume nomeado

```yaml
volumes:

  postgres_data:
```

Utilização

```yaml
volumes:
  - postgres_data:/var/lib/postgresql/data
```

---

# Bind Mount

```yaml
volumes:
  - ./:/app
```

---

# Redes

Criar

```yaml
networks:

  backend:
```

Utilizar

```yaml
services:

  api:
    networks:
      - backend
```

---

# Depends On

```yaml
depends_on:
  - postgres
```

Ou

```yaml
depends_on:
  postgres:
    condition: service_healthy
```

> `depends_on` controla a ordem de inicialização, mas não garante que o serviço esteja pronto. Para isso, utilize **Health Checks**.

---

# Health Check

Exemplo PostgreSQL

```yaml
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U admin"]
  interval: 10s
  timeout: 5s
  retries: 5
```

---

Redis

```yaml
healthcheck:
  test: ["CMD", "redis-cli", "ping"]
```

---

MongoDB

```yaml
healthcheck:
  test:
    [
      "CMD",
      "mongosh",
      "--eval",
      "db.adminCommand('ping')"
    ]
```

---

# Restart Policy

```yaml
restart: always
```

Outras opções

```yaml
restart: unless-stopped
```

```yaml
restart: on-failure
```

---

# Profiles

Executar somente alguns serviços.

```yaml
profiles:
  - dev
```

Executar

```bash
docker compose --profile dev up
```

---

# Logs

Todos

```bash
docker compose logs
```

Tempo real

```bash
docker compose logs -f
```

Somente API

```bash
docker compose logs api
```

---

# Containers

Listar

```bash
docker compose ps
```

---

# Subir Ambiente

```bash
docker compose up
```

Background

```bash
docker compose up -d
```

Reconstruir imagens

```bash
docker compose up --build
```

---

# Parar

```bash
docker compose down
```

Remover volumes

```bash
docker compose down -v
```

---

# Reiniciar Serviço

```bash
docker compose restart api
```

---

# Executar Comando

```bash
docker compose exec api bash
```

---

# Escalar Containers

```bash
docker compose up --scale api=3
```

---

# Exemplo Completo

```yaml
services:

  api:
    build: .
    container_name: api
    ports:
      - "5000:8080"

    environment:
      ASPNETCORE_ENVIRONMENT: Development

    depends_on:
      postgres:
        condition: service_healthy

    networks:
      - backend

  postgres:
    image: postgres:17

    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123

    volumes:
      - postgres_data:/var/lib/postgresql/data

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
      retries: 5

    networks:
      - backend

volumes:
  postgres_data:

networks:
  backend:
```

---

# Exemplo DevOps (.NET)

```text
API
│
├── PostgreSQL
├── MongoDB
├── Redis
├── RabbitMQ
├── PgAdmin
└── Nginx
```

Todos executados com:

```bash
docker compose up -d
```

---

# Trabalhando com Múltiplos Arquivos

Desenvolvimento

```text
compose.yaml
```

Produção

```text
compose.prod.yaml
```

Executar

```bash
docker compose -f compose.yaml \
-f compose.prod.yaml up
```

---

# Validar Arquivo

```bash
docker compose config
```

---

# Baixar Imagens

```bash
docker compose pull
```

---

# Construir Imagens

```bash
docker compose build
```

---

# Atualizar Containers

```bash
docker compose up -d --build
```

---

# Cenários Reais

## Ambiente .NET

```bash
docker compose up -d
```

Aplicação disponível:

```
http://localhost:5000
```

---

## Atualizar API

```bash
docker compose up -d --build api
```

---

## Reiniciar PostgreSQL

```bash
docker compose restart postgres
```

---

## Visualizar Logs

```bash
docker compose logs -f api
```

---

## Limpar Ambiente

```bash
docker compose down -v
```

---

# Laboratório

Criar:

```text
compose.yaml
```

Conteúdo

```yaml
services:

  nginx:
    image: nginx

    ports:
      - "8080:80"

  redis:
    image: redis:8
```

Executar

```bash
docker compose up -d
```

Verificar

```bash
docker compose ps
```

Parar

```bash
docker compose down
```

---

# Problemas Comuns

## Porta ocupada

Erro

```text
Bind for 0.0.0.0 failed
```

Verifique:

```bash
docker ps

ss -tulpn
```

---

## Serviço não inicia

Verificar:

```bash
docker compose logs
```

---

## Volume corrompido

Remover:

```bash
docker compose down -v
```

---

## Variável não encontrada

Verifique:

```text
.env
```

Ou

```bash
docker compose config
```

---

## Erro de sintaxe YAML

Validar:

```bash
docker compose config
```

---

# Boas Práticas

✅ Utilize `compose.yaml` como nome padrão do arquivo.

---

✅ Utilize arquivos `.env` para configurações sensíveis.

---

✅ Utilize `healthcheck` para bancos de dados e filas.

---

✅ Utilize volumes nomeados para persistência.

---

✅ Organize serviços em uma rede dedicada.

---

✅ Utilize `depends_on` junto com `healthcheck`.

---

✅ Evite utilizar `latest` em produção. Prefira versões específicas, como:

```yaml
image: postgres:17
image: redis:8
image: rabbitmq:4-management
```

---

✅ Mantenha um arquivo específico para produção (`compose.prod.yaml`).

---

# Cheat Sheet

```bash
# Subir ambiente
docker compose up
docker compose up -d

# Reconstruir imagens
docker compose up --build

# Parar
docker compose down

# Remover volumes
docker compose down -v

# Logs
docker compose logs
docker compose logs -f
docker compose logs api

# Containers
docker compose ps

# Executar comando
docker compose exec api bash

# Construir imagens
docker compose build

# Baixar imagens
docker compose pull

# Validar YAML
docker compose config

# Reiniciar serviço
docker compose restart api

# Escalar containers
docker compose up --scale api=3
```

---

# Resumo

Neste capítulo você aprendeu:

- O que é o Docker Compose
- Estrutura do arquivo `compose.yaml`
- Configuração de serviços
- Variáveis de ambiente
- Volumes e redes
- Health Checks
- Dependências entre serviços
- Profiles
- Logs e monitoramento
- Ambientes completos para aplicações .NET
- Boas práticas para desenvolvimento e produção

---
