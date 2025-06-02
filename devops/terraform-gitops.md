# 如何将 Terraform 与 GitOps 结合使用
原文链接：[How to Use Terraform with GitOps](https://spacelift.io/blog/terraform-gitops)
在本文中，我们将研究如何将 Terraform 与 GitOps 结合使用，解释两者及其好处。然后，我们将继续介绍一些实际示例，展示如何实现和配置 Terraform，以使用集成的 git 仓库和 pipline 在 Azure 中创建云基础设施。如果您不熟悉 Terraform 或不熟悉 GitOps，本文将帮助您入门并运行！

## 什么是 Terraform
Terraform 是由 HashiCorp 开发的基础设施即代码（IaC）工具。它允许您通过人类可读的配置文件来构建、更改、管理和版本化您的基础设施。它提供了一种统一的方式，使用易于学习和阅读的 Hashicorp 配置语言（HCL）语法来定义、预置和管理各种云提供商和服务中的资源。由于 Terraform 允许您在代码文件中描述您的基础设施（服务器、网络、存储等），因此可以在预置基础设施时实现版本控制、更改跟踪、协作和可重复性，从而确保跨环境的一致部署并避免手动配置错误。

Terraform 通过插件支持跨多个云提供商（AWS、Azure、Google Cloud）和本地数据中心的基础设施管理，这些插件将 Terraform 配置转换为每个平台的特定 API 调用。

Terraform 使用声明性方法。您定义基础设施的所需状态，然后 Terraform 会找出实现该状态的必要步骤。与手动发出命令或通过 Web 界面配置资源相比，这是一种更简单的基础设施管理方法。

Terraform 与自动化工具和 CI/CD 管道很好地集成。作为开发工作流程的一部分，您可以利用 Terraform 脚本来自动化基础设施预置和配置更改。

## 什么是 GitOps

GitOps 是一个操作框架，它利用流行的版本控制系统 Git 来管理基础设施和应用程序。它实施用于应用程序开发的 [DevOps最佳实践](https://spacelift.io/blog/devops-best-practices) ，例如版本控制、协作和 CI/CD（持续集成和持续交付），去实现自动化基础设施预置和部署。

Git 存储库充当存储和管理所有基础设施和应用程序配置的中心位置，确保每个人都从同一来源工作，增强协作并建立单一事实来源。通过 Git 版本控制跟踪配置更改，从而轻松回滚到以前的版本并提供清晰的审计跟踪。

您通过 YAML或JSON 等声明性语言定义想要的基础设施和应用的所需状态，以及专用工具会处理如何实现该状态。

与 Git 集成的 CI/CD 管道可以自动执行开发生命周期的各个阶段。例如，当提交配置更改并合并到主分支时，管道可以自动触发，根据新配置构建基础设施或应用程序，并将其部署到目标环境。

阅读更多： [改进工作流程的 15 个 GitOps 最佳实践](https://spacelift.io/blog/gitops-best-practices)

## GitOps 的工作原理是什么？
GitOps 通常涉及一个工作流，其中包括一个持续的反馈循环，以确保您的基础设施和应用程序始终反映存储在 Git 存储库中的最新配置。

![Gitops工作流](https://spacelift.io/_next/image?url=https%3A%2F%2Fspaceliftio.wpcomstaging.com%2Fwp-content%2Fuploads%2F2024%2F07%2Fgitops-workflow.png&w=1920&q=75)

GitOps 工作流可以包括以下步骤：
1. 开发人员进行更改 ：开发人员按照定义的分支和拉取请求工作流程修改 Git 存储库中的基础设施或应用程序配置，以供审查和批准。
2. 触发 CI/CD 管道 ：将更改合并到主分支后，CI/CD 管道会自动启动。
3. 应用配置：CI/CD 管道与 Git 存储库交互以检索最新的配置文件。
4. 强制执行所需状态 ：管道（或 GitOps 运算符）将所需状态转换为底层基础架构平台的作（例如，创建 VM 和更新配置）。
5. 基础设施融合： 底层平台会自动预置或更新资源，以匹配 Git 存储库中定义的所需状态。

由于 GitOps 是一个框架，它由多个工具的组合组成（或者它使用提供整体解决方案的平台，例如 GitHub、Spacelift 和 Terraform Cloud）：
|Category  类别|	Tools  工具|
|--|--|
|版本控制	|Git (Azure DevOps, GitHub, GitLab, Bitbucket)|
|CI/CD	|Azure DevOps Pipelines, Jenkins, GitHub Actions, GitLab CI/CD, CircleCI|
|基础设施即代码|	Terraform, Pulumi, Bicep, Cloudformation|
|Kubernetes|Helm, Kustomize, Argo CD, Flux|
|监控和警报|	Prometheus, Grafana|

## 使用 Terraform 和 GitOps 的好处
以下是使用 Terraform 和 GitOps 的一些好处：
- 提升协作：GitOps 通过使用熟悉的 Git 工作流进行基础设施管理，将开发和运营团队聚集在一起。
- 提高可靠性 ：通过维护单一事实来源，版本控制和声明性配置增强了部署的一致性和可重复性，从而减少了错误。同行评审和自动化测试有助于及早发现问题，从而提高整体安全性。
- 简化回滚： 在部署期间出现问题时，版本控制允许您轻松回滚到以前的配置。
- 可审计性：Git 提供清晰的更改历史记录，从而更容易跟踪谁进行了哪些修改以及何时提供清晰的审计跟踪。
- 可扩展性： 可以更轻松地大规模管理复杂的分布式系统。

## 如何使用 Terraform 实现 GitOps？
将 Terraform 与 GitOps 结合使用从以下四个步骤开始：
### 1. 设置 Git 存储库
创建一个版本控制存储库，其中包含您的所有 Terraform 配置文件和模块。使用不同环境（例如，开发、暂存、生产）的目录和可重用组件的模块，以逻辑方式组织您的存储库。

### 2. 使用 Terraform 配置基础设施
使用 Terraform 配置文件（.tf）定义基础设施代码。定义必要的资源、提供程序和变量来描述您的基础设施。

### 3. 为 Terraform 创建 pipeline 
设置 CI/CD 管道以自动应用 Terraform 配置。管道应包括验证、规划和应用 Terraform 代码的步骤，确保在部署之前审查和测试更改。将此管道与您的版本控制系统集成，以便对存储库的任何推送或合并都会触发管道，从而实现一致的自动化部署流程。

### 4. 使用拉取请求管理您的基础设施
实施一个工作流，其中对 Terraform 代码的所有更改都通过拉取请求（PR）进行。团队成员通过 PR 提出更改，然后在合并到主分支之前对其进行审查和批准。

### 其他注意事项
- 状态管理： 决定存储 Terraform 状态文件的位置（例如， 使用 DynamoDB 进行状态锁定的 AWS S3 等远程后端）。
- 密钥管理： 使用特定于云的密钥管理服务或其他工具安全地管理敏感信息（例如 API 密钥和密码）
- 监控和日志记录： 为您的 CI/CD 管道和基础设施更改实施监控和日志记录，以确保可见性和可追溯性。


## 示例 - 为 Terraform 设置 GitOps 管道
在此示例中，我们将使用 Azure DevOps 中的管道，使用 Terraform 创建存储帐户，从而在 Azure 云中设置到订阅的部署。

### 1. 设置 Git 存储库和服务连接
在 Azure DevOps 中创建一个项目来存放 Git 存储库和 CI/CD 管道。

还需要在 Azure DevOps 中设置服务连接，以允许它与 Azure 订阅交互。为此，请先在 Azure 中创建一个服务主体，并记下 appId、password 和 tenantId。以下命令显示了如何使用 Azure CLI 执行此作。
```bash
az ad sp create-for-rbac --name "terraform-pipeline-sp" --role Contributor --scopes /subscriptions/{subscription-id
```

在 Azure DevOps 中，转到 Azure DevOps 项目设置。在 Pipelines 部分下，单击 Service connections。使用服务主体凭据为 Azure Resource Manager 创建新的服务连接。

现在，转到项目的 repos 部分并创建一个新的存储库。


### 2. 使用 Terraform 配置基础设施
使用 Terraform 配置文件将基础设施定义为代码。这些文件指定 Azure 资源的所需状态。

main.tf
```
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "main" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = var.storage_account_name
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = var.account_tier
  account_replication_type = var.account_replication_type

  tags = {
    environment = "demo"
  }
}
```

virables.tf
```
variable "resource_group_name" {
  description = "The name of the resource group"
  default     = "myResourceGroup"
}

variable "location" {
  description = "The Azure region to deploy resources"
  default     = "uksouth"
}

variable "storage_account_name" {
  description = "The name of the storage account"
  default     = "mystorageaccount"
}

variable "account_tier" {
  description = "The tier of the storage account"
  default     = "Standard"
}

variable "account_replication_type" {
  description = "The replication type of the storage account"
  default     = "LRS"
}
```

将 Terraform 配置文件提交到 Azure DevOps 中的 Git 存储库。


### 3. 为 Terraform 创建管道
创建新文件以定义管道。此处的变量是使用环境变量直接在管道中声明的，但您也可以在 Terraform 代码的 .tfvars 文件中声明这些变量。如果未声明任何值，则将使用为变量设置的默认值。
- `<service-principal-client-id>` — 服务主体的应用程序 ID。
- `<service-principal-client-secret>` — 服务主体的密码。
- `<azure-subscription-id>` — 您的 Azure 订阅 ID。
- `<azure-tenant-id>` — Azure Active Directory 的租户 ID。
- `your-azure-service-connection` — 您在 Azure DevOps 中创建的 Azure 服务连接的名称


azure-pipelines.yaml

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  ARM_CLIENT_ID: '<service-principal-client-id>'
  ARM_CLIENT_SECRET: '<service-principal-client-secret>'
  ARM_SUBSCRIPTION_ID: '<azure-subscription-id>'
  ARM_TENANT_ID: '<azure-tenant-id>'
  TF_VAR_resource_group_name: 'myResourceGroup'
  TF_VAR_location: 'uksouth'
  TF_VAR_storage_account_name: 'mystorageaccount'
  TF_VAR_account_tier: 'Standard'
  TF_VAR_account_replication_type: 'LRS'

stages:
- stage: Terraform
  jobs:
  - job: Terraform
    steps:
    - checkout: self

    - task: UseTerraform@0
      inputs:
        version: 'latest'
        command: 'init'
        workingDirectory: $(System.DefaultWorkingDirectory)

    - task: UseTerraform@0
      inputs:
        command: 'validate'
        workingDirectory: $(System.DefaultWorkingDirectory)

    - task: UseTerraform@0
      inputs:
        command: 'plan'
        workingDirectory: $(System.DefaultWorkingDirectory)
        environmentServiceNameAzureRM: 'your-azure-service-connection'
        additionalArguments: '-out=tfplan'

    - task: UseTerraform@0
      inputs:
        command: 'apply'
        workingDirectory: $(System.DefaultWorkingDirectory)
        environmentServiceNameAzureRM: 'your-azure-service-connection'
        additionalArguments: 'tfplan'
        publishPlanResults: 'true'
```

完成后，将其提交到您的代码存储库。

接下来，转到 Azure DevOps 中的 Pipelines 部分，创建新管道，然后选择上传到存储库的文件。单击 Run 以触发管道。


### 4. 使用拉取请求管理您的基础设施
Azure DevOps 拉取请求（PR）是协作处理代码更改的核心功能。它们有助于在将更改合并到 main 分支之前进行代码审查过程。在大多数基于团队的情况下，您应该通过对拉取请求实施分支策略来强制实施 PR。这些可能包括要求一定数量的批准、强制进行代码覆盖率检查或在合并之前强制执行干净的构建。

在 Azure DevOps 中强制使用 PR 的一种方法是在主分支上启用“需要最少数量的审阅者”策略。这将阻止任何直接将代码更改推送到 main 分支的尝试。

1. 转到 Azure DevOps 项目并导航到 Repos > Branches.
2. 找到主分支并单击“...“菜单。
3. 选择 Branch policies.
4. 在 Build （构建） 部分下，您可能会看到现有策略或 Add policy （添加策略） 选项.
5. 查找名为 Require a minimum number of reviewers （需要最低数量的审阅者） 或类似措辞的策略。如果不存在，请单击 Add policy （添加策略），然后选择 Require a minimum number of reviewers （需要最少数量的审阅者）
6. 启用策略并设置所需的最小审阅者数量（通常为一两个）。
7. 开发人员可以从 Azure DevOps 中的 Web 门户或直接从其 IDE（如 Visual Studio Code）启动 PR。创建后，审阅者可以接受或拒绝合并或向代码添加注释。

## 总结
总之，GitOps 通过利用熟悉的工具（如 Git、拉取请求代码审查和 CI/CD 管道）简化了基础设施管理。Terraform GitOps 特别适合管理云原生部署，但核心原则可以应用于各种基础设施环境。