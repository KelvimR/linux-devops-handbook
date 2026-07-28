# Capítulo 16 — Azure CLI

> A **Azure CLI** é a interface de linha de comando da Microsoft para criar, consultar, atualizar e remover recursos do Azure. Ela é especialmente útil para automação, scripts Bash, pipelines de CI/CD e administração de ambientes em nuvem.

---

# Objetivos

Ao final deste capítulo você será capaz de:

* Instalar e configurar a Azure CLI
* Autenticar-se no Azure
* Gerenciar assinaturas
* Criar Resource Groups
* Consultar recursos
* Criar máquinas virtuais
* Gerenciar Storage Accounts
* Trabalhar com Azure Container Registry
* Gerenciar aplicações no Azure App Service
* Administrar clusters do Azure Kubernetes Service
* Utilizar Service Principals e Managed Identities
* Filtrar resultados com JMESPath
* Criar scripts de automação
* Aplicar boas práticas de segurança

---

# O que é a Azure CLI?

A Azure CLI é uma ferramenta multiplataforma para administrar recursos do Microsoft Azure pelo terminal.

O comando principal é:

```bash
az
```

Ela pode ser utilizada em:

* Linux
* Windows
* macOS
* Windows Subsystem for Linux
* Azure Cloud Shell
* Pipelines de CI/CD
* Containers
* Scripts Bash
* GitHub Actions
* Azure DevOps

A Azure CLI disponibiliza grupos de comandos para os diferentes serviços do Azure, como máquinas virtuais, redes, armazenamento, registros de containers e Kubernetes.

---

# Estrutura dos comandos

A estrutura geral é:

```bash
az grupo subgrupo comando [parâmetros]
```

Exemplo:

```bash
az group create \
  --name rg-devops-lab \
  --location brazilsouth
```

Neste comando:

```text
az
```

Executável da Azure CLI.

```text
group
```

Grupo responsável pelos Resource Groups.

```text
create
```

Operação que será executada.

```text
--name
```

Nome do recurso.

```text
--location
```

Região do Azure.

---

# Instalação

## Verificar se está instalada

```bash
az version
```

Ou:

```bash
az --version
```

---

# Instalação no Windows

A Azure CLI pode ser instalada no Windows por meio do instalador MSI, PowerShell ou pacote ZIP. No WSL, deve ser utilizado o procedimento da distribuição Linux instalada.

Após a instalação:

```powershell
az version
```

---

# Instalação no Ubuntu e Debian

Uma forma comum é utilizar o script oficial de instalação:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Depois:

```bash
az version
```

---

# Atualizando a Azure CLI

```bash
az upgrade
```

Em instalações gerenciadas pelo APT, também pode ser necessário utilizar:

```bash
sudo apt update

sudo apt upgrade azure-cli
```

---

# Azure Cloud Shell

O Azure Cloud Shell é um terminal disponibilizado diretamente pelo Azure.

Ele pode ser utilizado com:

* Bash
* PowerShell

O ambiente normalmente já possui ferramentas como:

```text
Azure CLI

Git

Kubectl

Helm

Terraform
```

O Cloud Shell é útil quando você precisa administrar recursos sem instalar ferramentas localmente.

---

# Ajuda

Mostrar grupos disponíveis:

```bash
az --help
```

Ajuda de um grupo:

```bash
az group --help
```

Ajuda de um comando:

```bash
az group create --help
```

Pesquisar exemplos:

```bash
az find "az group create"
```

---

# Login interativo

Autenticar:

```bash
az login
```

O comando abrirá o navegador para autenticação.

Quando o navegador não estiver disponível, a CLI pode orientar o login por código de dispositivo.

A autenticação interativa é indicada para operações manuais. Para automações, a Microsoft recomenda identidades de carga de trabalho, como Service Principals ou Managed Identities.

---

# Verificar conta autenticada

```bash
az account show
```

Formato de tabela:

```bash
az account show --output table
```

---

# Logout

```bash
az logout
```

Remover informações de contas armazenadas:

```bash
az account clear
```

---

# Assinaturas

Uma conta pode possuir acesso a várias assinaturas.

Listar:

```bash
az account list
```

Formato de tabela:

```bash
az account list --output table
```

---

# Selecionar assinatura

Pelo nome:

```bash
az account set \
  --subscription "Minha Assinatura"
```

Pelo ID:

```bash
az account set \
  --subscription "00000000-0000-0000-0000-000000000000"
```

---

# Mostrar assinatura atual

```bash
az account show \
  --query "{Nome:name, ID:id}" \
  --output table
```

