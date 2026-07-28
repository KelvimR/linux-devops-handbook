# Capítulo 21 — Terraform

> **Terraform** é uma ferramenta de **Infrastructure as Code (IaC)** que permite criar, alterar e destruir infraestrutura utilizando código. Em vez de criar recursos manualmente pelo portal da nuvem, toda a infraestrutura passa a ser versionada, reproduzível e automatizada.

---

# Objetivos

Ao final deste capítulo você será capaz de:

* Entender Infrastructure as Code (IaC)
* Instalar o Terraform
* Compreender Providers
* Criar Resources
* Utilizar Variables
* Utilizar Outputs
* Trabalhar com arquivos `.tf`
* Entender o Terraform State
* Utilizar Remote State
* Criar Modules
* Provisionar recursos no Azure
* Integrar Terraform com GitHub Actions
* Aplicar boas práticas

---

# O que é Infrastructure as Code?

Infrastructure as Code (IaC) é a prática de definir infraestrutura utilizando código.

Ao invés de criar recursos manualmente:

```text id="gk8a2u"
Portal Azure

↓

Criar Resource Group

↓

Criar Storage

↓

Criar App Service

↓

Criar Banco
```

Utilizamos arquivos:

```text id="wbf84q"
main.tf

variables.tf

outputs.tf
```

---

# Benefícios

* Infraestrutura versionada
* Reprodutibilidade
* Padronização
* Automação
* Auditoria
* Facilidade de rollback
* Integração com CI/CD
* Redução de erros manuais

---

# Terraform Workflow

Fluxo típico:

```text id="j1p9pk"
Escrever código

↓

terraform init

↓

terraform fmt

↓

terraform validate

↓

terraform plan

↓

terraform apply

↓

Infraestrutura criada
```

---

# Instalação

Verificar versão:

```bash id="r8z3wb"
terraform version
```

Ajuda:

```bash id="wy5b8y"
terraform -help
```

---

# Estrutura de um projeto

```text id="9grg0l"
terraform/

├── main.tf

├── variables.tf

├── outputs.tf

├── providers.tf

├── versions.tf

├── terraform.tfvars

└── modules/
```

---

# Arquivos mais comuns

| Arquivo            | Finalidade            |
| ------------------ | --------------------- |
| `main.tf`          | Recursos principais   |
| `variables.tf`     | Variáveis             |
| `outputs.tf`       | Saídas                |
| `providers.tf`     | Providers             |
| `versions.tf`      | Versões               |
| `terraform.tfvars` | Valores das variáveis |

---

# Primeiro arquivo

```hcl id="7ntj3m"
terraform {

  required_version = ">= 1.8"
}
```

---

# HCL

Terraform utiliza a linguagem **HCL (HashiCorp Configuration Language)**.

Exemplo:

```hcl id="guw0cx"
resource "TIPO" "NOME" {

}
```

---

# Providers

Provider é o componente responsável por conversar com uma plataforma.

Exemplos:

```text id="nfd7to"
Azure

AWS

Google Cloud

Docker

Kubernetes

GitHub

Cloudflare
```

---

# Configurando Azure Provider

```hcl id="r9wqcx"
terraform {

  required_providers {

    azurerm = {

      source = "hashicorp/azurerm"

      version = "~> 4.0"

    }

  }

}
```

---

Provider:

```hcl id="bh8p2n"
provider "azurerm" {

  features {}
}
```

---

# Inicializando

```bash id="vq4bph"
terraform init
```

Esse comando:

* Baixa Providers
* Cria `.terraform`
* Gera `.terraform.lock.hcl`

---

# Estrutura após init

```text id="yvsq7g"
terraform/

├── .terraform/

├── .terraform.lock.hcl

├── main.tf

├── variables.tf

└── outputs.tf
```

---

# Resource

Todo recurso segue:

```hcl id="tyubiq"
resource "TIPO" "NOME" {

}
```

---

# Primeiro Resource

Resource Group:

```hcl id="rg34c0"
resource "azurerm_resource_group" "rg" {

  name = "rg-devops"

  location = "Brazil South"

}
```

---

# Referenciando recursos

```hcl id="zngffh"
azurerm_resource_group.rg.name
```

ou

```hcl id="4qzjlwm"
azurerm_resource_group.rg.location
```

---

# Variáveis

Arquivo:

```text id="7fr1d0"
variables.tf
```

---

Criando variável:

```hcl id="oz0xtv"
variable "location" {

  type = string

}
```

---

Utilização:

```hcl id="8ddn6x"
location = var.location
```

---

# Valor padrão

```hcl id="gk0n3h"
variable "location" {

  type = string

  default = "Brazil South"

}
```

---

# Tipos

```hcl id="jlwmhe"
string

number

bool

list

map

object
```

---

# Exemplos

String

```hcl id="m0jffk"
variable "nome" {

  type = string
}
```

---

Número

```hcl id="z0o8gb"
variable "replicas" {

  type = number
}
```

---

Boolean

```hcl id="c4pw2r"
variable "production" {

  type = bool
}
```

