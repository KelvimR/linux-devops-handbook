# Capítulo 17 — CI/CD

> **CI/CD** permite automatizar validações, builds, testes e implantações. O objetivo é entregar mudanças com mais frequência, segurança, rastreabilidade e consistência.

---

# Objetivos

Ao final deste capítulo você será capaz de:

* Entender Integração Contínua
* Diferenciar entrega contínua de implantação contínua
* Criar pipelines com GitHub Actions
* Criar pipelines com Azure DevOps
* Executar restore, build e testes de projetos .NET
* Gerar artefatos de publicação
* Construir e publicar imagens Docker
* Utilizar Secrets e variáveis
* Criar ambientes de desenvolvimento, homologação e produção
* Publicar aplicações no Azure App Service
* Publicar imagens no Azure Container Registry
* Atualizar aplicações no Azure Kubernetes Service
* Implementar aprovações e estratégias de rollback
* Aplicar boas práticas de segurança

---

# O que é CI/CD?

CI/CD é um conjunto de práticas utilizadas para automatizar o processo de desenvolvimento e entrega de software.

A sigla representa:

```text
CI = Continuous Integration

CD = Continuous Delivery

CD = Continuous Deployment
```

Embora os dois últimos conceitos utilizem a mesma sigla, eles possuem significados diferentes.

---

# Continuous Integration

Continuous Integration significa:

```text
Integração Contínua
```

A cada alteração enviada ao repositório, uma automação pode executar:

```text
Restore

Build

Testes

Análise de código

Verificação de segurança

Geração de artefatos
```

Fluxo:

```text
Desenvolvedor
     │
     ▼
Git Push
     │
     ▼
Pipeline
     │
     ├── Restore
     ├── Build
     ├── Testes
     └── Validações
```

O objetivo é identificar problemas o mais cedo possível.

---

# Continuous Delivery

Continuous Delivery significa:

```text
Entrega Contínua
```

Depois que o código passa pelas validações, ele fica pronto para ser implantado.

A implantação em produção ainda depende de uma ação manual, como:

```text
Aprovação

Botão de deploy

Validação do responsável

Janela de implantação
```

Fluxo:

```text
Código
  │
  ▼
Build
  │
  ▼
Testes
  │
  ▼
Homologação
  │
  ▼
Aprovação manual
  │
  ▼
Produção
```

---

# Continuous Deployment

Continuous Deployment significa:

```text
Implantação Contínua
```

Toda alteração aprovada pelas validações é implantada automaticamente em produção.

Fluxo:

```text
Código
  │
  ▼
Build
  │
  ▼
Testes
  │
  ▼
Deploy automático
  │
  ▼
Produção
```

Essa estratégia exige:

* Testes confiáveis
* Monitoramento
* Health Checks
* Rollback automatizado
* Controle de versões
* Observabilidade
* Boa cobertura de testes

---

# Benefícios

CI/CD oferece benefícios como:

* Redução de tarefas manuais
* Entregas mais frequentes
* Padronização do processo
* Detecção antecipada de erros
* Histórico de execuções
* Rastreabilidade
* Maior segurança no deploy
* Facilidade de rollback
* Menor dependência da máquina do desenvolvedor

---

# Pipeline

Um pipeline é uma sequência automatizada de tarefas.

Exemplo:

```text
Checkout
   │
   ▼
Restore
   │
   ▼
Build
   │
   ▼
Testes
   │
   ▼
Publicação
   │
   ▼
Docker Build
   │
   ▼
Docker Push
   │
   ▼
Deploy
```

---

# Conceitos importantes

## Workflow

Representa toda a automação.

Exemplo:

```text
CI da aplicação .NET
```

---

## Trigger

Evento que inicia o pipeline.

Exemplos:

```text
Push

Pull Request

Tag

Agendamento

Execução manual
```

---

## Runner ou Agent

Máquina responsável por executar o pipeline.

Pode ser:

* Hospedada pelo GitHub
* Hospedada pelo Azure DevOps
* Self-hosted
* Linux
* Windows
* macOS

---

## Job

Conjunto de etapas executadas em uma máquina.

Exemplo:

```text
Job: build

Job: tests

Job: deploy
```

---

## Step

Uma ação individual dentro de um Job.

Exemplos:

```text
Baixar o código

Instalar o .NET

Executar testes

Criar imagem Docker
```

---

## Artifact

Arquivo gerado pelo pipeline.

Exemplos:

```text
Aplicação publicada

Arquivo ZIP

Relatório de testes

Pacote NuGet

Imagem Docker

Relatório de cobertura
```

---

## Environment

Representa um ambiente de implantação.

Exemplos:

```text
development

staging

production
```

---

# Estratégia de ambientes

Uma estrutura comum é:

```text
Desenvolvimento
      │
      ▼
Homologação
      │
      ▼
Produção
```

Exemplo de branches:

```text
feature/*
    │
    ▼
develop
    │
    ▼
release/*
    │
    ▼
main
```

Outra estratégia mais simples:

```text
Pull Request → Validação

main → Deploy em homologação

Tag → Deploy em produção
```

---

# GitHub Actions

O GitHub Actions permite criar automações dentro do próprio repositório GitHub.

Os workflows utilizam arquivos YAML com extensão `.yml` ou `.yaml` e normalmente ficam no diretório `.github/workflows`. Eles podem ser iniciados por eventos como `push`, `pull_request`, execução manual e agendamentos.

Estrutura:

```text
.github/
└── workflows/
    ├── ci.yml
    └── deploy.yml
```

---

# Primeiro Workflow

Crie:

```text
.github/workflows/ci.yml
```

Conteúdo:

```yaml
name: CI

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Mensagem
        run: echo "Pipeline executado com sucesso."
```

