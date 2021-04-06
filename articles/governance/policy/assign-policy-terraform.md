---
title: 'Snabb start: ny princip tilldelning med terraform'
description: I den här snabb starten använder du syntaxen terraform och HCL för att skapa en princip tilldelning som identifierar icke-kompatibla resurser.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93106406"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med terraform

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- [Terraform](https://www.terraform.io/) -version 0.12.0 eller högre konfigurerad i din miljö.
  Instruktioner finns i [Konfigurera terraform med hjälp av Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell).
- Den här snabb starten kräver att du kör Azure CLI version 2.13.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Skapa konfigurations-, variabel-och utdatafilen för terraform

I den här snabb starten skapar du en princip tilldelning och tilldelar definitionen **Granska virtuella datorer som inte använder hanterade diskar** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

Konfigurera först terraform-konfiguration, variabel och utdatafiler. Terraform-resurser för Azure Policy använda [Azure-providern](https://www.terraform.io/docs/providers/azurerm/index.html).

1. Skapa en ny mapp med namnet `policy-assignment` och ändra kataloger till den.

1. Skapa `main.tf` med följande kod:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Skapa `variables.tf` med följande kod:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används i. Det kan vara ett intervall från en hanterings grupp till en enskild resurs. Se till att ersätta `{scope}` med något av följande mönster:

   - Prenumeration: `/subscriptions/{subscriptionId}`
   - Resursgrupp: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Klusterresursen `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Skapa `output.tf` med följande kod:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Initiera terraform och skapa plan

Initiera sedan terraform för att ladda ned nödvändiga providers och skapa sedan en plan.

1. Kör kommandot [terraform init](https://www.terraform.io/docs/commands/init.html) . Det här kommandot hämtar de Azure-moduler som krävs för att skapa Azure-resurserna i terraform-konfigurationen.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Skärm bild av körning av terraform init-kommandot som visar hur du laddar ned azurerm-modulen och ett meddelande om att det lyckades.":::

1. Autentisera med [Azure CLI](/cli/azure/) för terraform. Mer information finns i [Azure provider: autentisera med hjälp av Azure CLI](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Skapa körnings planen med kommandot [terraform plan](https://www.terraform.io/docs/commands/plan.html) och **out** -parameter.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Skärm bild av kommandot terraform plan och out parameter för att visa den Azure-resurs som ska skapas.":::

   > [!NOTE]
   > Information om att bevara körnings planer och säkerhet finns i [terraform plan: säkerhets varning](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Använd Terraform-körningsplanen

Sist använder du körnings planen.

Kör kommandot [terraform Apply](https://www.terraform.io/docs/commands/apply.html) och ange det `assignment.tfplan` redan skapade.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Skärm bild där du kör kommandot terraform Apply och den resulterande resurs skapandet.":::

Med alternativet "tillämpa klart! Resurser: 1 tillagt, 0 har ändrats, 0 har förstörts. " meddelandet skapas nu princip tilldelningen. Eftersom vi har definierat `outputs.tf` filen returneras även _tilldelnings \_ -ID_ .

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Om du vill visa de resurser som inte är kompatibla med den här nya tilldelningen använder du det _tilldelnings \_ -ID_ som returnerades av `terraform apply` . Kör följande kommando för att hämta resurs-ID: n för de icke-kompatibla resurser som ska matas in i en JSON-fil:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Resultatet är jämförbart med det du vanligtvis skulle se under **Icke-kompatibla resurser** i Azure-portalens vy.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort tilldelningen som skapats använder du Azure CLI eller omvänd terraform-körnings plan med `terraform destroy` .

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)
