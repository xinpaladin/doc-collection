# Terraform Azure (AzureRM) Provider: How to Use & Examples - 翻译

> [原文链接](https://spacelift.io/blog/terraform-azurerm-provider)  因原文示例资源已废弃，对原文示例做了修改([代码](https://github.com/xinpaladin/terraform-azure-setup.git)), 个人环境如下
```
Terraform v1.12.0
azure-cli 2.74.0
registry.terraform.io/hashicorp/azurerm 3.117.1
```

将 Terraform 与 Azure 结合使用，可以使用基础结构即代码 （IaC） 自动预配和管理 Azure 资源。

Terraform 支持几乎所有 Azure 服务，并直接与 Azure Resource Manager (ARM) API 集成。这意味着您可以在代码中定义基础架构，例如虚拟机、网络或数据库，通过版本控制跟踪更改，并轻松地跨团队协作。

本文内容如下：
[toc]



## AzureRM Terraform provider 是什么?

AzureRM Terraform provider是一个插件，它可以让你直接通过 Terraform 管理 Microsoft Azure 基础架构。它充当 .tf 配置文件和 Azure Resource Manager (ARM) API 之间的桥梁。

AzureRM Terraform provider 允许你预配服务，例如 Azure Kubernetes Service (AKS)、应用服务、虚拟网络、密钥管理等。这使其成为 Azure 生态系统中基础设施即代码（IaC） 工作流的基础工具。

### Terraform AzureRM 提供程序的主要功能

AzureRM provider 的主要功能包括：

- 全面的 Azure 资源覆盖：支持广泛的 Azure 服务，包括虚拟机、存储帐户、虚拟网络、数据库和 Kubernetes 集群。
- 支持模块(modules)和可重用性：鼓励通过可重用组件构建模块化代码，使配置更具可扩展性和可维护性。
- 身份认证选项 ：提供多种身份认证方法，包括服务主体、托管标识和 Azure CLI。
- 资源标记和生命周期规则 ：支持为组织和生命周期块打标记，以管理资源的创建和删除行为。

## AzureRM Terraform provider 配置项
下面是 AzureRM Terraform provider最重要的配置选项：
|配置项|描述|
|:--:|:--:|
|features	    |启用提供程序所需的块（即使为空）|
|subscription_id	|显式设置要使用的 Azure 订阅|
|client_id	    |用于身份验证的 Azure AD app/client ID|
|client_secret	|client app使用的密钥/密码|
|tenant_id	    |Azure AD 租户ID|
|environment	               |Azure 云环境（例如， 公共云）|
|client_certificate_path	   |用于身份验证的 PEM/PKCS12 证书的路径（如果不使用 secret）|
|client_certificate_password|证书身份验证的密码（.pfx 证书需要）|
|skip_provider_registration |跳过 Azure resource providers的自动注册|
|auxiliary_tenant_ids	   |跨租户操作的租户ID 列|


## Terraform Azure provider 的身份验证方法

使用 Terraform Azure Provider（也称为 azurerm provider）时，有多种身份验证方法可用于将 Terraform 安全地连接到 Azure。每种方法都有特定的用例和要求。以下是每个选项的详细分类：

### 1. 具有客户端密钥的服务主体(推荐用于自动化)

这是在自动化场景（如 CI/CD 管道或周期性的 Terraform 运行）中最常用的身份验证方法。服务主体(Service Principal) 通过 Azure 基于角色的访问控制（RBAC）被授予适当的权限后, 充当 Terraform 部署的身份

您可以通过提供 Client ID, Client Secret, Tenant ID, and Subscription ID 进行身份验证。这些可以通过环境变量进行配置：
```bash
export ARM_CLIENT_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
export ARM_CLIENT_SECRET="your-client-secret"
export ARM_SUBSCRIPTION_ID="your-subscription-id"
export ARM_TENANT_ID="your-tenant-id"
```

或者在 Terraform provider block中指定:
```
provider "azurerm" {
  features = {}
  client_id       = var.client_id
  client_secret   = var.client_secret
  tenant_id       = var.tenant_id
  subscription_id = var.subscription_id
}
```

此方法易于实现且受到广泛支持，但它需要安全地处理和轮换密码，因此建议使用 Azure Key Vault 或其他密码管理器来管理它们

### 2. 具有客户端证书的服务主体(Service Principal)

这个验证方法将密钥替换为证书，从而提供增强的安全性和集中式证书生命周期管理。
```
provider "azurerm" {
  features = {}
  client_id                  = var.client_id
  client_certificate_path    = var.cert_path
  client_certificate_password = var.cert_password
  tenant_id                  = var.tenant_id
  subscription_id            = var.subscription_id
}
```

注意： 证书管理增加了作复杂性，尤其是在 CI/CD 管道中。 

### 3. Azure CLI 身份验证

此方法非常适合本地开发，它利用活动的 Azure CLI 会话对 Terraform 进行身份验证。使用 `az login` 登录后，如果未提供其他显式凭据，Terraform 会自动使用会话中的凭据。

```
provider "azurerm" {
  features = {}
}
```

### 4. 托管身份 （MSI） – 系统分配或用户分配

支持此身份验证方法，但请注意 use_msi 标志已弃用。相反，当 Terraform 检测到它在提供托管标识的 Azure 环境（例如 VM、Azure DevOps、AKS）中运行时，它将自动使用托管标识。

设置以下环境变量：
```bash
export ARM_USE_MSI=true
```

对于用户分配的标识：
```bash
export ARM_USE_MSI=true
export ARM_CLIENT_ID="client-id-of-user-assigned-msi"
```

### 5. 基于环境变量的身份验证

此方法依赖于环境变量来提供必要的身份验证凭据。Azure provider 支持一组变量，如 `ARM_CLIENT_ID`、`ARM_CLIENT_SECRET`、`ARM_TENANT_ID` 和 `ARM_SUBSCRIPTION_ID`.

这些变量通常与 CI/CD 工具结合使用，其中在运行时注入密钥是一种常见做法。

### 6. OIDC 联合身份验证（工作负载联合身份验证）

AzureRM v3.0+ 支持的这种新身份验证方法允许 Terraform 在不使用密码或证书的情况下进行身份验证。相反，它使用外部标识提供者（如 GitHub Actions、Azure DevOps 或其他云原生平台）提供的 OpenID Connect(OIDC) token。

Azure AD 应用需要配置为信任来自该提供程序的`token`，然后使用该`token`进行安全身份验证。设置它涉及 Azure 和您的身份提供商中的一些一次性配置，但完成后，它将提供高度安全且可扩展的解决方案。

你需要设置以下环境变量：
```bash
export ARM_CLIENT_ID="your-app-id"
export ARM_TENANT_ID="your-tenant-id"
export ARM_SUBSCRIPTION_ID="your-subscription-id"
export ARM_OIDC_TOKEN_FILE_PATH="/path/to/oidc-token"
```

## 如何设置 AzureRM Terraform provider

在开始使用 Terraform 管理 Azure 中的资源之前，您需要设置 AzureRM provider。这是一个快速的过程，但正确设置它很重要，这样才能顺利进行。

以下是您需要遵循的步骤。

### 1.确保已安装 Terraform

确保您的系统上已安装 Terraform。您可以通过运行以下命令进行检查：
```bash
terraform -v
# 安装 Azure Cli,若已安装可忽略
sudo apt remove azure-cli -y && sudo apt autoremove -y
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
# 检查
az version
```

### 2.创建工作目录

此目录将包含您的 Terraform 配置文件。
```bash
mkdir terraform-azure-setup
cd terraform-azure-setup
```
### 3.在 main.tf 中定义 AzureRM provider

创建一个名为 `main.tf` 的文件 ，您可以在其中定义 AzureRM provider and resources。

`main.tf `的示例内容:
```
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }

  required_version = ">= 1.0.0"
}

provider "azurerm" {
  features {}
}
```

`features {}` 块必须存在，即使它是空的。它用于配置特定于提供程序的行为，例如，存储帐户的软删除设置或某些服务的日志保留策略。 


### 4.使用 Azure 进行身份验证

Terraform 需要凭据来预配 Azure 资源。我们在上面介绍了几种身份验证方法，在此示例中，我们将使用 Service Principal 方法(第一种)。

创建服务主体：
```bash
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<SUBSCRIPTION_ID>"
```
此命令要求用户使用必要的权限登录到 Azure CLI。

然后设置这些环境变量：
```bash
export ARM_CLIENT_ID="<APP_ID>"
export ARM_CLIENT_SECRET="<PASSWORD>"
export ARM_SUBSCRIPTION_ID="<SUBSCRIPTION_ID>"
export ARM_TENANT_ID="<TENANT_ID>"
```
### 5.初始化 Terraform
运行 `terraform init` 命令以下载 AzureRM provider并初始化配置。

### 6.编写示例资源

所有 Azure 资源都必须存在于资源组中。此资源组充当我们基础设施的逻辑边界，支持生命周期控制（例如，使用单个 terraform destroy 拆除整个资源栈）和更轻松的成本管理。 

添加`resource`以测试提供程序是否正常工作。例如：
```
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "cn-east"
}
```

并非所有 Azure 服务在每个区域都可用。选择区域时检查兼容性。

### 7.Plan and apply

运行 `terraform plan` 以查看将创建哪些资源。然后，运行 `terraform apply` 以实际创建它们。

现在可以验证 Azure 门户以查看新创建的资源组。


## 对 Terraform Azure provider 进行版本控制

Terraform 提供商不断发展。对`provider`的每次更新都可能引入：
- 新资源(resources)和功能
- Bug 修复
- 弃用或中断性变更

如果没有约束，每个 terraform init 都可能会拉取最新版本，如果不兼容，这可能会破坏您的代码。这就是版本约束的用武之地。


您可以在 terraform 块中定义约束 ，通常在根模块中：
```
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.50"
    }
  }
  required_version = ">= 1.1.0"
}

provider "azurerm" {
  features {}
}

```

`~> 3.50` 表示：使用从 3.50.0 到 4.0.0 的任何版本 ，但不包括 4.0.0。
`>= 3.50` 表示：使用任何大于等于3.50.0的任何版本

请记住，即使是很小的更新也会带来行为变化。有意固定版本并测试更改。



## 示例：使用 Azure provider 创建 Azure 资源

在此实际示例中，我们将使用 Terraform 和 AzureRM provider 在 Azure 中配置典型的 Web 应用程序资源栈。这包括用于托管应用代码的 App Service 和用于持久存储的 Azure SQL 数据库。

这是内部工具、商业apps或面向公众的 SaaS 平台的常见部署模式。目标不仅仅是预置资源，而是定义一个连贯的云原生基础设施，该基础设施是可重复的、版本控制的，并且易于跨环境管理。

我们假设你使用的是 Terraform v1.3+ 和 AzureRM 提供程序 v3.x 或更高版本。


### 1. 定义 Azure 资源组

我们首先定义一个 resource_group，它用作我们部署的其他所有内容的逻辑边界。
```
resource "azurerm_resource_group" "rg" {
  name     = "example-webapp-rg"
  location = "East US"
}
```
我们选择 "southeastasia" 作为位置，但这可以是适合您的延迟或合规性需求的任何 Azure 区域。

### 2. 创建应用服务计划

在托管 Web 应用程序之前，我们需要定义其计算环境，即`App Service Plan`。此计划抽象化了基础 VM 实例。

在这里，我们使用大小为 S1 的标准层，它为我们提供了对自定义域和 TLS 的支持。Premium 计划中提供了自动缩放功能，因此请考虑在生产工作负载需要时进行升级。
```
resource "azurerm_service_plan" "service_plan" {
  name                = "example-appservice-plan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  os_type             = "Linux"
  sku_name            = "S1"
}
```

这可确保所有资源都位于相同的逻辑和地理边界内。

### 3. 定义应用服务（Web 应用）
接下来，我们定义实际的 Web 应用程序。此资源将指向我们刚刚创建的应用服务计划。

`app_settings` 块允许我们配置运行时行为。该设置 `"WEBSITE_RUN_FROM_PACKAGE" = "1"` 指示 Azure 从 ZIP 包运行应用程序，这是可预测的版本控制版本的推荐部署策略。
```
resource "azurerm_linux_web_app" "app" {
  name                = "example-webapp"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  service_plan_id     = azurerm_service_plan.service_plan.id
  
  site_config {}
  
  app_settings = {
    "WEBSITE_RUN_FROM_PACKAGE" = "1"
  }

  connection_string {
    name  = "DefaultConnection"
    type  = "SQLAzure"
    value = "Server=tcp:${azurerm_mssql_server.sql_server.fully_qualified_domain_name},1433;Initial Catalog=${azurerm_mssql_database.sql_db.name};User ID=${azurerm_mssql_server.sql_server.administrator_login};Password=StrongP@ssword123!;Encrypt=true;Connection Timeout=30;"
  }
}
```
此设置现在已准备好通过 CI/CD 工具（如 Azure DevOps、GitHub Actions 或其他发布自动化管道）接受部署。

### 4. 预配 Azure SQL Server

对于我们的后端数据库，我们首先要配置一个逻辑 SQL Server。这并不直接托管数据，它充当一个或多个数据库的容器。您通常会为每个应用程序环境部署一个 SQL Server，除非您有多租户或其他架构限制。

为简单起见，我们在此处使用静态凭证。在生产环境中，应通过 Azure Key Vault 管理密码，并使用 `azurerm_key_vault_secret` 或通过环境变量通过 Terraform 注入密码 。
```
resource "azurerm_mssql_server" "sql_server" {
  name                         = "mssqlserver"
  resource_group_name          = azurerm_resource_group.rg.name
  location                     = azurerm_resource_group.rg.location
  version                      = "12.0"
  administrator_login          = "sqladminuser"
  administrator_login_password = "StrongP@ssword123!"
  minimum_tls_version          = "1.2"
}
```
### 5. 创建 Azure SQL 数据库
现在，我们定义应用程序将连接到的实际数据库。`sku_name = "S0"` 为大多数中小型工作负载提供了性能和成本的平衡。
```
resource "azurerm_mssql_database" "sql_db" {
  name         = "example-db"
  server_id    = azurerm_mssql_server.sql_server.id
  collation    = "SQL_Latin1_General_CP1_CI_AS"
  license_type = "LicenseIncluded"
  max_size_gb  = 2
  sku_name     = "S0"
  enclave_type = "VBS"

  tags = {
    foo = "bar"
  }

  # prevent the possibility of accidental data loss
  lifecycle {
    prevent_destroy = true
  }
}
```

对于较大或可变的工作负载，请考虑使用 vCore-based 的定价或自动缩放选项。

### 6. 可选：输出

要使您的基础设施更易于访问，尤其是在自动化管道中，请定义输出：
```
output "app_url" {
  value = azurerm_linux_web_app.app.default_hostname
}

output "sql_server_fqdn" {
  value = azurerm_mssql_server.sql_server.fully_qualified_domain_name
}
```

## 其他 Azure Terraform 提供程序

除了 AzureRM 之外，Microsoft 还为 Azure 相关服务提供其他 Terraform 提供程序：
- AzAPI：提供对 Azure 资源管理器 API 的直接访问，这些 API 可用于管理 AzureRM 尚不支持的资源。
- AzureAD：管理 Microsoft Entra （Azure Active Directory） 资源，例如用户、组和应用程序。并非所有 Entra 功能都可用。
- AzureDevOps：管理 Azure DevOps 资源，例如管道和仓库。
- AzureStack：管理混合云环境的 Azure Stack Hub 资源。