---

# Entendendo o Workflow

Nome:

```yaml
name: CI
```

---

Eventos:

```yaml
on:
  push:
  pull_request:
```

---

Job:

```yaml
jobs:
  build:
```

---

Runner:

```yaml
runs-on: ubuntu-latest
```

---

Etapa:

```yaml
steps:
```

---

Ação reutilizável:

```yaml
uses: actions/checkout@v4
```

---

Comando:

```yaml
run: echo "Executando"
```

---

# Triggers

## Push

```yaml
on:
  push:
```

---

## Push na main

```yaml
on:
  push:
    branches:
      - main
```

---

## Múltiplas branches

```yaml
on:
  push:
    branches:
      - main
      - develop
```

---

## Pull Request

```yaml
on:
  pull_request:
    branches:
      - main
```

---

## Tags

```yaml
on:
  push:
    tags:
      - "v*"
```

Exemplos correspondentes:

```text
v1.0.0

v2.3.1
```

---

## Execução manual

```yaml
on:
  workflow_dispatch:
```

---

## Agendamento

```yaml
on:
  schedule:
    - cron: "0 3 * * *"
```

Esse exemplo agenda uma execução diária.

> Os horários de agendamento do GitHub Actions utilizam UTC.

---

# Pipeline .NET

O GitHub Actions pode automatizar restore, build e testes de projetos .NET. A documentação oficial da Microsoft descreve esse processo como uma forma de Integração Contínua em que alterações e Pull Requests acionam a validação do código.

Exemplo:

```yaml
name: .NET CI

on:
  push:
    branches:
      - main
      - develop

  pull_request:
    branches:
      - main
      - develop

jobs:

  build-and-test:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Configurar .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: "10.0.x"

      - name: Restaurar dependências
        run: dotnet restore

      - name: Compilar
        run: dotnet build --no-restore --configuration Release

      - name: Executar testes
        run: dotnet test --no-build --configuration Release
```

---

# Pipeline para uma Solution

```yaml
env:
  SOLUTION_PATH: AirlineBookingSystem.slnx
```

Utilização:

```yaml
- name: Restaurar
  run: dotnet restore ${{ env.SOLUTION_PATH }}

- name: Compilar
  run: >
    dotnet build
    ${{ env.SOLUTION_PATH }}
    --configuration Release
    --no-restore

- name: Testar
  run: >
    dotnet test
    ${{ env.SOLUTION_PATH }}
    --configuration Release
    --no-build
```

---

# Pipeline completo .NET

```yaml
name: .NET CI

on:
  push:
    branches:
      - main
      - develop

  pull_request:
    branches:
      - main
      - develop

env:
  DOTNET_VERSION: "10.0.x"
  SOLUTION_PATH: "AirlineBookingSystem.slnx"
  BUILD_CONFIGURATION: "Release"

jobs:

  build:

    name: Build and Test

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Configurar .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }}

      - name: Exibir versão
        run: dotnet --version

      - name: Restaurar dependências
        run: >
          dotnet restore
          ${{ env.SOLUTION_PATH }}

      - name: Compilar
        run: >
          dotnet build
          ${{ env.SOLUTION_PATH }}
          --configuration ${{ env.BUILD_CONFIGURATION }}
          --no-restore

      - name: Executar testes
        run: >
          dotnet test
          ${{ env.SOLUTION_PATH }}
          --configuration ${{ env.BUILD_CONFIGURATION }}
          --no-build
          --verbosity normal
```

---

# Publicação da aplicação

```yaml
- name: Publicar aplicação
  run: >
    dotnet publish
    src/MinhaApi/MinhaApi.csproj
    --configuration Release
    --output ./publish
    --no-build
```

---

# Upload de Artifact

```yaml
- name: Enviar artefato
  uses: actions/upload-artifact@v4
  with:
    name: aplicacao-publicada
    path: ./publish
```

O artefato poderá ser utilizado por outro Job.

---

# Download do Artifact

```yaml
- name: Baixar artefato
  uses: actions/download-artifact@v4
  with:
    name: aplicacao-publicada
    path: ./publish
```

---

# Cache do NuGet

```yaml
- name: Configurar .NET
  uses: actions/setup-dotnet@v4
  with:
    dotnet-version: "10.0.x"
    cache: true
    cache-dependency-path: "**/packages.lock.json"
```

Para utilizar o cache dessa forma, mantenha arquivos:

```text
packages.lock.json
```

---

# Gerar arquivo de lock

```bash
dotnet restore --use-lock-file
```

---

# Relatório de testes

```yaml
- name: Executar testes
  run: >
    dotnet test
    --configuration Release
    --logger "trx;LogFileName=test-results.trx"
    --results-directory ./test-results
```

Enviar resultado:

```yaml
- name: Publicar resultados
  if: always()
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: ./test-results
```

---

# Cobertura de testes

```yaml
- name: Executar testes com cobertura
  run: >
    dotnet test
    --configuration Release
    --collect:"XPlat Code Coverage"
    --results-directory ./coverage
```

Enviar:

```yaml
- name: Publicar cobertura
  uses: actions/upload-artifact@v4
  with:
    name: code-coverage
    path: ./coverage
```

---

# Variáveis

Variáveis globais:

```yaml
env:
  APP_NAME: minha-api
  BUILD_CONFIGURATION: Release
```

Utilização:

```yaml
run: echo "${{ env.APP_NAME }}"
```

---

# Variáveis no Job

```yaml
jobs:

  build:

    env:
      PROJECT_PATH: src/MinhaApi/MinhaApi.csproj
```

---

# Variáveis no Step

```yaml
- name: Mostrar ambiente
  env:
    ENVIRONMENT_NAME: Development

  run: echo "$ENVIRONMENT_NAME"
```