---

# Locations

Listar regiões disponíveis:

```bash
az account list-locations
```

Formato resumido:

```bash
az account list-locations \
  --query "[].{Nome:name,Descricao:displayName}" \
  --output table
```

Exemplo de região:

```text
brazilsouth
```

Também existem regiões como:

```text
eastus

westus

westeurope

northeurope
```

A disponibilidade de determinados serviços e tamanhos pode variar por região.

---

# Variáveis para os exemplos

Para evitar repetir valores, podemos criar variáveis Bash:

```bash
RESOURCE_GROUP="rg-devops-lab"

LOCATION="brazilsouth"

APP_NAME="app-devops-lab"

ACR_NAME="acrdevopslab"

AKS_NAME="aks-devops-lab"
```

Utilização:

```bash
echo "$RESOURCE_GROUP"
```

---

# Resource Groups

Um Resource Group é um agrupamento lógico de recursos do Azure.

Ele pode conter:

* Máquinas virtuais
* Bancos de dados
* Redes
* Storage Accounts
* Aplicações
* Container Registries
* Clusters Kubernetes

---

# Criar Resource Group

```bash
az group create \
  --name rg-devops-lab \
  --location brazilsouth
```

---

# Listar Resource Groups

```bash
az group list
```

Formato de tabela:

```bash
az group list \
  --output table
```

---

# Mostrar um Resource Group

```bash
az group show \
  --name rg-devops-lab
```

---

# Verificar se existe

```bash
az group exists \
  --name rg-devops-lab
```

---

# Atualizar tags

```bash
az group update \
  --name rg-devops-lab \
  --set tags.ambiente=desenvolvimento
```

---

# Excluir Resource Group

```bash
az group delete \
  --name rg-devops-lab
```

Sem confirmação interativa:

```bash
az group delete \
  --name rg-devops-lab \
  --yes
```

Executar sem aguardar a conclusão:

```bash
az group delete \
  --name rg-devops-lab \
  --yes \
  --no-wait
```

> ⚠️ Excluir um Resource Group normalmente remove todos os recursos contidos nele.

---

# Consultando recursos

Listar todos os recursos:

```bash
az resource list
```

Por Resource Group:

```bash
az resource list \
  --resource-group rg-devops-lab
```

Formato de tabela:

```bash
az resource list \
  --resource-group rg-devops-lab \
  --output table
```

O grupo `az resource` permite listar e administrar recursos genéricos do Azure.

---

# Tags

Tags permitem classificar recursos.

Exemplos:

```text
ambiente=desenvolvimento

projeto=runinsight

responsavel=backend

centro-custo=tecnologia
```

---

# Criar Resource Group com tags

```bash
az group create \
  --name rg-devops-lab \
  --location brazilsouth \
  --tags ambiente=desenvolvimento projeto=linux-devops-handbook
```

---

# Filtrar recursos por tag

```bash
az resource list \
  --tag ambiente=desenvolvimento \
  --output table
```

---

# Formatos de saída

A Azure CLI utiliza JSON como formato de saída padrão, mas aceita outros formatos por meio de `--output`, `--out` ou `-o`.

Principais formatos:

```text
json

jsonc

table

tsv

yaml

yamlc

none
```

---

# JSON

```bash
az group list \
  --output json
```

---

# Tabela

```bash
az group list \
  --output table
```

Forma reduzida:

```bash
az group list -o table
```

---

# TSV

Útil para capturar valores em scripts:

```bash
az account show \
  --query id \
  --output tsv
```

Salvar em variável:

```bash
SUBSCRIPTION_ID=$(az account show \
  --query id \
  --output tsv)
```

---

# Ocultar saída

```bash
az group create \
  --name rg-devops-lab \
  --location brazilsouth \
  --output none
```

---

# Definir formato padrão

```bash
az config set core.output=table
```

Voltar para JSON:

```bash
az config set core.output=json
```

---

# Consultas com JMESPath

Todos os comandos da Azure CLI aceitam o parâmetro `--query`.

A consulta é aplicada ao resultado JSON antes da formatação da saída. A linguagem utilizada é JMESPath.

---

# Selecionar uma propriedade

```bash
az account show \
  --query name
```

Sem aspas:

```bash
az account show \
  --query name \
  --output tsv
```

---

# Selecionar várias propriedades

```bash
az account show \
  --query "{Nome:name, ID:id, Tenant:tenantId}" \
  --output table
```

---

# Filtrar coleção

```bash
az group list \
  --query "[?location=='brazilsouth']"
```

---

# Transformar o resultado

