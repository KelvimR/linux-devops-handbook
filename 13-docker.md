# Capítulo 13 — Docker

> O **Docker** revolucionou a forma como aplicações são desenvolvidas, distribuídas e executadas. Com containers, é possível garantir que uma aplicação funcione da mesma maneira em qualquer ambiente, eliminando problemas como "na minha máquina funciona".

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender o que é Docker
- Diferenciar imagens e containers
- Instalar o Docker
- Trabalhar com imagens
- Criar e gerenciar containers
- Utilizar volumes e redes
- Criar Dockerfiles
- Trabalhar com Docker Compose
- Publicar imagens no Docker Hub
- Aplicar boas práticas em ambientes de produção

---

# O que é Docker?

Docker é uma plataforma de virtualização baseada em containers.

Um container é um ambiente isolado que compartilha o kernel do sistema operacional, tornando-o muito mais leve do que uma máquina virtual.

Benefícios:

- Portabilidade
- Isolamento
- Facilidade de deploy
- Escalabilidade
- Reprodutibilidade
- Rapidez

---

# Máquina Virtual x Container

| Máquina Virtual | Container |
|-----------------|-----------|
| Possui sistema operacional completo | Compartilha o kernel do host |
| Mais pesada | Muito mais leve |
| Inicialização lenta | Inicialização rápida |
| Consome mais memória | Consome menos memória |
| Excelente isolamento | Excelente desempenho |

---

# Arquitetura do Docker

```
+---------------------------+
| Docker CLI                |
+---------------------------+
            │
            ▼
+---------------------------+
| Docker Engine (Daemon)    |
+---------------------------+
            │
            ▼
+---------------------------+
| Containers                |
| Images                    |
| Networks                  |
| Volumes                   |
+---------------------------+
```

---

# Instalação

Ubuntu

```bash
sudo apt update

sudo apt install docker.io
```

Iniciar serviço

```bash
sudo systemctl start docker
```

Habilitar no boot

```bash
sudo systemctl enable docker
```

Verificar versão

```bash
docker --version
```

---

# Executando o primeiro container

```bash
docker run hello-world
```

O Docker fará:

- Download da imagem
- Criará um container
- Executará o processo
- Encerrará o container

---

# Verificando informações

```bash
docker version
```

Detalhes do ambiente

```bash
docker info
```

---

# Trabalhando com imagens

Baixar imagem

```bash
docker pull nginx
```

Outro exemplo

```bash
docker pull redis
```

---

Listar imagens

```bash
docker images
```

ou

```bash
docker image ls
```

---

Remover imagem

```bash
docker rmi nginx
```

---

# Trabalhando com containers

Criar e executar

```bash
docker run nginx
```

Modo interativo

```bash
docker run -it ubuntu bash
```

Executar em background

```bash
docker run -d nginx
```

Nomear container

```bash
docker run -d --name meu-nginx nginx
```

---

# Portas

Publicar porta

```bash
docker run -d -p 8080:80 nginx
```

Formato

```
HOST:CONTAINER
```

Agora acessar:

```
http://localhost:8080
```

---

# Containers em execução

```bash
docker ps
```

Todos

```bash
docker ps -a
```

---

# Iniciar container

```bash
docker start meu-nginx
```

---

# Parar container

```bash
docker stop meu-nginx
```

---

# Reiniciar

```bash
docker restart meu-nginx
```

---

# Remover container

```bash
docker rm meu-nginx
```

Forçar remoção

```bash
docker rm -f meu-nginx
```

---

# Logs

Visualizar

```bash
docker logs meu-nginx
```

Tempo real

```bash
docker logs -f meu-nginx
```

Últimas linhas

```bash
docker logs --tail 100 meu-nginx
```

---

# Executar comandos

Entrar no container

```bash
docker exec -it meu-nginx bash
```

Se não existir bash

```bash
docker exec -it meu-nginx sh
```

---

Executar comando

```bash
docker exec meu-nginx ls
```

---

# Copiar arquivos

Host → Container

```bash
docker cp arquivo.txt meu-nginx:/tmp
```

Container → Host

```bash
docker cp meu-nginx:/etc/nginx/nginx.conf .
```

---

# Volumes

Criar volume

```bash
docker volume create dados
```

Listar

```bash
docker volume ls
```

Detalhes

```bash
docker volume inspect dados
```

---

Utilizar volume

```bash
docker run -d \
-v dados:/var/lib/mysql \
mysql
```

---

Bind Mount

```bash
docker run -d \
-v $(pwd):/app \
ubuntu
```

---

# Redes

Listar

```bash
docker network ls
```

Criar

```bash
docker network create backend
```

Conectar container

```bash
docker network connect backend meu-nginx
```

---

# Inspecionar

Container

```bash
docker inspect meu-nginx
```

Imagem

```bash
docker image inspect nginx
```

---

# Estatísticas

