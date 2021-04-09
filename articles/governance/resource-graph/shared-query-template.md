---
title: 'Snabb start: skapa en delad fråga med mallar'
description: I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en delad resurs diagram fråga som räknar virtuella datorer av operativ systemet.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 8d631ffcb14af93f10e578097470efc6156287d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594324"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Snabb start: skapa en delad fråga med en ARM-mall

Resurs diagram frågor kan sparas som en _privat fråga_ eller en _delad fråga_. En privat fråga sparas i användarens Portal profil och är inte synlig för andra. En delad fråga är ett Resource Manager-objekt som kan delas med andra via behörigheter och rollbaserad åtkomst. En delad fråga ger gemensam och konsekvent körning av resurs identifiering. I den här snabb starten används en Azure Resource Manager-mall (ARM-mall) för att skapa en delad fråga.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera ARM-mallen för att skapa en delad fråga till Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

I den här snabb starten skapar du en delad fråga som heter _antal virtuella datorer av OS_. Om du vill testa den här frågan i SDK eller i portalen med resurs diagram Utforskaren, se [exempel-Count Virtual Machines by OS Type](./samples/starter.md#count-os).

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

Den resurs som definierats i mallen är:

- [Microsoft. ResourceGraph/frågor](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>Distribuera mallen

> [!NOTE]
> Azure Resource Graph-tjänsten är kostnads fri. Mer information finns i [Översikt över Azure Resource Graph](./overview.md).

1. Logga in på Azure-portalen och öppna mallen genom att välja följande bild:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera ARM-mallen för att skapa en delad fråga till Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Välj eller ange följande värden:

   | Name | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Välj **Skapa ny**, ange ett namn och välj sedan **OK**. |
   | Location | Välj en region. Välj till exempel **USA, centrala**. |
   | Frågenamn | Lämna standardvärdet: **antal virtuella datorer per operativ system**. |
   | Fråga kod | Låt standardvärdet vara kvar: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Beskrivning av fråga | Lämna standardvärdet: **den här delade frågan räknar alla virtuella dator resurser och sammanfattar efter OS-typ.** |
   | Jag samtycker till de villkor som anges ovan | Select |

1. Välj **Köp**.

Vissa ytterligare resurser:

- Du hittar fler exempel på mallar i [Azure snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mal linne Reference går du till [referens för Azure-mallar](/azure/templates/microsoft.resourcegraph/allversions).
- Information om hur du utvecklar ARM-mallar finns i [Azure Resource Manager-dokumentation](../../azure-resource-manager/management/overview.md).
- Information om distribution på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Följ dessa steg om du vill köra den nya delade frågan:

1. I Portal Sök fältet söker du efter **resurs diagram frågor** och markerar den.

1. Välj den delade frågan **antal virtuella datorer per operativ system** och välj sedan fliken **resultat** på sidan **Översikt** .

Den delade frågan kan också öppnas från resursens diagram Utforskaren:

1. I Portal Sök fältet söker du efter **resurs diagram Utforskaren** och markerar den.

1. Välj knappen **öppna en fråga** .

1. Ändra **typ** till _delade frågor_. Om du inte ser **antalet virtuella datorer per operativ system** i listan använder du rutan filter för att begränsa resultaten. När **antalet virtuella datorer efter delad operativ Systems** fråga visas väljer du dess namn.

1. När frågan har lästs in väljer du knappen **Kör fråga** . Resultaten visas på fliken **resultat** .

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg om du vill ta bort den delade frågan som skapats:

1. I Portal Sök fältet söker du efter **resurs diagram frågor** och markerar den.

1. Markera kryss rutan bredvid den delade frågan **antal virtuella datorer per operativ system**.

1. Välj knappen **ta bort** längst upp på sidan.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en delad fråga för resurs diagram.

Om du vill veta mer om delade frågor fortsätter du till självstudien för:

> [!div class="nextstepaction"]
> [Hantera frågor i Azure Portal](./tutorials/create-share-query.md)