```bash
az group list \
  --query "[].{Nome:name,Regiao:location}" \
  --output table
```

---

# Ordenar resultado

```bash
az group list \
  --query "sort_by([], &name)[].{Nome:name,Regiao:location}" \
  --output table
```

---

# Máquinas Virtuais

Listar máquinas virtuais:

```bash
az vm list
```

Formato de tabela:

```bash
az vm list \
  --show-details \
  --output table
```

O grupo `az vm` centraliza operações relacionadas às máquinas virtuais do Azure.

---

# Criar máquina virtual Linux

```bash
az vm create \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys
```

---

# Criar VM utilizando chave existente

```bash
az vm create \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/id_ed25519.pub
```

---

# Abrir porta

```bash
az vm open-port \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --port 80
```

Para SSH:

```bash
az vm open-port \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --port 22
```

---

# Consultar IP público

```bash
az vm show \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --show-details \
  --query publicIps \
  --output tsv
```

---

# Conectar por SSH

```bash
ssh azureuser@IP_PUBLICO
```

---

# Parar VM

```bash
az vm stop \
  --resource-group rg-devops-lab \
  --name vm-linux-devops
```

---

# Desalocar VM

```bash
az vm deallocate \
  --resource-group rg-devops-lab \
  --name vm-linux-devops
```

> Parar e desalocar não possuem exatamente o mesmo efeito. A desalocação libera os recursos computacionais associados à VM.

---

# Iniciar VM

```bash
az vm start \
  --resource-group rg-devops-lab \
  --name vm-linux-devops
```

---

# Reiniciar VM

```bash
az vm restart \
  --resource-group rg-devops-lab \
  --name vm-linux-devops
```

---

# Excluir VM

```bash
az vm delete \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --yes
```

---

# Executar comando em uma VM

```bash
az vm run-command invoke \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --command-id RunShellScript \
  --scripts "uname -a"
```

Outro exemplo:

```bash
az vm run-command invoke \
  --resource-group rg-devops-lab \
  --name vm-linux-devops \
  --command-id RunShellScript \
  --scripts "docker --version"
```

---

# Storage Accounts

Storage Accounts são utilizadas para armazenar:

* Blobs
* Arquivos
* Filas
* Tabelas
* Discos
* Dados de aplicações

Os nomes das Storage Accounts precisam ser globalmente únicos no Azure.

---

# Criar Storage Account

```bash
az storage account create \
  --name stdevopslab12345 \
  --resource-group rg-devops-lab \
  --location brazilsouth \
  --sku Standard_LRS \
  --kind StorageV2
```

---

# Listar Storage Accounts

```bash
az storage account list \
  --output table
```

---

# Mostrar detalhes

```bash
az storage account show \
  --name stdevopslab12345 \
  --resource-group rg-devops-lab
```

---

# Obter Connection String

```bash
az storage account show-connection-string \
  --name stdevopslab12345 \
  --resource-group rg-devops-lab \
  --query connectionString \
  --output tsv
```

> ⚠️ Connection Strings e chaves de acesso são informações sensíveis. Não as coloque em commits, logs ou arquivos públicos.

---

# Criar container Blob

```bash
az storage container create \
  --name arquivos \
  --account-name stdevopslab12345 \
  --auth-mode login
```

---

# Enviar arquivo

```bash
az storage blob upload \
  --account-name stdevopslab12345 \
  --container-name arquivos \
  --name README.md \
  --file README.md \
  --auth-mode login
```

---

# Listar Blobs

```bash
az storage blob list \
  --account-name stdevopslab12345 \
  --container-name arquivos \
  --auth-mode login \
  --output table
```

---

# Baixar Blob

```bash
az storage blob download \
  --account-name stdevopslab12345 \
  --container-name arquivos \
  --name README.md \
  --file README-download.md \
  --auth-mode login
```

---

# Excluir Storage Account

```bash
az storage account delete \
  --name stdevopslab12345 \
  --resource-group rg-devops-lab \
  --yes
```

---

# Azure Container Registry

O Azure Container Registry, ou ACR, armazena imagens de containers privadas.

Exemplos:

```text
minha-api:1.0.0

payment-api:2.1.0

notifications-worker:1.3.5
```

O grupo `az acr` contém comandos para administrar registros, repositórios, imagens, autenticação e tarefas automatizadas.

---

# Criar ACR

O nome deve ser globalmente único e utilizar apenas letras e números.

```bash
az acr create \
  --resource-group rg-devops-lab \
  --name acrdevopslab12345 \
  --sku Basic
```

---

# Listar registros