---

# Secrets

Secrets são utilizados para armazenar informações sensíveis.

Exemplos:

```text
Tokens

Credenciais

Client Secrets

Senhas

Chaves de acesso
```

No GitHub, configure em:

```text
Settings
  └── Secrets and variables
      └── Actions
```

Utilização:

```yaml
${{ secrets.NOME_DO_SECRET }}
```

Exemplo:

```yaml
- name: Autenticar
  run: echo "${{ secrets.TOKEN }}"
```

> Evite imprimir Secrets no terminal, mesmo que a plataforma tente ocultá-los.

---

# GitHub Environments

Um Environment representa um destino de implantação.

Exemplos:

```text
development

staging

production
```

Configuração no Job:

```yaml
jobs:

  deploy:

    environment: production
```

Os Environments podem possuir:

* Secrets próprios
* Variáveis próprias
* Aprovações
* Regras de proteção
* Histórico de deployments

---

# Dependência entre Jobs

```yaml
jobs:

  build:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Build"

  deploy:
    needs: build

    runs-on: ubuntu-latest

    steps:
      - run: echo "Deploy"
```

O Job `deploy` só será executado após o Job `build`.

---

# Condições

Executar apenas na branch main:

```yaml
if: github.ref == 'refs/heads/main'
```

Exemplo:

```yaml
deploy:

  if: github.ref == 'refs/heads/main'

  needs: build

  runs-on: ubuntu-latest
```

---

# Executar mesmo em caso de falha

```yaml
if: always()
```

Exemplo:

```yaml
- name: Publicar logs
  if: always()
  uses: actions/upload-artifact@v4
```

---

# Executar apenas em caso de sucesso

```yaml
if: success()
```

---

# Executar apenas em caso de falha

```yaml
if: failure()
```

---

# Matrix

Matrix permite executar o mesmo Job em diferentes versões.

```yaml
strategy:

  matrix:

    dotnet-version:
      - "8.0.x"
      - "9.0.x"
      - "10.0.x"
```

Pipeline:

```yaml
jobs:

  test:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        dotnet-version:
          - "8.0.x"
          - "9.0.x"
          - "10.0.x"

    steps:

      - uses: actions/checkout@v4

      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ matrix.dotnet-version }}

      - run: dotnet test
```

---

# Docker no GitHub Actions

Fluxo:

```text
Checkout
   │
   ▼
Docker Login
   │
   ▼
Docker Build
   │
   ▼
Docker Push
```

---

# Build da imagem

```yaml
- name: Construir imagem
  run: >
    docker build
    --tag minha-api:${{ github.sha }}
    .
```

---

# Tag com SHA do commit

```yaml
${{ github.sha }}
```

Exemplo:

```text
minha-api:92b17abc
```

Isso permite identificar exatamente qual commit gerou a imagem.

---

# Login no Docker Registry

Exemplo genérico:

```yaml
- name: Login no Registry
  uses: docker/login-action@v3
  with:
    registry: ${{ secrets.REGISTRY_SERVER }}
    username: ${{ secrets.REGISTRY_USERNAME }}
    password: ${{ secrets.REGISTRY_PASSWORD }}
```

---

# Build e Push

```yaml
- name: Build e Push
  uses: docker/build-push-action@v6
  with:
    context: .
    push: true
    tags: |
      ${{ secrets.REGISTRY_SERVER }}/minha-api:${{ github.sha }}
      ${{ secrets.REGISTRY_SERVER }}/minha-api:latest
```

Para produção, prefira tags imutáveis ou versionadas.

Exemplo:

```text
minha-api:1.3.0

minha-api:92b17abc
```

---

# GitHub Container Registry

Login:

```yaml
- name: Login no GHCR
  uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
```

Permissões:

```yaml
permissions:
  contents: read
  packages: write
```

Build:

```yaml
- name: Build e Push
  uses: docker/build-push-action@v6
  with:
    context: .
    push: true
    tags: ghcr.io/${{ github.repository_owner }}/minha-api:${{ github.sha }}
```

---

# Azure Login

Para autenticação moderna, prefira OpenID Connect em vez de armazenar um Client Secret de longa duração.

Permissões:

```yaml
permissions:
  id-token: write
  contents: read
```

Login:

```yaml
- name: Login no Azure
  uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

---

# Azure Container Registry

Variáveis:

```yaml
env:
  ACR_NAME: acrdevopslab
  ACR_SERVER: acrdevopslab.azurecr.io
  IMAGE_NAME: minha-api
```

Login:

```yaml
- name: Login no Azure
  uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

Login no ACR:

```yaml
- name: Login no ACR
  run: az acr login --name "${{ env.ACR_NAME }}"
```

Build:

```yaml
- name: Docker Build
  run: |
    docker build \
      -t "${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}" \
      .
```

Push:

```yaml
- name: Docker Push
  run: |
    docker push \
      "${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}"
```

O ACR também aceita operações de login, push e pull por meio da Docker CLI.

---

# Pipeline Docker + ACR

```yaml
name: Docker Build and Push

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

env:
  ACR_NAME: acrdevopslab
  ACR_SERVER: acrdevopslab.azurecr.io
  IMAGE_NAME: minha-api

jobs:

  build-and-push:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Login no Azure
        uses: azure/login@v2
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Login no ACR
        run: az acr login --name "${{ env.ACR_NAME }}"

      - name: Build
        run: |
          docker build \
            -t "${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}" \
            .

      - name: Push
        run: |
          docker push \
            "${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}"
```

---

# Deploy no Azure App Service

Fluxo:

```text
Build .NET
   │
   ▼
Publish
   │
   ▼
Login Azure
   │
   ▼
Deploy App Service
```

