---
title: Konvertera Portal mal len till mall specifikation
description: Beskriver hur du konverterar en befintlig mall i Azure Portal galleriet till en mall-specifikationer.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555941"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Konvertera mallens galleri i Portal till mall-specifikationer

Azure Portal är ett sätt att lagra Azure Resource Manager mallar (ARM-mallar) i ditt konto. Mall- [specifikationer](template-specs.md) ger dock ett enklare sätt att dela dina mallar med användare i din organisation och länka till andra mallar. Den här artikeln visar hur du konverterar befintliga mallar i mallgalleriet till mall-specifikationer.

Om du vill se om du har några mallar att konvertera kan du Visa mallgalleriet [i portalen](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Dessa mallar har resurs typen `Microsoft.Gallery/myareas/galleryitems` .

## <a name="convert-with-powershell-script"></a>Konvertera med PowerShell-skript

För att förenkla konvertering av mallar i mallgalleriet använder du ett PowerShell-skript från lagrings platsen för Azure snabb starts mal len. När du kör skriptet kan du antingen skapa en ny mall-specifikation för varje mall eller ladda ned en mall som skapar mallen specifikation. Skriptet tar inte bort mallen från mallgalleriet.

1. Kopiera [skriptet för migrering](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Spara en lokal kopia med namnet *Migrate-GalleryItems.ps1*.
1. Ange värden för parametrarna och för att skapa nya specifikationer för mallar `-ResourceGroupName` `-Location` . 

   Ställ in `ItemsToExport` på `MyGalleryItems` för att exportera mallarna. Ange att den ska `AllGalleryItems` Exportera alla mallar som du har åtkomst till.

   I följande exempel skapas nya mall-specifikationer för varje mall i en resurs grupp med namnet **migratedRG**. Skriptet skapar resurs gruppen om den inte finns.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Om du vill hämta mallar som du kan använda för att skapa mall-specifikationerna anger du inte värden för resurs gruppen eller platsen. Ange i stället `-ExportToFile` . Mallen är inte densamma som din mall i galleriet. I stället innehåller den en mall för mallkod som skapar mallens specifikation för din mall.

   I följande exempel hämtas mallarna utan att mallens specifikationer skapas.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Information om hur du distribuerar mallen som skapar mall-specifikationen finns i [snabb start: skapa och distribuera mall specifikation (för hands version)](quickstart-create-template-specs.md).

Mer information om skriptet och dess parametrar finns i [skapa TemplateSpecs från mallar i Template Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create).

## <a name="manually-convert-through-portal"></a>Konvertera manuellt via portalen

Du kan kopiera mallar från galleriet manuellt till en ny mall-specifikationer.

1. Öppna [mallarna (för hands versionen)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) i portalen.
1. Välj den mall som du vill migrera.
1. Välj **Visa mall**.
1. Kopiera mal linne hållet.
1. I Portal Sök fältet söker du efter **specifikationer för mallar**. Välj det alternativet.
1. Välj **Skapa mall-specifikation**.
1. Ange värden för namn, prenumeration, resurs grupp, plats och version.
1. Välj **Nästa: Redigera mall**.
1. För innehållet i mallen klistrar du in mallen som du kopierade från mallgalleriet.
1. Välj **Granska + skapa**.
1. När verifieringen har slutförts väljer du **skapa**.

Om du behöver dela mallen med andra användare i din organisation, [ställer du in rollbaserad åtkomst kontroll](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) för gruppen eller användarna som behöver åtkomst.

## <a name="next-steps"></a>Nästa steg

Mer information om specifikationer för mallar finns i [skapa och distribuera specifikationer för mallar](template-specs.md).
