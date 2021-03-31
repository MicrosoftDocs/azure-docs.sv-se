---
title: Flytta regioner för resurser i Microsoft. Resources
description: Visa hur du flyttar resurser i namn området Microsoft. Resources till nya regioner.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88951059"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Flytta Microsoft. Resources-resurser till ny region

Du kan behöva flytta en befintlig resurs till en ny region. Den här artikeln visar hur du flyttar två resurs typer – templateSpecs och deploymentScripts – som finns i namn området Microsoft. Resources.

## <a name="move-template-specs-to-new-region"></a>Flytta specifikationer för mall till ny region

Om du har en [mall-specifikation](../templates/template-specs.md) i en region och vill flytta den till en ny region kan du exportera mallen och distribuera den igen.

1. Använd kommandot för att exportera en befintlig mall specifikation. För parameter värden anger du de värden som matchar den mall specifikation som du vill exportera.

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

1. Använd specifikationen exporterad mall för att skapa en ny mall-specifikation. I följande exempel visas `westus` för den nya regionen, men du kan ange den region som du vill använda.

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

## <a name="move-deployment-scripts-to-new-region"></a>Flytta distributions skript till en ny region

1. Välj den resurs grupp som innehåller det distributions skript som du vill flytta till en ny region.

1. [Exportera mallen](../templates/export-template-portal.md). När du exporterar väljer du distributions skriptet och eventuella andra nödvändiga resurser.

1. Ta bort följande egenskaper i den exporterade mallen:

   * tenantId
   * principalId
   * ClientID

1. Den exporterade mallen har ett hårdkodad-värde för området för distributions skriptet.

   ```json
   "location": "westus2",
   ```

   Ändra mallen så att den tillåter en parameter för att ange platsen. Mer information finns i [Ange resurs plats i arm-mallen](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Distribuera den exporterade mallen](../templates/deploy-powershell.md) och ange en ny region för distributions skriptet.

## <a name="next-steps"></a>Nästa steg

* Information om hur du flyttar resurser till en ny resurs grupp eller prenumeration finns i [Flytta resurser till en ny resurs grupp eller prenumeration](move-resource-group-and-subscription.md).
* Information om hur du flyttar resurser till en ny region finns i [Flytta Azure-resurser mellan regioner](move-region.md).
