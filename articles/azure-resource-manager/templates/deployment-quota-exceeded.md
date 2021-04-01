---
title: Distributions kvoten överskreds
description: Beskriver hur du löser problemet med fler än 800 distributioner i resurs grupps historiken.
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 8996d7817eea2f8daf44fbc9b4416c884b05940f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87987060"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Lös fel när antalet distributioner överskrider 800

Varje resurs grupp är begränsad till 800 distributioner i distributions historiken. I den här artikeln beskrivs det fel som du får när en distribution Miss lyckas eftersom den skulle överskrida de tillåtna 800-distributionerna. Lös problemet genom att ta bort distributioner från resurs grupps historiken. Att ta bort en distribution från historiken påverkar inte några av de distribuerade resurserna.

Azure Resource Manager tar automatiskt bort distributioner från historiken när du närmar dig gränsen. Du kan fortfarande se det här felet av någon av följande orsaker:

1. Du har ett CanNotDelete-lås på resurs gruppen som förhindrar borttagningar från distributions historiken.
1. Du har valt att inte ta bort automatiskt.
1. Du har ett stort antal distributioner som körs samtidigt och de automatiska borttagningarna bearbetas inte tillräckligt snabbt för att minska det totala antalet.

Information om hur du tar bort låset eller väljer i till automatiska borttagningar finns i [automatiska borttagningar från distributions historiken](deployment-history-deletions.md).

Den här artikeln beskriver hur du manuellt tar bort distributioner från historiken.

## <a name="symptom"></a>Symptom

Under distributionen får du ett fel meddelande om att den aktuella distributionen kommer att överskrida kvoten på 800-distributioner.

## <a name="solution"></a>Lösning

### <a name="azure-cli"></a>Azure CLI

Använd kommandot [AZ Deployment Group Delete](/cli/azure/group/deployment) för att ta bort distributioner från historiken.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Om du vill ta bort alla distributioner som är äldre än fem dagar använder du:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Du kan hämta det aktuella antalet i distributions historiken med följande kommando:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Använd kommandot [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) för att ta bort distributioner från historiken.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Om du vill ta bort alla distributioner som är äldre än fem dagar använder du:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Du kan hämta det aktuella antalet i distributions historiken med följande kommando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Lösningar från tredje part

Följande externa lösningar riktar sig till vissa scenarier:

* [Azure Logic Apps-och PowerShell-lösningar](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps-tillägg](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
