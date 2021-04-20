---
title: Flytta regioner för resurser i Microsoft.Resources
description: Visa hur du flyttar resurser som finns i namnområdet Microsoft.Resources till nya regioner.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725880"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Flytta Microsoft.Resources-resurser till en ny region

Du kan behöva flytta en befintlig resurs till en ny region. Den här artikeln visar hur du flyttar två resurstyper – templateSpecs och deploymentScripts – som finns i namnområdet Microsoft.Resources.

## <a name="move-template-specs-to-new-region"></a>Flytta mallspecifikter till en ny region

Om du har en [mallspecifikitet](../templates/template-specs.md) i en region och vill flytta den till en ny region kan du exportera mallspecifikationen och distribuera den igen.

1. Använd kommandot för att exportera en befintlig mallspecifikt. För parametervärdena anger du de värden som matchar mallspecifikationen som du vill exportera.

   För Azure PowerShell använder du:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Om du använder Azure CLI använder du:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Använd den exporterade mallspecifikationen för att skapa en ny mallspecifikör. Följande exempel visas för `westus` den nya regionen, men du kan ange den region du vill ha.

   För Azure PowerShell använder du:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Om du använder Azure CLI använder du:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Flytta distributionsskript till en ny region

1. Välj den resursgrupp som innehåller distributionsskriptet som du vill flytta till en ny region.

1. [Exportera mallen](../templates/export-template-portal.md). När du exporterar väljer du distributionsskriptet och eventuella andra nödvändiga resurser.

1. Ta bort följande egenskaper i den exporterade mallen:

   * tenantId
   * principalId
   * ClientID

1. Den exporterade mallen har ett hårdkodat värde för regionen för distributionsskriptet.

   ```json
   "location": "westus2",
   ```

   Ändra mallen så att en parameter tillåts för att ange platsen. Mer information finns i Ange [resursplats i ARM-mall](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Distribuera den exporterade mallen och](../templates/deploy-powershell.md) ange en ny region för distributionsskriptet.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du flyttar resurser till en ny resursgrupp eller prenumeration finns i [Flytta resurser till en ny resursgrupp eller prenumeration.](move-resource-group-and-subscription.md)
* Mer information om hur du flyttar resurser till en ny region finns [i Flytta resurser mellan regioner.](move-resources-overview.md#move-resources-across-regions)