---

# Deploy por pacote

```yaml
- name: Publicar
  run: >
    dotnet publish
    src/MinhaApi/MinhaApi.csproj
    --configuration Release
    --output ./publish

- name: Login no Azure
  uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

- name: Deploy
  uses: azure/webapps-deploy@v3
  with:
    app-name: app-devops-lab
    package: ./publish
```

---

# Workflow App Service completo

```yaml
name: Deploy App Service

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

env:
  DOTNET_VERSION: "10.0.x"
  PROJECT_PATH: "src/MinhaApi/MinhaApi.csproj"
  APP_NAME: "app-devops-lab"

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Configurar .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }}

      - name: Restore
        run: dotnet restore "${{ env.PROJECT_PATH }}"

      - name: Build
        run: >
          dotnet build
          "${{ env.PROJECT_PATH }}"
          --configuration Release
          --no-restore

      - name: Test
        run: >
          dotnet test
          --configuration Release
          --no-build

      - name: Publish
        run: >
          dotnet publish
          "${{ env.PROJECT_PATH }}"
          --configuration Release
          --output ./publish
          --no-build

      - name: Upload Artifact
        uses: actions/upload-artifact@v4
        with:
          name: app
          path: ./publish

  deploy:

    needs: build

    runs-on: ubuntu-latest

    environment: production

    steps:

      - name: Download Artifact
        uses: actions/download-artifact@v4
        with:
          name: app
          path: ./publish

      - name: Login no Azure
        uses: azure/login@v2
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Deploy
        uses: azure/webapps-deploy@v3
        with:
          app-name: ${{ env.APP_NAME }}
          package: ./publish
```

---

# Deploy no AKS

Fluxo:

```text
Testes
  │
  ▼
Docker Build
  │
  ▼
Push ACR
  │
  ▼
Credenciais AKS
  │
  ▼
Atualização do Deployment
  │
  ▼
Rollout Status
```

O GitHub Actions pode automatizar o build e a implantação de imagens armazenadas no ACR para aplicações executadas no AKS.

---

# Obter credenciais do AKS

```yaml
- name: Configurar AKS
  run: |
    az aks get-credentials \
      --resource-group rg-devops-lab \
      --name aks-devops-lab \
      --overwrite-existing
```

---

# Atualizar imagem

```yaml
- name: Atualizar Deployment
  run: |
    kubectl set image \
      deployment/minha-api \
      api="${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}"
```

---

# Acompanhar Rollout

```yaml
- name: Verificar Rollout
  run: |
    kubectl rollout status \
      deployment/minha-api \
      --timeout=180s
```

---

# Rollback automático

```yaml
- name: Rollback
  if: failure()
  run: |
    kubectl rollout undo \
      deployment/minha-api
```

> Um rollback automático deve ser usado com cuidado. Em alguns incidentes, investigar o estado antes de alterar novamente o ambiente pode ser mais seguro.

---

# Pipeline ACR + AKS

```yaml
name: Deploy AKS

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

env:
  RESOURCE_GROUP: rg-devops-lab
  AKS_NAME: aks-devops-lab
  ACR_NAME: acrdevopslab
  ACR_SERVER: acrdevopslab.azurecr.io
  IMAGE_NAME: minha-api
  DEPLOYMENT_NAME: minha-api
  CONTAINER_NAME: api

jobs:

  build-test-push:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Configurar .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: "10.0.x"

      - name: Restore
        run: dotnet restore

      - name: Build
        run: dotnet build --configuration Release --no-restore

      - name: Test
        run: dotnet test --configuration Release --no-build

      - name: Login no Azure
        uses: azure/login@v2
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Login no ACR
        run: az acr login --name "${{ env.ACR_NAME }}"

      - name: Build da imagem
        run: |
          docker build \
            -t "${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}" \
            .

      - name: Push da imagem
        run: |
          docker push \
            "${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}"

  deploy:

    needs: build-test-push

    runs-on: ubuntu-latest

    environment: production

    steps:

      - name: Login no Azure
        uses: azure/login@v2
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Configurar AKS
        run: |
          az aks get-credentials \
            --resource-group "${{ env.RESOURCE_GROUP }}" \
            --name "${{ env.AKS_NAME }}" \
            --overwrite-existing

      - name: Atualizar Deployment
        run: |
          kubectl set image \
            deployment/${{ env.DEPLOYMENT_NAME }} \
            ${{ env.CONTAINER_NAME }}="${{ env.ACR_SERVER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}"

      - name: Verificar Rollout
        run: |
          kubectl rollout status \
            deployment/${{ env.DEPLOYMENT_NAME }} \
            --timeout=180s
```

---

# Docker Compose no Pipeline

Um pipeline pode subir dependências temporárias para testes de integração.

Exemplo:

```yaml
- name: Subir infraestrutura
  run: docker compose up -d

- name: Verificar containers
  run: docker compose ps

- name: Executar testes
  run: dotnet test

- name: Exibir logs
  if: always()
  run: docker compose logs

- name: Encerrar infraestrutura
  if: always()
  run: docker compose down -v
```

---

# Aguardar serviços

Subir containers não significa que os serviços já estão prontos.

Utilize:

* Health Checks
* Scripts de espera
* Retentativas
* Testes de conectividade

Exemplo:

```yaml
- name: Aguardar PostgreSQL
  run: |
    for tentativa in {1..30}
    do
      if docker compose exec -T postgres \
        pg_isready -U admin
      then
        echo "PostgreSQL disponível."
        exit 0
      fi

      sleep 2
    done

    echo "PostgreSQL indisponível."
    exit 1
```

---

# Service Containers

O GitHub Actions também permite definir serviços dentro do Job.

Exemplo com PostgreSQL:

```yaml
jobs:

  integration-tests:

    runs-on: ubuntu-latest

    services:

      postgres:

        image: postgres:17

        env:
          POSTGRES_USER: admin
          POSTGRES_PASSWORD: admin123
          POSTGRES_DB: tests

        ports:
          - 5432:5432

        options: >-
          --health-cmd "pg_isready -U admin"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
```

Variável da aplicação:

```yaml
env:
  ConnectionStrings__Default: >-
    Host=localhost;
    Port=5432;
    Database=tests;
    Username=admin;
    Password=admin123
```

> Use credenciais descartáveis apenas para o ambiente temporário de testes.

---

# Azure DevOps Pipelines

O Azure DevOps utiliza arquivos YAML para definir pipelines.

A estrutura pode conter:

```text
Pipeline

Stages

Jobs

Steps

Tasks

Templates
```

A referência oficial do Azure Pipelines define um pipeline como um ou mais Stages que representam as principais divisões do processo de CI/CD.

Arquivo comum:

```text
azure-pipelines.yml
```

---

# Primeiro Pipeline Azure DevOps

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: ubuntu-latest

steps:

  - script: echo "Pipeline executado"
    displayName: Mensagem
```

---

# Pipeline .NET no Azure DevOps

```yaml
trigger:
  branches:
    include:
      - main
      - develop

pr:
  branches:
    include:
      - main
      - develop

pool:
  vmImage: ubuntu-latest

variables:
  buildConfiguration: Release
  solution: AirlineBookingSystem.slnx

steps:

  - task: UseDotNet@2
    displayName: Configurar .NET
    inputs:
      packageType: sdk
      version: 10.0.x

  - script: >
      dotnet restore
      $(solution)
    displayName: Restore

  - script: >
      dotnet build
      $(solution)
      --configuration $(buildConfiguration)
      --no-restore
    displayName: Build

  - script: >
      dotnet test
      $(solution)
      --configuration $(buildConfiguration)
      --no-build
    displayName: Test
```

---

# Stages no Azure DevOps

```yaml
stages:

  - stage: Build
    jobs:
      - job: BuildApplication
        steps:
          - script: echo "Build"

  - stage: Deploy
    dependsOn: Build

    jobs:
      - job: DeployApplication
        steps:
          - script: echo "Deploy"
```

---

# Pipeline completo com Stages

```yaml
trigger:
  branches:
    include:
      - main

variables:
  buildConfiguration: Release
  projectPath: src/MinhaApi/MinhaApi.csproj

stages:

  - stage: Build

    displayName: Build and Test

    jobs:

      - job: BuildApplication

        pool:
          vmImage: ubuntu-latest

        steps:

          - task: UseDotNet@2
            inputs:
              packageType: sdk
              version: 10.0.x

          - script: >
              dotnet restore
              $(projectPath)
            displayName: Restore

          - script: >
              dotnet build
              $(projectPath)
              --configuration $(buildConfiguration)
              --no-restore
            displayName: Build

          - script: >
              dotnet test
              --configuration $(buildConfiguration)
              --no-build
            displayName: Test

          - script: >
              dotnet publish
              $(projectPath)
              --configuration $(buildConfiguration)
              --output $(Build.ArtifactStagingDirectory)/publish
            displayName: Publish

          - task: PublishPipelineArtifact@1
            inputs:
              targetPath: $(Build.ArtifactStagingDirectory)/publish
              artifact: app

  - stage: Deploy

    dependsOn: Build

    condition: succeeded()

    jobs:

      - deployment: DeployProduction

        environment: production

        pool:
          vmImage: ubuntu-latest

        strategy:

          runOnce:

            deploy:

              steps:

                - download: current
                  artifact: app

                - script: echo "Realizar deploy"
                  displayName: Deploy
```

---

# Variables no Azure DevOps

```yaml
variables:
  buildConfiguration: Release
  environmentName: production
```

Utilização:

```yaml
$(buildConfiguration)
```

---

# Variable Groups

Variable Groups podem ser utilizados para compartilhar configurações.

Exemplo:

```yaml
variables:
  - group: production-settings
```

Eles podem armazenar:

* Variáveis comuns
* Secrets
* URLs
* Nomes de recursos
* Configurações de ambiente

---

# Service Connections

Service Connections permitem que o Azure DevOps se autentique em serviços externos.

Exemplos:

```text
Azure Resource Manager

Docker Registry

Kubernetes

GitHub

NuGet
```

Utilização conceitual:

```yaml
azureSubscription: MinhaServiceConnection
```

---

# Templates

Templates ajudam a reutilizar etapas, Jobs ou Stages.

A documentação oficial recomenda templates para centralizar conteúdo, parâmetros e lógica reutilizável em pipelines YAML.

Estrutura:

```text
pipelines/
├── azure-pipelines.yml
└── templates/
    ├── build.yml
    └── deploy.yml
```

---

# Template de Build

Arquivo:

```text
pipelines/templates/build.yml
```

```yaml
parameters:

  - name: projectPath
    type: string

  - name: configuration
    type: string
    default: Release

steps:

  - script: >
      dotnet restore
      ${{ parameters.projectPath }}
    displayName: Restore

  - script: >
      dotnet build
      ${{ parameters.projectPath }}
      --configuration ${{ parameters.configuration }}
      --no-restore
    displayName: Build
```

Utilização:

```yaml
steps:

  - template: templates/build.yml

    parameters:
      projectPath: src/MinhaApi/MinhaApi.csproj
      configuration: Release
```

---

# Reusable Workflows no GitHub

O GitHub também permite criar workflows reutilizáveis.

Arquivo:

```text
.github/workflows/dotnet-build.yml
```

```yaml
name: Reusable .NET Build

