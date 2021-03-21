---
title: Det gick inte att återställa till en lyckad distribution
description: Ange att en misslyckad distribution ska återställas till en lyckad distribution.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492220"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Det gick inte att återställa vid lyckad distribution

När en distribution Miss lyckas kan du automatiskt distribuera om en tidigare distribuerad distribution från din distributions historik. Den här funktionen är användbar om du har ett känt bra tillstånd för distribution av infrastrukturen och vill återställa till det här läget. Du kan ange antingen en viss tidigare distribution eller den senaste lyckade distributionen.

> [!IMPORTANT]
> Den här funktionen återställer en misslyckad distribution genom att distribuera om en tidigare distribution. Det här resultatet kan skilja sig från vad du skulle förväntat dig från att ångra den misslyckade distributionen. Se till att du förstår hur den tidigare distributionen distribueras om.

## <a name="considerations-for-redeploying"></a>Överväganden vid omdistribution

Innan du använder den här funktionen bör du tänka på följande information om hur omdistributionen hanteras:

- Den tidigare distributionen körs med [slutfört läge](./deployment-modes.md#complete-mode), även om du har använt [stegvist läge](./deployment-modes.md#incremental-mode) under den tidigare distributionen. Omdistribution i slutfört läge kan ge oväntade resultat när den tidigare distributionen användes stegvis. Det fullständiga läget innebär att alla resurser som inte ingår i den tidigare distributionen tas bort. Ange en tidigare distribution som representerar alla resurser och deras tillstånd som du vill ska finnas i resurs gruppen. Mer information finns i [distributions lägen](./deployment-modes.md).
- Omdistributionen körs exakt eftersom den kördes tidigare med samma parametrar. Du kan inte ändra parametrarna.
- Omdistributionen påverkar endast resurserna, alla data ändringar påverkas inte.
- Du kan bara använda den här funktionen med resurs grupps distributioner. Den stöder inte distributioner av prenumerationer, hanterings grupper eller klient nivåer. Mer information om distribution av prenumerations nivåer finns i [skapa resurs grupper och resurser på prenumerations nivå](./deploy-to-subscription.md).
- Du kan bara använda det här alternativet med distributioner på rotnivå. Distributioner från en kapslad mall är inte tillgängliga för omdistribution.

Om du vill använda det här alternativet måste distributionerna ha unika namn i distributions historiken. Den är bara med unika namn som en speciell distribution kan identifieras. Om du inte har unika namn kan en misslyckad distribution skriva över en lyckad distribution i historiken.

Om du anger en tidigare distribution som inte finns i distributions historiken returnerar återställningen ett fel.

## <a name="powershell"></a>PowerShell

Om du vill distribuera om den senaste lyckade distributionen lägger du till `-RollbackToLastDeployment` parametern som en flagga.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Om du vill distribuera om en speciell distribution använder du `-RollBackDeploymentName` parametern och anger namnet på distributionen. Den angivna distributionen måste ha slutförts.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Om du vill distribuera om den senaste lyckade distributionen lägger du till `--rollback-on-error` parametern som en flagga.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Om du vill distribuera om en speciell distribution använder du `--rollback-on-error` parametern och anger namnet på distributionen. Den angivna distributionen måste ha slutförts.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST-API

Om du vill distribuera om den senaste lyckade distributionen om den aktuella distributionen Miss lyckas, använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Om du vill distribuera om en angiven distribution, om den aktuella distributionen Miss lyckas, använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Den angivna distributionen måste ha slutförts.

## <a name="next-steps"></a>Nästa steg

- Information om fullständiga och stegvisa lägen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
