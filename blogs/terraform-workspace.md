# What are Terraform Workspaces? Overview with Examples - 翻译
> [原文链接](https://spacelift.io/blog/terraform-workspaces), 未使用文档中示例，[个人测试代码](https://github.com/xinpaladin/terraform-workspace.git)
在这篇文章中，我们将介绍 Terraform 的另一个有趣功能——工作区。Workspace 是一种维护部署的多个副本的方法，这些副本可以在旅途中创建和销毁。

## 什么是 Terraform Workspace？

Terraform workspaces 使我们能够管理同一配置的多个部署。当我们使用 Terraform 创建云资源时，资源是在默认工作区(default)中创建的。这是一个非常方便的工具，通过为我们提供资源分配、区域部署、多账户部署等方面的灵活性，让我们可以测试配置。

Terraform 管理的所有资源的信息都存储在状态文件中。将此状态文件存储在安全位置非常重要。每个 Terraform 执行都与一个状态文件相关联，以便进行验证和参考。对 Terraform 配置的任何修改（无论是计划还是应用）始终首先使用状态文件中的引用进行验证，然后将执行结果更新该状态配置文件。

如果你没有有意识地使用任何工作区，那么所有这些都已经发生在默认工作区中。工作区可帮助您使用相同的状态文件(格式相同，内容路径不同)时，隔离相同 Terraform 配置的多个独立部署。


## Terraform 环境和工作区之间有什么区别？
Terraform 环境通常是指基础设施的整体设置，包括定义它的所有配置和资源。另一方面，一个工作区是一个命名状态文件，使您能够管理同一基础设施配置的多个隔离实例。通过保持状态文件独立，工作区有助于防止冲突并简化不同部署的管理。

Check out our guide on How to Manage Multiple Terraform Environments Efficiently.
查看我们的指南，了解如何有效地管理多个 Terraform 环境 。

## Terraform 工作区与 Terraform 模块 对比

Terraform 工作区和 Terraform 模块是两个不同的概念，在 Terraform 生态系统中服务于不同的目的。工作区允许用户通过隔离状态文件，使用相同的配置管理不同的基础设施集合。然而，模块是一起使用的多个资源的逻辑容器，有助于代码的可重用性和更好的组织。

## 如何使用 Terraform workspace 命令
首先，让我们看看帮助中可用的选项：
```bash
terraform workspace --help
Usage: terraform [global options] workspace

  new, list, show, select, and delete Terraform workspaces.

Subcommands:
    delete    Delete a workspace
    list      List Workspaces
    new       Create a new workspace
    select    Select a workspace
    show      Show the name of the current workspace
```
这里的选项非常简单。我们可以使用 workspace 命令列出所有可用的工作区并显示当前选定的工作区。我们还可以创建新的工作区并删除旧的工作区。最后，要在工作区中导航，我们使用 `select` 命令。


### 1. 创建 EC2 实例
为了这篇博文，让我们考虑一个简单的 Terraform 配置，使用以下配置创建 EC2 实例。我们目前对 AMI 值、要创建的实例类型和名称标签使用三个变量。
```
resource "aws_instance" "my_vm" {
  ami           = var.ami //Ubuntu AMI
  instance_type = var.instance_type
 
  tags = {
    Name = var.name_tag,
  }
}
```
如果我们此时运行 terraform plan 命令，它将显示它需要创建一个资源，即 EC2 实例。创建资源时，状态文件将更新其信息和其他属性。

继续创建此 EC2 实例。作为参考，我正在使用 Ubuntu 20.04 映像创建一个 t2.micro 实例。
```
Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_id = (known after apply)
  + public_ip   = (known after apply)

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.my_vm: Creating...
aws_instance.my_vm: Still creating... [10s elapsed]
aws_instance.my_vm: Still creating... [20s elapsed]
aws_instance.my_vm: Still creating... [30s elapsed]
aws_instance.my_vm: Still creating... [40s elapsed]
aws_instance.my_vm: Creation complete after 42s [id=i-07708992d1d3272c1]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-07708992d1d3272c1"
public_ip = "3.73.0.139"
```
As we can see from the output, the EC2 instance was successfully created. Run the plan command again, and see if Terraform wants to perform any additional actions at this point. It probably won’t.
从输出中我们可以看出，EC2 实例已成功创建。再次运行 plan 命令，并查看 Terraform 此时是否要执行任何其他作。它可能不会。


### 2. 运行 `terraform workspace show`

要检查我们当前所在的工作区，请运行以下命令。
```shell
terraform workspace show
default
```
此处的输出显示我们当前位于名为 default 的工作区中。


### 3. 运行 `terraform workspace list`

要确保当前不存在其他工作区，请运行 `list` 命令，如下所示。
```shell
terraform workspace list
* default
```

`list` 命令列出了当前创建的所有工作区，包括默认工作区。默认工作区旁边的开始标记表示我们当前选择的工作区。

### 4. 创建新工作区

让我们创建另一个工作区并选择相同的工作区。我们可以通过运行`new`命令定义新工作区的名称，如下所示。
```shell
terraform workspace new test_workspace
Created and switched to workspace "test_workspace"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
```
在这里，我选择了新 Terraform 工作区的名称"test_workspace"。 请注意，运行此命令会创建新的工作区并切换到该工作区。

### 5. 验证设置

我们可以通过运行 `show` 命令来验证是否进行了此选择，如下所示。
```shell
terraform workspace show
test_workspace
```
当然，另一种验证方法是运行 `list` 命令并查看星号(`*`) 指向的位置。
```shell
terraform workspace list
  default
* test_workspace
```


## Terraform 工作区和状态文件
当我们创建新工作区时，Terraform 会在最初配置的同一远程后端中创建相应的新状态文件。请务必注意，正在使用的后端也应该能够[支持工作区](https://developer.hashicorp.com/terraform/language/state/workspaces).

在此示例中，我使用 AWS S3 存储桶作为远程后端。

当我们查看 Terraform 状态 S3 存储桶的内容时，除了默认的 terraform.tfstate 文件外，我们还可以看到创建了一个名为 "env：/" 的新目录，在该目录中创建了另一个名称为工作区 （test_workspace） 的目录。在此位置维护一个新的 terraform.tfstate 文件。
请忽略下面屏幕截图中的其他详细信息 。Key 列与此相关。

![terraform workspaces and state file](https://spacelift.io/_next/image?url=https%3A%2F%2Fspaceliftio.wpcomstaging.com%2Fwp-content%2Fuploads%2F2022%2F11%2Fterraform-workspaces-and-state-file.png&w=1920&q=75)
```
├── terraform.tfstate
└── env
    └── test_workspace
        └── terraform.tfstate
```

仔细观察，默认状态文件的大小比自定义工作区特定的状态文件的大小大得多。这表明新的状态文件已创建，但它不包含默认状态文件中的任何信息。这就是 Terraform 创建隔离环境并以不同方式维护其状态文件的方式。

运行 `terraform apply` 之前 test_workspace 状态文件的内容如下所示：
```
{
  "version": 4,
  "terraform_version": "1.2.3",
  "serial": 0,
  "lineage": "c1aa5782-da15-419e-70f8-7024cadd0cfe",
  "outputs": {},
  "resources": []
}
```

因此，如果我们现在在同一目录中运行 plan 命令，Terraform 将根据所选工作区考虑状态文件。此状态文件中未捕获或维护任何资源，因此它将建议创建新的 EC2 实例。
```
terraform plan
…
Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_id = (known after apply)
  + public_ip   = (known after apply)
```
**注意**： 计划输出未指定在规划时使用的工作区信息，因此在应用这些更改时请务必非常小心，因为使用错误的工作区可能会破坏现有的工作环境。

尽管在默认工作区中使用相同的配置创建了 EC2 实例，但 Terraform 会忽略其在新工作区中的存在。这为基础设施在各种环境中的管理方式创造了许多可能性。Terraform 工作区的隔离性质用于在将现有配置应用于关键环境之前测试对现有配置的修改，但这只是用例之一。Terraform 工作区的隔离性质用于测试对关键系统的现有配置的修改(例如新建一套进行修改验证，没有错误后再将配置变更应用的实际环境中)，但这只是用例之一。


## 如何删除 Terraform 工作区

要删除工作区，请首先选择其他工作区。在本例中，我们返回到默认工作区并运行 `delete` 命令。Terraform 不允许我们删除当前选定的工作区。
```
terraform workspace select default
Switched to workspace "default".

terraform workspace delete test_workspace
Deleted workspace "test_workspace"!
```

S3 后端中的相应目录结构将与状态文件一起删除。
![terraform workspace delete](https://spacelift.io/_next/image?url=https%3A%2F%2Fspaceliftio.wpcomstaging.com%2Fwp-content%2Fuploads%2F2022%2F11%2Fdelete-test-terraform-workspace.png&w=1920&q=75)
此外，如果您尝试删除存在被Terraform管理的资源的工作区，它不会让您删除该工作区，建议改用 -force 选项。
```
terraform workspace delete test_workspace
╷
│ Error: Workspace is not empty
│ 
│ Workspace "test_workspace" is currently tracking the following resource instances:
│   - aws_instance.my_vm
│ 
│ Deleting this workspace would cause Terraform to lose track of any associated remote objects, which would then require you to delete them manually outside of Terraform. You should destroy these objects with
│ Terraform before deleting the workspace.
│ 
│ If you want to delete this workspace anyway, and have Terraform forget about these managed objects, use the -force option to disable this safety check.
```

使用 -force 选项可能不是一个好主意，因为我们将失去对 Terraform 管理的所有资源的跟踪。更好的选择是选择该工作区，运行 `destroy` 命令，然后再次尝试删除工作区。

**注意**： 无法删除默认工作区。

## 如何使用 Terraform 工作区管理变量
当您需要针对不同环境（如开发、测试、暂存和生产）进行不同配置时，使用 Terraform 工作区管理变量至关重要。

首先，您需要像通常对任何 Terraform 配置所做的那样声明变量。使用 tfvars 文件可以很容易地为这些变量提供值。

对于每个环境，您可以创建一个 tfvars 文件：
```
vars_dev.tfvars
vars_test.tfvars
vars_stage.tfvars
vars_prod.tfvars
```

根据你所在的工作区（假设你在开发工作区上），你将运行`apply`如下：
```shell
terraform apply –var-file=vars_dev.tfvars
```
您还可以根据工作区有条件地将值分配给不同的参数。让我们看一个例子：
```
locals {
  instance_type = terraform.workspace == "prod" ? "t2.large" : "t2.micro"
}
```

如果工作区是 prod， 则上述代码会将 t2.large 赋值给 instance_type 局部变量，如果选择了任何其他工作区，则为 t2.micro 。

There is also an option to set up environment variables conditionally based on the workspace you are on, but this implementation uses a differentwill take advantage of another scripting language (bash/powershell/python), and you will need to create the logic of the script yourself. That’s why using multiple tfvars files makes the most sense in this approach.
也可以利用另外的脚本语言(bash/powershell/python)实现根据不同的工作区设置不同的环境变量，需要自己写逻辑脚本。这就是为什么在这种方法中使用多个 tfvars 文件最有意义的原因。

作为最佳实践，您应该尽可能为变量分配默认值，尤其是在使用工作区时，以避免在 tfvars 文件中重复代码。这将使您的配置不易出错

## Terraform 工作区插值
有了 Terraform 工作区的基础知识，在 Terraform 配置对象中使用这些知识来识别属于相应工作区的资源是有意义的。

例如，使用上一个示例中的相同配置创建的 EC2 实例将使用相同的名称创建，即 name_tag 变量中指定的任何值。当我们在 AWS 控制台中查看这些实例时，很难快速识别哪个 EC2 实例属于哪个工作区。

Terraform 提供了一个内置变量来引用当前所选工作区的值，如下所示：
```
${terraform.workspace}
```

让我们使用它来根据所选的相应工作区设置我们的名称标签。在下面的配置中，我们将 name_tag 变量设置为默认值 EC2。aws_instance 资源块将此变量与工作区内置变量结合使用，以设置不同的相应名称。
```
variable "name_tag" {
  type        = string
  description = "Name of the EC2 instance"
  default     = "EC2"
}
 
resource "aws_instance" "my_vm" {
  ami           = var.ami //Ubuntu AMI
  instance_type = var.instance_type
  
  tags = {
    Name = format("%s_%s", var.name_tag, terraform.workspace)
  }
}
```

注意： format() 函数用于将多个字符串连接起来以获得有效的 name 值。

我们删除了上一节中的工作区，因此我们还创建一个名为“test”的新工作区，并在工作区（default 和 test）中创建 EC2 实例。请参阅下面的控制台输出：
```
terraform workspace list
* default



terraform apply
…
Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_id = (known after apply)
  + public_ip   = (known after apply)

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.my_vm: Creating...
aws_instance.my_vm: Still creating... [10s elapsed]
aws_instance.my_vm: Still creating... [20s elapsed]
aws_instance.my_vm: Still creating... [30s elapsed]
aws_instance.my_vm: Creation complete after 31s [id=i-0c0a6ffa4405249d7]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-0c0a6ffa4405249d7"
public_ip = "3.122.229.252"




terraform workspace new test
Created and switched to workspace "test"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.



terraform apply
…
Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_id = (known after apply)
  + public_ip   = (known after apply)

Do you want to perform these actions in workspace "test"?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.my_vm: Creating...
aws_instance.my_vm: Still creating... [10s elapsed]
aws_instance.my_vm: Still creating... [20s elapsed]
aws_instance.my_vm: Still creating... [30s elapsed]
aws_instance.my_vm: Creation complete after 32s [id=i-0362373fe324e402f]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-0362373fe324e402f"
public_ip = "3.72.73.27"
```

在这里，两个 EC2 实例是使用相同的配置创建的，但位于不同的工作区中。要验证内置变量是否有效，请登录 AWS 控制台并验证新创建的 EC2 实例的名称。
![terraform workspaces - EC2 instances](https://spacelift.io/_next/image?url=https%3A%2F%2Fspaceliftio.wpcomstaging.com%2Fwp-content%2Fuploads%2F2022%2F11%2Fterraform-workspaces-EC2-instances.png&w=1920&q=75)

正如我们所看到的，名称已按预期设置，现在我们可以轻松识别哪个实例属于哪个 Terraform 工作区

## 使用 Terraform 工作区的特定于环境的资源要求

使用 Terraform 工作区使我们能够隔离生产和子生产环境的基础设施管理，我们还可以利用工作区插值序列为它们分配适当的资源。

这有助于我们避免创建临时子生产环境的不必要成本，因为这些环境可以是原始配置的缩小版本。通过工作区插值序列和条件，配置得到了改进，如下所示。

在下面显示的示例中，我们使用工作区插值序列来定义要根据所选工作区创建的 EC2 实例数。如果选择了默认工作区，则给定的配置将创建三个实例，而对于所有其他工作区，它只会创建一个实例。
```
variable "name_tag" {
 type        = string
 description = "Name of the EC2 instance"
 default     = "EC2"
}
 
resource "aws_instance" "my_vm" {
 count         = terraform.workspace == "default" ? 3 : 1
 ami           = var.ami //Ubuntu AMI
 instance_type = var.instance_type
 
 tags = {
   Name = format("%s_%s_%s", var.name_tag, terraform.workspace, count.index)
 }
}
```

此外，还对 Name 标记进行了相应的更改，以包括 count 索引以区分多个实例。当我们在 default 和 test 工作区（我们在上一节中创建）中应用此配置时，我们应该能够看到以下名称为：
```
EC2_default_0
EC2_default_1
EC2_default_2
EC2_test_0
```

让我们重复这些步骤，在两个工作区中应用此配置，如上一节的控制台输出中所述。下面的屏幕截图显示了在 default 和 test 工作区中创建的相应 EC2 实例。

![Environment-specific resource requirements using workspaces](https://spacelift.io/_next/image?url=https%3A%2F%2Fspaceliftio.wpcomstaging.com%2Fwp-content%2Fuploads%2F2022%2F11%2FEnvironment-specific-resource-requirements-using-workspaces.png&w=1920&q=75)

因此，我们已经能够使用 Terraform 工作区的插值序列来限制瞬态环境的资源利用率。同样，我们可以将 workspaces 的概念用于更具体的用例。

## Git 分支与 Terraform 工作区

您不应将版本控制系统中的分支与 Terraform 工作区混淆。两者有不同的目的。

Git 分支维护用于开发新功能或 Terraform 模块的相同配置的各种版本控制副本，而工作区完全依赖于 Terraform 在远程后端维护的状态文件。

通常，不建议在默认工作区中使用功能分支进行部署。下表总结了各种组合的影响。它假定：

1. Terraform 配置在 Git 存储库中维护
2. 工作区用于创建副本集，用于调试或开发目的
3. 为 Terraform 工作流配置了远程后端

||默认工作区|测试工作区|
|:--:|--|--|
|main分支|这是理想的场景。|当我们想要创建现有环境的缩小副本以进行调试或开发时。|
|功能分支|严格没有。功能分支可能包含仍在开发中的配置和模块。因此，应不惜一切代价避免使用默认工作区进行部署。|可能不会破坏生产，但肯定会干扰其他人正在进行的工作。也许可以考虑创建一个新的工作区。|

使用 Terraform 时，如果使用工作区，则它们优先于版本控制策略。

## Terraform 工作区最佳实践
如上一节所述，在 Terraform 工作流中引入工作区以及现有的 Git 实践也会增加人为错误的风险 。如果团队不擅长结合使用工作区和分支，则错误部署基础设施的可能性很高。

正如我们之前看到的，工作区创建了一个单独的工作目录结构来存储状态文件。这也意味着每个工作区的插件和模块都是单独缓存的。在开发人员可以创建自己的工作区来测试其更改的团队中，这可能会导致远程后端主机上出现带宽和空间问题。

工作区最适合用于在生产部署之前测试基础设施的隔离副本中的更改。它们是临时的，可能不是管理多个暂存环境的最佳解决方案，因为组织通常希望这些环境严格分开。