on:

  workflow_call:

    inputs:

      project-path:
        required: true
        type: string

      dotnet-version:
        required: false
        type: string
        default: "10.0.x"

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

      - uses: actions/checkout@v4

      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ inputs.dotnet-version }}

      - run: dotnet restore "${{ inputs.project-path }}"

      - run: >
          dotnet build
          "${{ inputs.project-path }}"
          --configuration Release
          --no-restore
```

Utilização:

```yaml
jobs:

  build:

    uses: ./.github/workflows/dotnet-build.yml

    with:
      project-path: src/MinhaApi/MinhaApi.csproj
      dotnet-version: "10.0.x"
```

---

# Versionamento Semântico

Formato:

```text
MAJOR.MINOR.PATCH
```

Exemplo:

```text
1.4.2
```

Significado:

```text
MAJOR = mudança incompatível

MINOR = nova funcionalidade compatível

PATCH = correção compatível
```

Tags:

```bash
git tag v1.0.0

git push origin v1.0.0
```

---

# Imagem Docker por versão

```text
minha-api:1.0.0

minha-api:1.1.0

minha-api:1.1.1
```

Também pode ser mantida uma tag adicional:

```text
minha-api:stable
```

Evite depender somente de:

```text
latest
```

---

# Estratégias de Deploy

## Recreate

A versão antiga é removida antes da nova iniciar.

```text
Versão antiga
    │
    ▼
Parada
    │
    ▼
Versão nova
```

Pode causar indisponibilidade.

---

## Rolling Update

A nova versão substitui a anterior gradualmente.

```text
v1 v1 v1
   │
   ▼
v2 v1 v1
   │
   ▼
v2 v2 v1
   │
   ▼
v2 v2 v2
```

É o comportamento comum de Deployments Kubernetes.

---

## Blue-Green

Dois ambientes são mantidos.

```text
Blue  = versão atual

Green = nova versão
```

Após validação, o tráfego é direcionado ao novo ambiente.

```text
Usuários
   │
   ▼
Blue
```

Depois:

```text
Usuários
   │
   ▼
Green
```

Benefício:

* Rollback rápido

Desvantagem:

* Maior uso de infraestrutura

---

## Canary

A nova versão recebe apenas parte do tráfego.

```text
90% → versão atual

10% → nova versão
```

Se estiver estável:

```text
50% → nova versão
```

Depois:

```text
100% → nova versão
```

---

# Health Check após deploy

Exemplo:

```yaml
- name: Validar aplicação
  run: |
    for tentativa in {1..10}
    do
      if curl --fail \
        --silent \
        https://api.exemplo.com/health
      then
        echo "Aplicação saudável."
        exit 0
      fi

      sleep 10
    done

    echo "Aplicação indisponível."
    exit 1
```

---

# Smoke Tests

Smoke Tests validam funcionalidades essenciais após o deploy.

Exemplos:

```text
API responde?

Health Check está saudável?

Banco está acessível?

Endpoint de autenticação responde?

Fila está disponível?
```

Exemplo:

```bash
curl --fail https://api.exemplo.com/health

curl --fail https://api.exemplo.com/swagger/index.html
```

---

# Rollback

## Kubernetes

```bash
kubectl rollout undo deployment/minha-api
```

---

## App Service com Slots

Fluxo:

```text
Production Slot

Staging Slot
```

A nova versão é publicada em Staging.

Depois ocorre o Swap:

```text
Staging → Production
```

Em caso de problema, pode ser realizado outro Swap.

---

## Imagem anterior

```bash
kubectl set image \
  deployment/minha-api \
  api=acr.azurecr.io/minha-api:1.2.0
```

---

# Migrações de banco

Migrações precisam ser tratadas com cuidado.

Possibilidades:

* Executar antes do deploy
* Executar em um Job separado
* Executar como Kubernetes Job
* Executar por ferramenta administrativa
* Utilizar estratégia compatível com versões anteriores

Evite alterações destrutivas durante um deploy automático sem proteção.

---

# Estratégia Expand and Contract

## Expand

Adicionar nova estrutura mantendo compatibilidade.

Exemplo:

```text
Adicionar nova coluna sem remover a antiga
```

---

## Migrate

Atualizar aplicação e dados gradualmente.

---

## Contract

Remover estrutura antiga após todas as aplicações estarem atualizadas.

Essa estratégia reduz riscos em sistemas distribuídos.

---

# Aprovações

Deploys de produção podem exigir aprovação.

Exemplo:

```text
Build aprovado
     │
     ▼
Homologação aprovada
     │
     ▼
Aprovação manual
     │
     ▼
Produção
```

No GitHub, isso pode ser configurado com Environments.

No Azure DevOps, pode ser configurado por meio de Environments, Approvals e Checks.

---

# Branch Protection

Proteções recomendadas para a branch principal:

* Exigir Pull Request
* Exigir revisão
* Exigir pipeline aprovado
* Bloquear push direto
* Exigir resolução de comentários
* Exigir branch atualizada
* Impedir exclusão da branch

---

# Segurança da cadeia de entrega

## Menor privilégio

Conceda ao pipeline apenas as permissões necessárias.

---

## OIDC

Prefira autenticação federada com tokens de curta duração.

Evite credenciais permanentes quando possível.

---

## Fixar versões das Actions

Exemplo:

```yaml
uses: actions/checkout@v4
```

Em ambientes mais rígidos, também pode ser utilizado o SHA completo da Action.

---

## Não imprimir Secrets

Evite:

```bash
echo "$TOKEN"
```

---

## Não armazenar Secrets no repositório

Nunca faça commit de:

```text
.env

Credenciais Azure

Chaves SSH

Tokens

Connection Strings