```bash
az acr list \
  --output table
```

---

# Mostrar servidor de login

```bash
az acr show \
  --name acrdevopslab12345 \
  --query loginServer \
  --output tsv
```

---

# Login no ACR

```bash
az acr login \
  --name acrdevopslab12345
```

---

# Build da imagem no próprio Azure

```bash
az acr build \
  --registry acrdevopslab12345 \
  --image minha-api:1.0.0 \
  .
```

Esse comando envia o contexto do projeto e executa o build no ACR.

---

# Build local e Push

Obter servidor:

```bash
ACR_SERVER=$(az acr show \
  --name acrdevopslab12345 \
  --query loginServer \
  --output tsv)
```

Criar imagem:

```bash
docker build \
  -t "$ACR_SERVER/minha-api:1.0.0" \
  .
```

Enviar:

```bash
docker push \
  "$ACR_SERVER/minha-api:1.0.0"
```

---

# Listar repositórios

```bash
az acr repository list \
  --name acrdevopslab12345 \
  --output table
```

---

# Listar tags

```bash
az acr repository show-tags \
  --name acrdevopslab12345 \
  --repository minha-api \
  --output table
```

---

# Excluir imagem

```bash
az acr repository delete \
  --name acrdevopslab12345 \
  --image minha-api:1.0.0 \
  --yes
```

---

# App Service

O Azure App Service permite hospedar:

* Aplicações .NET
* APIs
* Aplicações Node.js
* Aplicações Java
* Aplicações Python
* Containers

---

# Criar App Service Plan

```bash
az appservice plan create \
  --name plan-devops-lab \
  --resource-group rg-devops-lab \
  --sku B1 \
  --is-linux
```

---

# Criar Web App .NET

```bash
az webapp create \
  --resource-group rg-devops-lab \
  --plan plan-devops-lab \
  --name app-devops-lab-12345 \
  --runtime "DOTNETCORE:8.0"
```

> A lista de runtimes suportados pode mudar. Consulte os runtimes disponíveis antes de criar a aplicação.

---

# Listar runtimes

```bash
az webapp list-runtimes \
  --os linux
```

---

# Listar Web Apps

```bash
az webapp list \
  --output table
```

---

# Mostrar URL

```bash
az webapp show \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345 \
  --query defaultHostName \
  --output tsv
```

---

# Configurar variável de ambiente

```bash
az webapp config appsettings set \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345 \
  --settings ASPNETCORE_ENVIRONMENT=Production
```

---

# Configurar múltiplas variáveis

```bash
az webapp config appsettings set \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345 \
  --settings \
    ASPNETCORE_ENVIRONMENT=Production \
    FeatureFlags__NovaTela=true
```

---

# Listar configurações

```bash
az webapp config appsettings list \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345 \
  --output table
```

> A saída pode conter dados sensíveis. Evite exibi-la em logs de pipelines.

---

# Reiniciar Web App

```bash
az webapp restart \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345
```

---

# Parar

```bash
az webapp stop \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345
```

---

# Iniciar

```bash
az webapp start \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345
```

---

# Exibir logs

Ativar logs:

```bash
az webapp log config \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345 \
  --application-logging filesystem \
  --level information
```

Acompanhar:

```bash
az webapp log tail \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345
```

---

# Deploy de aplicação .NET

Publicar:

```bash
dotnet publish \
  -c Release \
  -o publish
```

Criar ZIP:

```bash
cd publish

zip -r ../publish.zip .

cd ..
```

Enviar:

```bash
az webapp deploy \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345 \
  --src-path publish.zip \
  --type zip
```

---

# Azure Kubernetes Service

O Azure Kubernetes Service, ou AKS, é o serviço gerenciado de Kubernetes do Azure.

A Azure CLI possui o grupo `az aks` para criação, consulta, atualização e administração dos clusters. Alguns recursos adicionais podem depender de extensões da CLI.

---

# Criar cluster AKS

```bash
az aks create \
  --resource-group rg-devops-lab \
  --name aks-devops-lab \
  --node-count 2 \
  --generate-ssh-keys
```

---

# Listar clusters

```bash
az aks list \
  --output table
```

---

# Obter credenciais

```bash
az aks get-credentials \
  --resource-group rg-devops-lab \
  --name aks-devops-lab
```

Isso atualiza o arquivo:

```text
~/.kube/config
```

---

# Testar conexão

```bash
kubectl get nodes
```

---

# Mostrar informações do cluster

```bash
az aks show \
  --resource-group rg-devops-lab \
  --name aks-devops-lab
```

---

# Escalar quantidade de nós

