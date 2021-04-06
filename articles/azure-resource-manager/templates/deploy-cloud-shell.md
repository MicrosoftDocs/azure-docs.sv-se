---
title: Distribuera mallar med Cloud Shell
description: Använd Azure Resource Manager och Azure Cloud Shell för att distribuera resurser till Azure. Resurserna definieras i en Azure Resource Manager-mall (ARM-mall).
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028615"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Distribuera ARM-mallar från Azure Cloud Shell

Du kan använda [Azure Cloud Shell](../../cloud-shell/overview.md) för att distribuera en Azure Resource Manager-mall (arm-mall). Du kan distribuera antingen en ARM-mall som lagras på distans eller en ARM-mall som lagras på det lokala lagrings kontot för Cloud Shell.

Du kan distribuera till valfri omfattning. Den här artikeln visar hur du distribuerar till en resurs grupp.

## <a name="deploy-remote-template"></a>Distribuera fjärran sluten mall

Om du vill distribuera en extern mall anger du URI för mallen exakt som du skulle göra för alla externa distributioner. Den externa mallen kan finnas i en GitHub-lagringsplats eller ett externt lagrings konto.

1. Öppna Cloud Shell prompten.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Öppna Cloud Shell":::

1. Använd följande kommandon för att distribuera mallen:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Distribuera en lokal mall

Om du vill distribuera en lokal mall måste du först överföra mallen till det lagrings konto som är anslutet till Cloud Shell-sessionen.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj din Cloud Shell-resursgrupp. Namnet har formatet `cloud-shell-storage-<region>`.

   ![Välj resursgrupp](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Välj lagringskontot för Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Välj lagrings konto":::

1. Välj **fil resurser**.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Välj fil resurser":::

1. Välj standard fil resurs för Cloud Shell. Fil resursen har namn formatet `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Standard fil resurs":::

1. Lägg till en ny katalog som ska innehålla mallarna. Välj den katalogen.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Lägg till katalog":::

1. Välj **Överför**.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Ladda upp mall":::

1. Leta upp och överför mallen.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Välj mall":::

1. Öppna Cloud Shell prompten.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Öppna Cloud Shell":::

1. Navigera till **clouddrive** -katalogen. Navigera till den katalog som du har lagt till för att lagra mallarna.

1. Använd följande kommandon för att distribuera mallen:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Nästa steg

- Mer information om distributions kommandon finns i [distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md) och [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
- Information om hur du förändrar ändringar innan du distribuerar en mall finns i åtgärder för att [distribuera konsekvens i arm-mallar](template-deploy-what-if.md).