Certificados privados
```

---

## Dependabot

O Dependabot pode ajudar a acompanhar atualizações de dependências e Actions.

Arquivo:

```text
.github/dependabot.yml
```

Exemplo:

```yaml
version: 2

updates:

  - package-ecosystem: github-actions
    directory: "/"
    schedule:
      interval: weekly

  - package-ecosystem: nuget
    directory: "/"
    schedule:
      interval: weekly
```

---

# Concorrência

Evita múltiplos deploys simultâneos.

```yaml
concurrency:

  group: production

  cancel-in-progress: false
```

Para Pull Requests:

```yaml
concurrency:

  group: ci-${{ github.ref }}

  cancel-in-progress: true
```

---

# Timeout

```yaml
jobs:

  build:

    timeout-minutes: 15
```

Evita Jobs presos indefinidamente.

---

# Retenção de Artifacts

```yaml
- uses: actions/upload-artifact@v4

  with:
    name: app
    path: ./publish
    retention-days: 7
```

---

# Self-hosted Runner

Um Runner próprio pode ser utilizado quando o pipeline precisa:

* Acessar rede interna
* Utilizar ferramentas específicas
* Executar em hardware próprio
* Acessar servidores privados
* Utilizar maior capacidade computacional

Configuração:

```yaml
runs-on: self-hosted
```

Com labels:

```yaml
runs-on:
  - self-hosted
  - linux
  - production
```

> Runners próprios precisam de atualização, isolamento, monitoramento e controle de acesso.

---

# Observabilidade do Pipeline

Monitore:

* Duração do build
* Taxa de falhas
* Testes instáveis
* Frequência de deploy
* Tempo para recuperação
* Taxa de rollback
* Tempo de espera por aprovação
* Uso dos Runners

---

# Cenário completo

```text
Desenvolvedor
     │
     ▼
Pull Request
     │
     ├── Restore
     ├── Build
     ├── Testes unitários
     ├── Testes de integração
     ├── Análise de segurança
     └── Validação do Dockerfile
     │
     ▼
Merge na main
     │
     ├── Docker Build
     ├── Push para ACR
     ├── Deploy em homologação
     └── Smoke Tests
     │
     ▼
Aprovação
     │
     ▼
Deploy em produção
     │
     ├── Health Check
     ├── Smoke Tests
     ├── Monitoramento
     └── Rollback, se necessário
```

---

# Laboratório 1 — CI para .NET

Crie:

```text
.github/workflows/ci.yml
```

Conteúdo:

```yaml
name: CI

on:
  push:
    branches:
      - main
      - develop

  pull_request:
    branches:
      - main
      - develop

env:
  DOTNET_VERSION: "10.0.x"
  SOLUTION_PATH: "MinhaSolucao.slnx"
  CONFIGURATION: "Release"

jobs:

  build-and-test:

    runs-on: ubuntu-latest

    timeout-minutes: 15

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Configurar .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }}

      - name: Restore
        run: >
          dotnet restore
          "${{ env.SOLUTION_PATH }}"

      - name: Build
        run: >
          dotnet build
          "${{ env.SOLUTION_PATH }}"
          --configuration "${{ env.CONFIGURATION }}"
          --no-restore

      - name: Test
        run: >
          dotnet test
          "${{ env.SOLUTION_PATH }}"
          --configuration "${{ env.CONFIGURATION }}"
          --no-build
          --logger "trx;LogFileName=tests.trx"
          --results-directory ./test-results

      - name: Upload dos resultados
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: ./test-results
          retention-days: 7
```

Ajuste:

```text
MinhaSolucao.slnx
```

para o caminho da sua Solution.

---

# Laboratório 2 — Docker Build

Adicione outro Job:

```yaml
docker-build:

  needs: build-and-test

  runs-on: ubuntu-latest

  steps:

    - name: Checkout
      uses: actions/checkout@v4

    - name: Build da imagem
      run: |
        docker build \
          -t minha-api:${{ github.sha }} \
          .
```

---

# Laboratório 3 — Testar o container

```yaml
- name: Iniciar container
  run: |
    docker run \
      --detach \
      --name api-test \
      --publish 8080:8080 \
      minha-api:${{ github.sha }}

- name: Aguardar aplicação
  run: |
    for tentativa in {1..20}
    do
      if curl --fail http://localhost:8080/health
      then
        exit 0
      fi

      sleep 3
    done

    docker logs api-test

    exit 1

- name: Remover container
  if: always()
  run: docker rm --force api-test || true
```

---

# Problemas comuns

## Workflow não executa

Verifique:

* Caminho do arquivo
* Extensão YAML
* Trigger
* Branch utilizada
* Sintaxe
* Permissões do repositório

Caminho correto:

```text
.github/workflows/ci.yml
```

---

## Restore falha

Execute localmente:

```bash
dotnet restore
```

Verifique:

* Caminho da Solution
* Feeds privados
* Versão do SDK
* `NuGet.config`
* Dependências inexistentes

---

## SDK não encontrado

Verifique:

```yaml
uses: actions/setup-dotnet@v4

with:
  dotnet-version: "10.0.x"
```

Também confira o `global.json`.

---

## Solution não encontrada

Erro comum:

```text
MSB1009: Project file does not exist
```

Verifique:

```bash
find . -name "*.sln"

find . -name "*.slnx"

find . -name "*.csproj"
```

Atualize o caminho no workflow.

---

## Testes não encontrados

Verifique:

```bash
dotnet test MinhaSolucao.slnx
```

Confira se os projetos de teste estão adicionados à Solution.

---

## Dockerfile não encontrado

Verifique:

```bash
find . -name "Dockerfile"
```

Especifique:

```bash
docker build \
  --file src/MinhaApi/Dockerfile \
  --tag minha-api .