```bash
az aks scale \
  --resource-group rg-devops-lab \
  --name aks-devops-lab \
  --node-count 3
```

---

# Parar cluster

```bash
az aks stop \
  --resource-group rg-devops-lab \
  --name aks-devops-lab
```

---

# Iniciar cluster

```bash
az aks start \
  --resource-group rg-devops-lab \
  --name aks-devops-lab
```

---

# Anexar ACR ao AKS

```bash
az aks update \
  --resource-group rg-devops-lab \
  --name aks-devops-lab \
  --attach-acr acrdevopslab12345
```

---

# Verificar acesso ao ACR

```bash
az aks check-acr \
  --resource-group rg-devops-lab \
  --name aks-devops-lab \
  --acr acrdevopslab12345.azurecr.io
```

---

# Excluir cluster

```bash
az aks delete \
  --resource-group rg-devops-lab \
  --name aks-devops-lab \
  --yes
```

---

# Azure Key Vault

O Azure Key Vault é utilizado para armazenar:

* Secrets
* Certificados
* Chaves criptográficas
* Connection Strings
* Tokens

---

# Criar Key Vault

O nome precisa ser globalmente único.

```bash
az keyvault create \
  --name kv-devops-lab-12345 \
  --resource-group rg-devops-lab \
  --location brazilsouth
```

---

# Criar Secret

```bash
az keyvault secret set \
  --vault-name kv-devops-lab-12345 \
  --name DatabasePassword \
  --value "VALOR_SENSIVEL"
```

> Evite informar secrets diretamente no terminal, pois eles podem aparecer no histórico do shell.

---

# Criar Secret a partir de variável

```bash
read -s DATABASE_PASSWORD

echo
```

Depois:

```bash
az keyvault secret set \
  --vault-name kv-devops-lab-12345 \
  --name DatabasePassword \
  --value "$DATABASE_PASSWORD"
```

---

# Listar Secrets

```bash
az keyvault secret list \
  --vault-name kv-devops-lab-12345 \
  --output table
```

---

# Recuperar Secret

```bash
az keyvault secret show \
  --vault-name kv-devops-lab-12345 \
  --name DatabasePassword \
  --query value \
  --output tsv
```

> Não execute esse comando em pipelines que exibem toda a saída no log.

---

# Excluir Secret

```bash
az keyvault secret delete \
  --vault-name kv-devops-lab-12345 \
  --name DatabasePassword
```

---

# Service Principals

Um Service Principal é uma identidade utilizada por:

* Pipelines
* Scripts
* Aplicações
* Automações
* GitHub Actions
* Azure DevOps

Para automação, ele é mais apropriado do que utilizar a conta pessoal do desenvolvedor. A Microsoft mantém uma forma específica de autenticação da Azure CLI por Service Principal.

---

# Criar Service Principal

```bash
az ad sp create-for-rbac \
  --name sp-devops-lab \
  --role Contributor \
  --scopes /subscriptions/ID_DA_ASSINATURA/resourceGroups/rg-devops-lab
```

A resposta inclui credenciais que devem ser protegidas.

Exemplo conceitual de retorno:

```json
{
  "appId": "...",
  "password": "...",
  "tenant": "..."
}
```

> ⚠️ Nunca faça commit do resultado desse comando.

---

# Login com Service Principal

```bash
az login \
  --service-principal \
  --username "$AZURE_CLIENT_ID" \
  --password "$AZURE_CLIENT_SECRET" \
  --tenant "$AZURE_TENANT_ID"
```

---

# Variáveis recomendadas

```bash
export AZURE_CLIENT_ID="..."

export AZURE_CLIENT_SECRET="..."

export AZURE_TENANT_ID="..."
```

---

# Escopo mínimo

Evite conceder permissões sobre toda a assinatura sem necessidade.

Prefira:

```text
Resource Group específico
```

Em vez de:

```text
Assinatura inteira
```

Exemplo de escopo:

```bash
SCOPE="/subscriptions/$SUBSCRIPTION_ID/resourceGroups/rg-devops-lab"
```

---

# Managed Identity

Managed Identity é uma identidade gerenciada pelo Azure.

Ela evita a necessidade de armazenar:

* Senhas
* Client Secrets
* Certificados
* Credenciais em arquivos

A Microsoft recomenda Managed Identity para recursos do Azure que precisam acessar outros serviços compatíveis com autenticação do Microsoft Entra.

---

# Login com identidade gerenciada

Dentro de um recurso Azure configurado com identidade:

```bash
az login --identity
```

---

# Identidade atribuída pelo usuário

