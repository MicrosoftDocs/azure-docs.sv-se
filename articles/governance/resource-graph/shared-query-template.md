---
title: 'Snabbstart: Skapa en delad fråga med mallar'
description: I den här snabbstarten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en Resource Graph delad fråga som räknar virtuella datorer efter operativsystem.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: ac736d4371531bb38a8cd2cf095acbdfbc7c08a1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535794"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Snabbstart: Skapa en delad fråga med hjälp av en ARM-mall

Resource Graph kan sparas som en privat _fråga eller_ en delad _fråga._ En privat fråga sparas i enskilda personers portalprofil och är inte synlig för andra. En delad fråga är Resource Manager objekt som kan delas med andra via behörigheter och rollbaserad åtkomst. En delad fråga ger en gemensam och konsekvent körning av resursidentifiering. Den här snabbstarten använder en Azure Resource Manager mall (ARM-mall) för att skapa en delad fråga.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera ARM-mallen för att skapa en delad fråga till Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

I den här snabbstarten skapar du en delad fråga med namnet _Count VMs by OS (Räkna virtuella datorer efter operativsystem)._ Om du vill testa den här frågan i SDK eller i portalen Resource Graph Explorer, se [Exempel – Räkna virtuella datorer efter OS-typ.](./samples/starter.md#count-os)

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

Den resurs som definieras i mallen är:

- [Microsoft.ResourceGraph/frågor](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>Distribuera mallen

> [!NOTE]
> Azure Resource Graph-tjänsten är kostnadsfri. Mer information finns i [Översikt över Azure Resource Graph](./overview.md).

1. Logga in på Azure-portalen och öppna mallen genom att välja följande bild:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera ARM-mallen för att skapa en delad fråga till Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Välj eller ange följande värden:

   | Name | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Välj **Skapa ny,** ange ett namn och välj sedan **OK.** |
   | Location | Välj en region. Välj till exempel **USA, centrala**. |
   | Frågenamn | Låt standardvärdet vara: **Antal virtuella datorer efter operativsystem.** |
   | Frågekod | Låt standardvärdet vara kvar: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Frågebeskrivning | Lämna standardvärdet: Den **här delade frågan räknar alla resurser för virtuella datorer och sammanfattar efter os-typ.** |
   | Jag godkänner de villkor som anges ovan | (Välj) |

1. Välj **Köp**.

Några ytterligare resurser:

- Mer information om exempelmallar finns i [Azure-snabbstartsmallen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mallreferensen går du till [Azure-mallreferensen](/azure/templates/microsoft.resourcegraph/allversions).
- Information om hur du utvecklar ARM-mallar finns i [Azure Resource Manager dokumentation](../../azure-resource-manager/management/overview.md).
- Information om distribution på prenumerationsnivå finns [i Skapa resursgrupper och resurser på prenumerationsnivå.](../../azure-resource-manager/templates/deploy-to-subscription.md)

## <a name="validate-the-deployment"></a>Verifiera distributionen

Följ dessa steg om du vill köra den nya delade frågan:

1. I portalens sökfält söker du **efter Resource Graph och** väljer den.

1. Välj den delade frågan **med namnet Count VMs by OS**(Räkna virtuella datorer efter operativsystem) och välj **sedan** fliken Resultat på **sidan** Översikt.

Den delade frågan kan också öppnas från Resource Graph Explorer:

1. I portalens sökfält söker du efter **Resource Graph Explorer** och väljer den.

1. Välj knappen **Öppna en** fråga.

1. Ändra **Typ** _till Delade frågor._ Om du inte ser antal virtuella **datorer efter operativsystem** i listan använder du filterrutan för att begränsa resultatet. När frågan **Count VMs by OS** shared (Räkna virtuella datorer efter operativsystem) visas väljer du dess namn.

1. När frågan har lästs in väljer du **knappen Kör** fråga. Resultaten visas på **fliken** Resultat.

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg om du vill ta bort den delade fråga som skapats:

1. I portalens sökfält söker du **efter Resource Graph och** väljer den.

1. Ange kryssrutan bredvid den delade frågan med namnet **Count VMs by OS (Räkna virtuella datorer efter operativsystem).**

1. Välj **knappen** Ta bort längst upp på sidan.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Resource Graph delad fråga.

Om du vill veta mer om delade frågor fortsätter du till självstudien för:

> [!div class="nextstepaction"]
> [Hantera frågor i Azure Portal](./tutorials/create-share-query.md)