```

---

## Falha no Push

Verifique:

* Login no Registry
* Nome da imagem
* Permissões
* URL do Registry
* Tag
* Credenciais ou identidade federada

---

## Deploy executado antes do build

Configure:

```yaml
needs: build
```

---

## Secret vazio

Verifique:

* Nome exato
* Environment correto
* Permissão do workflow
* Escopo do Secret
* Origem do evento

---

## Pipeline funciona localmente, mas falha no Runner

Possíveis causas:

* Diferença de sistema operacional
* Caminhos com letras maiúsculas e minúsculas
* Arquivos não enviados ao Git
* Variáveis locais ausentes
* Dependência instalada apenas na sua máquina
* Porta indisponível
* Permissões

---

## Teste de integração falha

Verifique:

```bash
docker compose logs
```

Também confira:

* Health Checks
* Connection Strings
* Nome do serviço
* Porta interna
* Tempo de inicialização
* Migrações

---

# Boas práticas

✅ Execute CI em todo Pull Request.

---

✅ Bloqueie o merge quando o pipeline falhar.

---

✅ Separe Build e Deploy.

---

✅ Gere o artefato uma vez e reutilize-o nos ambientes.

---

✅ Não compile novamente para cada ambiente.

---

✅ Utilize tags imutáveis para imagens.

---

✅ Utilize o SHA do commit:

```text
minha-api:92b17abc
```

---

✅ Utilize Environments para produção.

---

✅ Exija aprovação em deploys críticos.

---

✅ Utilize OIDC ou identidades gerenciadas quando possível.

---

✅ Evite credenciais de longa duração.

---

✅ Configure timeout para Jobs.

---

✅ Utilize cache com cuidado.

---

✅ Publique logs e resultados mesmo quando os testes falharem.

---

✅ Utilize Health Checks depois do deploy.

---

✅ Implemente uma estratégia clara de rollback.

---

✅ Mantenha os pipelines versionados no mesmo repositório da aplicação.

---

✅ Reutilize workflows e templates.

---

✅ Mantenha pipelines pequenos e legíveis.

---

✅ Atualize Actions, Tasks e imagens utilizadas pelo pipeline.

---

# Checklist de CI

```text
[ ] Checkout do código

[ ] SDK configurado

[ ] Dependências restauradas

[ ] Código compilado

[ ] Testes unitários executados

[ ] Testes de integração executados

[ ] Cobertura gerada

[ ] Análise de segurança executada

[ ] Artefato publicado

[ ] Resultado visível no Pull Request
```

---

# Checklist de CD

```text
[ ] Artefato versionado

[ ] Ambiente definido

[ ] Autenticação segura

[ ] Configurações disponíveis

[ ] Secrets protegidos

[ ] Banco preparado

[ ] Deploy executado

[ ] Health Check validado

[ ] Smoke Tests executados

[ ] Monitoramento ativo

[ ] Rollback disponível
```

---

# Cheat Sheet — GitHub Actions

```yaml
# Checkout
- uses: actions/checkout@v4

# .NET
- uses: actions/setup-dotnet@v4
  with:
    dotnet-version: "10.0.x"

# Restore
- run: dotnet restore

# Build
- run: dotnet build --configuration Release --no-restore

# Test
- run: dotnet test --configuration Release --no-build

# Publish
- run: dotnet publish -c Release -o ./publish

# Artifact
- uses: actions/upload-artifact@v4
  with:
    name: app
    path: ./publish

# Download
- uses: actions/download-artifact@v4
  with:
    name: app

# Docker Build
- run: docker build -t minha-api:${{ github.sha }} .

# Condição
if: github.ref == 'refs/heads/main'

# Dependência
needs: build

# Environment
environment: production

# Execução manual
on:
  workflow_dispatch:
```

---

# Cheat Sheet — Azure DevOps

```yaml
# Trigger
trigger:
  branches:
    include:
      - main

# Agent
pool:
  vmImage: ubuntu-latest

# Variável
variables:
  buildConfiguration: Release

# Script
- script: dotnet build
  displayName: Build

# .NET
- task: UseDotNet@2
  inputs:
    packageType: sdk
    version: 10.0.x

# Artifact
- task: PublishPipelineArtifact@1
  inputs:
    targetPath: $(Build.ArtifactStagingDirectory)
    artifact: app

# Stage
stages:
  - stage: Build
  - stage: Deploy

# Dependência
dependsOn: Build

# Condição
condition: succeeded()

# Environment
environment: production
```

---

# Resumo

Neste capítulo você aprendeu:

* Conceitos de CI/CD
* Diferença entre Continuous Delivery e Continuous Deployment
* Estrutura de pipelines
* Workflows, Jobs, Steps, Runners e Artifacts
* GitHub Actions
* Azure DevOps Pipelines
* Restore, Build, Test e Publish de aplicações .NET
* Testes unitários e de integração
* Docker Build e Push
* Azure Container Registry
* Deploy no Azure App Service
* Deploy no Azure Kubernetes Service
* Secrets, variáveis e Environments
* Aprovações
* Templates e workflows reutilizáveis
* Estratégias Rolling, Blue-Green e Canary
* Health Checks, Smoke Tests e Rollback
* Boas práticas de segurança

---

# Próximo capítulo

No **Capítulo 18 — Monitoramento e Observabilidade**, aprenderemos:

* Diferença entre monitoramento e observabilidade
* Métricas
* Logs
* Traces
* Health Checks
* Prometheus
* Grafana
* OpenTelemetry
* Application Insights
* Alertas
* Monitoramento de Linux
* Monitoramento de Docker
* Monitoramento de Kubernetes
* Monitoramento de aplicações .NET
* Diagnóstico de incidentes em produção