Por Client ID:

```bash
az login \
  --identity \
  --client-id ID_DA_IDENTIDADE
```

Também é possível selecionar a identidade por Object ID ou Resource ID.

---

# Extensões

Alguns recursos são disponibilizados por extensões da Azure CLI.

Listar:

```bash
az extension list \
  --output table
```

---

# Listar extensões disponíveis

```bash
az extension list-available \
  --output table
```

A Microsoft mantém uma lista oficial das extensões suportadas, incluindo extensões em estados GA, Preview e Experimental.

---

# Adicionar extensão

```bash
az extension add \
  --name nome-da-extensao
```

---

# Atualizar extensão

```bash
az extension update \
  --name nome-da-extensao
```

---

# Remover extensão

```bash
az extension remove \
  --name nome-da-extensao
```

---

# Configuração da CLI

Listar configurações:

```bash
az config get
```

---

# Definir Resource Group padrão

```bash
az config set \
  defaults.group=rg-devops-lab
```

Agora alguns comandos não precisarão de:

```text
--resource-group
```

---

# Definir localização padrão

```bash
az config set \
  defaults.location=brazilsouth
```

---

# Remover configurações padrão

```bash
az config unset defaults.group

az config unset defaults.location
```

---

# Scripts Bash com Azure CLI

Exemplo de provisionamento:

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

RESOURCE_GROUP="rg-devops-lab"
LOCATION="brazilsouth"
STORAGE_ACCOUNT="stdevopslab12345"

echo "Validando autenticação..."

az account show \
  --output none

echo "Criando Resource Group..."

az group create \
  --name "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --output none

echo "Criando Storage Account..."

az storage account create \
  --name "$STORAGE_ACCOUNT" \
  --resource-group "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --sku Standard_LRS \
  --kind StorageV2 \
  --output none

echo "Provisionamento concluído."
```

---

# Verificar se recurso existe

```bash
if az group exists \
  --name "$RESOURCE_GROUP" \
  --output tsv | grep -q true
then
  echo "Resource Group já existe."
else
  az group create \
    --name "$RESOURCE_GROUP" \
    --location "$LOCATION"
fi
```

---

# Capturar resultado

```bash
APP_URL=$(az webapp show \
  --resource-group "$RESOURCE_GROUP" \
  --name "$APP_NAME" \
  --query defaultHostName \
  --output tsv)
```

Mostrar:

```bash
echo "Aplicação: https://$APP_URL"
```

---

# Tratamento de erros

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

trap 'echo "Erro na linha $LINENO."' ERR
```

---

# Script de limpeza

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

RESOURCE_GROUP="rg-devops-lab"

read -r -p "Excluir $RESOURCE_GROUP? [s/N]: " resposta

if [[ "$resposta" =~ ^[sS]$ ]]
then
  az group delete \
    --name "$RESOURCE_GROUP" \
    --yes \
    --no-wait

  echo "Exclusão iniciada."
else
  echo "Operação cancelada."
fi
```

---

# Cenário: pipeline de CI/CD

Variáveis comuns:

```text
AZURE_CLIENT_ID

AZURE_TENANT_ID

AZURE_SUBSCRIPTION_ID
```

Fluxo:

```text
Pipeline
   │
   ├── Autenticação no Azure
   ├── Build da aplicação
   ├── Build da imagem Docker
   ├── Push para o ACR
   ├── Atualização do App Service ou AKS
   └── Validação do deploy
```

---

# Exemplo de deploy no ACR

```bash
az acr login \
  --name "$ACR_NAME"

docker build \
  -t "$ACR_SERVER/minha-api:$VERSION" \
  .

docker push \
  "$ACR_SERVER/minha-api:$VERSION"
```

---

# Exemplo de atualização no AKS

```bash
az aks get-credentials \
  --resource-group "$RESOURCE_GROUP" \
  --name "$AKS_NAME" \
  --overwrite-existing

kubectl set image \
  deployment/minha-api \
  api="$ACR_SERVER/minha-api:$VERSION"

kubectl rollout status \
  deployment/minha-api
```

---

# Cenário: inventário de recursos

Listar nome, tipo e região:

```bash
az resource list \
  --query "[].{Nome:name,Tipo:type,Regiao:location}" \
  --output table
```

---

# Cenário: localizar IPs públicos

```bash
az network public-ip list \
  --query "[].{Nome:name,IP:ipAddress,Grupo:resourceGroup}" \
  --output table
```

---

# Cenário: listar VMs em execução

```bash
az vm list \
  --show-details \
  --query "[?powerState=='VM running'].{Nome:name,Grupo:resourceGroup,IP:publicIps}" \
  --output table