```bash
docker stats
```

---

# Limpeza

Containers parados

```bash
docker container prune
```

Imagens não utilizadas

```bash
docker image prune
```

Tudo

```bash
docker system prune
```

Tudo incluindo volumes

```bash
docker system prune -a --volumes
```

⚠️ Utilize com cuidado.

---

# Dockerfile

Exemplo para .NET

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

WORKDIR /src

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0

WORKDIR /app

COPY --from=build /app .

ENTRYPOINT ["dotnet","MinhaApi.dll"]
```

---

# Build

```bash
docker build -t minha-api .
```

---

Executar

```bash
docker run -d \
-p 5000:8080 \
--name api \
minha-api
```

---

# Variáveis de Ambiente

```bash
docker run -e ASPNETCORE_ENVIRONMENT=Production minha-api
```

Múltiplas

```bash
docker run \
-e ConnectionStrings__Default=... \
-e ASPNETCORE_ENVIRONMENT=Production \
minha-api
```

---

# Docker Compose

Arquivo

```text
docker-compose.yml
```

Exemplo

```yaml
services:

  api:
    build: .
    ports:
      - "5000:8080"

  postgres:
    image: postgres:17
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123

  redis:
    image: redis:8
```

---

Subir

```bash
docker compose up
```

Background

```bash
docker compose up -d
```

Parar

```bash
docker compose down
```

---

# Docker Hub

Login

```bash
docker login
```

Tag

```bash
docker tag minha-api usuario/minha-api:1.0
```

Enviar

```bash
docker push usuario/minha-api:1.0
```

---

# Exportar e Importar

Salvar imagem

```bash
docker save -o nginx.tar nginx
```

Importar

```bash
docker load -i nginx.tar
```

---

# Docker Inspect

IP do container

```bash
docker inspect meu-nginx
```

Apenas IP

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' meu-nginx
```

---

# Cenários Reais

## API .NET

```bash
docker compose up -d
```

Ver logs

```bash
docker logs -f api
```

---

## PostgreSQL

```bash
docker exec -it postgres psql -U admin
```

---

## Redis

```bash
docker exec -it redis redis-cli
```

---

## MongoDB

```bash
docker exec -it mongodb mongosh
```

---

## RabbitMQ

```bash
docker logs rabbitmq
```

---

# Laboratório

Baixar Nginx

```bash
docker pull nginx
```

Executar

```bash
docker run -d \
-p 8080:80 \
--name nginx-lab \
nginx
```

Verificar

```bash
docker ps
```

Entrar

```bash
docker exec -it nginx-lab bash
```

Logs

```bash
docker logs nginx-lab
```

Parar

```bash
docker stop nginx-lab
```

Remover

```bash
docker rm nginx-lab
```

---

# Problemas Comuns

## Permission denied

Adicionar usuário ao grupo Docker

```bash
sudo usermod -aG docker $USER
```

Atualizar sessão

```bash
newgrp docker
```

---

## Cannot connect to the Docker daemon

Verifique o serviço

```bash
sudo systemctl status docker
```

Iniciar

```bash
sudo systemctl start docker
```

---

## Port is already allocated

Verifique

```bash
ss -tulpn
```

Ou

```bash
docker ps
```

---

## Image not found

Execute

```bash
docker pull imagem
```

---

## Container exited

Verifique

```bash
docker logs container
```

---

# Boas Práticas

✅ Utilize imagens oficiais sempre que possível.

---

✅ Prefira imagens pequenas, como Alpine, quando compatíveis.

---

✅ Utilize Multi-stage Build para aplicações .NET.

---

✅ Não execute aplicações como `root` dentro do container.

---

✅ Utilize volumes para dados persistentes.

---

✅ Utilize variáveis de ambiente para configurações.

---

✅ Remova imagens e containers não utilizados regularmente.

---

# Cheat Sheet

```bash
# Imagens
docker images
docker pull nginx
docker rmi nginx

# Containers
docker run nginx
docker run -d --name web -p 8080:80 nginx
docker ps
docker ps -a
docker stop web
docker start web
docker restart web
docker rm web

# Logs
docker logs web
docker logs -f web

# Exec
docker exec -it web bash

# Volumes
docker volume ls
docker volume create dados

# Redes
docker network ls
docker network create backend

# Build
docker build -t minha-api .

# Compose
docker compose up -d
docker compose down

# Docker Hub
docker login
docker push usuario/imagem

# Limpeza
docker system prune
docker system prune -a --volumes

# Estatísticas
docker stats
```

---

# Resumo

Neste capítulo você aprendeu:

- Conceitos fundamentais do Docker
- Imagens e containers
- Volumes e redes
- Dockerfile
- Multi-stage Build
- Docker Compose
- Docker Hub
- Logs e inspeção
- Solução de problemas comuns
- Boas práticas para ambientes de desenvolvimento e produção

---