---

Lista

```hcl id="dtsmww"
variable "ips" {

  type = list(string)
}
```

---

Mapa

```hcl id="n93m2t"
variable "tags" {

  type = map(string)
}
```

---

# terraform.tfvars

```hcl id="sl3o8m"
location = "Brazil South"

production = true

replicas = 3
```

---

# Outputs

Arquivo:

```text id="ywqjzv"
outputs.tf
```

---

Exemplo:

```hcl id="stjlwm"
output "resource_group" {

  value = azurerm_resource_group.rg.name

}
```

---

Visualizar:

```bash id="f3c9hp"
terraform output
```

---

Output específico

```bash id="x6m2gh"
terraform output resource_group
```

---

# Formatação

```bash id="2mjlwm"
terraform fmt
```

Recursivo:

```bash id="v3ntre"
terraform fmt -recursive
```

---

# Validação

```bash id="eqm2rn"
terraform validate
```

---

# Planejamento

```bash id="yz5m1d"
terraform plan
```

Terraform mostra:

```text id="onx9qm"
+ criar

~ alterar

- remover
```

---

Salvar plano:

```bash id="twu2aq"
terraform plan \
  -out=tfplan
```

---

Aplicar plano salvo

```bash id="i1dpso"
terraform apply tfplan
```

---

# Aplicação

```bash id="v8kdfq"
terraform apply
```

Sem confirmação:

```bash id="b4pqma"
terraform apply -auto-approve
```

> Em produção, prefira revisar o plano antes da aplicação.

---

# Destruir infraestrutura

```bash id="gl4d3x"
terraform destroy
```

Sem confirmação:

```bash id="o7dzv4"
terraform destroy -auto-approve
```

---

# Terraform State

Terraform precisa saber quais recursos já existem.

Arquivo:

```text id="9h9rqb"
terraform.tfstate
```

---

Ele contém:

* IDs
* Recursos
* Dependências
* Atributos

---

Nunca edite manualmente.

---

# Mostrar State

```bash id="r6bmdk"
terraform show
```

---

Listar recursos

```bash id="5xk5tf"
terraform state list
```

---

Detalhes

```bash id="d2b8fv"
terraform state show \
azurerm_resource_group.rg
```

---

# Refresh

```bash id="x6e5ha"
terraform plan -refresh-only
```

---

# Dependências

Terraform identifica dependências automaticamente.

Exemplo:

```hcl id="jl2sqh"
resource_group_name =
azurerm_resource_group.rg.name
```

---

# Resource Group + Storage

```hcl id="ib17bn"
resource "azurerm_storage_account" "storage" {

  name = "storagekelvim123"

  resource_group_name =
    azurerm_resource_group.rg.name

  location =
    azurerm_resource_group.rg.location

  account_tier = "Standard"

  account_replication_type = "LRS"

}
```

---

# Tags

```hcl id="q9sjr7"
tags = {

  ambiente = "dev"

  projeto = "terraform"

}
```

---

# Locals

```hcl id="q8z1m7"
locals {

  prefix = "devops"

}
```

Uso:

```hcl id="8pj6gn"
name = "${local.prefix}-rg"
```

---

# Expressions

```hcl id="6xmrpn"
"${var.nome}-api"
```

---

# Condicional

```hcl id="fdo7jv"
count = var.production ? 2 : 1
```

---

# Count

```hcl id="bkp4jc"
resource "..." "vm" {

  count = 3

}
```

Acesso:

```hcl id="o3wxpf"
vm[0]

vm[1]
```

---

# For Each

```hcl id="uvsh6n"
for_each = toset([

"dev",

"qa",

"prod"

])
```

---

# Dynamic Blocks

Permitem gerar blocos dinamicamente.

Exemplo conceitual:

```hcl id="w2y7c8"
dynamic "ip_rule" {

}
```

---

# Data Sources

Consultar recursos existentes.

```hcl id="d4a3ke"
data "azurerm_client_config" "current" {}
```

Uso:

```hcl id="k4r6pf"
data.azurerm_client_config.current
```

---

# Modules

Um módulo é um conjunto reutilizável de recursos.

Estrutura:

```text id="gmq2c6"
modules/

└── storage/

    ├── main.tf

    ├── variables.tf

    └── outputs.tf
```

---

Chamando módulo:

```hcl id="tlq1o8"
module "storage" {

  source = "./modules/storage"

}
```

---

# Remote Modules

```hcl id="gl0tw5"
module "network" {

  source =
  "git::https://github.com/empresa/modules.git//network"

}
```

---

# Outputs do módulo

```hcl id="d0u9zs"
module.storage.storage_name
```

---

# Workspaces

Listar:

```bash id="5r9wuj"
terraform workspace list
```

Criar:

```bash id="86v1cy"
terraform workspace new dev
```

Trocar:

```bash id="0g8gnq"
terraform workspace select prod
```

Atual:

```bash id="3vcz6v"
terraform workspace show
```

---

# Backend

Por padrão:

```text id="2ktjlwm"
terraform.tfstate
```

local.

Em produção:

```text id="9wtxlu"
Azure Storage

S3

Terraform Cloud
```

---

# Azure Backend

```hcl id="4fg3lr"
terraform {

  backend "azurerm" {}

}
```

Inicializar:

```bash id="a2tdum"
terraform init
```

---

# Importar recurso existente

```bash id="b7iy3o"
terraform import \
azurerm_resource_group.rg \
rg-devops
```

---

# Remover do State

Sem remover da nuvem:

```bash id="smzhv4"
terraform state rm \
azurerm_resource_group.rg
```

---

# Provisioners

Exemplo:

```hcl id="rzc6z1"
provisioner "local-exec" {

  command = "echo Deploy"

}
```

> Prefira provisioners apenas quando realmente necessários.

---

# Azure Login

```bash id="d6gvrm"
az login
```

---

Selecionar Subscription

```bash id="r4sld8"
az account set \
--subscription "Minha Subscription"
```

---

# Exemplo completo

```hcl id="x5a8pr"
terraform {

  required_version = ">= 1.8"

  required_providers {

    azurerm = {

      source = "hashicorp/azurerm"

      version = "~>4.0"

    }

  }

}

provider "azurerm" {

  features {}

}

resource "azurerm_resource_group" "rg" {

  name = "rg-devops"

  location = "Brazil South"

}

resource "azurerm_storage_account" "storage" {

  name = "storagekelvim123"

  resource_group_name =
    azurerm_resource_group.rg.name

  location =
    azurerm_resource_group.rg.location

  account_tier = "Standard"

  account_replication_type = "LRS"

}
```

---

# GitHub Actions

Pipeline:

```yaml id="u5q8gn"
name: Terraform

on:

  push:

    branches:

      - main

jobs:

  terraform:

    runs-on: ubuntu-latest

    steps:

      - uses: actions/checkout@v4

      - uses: hashicorp/setup-terraform@v3

      - run: terraform init

      - run: terraform fmt -check

      - run: terraform validate

      - run: terraform plan
```

---

# Pipeline completo

```text id="tf3n7o"
Checkout

↓

Terraform Init

↓

Fmt

↓

Validate

↓

Plan

↓

Approval

↓

Apply
```

---

# Estrutura recomendada

```text id="o9c5bw"
terraform/

├── modules/

├── envs/

│   ├── dev

│   ├── qa

│   └── prod

├── main.tf

├── providers.tf

├── variables.tf

├── outputs.tf

└── versions.tf
```

---

# Arquivos que não vão para o Git

```text id="l2h6jr"
.terraform/

terraform.tfstate

terraform.tfstate.backup

*.tfvars
```

Exemplo de `.gitignore`:

```text id="0t4fza"
.terraform/
*.tfstate
*.tfstate.*
terraform.tfvars
```

---

# Boas práticas

✅ Utilize módulos.

---

✅ Utilize Remote State.

---

✅ Versione Providers.

---

✅ Utilize `terraform fmt`.

---

✅ Utilize `terraform validate`.

---

✅ Revise o `terraform plan`.

---

✅ Utilize ambientes separados.

---

✅ Utilize `terraform.tfvars` diferentes por ambiente.

---

✅ Nunca faça commit do State local.

---

✅ Nunca faça commit de Secrets.

---

✅ Utilize Azure Key Vault.

---

✅ Utilize GitHub Actions.

---

# Fluxo recomendado

```text id="n9jlwm"
terraform init

↓

terraform fmt

↓

terraform validate

↓

terraform plan

↓

Code Review

↓

terraform apply
```

---

# Laboratório

Criar:

* Resource Group
* Storage Account
* App Service Plan
* App Service

Depois:

* Executar `terraform destroy`

---

# Troubleshooting

## Provider não encontrado

```bash id="lmv4re"
terraform init
```

---

## Sintaxe inválida

```bash id="vjlwm1"
terraform validate
```

---

## Código mal formatado

```bash id="sqj4tw"
terraform fmt
```

---

## Diferença inesperada

```bash id="jlwm44"
terraform plan
```

Compare com a infraestrutura atual antes de aplicar alterações.

---

## State inconsistente

```bash id="tjlwm6"
terraform refresh
```

Ou:

```bash id="jlwm7q"
terraform plan -refresh-only
```

---

# Cheat Sheet

```bash id="fjlwm5"
terraform init

terraform fmt

terraform validate

terraform plan

terraform apply

terraform destroy

terraform show

terraform output

terraform state list

terraform workspace list

terraform workspace new dev

terraform workspace select prod

terraform import

terraform version
```

---

# Resumo

Neste capítulo você aprendeu:

* Infrastructure as Code
* Providers
* Resources
* Variables
* Outputs
* Locals
* Expressions
* Count e For Each
* Data Sources
* Modules
* Workspaces
* Terraform State
* Remote State
* Azure Provider
* GitHub Actions
* Estrutura recomendada
* Boas práticas
* Troubleshooting

---