```

---

# Cenário: aplicar tags em recursos

```bash
RESOURCE_ID=$(az resource show \
  --resource-group rg-devops-lab \
  --name app-devops-lab-12345 \
  --resource-type Microsoft.Web/sites \
  --query id \
  --output tsv)
```

Aplicar:

```bash
az tag update \
  --resource-id "$RESOURCE_ID" \
  --operation merge \
  --tags ambiente=dev projeto=handbook
```

---

# Laboratório

## Objetivo

Criar:

```text
Resource Group

Storage Account

Blob Container

Arquivo de teste
```

---

## Definir variáveis

```bash
RESOURCE_GROUP="rg-azure-cli-lab"

LOCATION="brazilsouth"

STORAGE_ACCOUNT="stazclilab12345"

CONTAINER_NAME="arquivos"
```

> O nome da Storage Account precisa ser globalmente único. Ajuste o valor antes de executar.

---

## Criar Resource Group

```bash
az group create \
  --name "$RESOURCE_GROUP" \
  --location "$LOCATION"
```

---

## Criar Storage Account

```bash
az storage account create \
  --name "$STORAGE_ACCOUNT" \
  --resource-group "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --sku Standard_LRS \
  --kind StorageV2
```

---

## Criar Blob Container

```bash
az storage container create \
  --name "$CONTAINER_NAME" \
  --account-name "$STORAGE_ACCOUNT" \
  --auth-mode login
```

---

## Criar arquivo

```bash
echo "Laboratório Azure CLI" > laboratorio.txt
```

---

## Enviar arquivo

```bash
az storage blob upload \
  --account-name "$STORAGE_ACCOUNT" \
  --container-name "$CONTAINER_NAME" \
  --name laboratorio.txt \
  --file laboratorio.txt \
  --auth-mode login
```

---

## Listar arquivos

```bash
az storage blob list \
  --account-name "$STORAGE_ACCOUNT" \
  --container-name "$CONTAINER_NAME" \
  --auth-mode login \
  --output table
```

---

## Excluir laboratório

```bash
az group delete \
  --name "$RESOURCE_GROUP" \
  --yes \
  --no-wait
```

---

# Troubleshooting

## `az: command not found`

A Azure CLI não está instalada ou não está disponível no `PATH`.

Verifique:

```bash
which az
```

Depois:

```bash
az version
```

---

## Login expirado

Execute novamente:

```bash
az login
```

---

## Assinatura incorreta

Verifique:

```bash
az account show \
  --output table
```

Selecione:

```bash
az account set \
  --subscription "NOME_OU_ID"
```

---

## Resource Group não encontrado

Verifique:

```bash
az group list \
  --output table
```

Também confira a assinatura atual:

```bash
az account show \
  --query name \
  --output tsv
```

---

## Nome já utilizado

Alguns recursos exigem nomes globalmente únicos.

Exemplos:

```text
Storage Account

Key Vault

Container Registry

Web App
```

Escolha outro nome.

---

## Permissão negada

Erro comum:

```text
AuthorizationFailed
```

Verifique:

* Usuário autenticado
* Assinatura ativa
* Role atribuída
* Escopo da permissão
* Resource Group utilizado

---

## Comando não reconhecido

Verifique a versão:

```bash
az version
```

Atualize:

```bash
az upgrade
```

Confira se o recurso depende de extensão:

```bash
az extension list \
  --output table
```

---

## Resultado vazio

Remova temporariamente o `--query`:

```bash
az resource list \
  --output json
```

Depois ajuste a expressão JMESPath.

---

## Erro no AKS

Verificar cluster:

```bash
az aks show \
  --resource-group "$RESOURCE_GROUP" \
  --name "$AKS_NAME"
```

Atualizar credenciais:

```bash
az aks get-credentials \
  --resource-group "$RESOURCE_GROUP" \
  --name "$AKS_NAME" \
  --overwrite-existing
