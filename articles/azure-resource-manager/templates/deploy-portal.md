---
title: Distribuera resurser med Azure Portal
description: Använd Azure Portal och Azure resurs hantering för att distribuera dina resurser till en resurs grupp i din prenumeration.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d8467bb4e51fc4e6ba89a84f1260a8d2743758d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028683"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Distribuera resurser med ARM-mallar och Azure Portal

Lär dig hur du använder [Azure Portal](https://portal.azure.com) med [Azure Resource Manager mallar (arm-mallar)](overview.md) för att distribuera dina Azure-resurser. Information om hur du hanterar dina resurser finns i [Hantera Azure-resurser med hjälp av Azure Portal](../management/manage-resources-portal.md).

Att distribuera Azure-resurser med hjälp av Azure Portal omfattar vanligt vis två steg:

- Skapa en resursgrupp.
- Distribuera resurser till resurs gruppen.

Du kan också skapa en anpassad ARM-mall för att distribuera Azure-resurser.

I den här artikeln visas båda metoderna.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Om du vill skapa en ny resurs grupp väljer du **resurs grupper** från [Azure Portal](https://portal.azure.com).

   ![Välj resurs grupper](./media/deploy-portal/select-resource-groups.png)

1. Under resurs grupper väljer du **Lägg till**.

   ![Lägg till resurs grupp](./media/deploy-portal/add-resource-group.png)

1. Välj eller ange följande egenskaps värden:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resurs grupp**: ge resurs gruppen ett namn.
    - **Region**: Ange en Azure-plats. Den här platsen är den plats där resurs gruppen lagrar metadata om resurserna. Av kompatibilitetsskäl kan du vilja ange var metadata lagras. I allmänhet rekommenderar vi att du anger en plats där de flesta av dina resurser kommer att vara. Att använda samma plats kan förenkla din mall.

   ![Ange grupp värden](./media/deploy-portal/set-group-properties.png)

1. Välj **Granska + skapa**.
1. Granska värdena och välj sedan **skapa**.
1. Välj **Uppdatera** innan du kan se den nya resurs gruppen i listan.

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resurs grupp

När du har skapat en resurs grupp kan du distribuera resurser till gruppen från Marketplace. Marketplace innehåller fördefinierade lösningar för vanliga scenarier.

1. Starta en distribution genom att välja **skapa en resurs** från [Azure Portal](https://portal.azure.com).

   ![Ny resurs](./media/deploy-portal/new-resources.png)

1. Hitta den typ av resurs som du vill distribuera. Resurserna är ordnade i kategorier. Om du inte ser den specifika lösning som du vill distribuera kan du söka efter den i Marketplace. Följande skärm bild visar att Ubuntu-servern är markerad.

   ![Välj resurs typ](./media/deploy-portal/select-resource-type.png)

1. Beroende på typen av vald resurs har du en samling relevanta egenskaper som du kan ställa in före distributionen. För alla typer måste du välja en mål resurs grupp. Följande bild visar hur du skapar en virtuell Linux-dator och distribuerar den till den resurs grupp som du skapade.

   ![Skapa resursgrupp](./media/deploy-portal/select-existing-group.png)

   Du kan välja att skapa en resurs grupp när du distribuerar dina resurser. Välj **Skapa ny** och ge resurs gruppen ett namn.

1. Distributionen börjar. Distributionen kan ta flera minuter. Vissa resurser tar längre tid än andra resurser. När distributionen är färdig visas ett meddelande. Välj **gå till resurs** för att öppna

   ![Visa meddelande](./media/deploy-portal/view-notification.png)

1. När du har distribuerat resurserna kan du lägga till fler resurser i resurs gruppen genom att välja **Lägg till**.

   ![Lägg till resurs](./media/deploy-portal/add-resource.png)

Även om du inte ser det, använde portalen en ARM-mall för att distribuera de resurser som du har valt. Du kan hitta mallen från distributions historiken. Mer information finns i [Exportera mall efter distribution](export-template-portal.md#export-template-after-deployment).

## <a name="deploy-resources-from-custom-template"></a>Distribuera resurser från anpassad mall

Om du vill köra en distribution men inte använda någon av mallarna i Marketplace kan du skapa en anpassad mall som definierar infrastrukturen för lösningen. Mer information om hur du skapar mallar finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).

> [!NOTE]
> Portal gränssnittet har inte stöd för att referera till en [hemlighet från en Key Vault](key-vault-parameter.md). Använd i stället [PowerShell](deploy-powershell.md) eller [Azure CLI](deploy-cli.md) för att distribuera mallen lokalt eller från en extern URI.

1. Om du vill distribuera en anpassad mall via portalen väljer du **skapa en resurs**, Sök efter **mall**. och välj sedan **malldistribution**.

   ![Distribution av Sök mall](./media/deploy-portal/search-template.png)

1. Välj **Skapa**.
1. Du ser flera alternativ för att skapa en mall:

    - Skapa **en egen mall i redigeraren**: skapa en egen mall i redigeraren för Portal mal len.
    - **Vanliga mallar**: Välj bland vanliga lösningar.
    - **Läs in en GitHub snabb starts mall**: Välj från [snabb starts mallar](https://azure.microsoft.com/resources/templates/).

   ![Visnings alternativ](./media/deploy-portal/see-options.png)

    Den här självstudien innehåller instruktioner för att läsa in en snabb starts mall.

1. Under **Läs in en GitHub snabb starts mall** skriver eller väljer du **101-Storage-Account-Create**.

    Du kan välja mellan två alternativ:

    - **Välj mall**: Distribuera mallen.
    - **Redigera mall**: redigera snabb starts mal len innan du distribuerar den.

1. Välj **Redigera mall** för att utforska redigeraren för Portal mal len. Mallen läses in i redigeraren. Observera att det finns två parametrar: `storageAccountType` och `location` .

   ![Skapa mallen](./media/deploy-portal/show-json.png)

1. Gör en mindre ändring i mallen. Uppdatera till exempel `storageAccountName` variabeln till:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Välj **Spara**. Nu ser du distributions gränssnittet för Portal mal len. Lägg märke till de två parametrarna som du har definierat i mallen.
1. Ange eller Välj egenskaps värden:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resurs grupp**: Välj **Skapa ny** och ge ett namn.
    - **Plats**: Välj en Azure-plats.
    - **Typ av lagrings konto**: Använd standardvärdet.
    - **Plats**: Använd standardvärdet.
    - **Jag godkänner villkoren ovan**: (välj)

1. Välj **Köp**.

## <a name="next-steps"></a>Nästa steg

- Information om hur du visar gransknings loggar finns i [gransknings åtgärder med Resource Manager](../management/view-activity-logs.md).
- Information om hur du felsöker distributions fel finns i [Visa distributions åtgärder](deployment-history.md).
- Information om hur du exporterar en mall från en distribution eller resurs grupp finns i [Exportera arm-mallar](export-template-portal.md).
- För att på ett säkert sätt distribuera tjänsten över flera regioner, se [Azure Deployment Manager](deployment-manager-overview.md).
