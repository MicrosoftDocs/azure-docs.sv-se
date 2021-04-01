---
title: Distribuera med terraform från Azure Cloud Shell | Microsoft Docs
description: Distribuera med terraform från Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform
ms.openlocfilehash: e13e6d9ac2f4600e41f221efd23997f712dffc54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92745444"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Distribuera med terraform från bash i Azure Cloud Shell
Den här artikeln vägleder dig genom att skapa en resurs grupp med [terraform AzureRM-providern](https://www.terraform.io/docs/providers/azurerm/index.html).

[Hashicorp terraform](https://www.terraform.io/) är ett verktyg med öppen källkod som Codifies-API: er i deklarativ konfigurationsfiler som kan delas mellan team medlemmar som ska redige ras, granskas och versions hantering. Microsoft AzureRM-providern används för att interagera med resurser som stöds av Azure Resource Manager via AzureRM-API: er.

## <a name="automatic-authentication"></a>Automatisk autentisering
Terraform installeras i bash i Cloud Shell som standard. Dessutom autentiserar Cloud Shell automatiskt Azure CLI-prenumerationen för att distribuera resurser via terraform Azure-moduler.

Terraform använder standard Azure CLI-prenumerationen som har angetts. Om du vill uppdatera standard prenumerationer kör du:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Genomgång
### <a name="launch-bash-in-cloud-shell"></a>Starta bash i Cloud Shell
1. Starta Cloud Shell från önskad plats
2. Verifiera att den önskade prenumerationen har angetts

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Skapa en terraform-mall
Skapa en ny terraform-mall med namnet main.tf med din önskade text redigerare.

```
vim main.tf
```

Kopiera/klistra in följande kod i Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Spara filen och avsluta text redigeraren.

### <a name="terraform-init"></a>Terraform init
Börja med att köra `terraform init` .

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

[Terraform init-kommandot](https://www.terraform.io/docs/commands/init.html) används för att initiera en arbets katalog som innehåller konfigurationsfiler för terraform. `terraform init`Kommandot är det första kommandot som ska köras efter att du har skrivit en ny terraform-konfiguration eller klonat en befintlig från versions kontroll. Det är säkert att köra det här kommandot flera gånger.

### <a name="terraform-plan"></a>Terraform-plan
Förhandsgranska resurserna som ska skapas av terraform-mallen med `terraform plan` .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

Kommandot [terraform plan](https://www.terraform.io/docs/commands/plan.html) används för att skapa en körningsplan. Terraform utför en uppdatering, om det inte uttryckligen inaktive ras, och avgör sedan vilka åtgärder som krävs för att uppnå det önskade tillstånd som anges i konfigurationsfilerna. Planen kan sparas med-out och tillhandahålls sedan terraform för att säkerställa att endast de i förväg planerade åtgärderna utförs.

### <a name="terraform-apply"></a>Terraform-kommandot apply
Etablera Azure-resurserna med `terraform apply` .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Kommandot terraform Apply](https://www.terraform.io/docs/commands/apply.html) används för att tillämpa de ändringar som krävs för att uppnå önskat tillstånd för konfigurationen.

### <a name="verify-deployment-with-azure-cli"></a>Verifiera distribution med Azure CLI
Kör `az group show -n myRgName` för att kontrol lera att resursen har slutfört etableringen.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Rensa med terraform-förstöring
Rensa resurs gruppen som skapats med [kommandot terraform förstör](https://www.terraform.io/docs/commands/destroy.html) för att rensa terraform-skapade infrastruktur.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Du har skapat en Azure-resurs via terraform. Besök nästa steg för att fortsätta lära dig mer Cloud Shell.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Azure-providern för terraform](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash i Cloud Shell snabb start](quickstart.md)