```

Testar:

```bash
kubectl cluster-info
```

---

# Boas Práticas

✅ Utilize variáveis para nomes repetidos:

```bash
RESOURCE_GROUP="rg-devops-lab"
```

---

✅ Utilize `set -Eeuo pipefail` em scripts Bash.

---

✅ Utilize `--output none` quando a resposta não for necessária.

---

✅ Utilize `--query` e `--output tsv` para capturar valores.

---

✅ Defina versões específicas para imagens de containers:

```text
minha-api:1.0.3
```

Evite:

```text
minha-api:latest
```

---

✅ Utilize Service Principals somente em automações que realmente precisam deles.

---

✅ Prefira Managed Identity quando o script estiver sendo executado dentro do Azure.

---

✅ Conceda o menor nível de permissão necessário.

---

✅ Nunca armazene secrets diretamente em scripts.

---

✅ Utilize Azure Key Vault para informações sensíveis.

---

✅ Utilize tags para identificar ambiente, projeto e responsável.

---

✅ Exclua ambientes de laboratório após os estudos para evitar recursos ativos desnecessários.

---

# Comandos que exigem cuidado

## Excluir Resource Group

```bash
az group delete \
  --name NOME \
  --yes
```

Remove os recursos do grupo.

---

## Excluir AKS

```bash
az aks delete \
  --resource-group GRUPO \
  --name CLUSTER \
  --yes
```

---

## Excluir Storage Account

```bash
az storage account delete \
  --resource-group GRUPO \
  --name STORAGE \
  --yes
```

---

## Exibir credenciais

```bash
az storage account show-connection-string
```

```bash
az keyvault secret show
```

Evite executar esses comandos em ambientes nos quais a saída possa ser registrada.

---

# Cheat Sheet

```bash
# Versão
az version

# Ajuda
az --help
az group --help
az group create --help

# Login
az login
az logout

# Conta
az account show
az account list -o table
az account set --subscription "ASSINATURA"

# Regiões
az account list-locations -o table

# Resource Groups
az group create -n GRUPO -l brazilsouth
az group list -o table
az group show -n GRUPO
az group exists -n GRUPO
az group delete -n GRUPO --yes

# Recursos
az resource list -g GRUPO -o table

# VMs
az vm list -d -o table
az vm create -g GRUPO -n VM --image Ubuntu2204 --generate-ssh-keys
az vm start -g GRUPO -n VM
az vm stop -g GRUPO -n VM
az vm deallocate -g GRUPO -n VM
az vm restart -g GRUPO -n VM
az vm delete -g GRUPO -n VM --yes

# Storage
az storage account create \
  -n STORAGE \
  -g GRUPO \
  -l brazilsouth \
  --sku Standard_LRS

az storage account list -o table
az storage container create
az storage blob upload
az storage blob list

# ACR
az acr create -g GRUPO -n REGISTRY --sku Basic
az acr login -n REGISTRY
az acr build -r REGISTRY -t api:1.0.0 .
az acr repository list -n REGISTRY -o table

# App Service
az appservice plan create
az webapp create
az webapp list -o table
az webapp restart
az webapp log tail
az webapp deploy

# AKS
az aks create
az aks list -o table
az aks get-credentials
az aks scale
az aks stop
az aks start
az aks delete

# Key Vault
az keyvault create
az keyvault secret set
az keyvault secret list
az keyvault secret show

# Service Principal
az ad sp create-for-rbac

# Managed Identity
az login --identity

# Extensões
az extension list -o table
az extension add --name EXTENSAO
az extension update --name EXTENSAO
az extension remove --name EXTENSAO

# Saída
az group list -o json
az group list -o table
az group list -o tsv
az group list -o yaml

# Query
az group list \
  --query "[].{Nome:name,Regiao:location}" \
  -o table

# Configuração
az config set defaults.group=GRUPO
az config set defaults.location=brazilsouth
```

---

# Resumo

Neste capítulo você aprendeu:

* O que é a Azure CLI
* Como instalar e atualizar a ferramenta
* Como realizar autenticação
* Como selecionar assinaturas
* Como criar Resource Groups
* Como consultar e filtrar recursos
* Como administrar máquinas virtuais
* Como trabalhar com Storage Accounts
* Como criar e utilizar um Azure Container Registry
* Como publicar aplicações no App Service
* Como criar e administrar clusters AKS
* Como utilizar Azure Key Vault
* Como autenticar automações com Service Principals
* Como utilizar Managed Identities
* Como criar scripts Bash para provisionamento
* Como utilizar JMESPath e formatos de saída
* Boas práticas de segurança e automação

---

# Próximo capítulo

No **Capítulo 17 — CI/CD**, aprenderemos:

* Conceitos de Integração Contínua
* Conceitos de Entrega e Implantação Contínua
* GitHub Actions
* Azure DevOps Pipelines
* Jobs e Steps
* Variáveis e Secrets
* Build de aplicações .NET
* Execução de testes
* Build de imagens Docker
* Push para Azure Container Registry
* Deploy no Azure App Service
* Deploy no Azure Kubernetes Service
* Estratégias de rollback
* Aprovações e ambientes
