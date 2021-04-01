---
title: Flytta Azure Event Grid anpassade ämnen till en annan region
description: Den här artikeln visar hur du flyttar Azure Event Grid anpassade ämnen från en region till en annan region.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89145355"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Flytta Azure Event Grid anpassade ämnen till en annan region
Du kanske vill flytta dina resurser till en annan region av olika anledningar. Till exempel för att dra nytta av en ny Azure-region för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven. 

Här följer de övergripande steg som beskrivs i den här artikeln: 

- **Exportera den anpassade ämnes** resursen till en Azure Resource Manager-mall. 

    > [!IMPORTANT]
    > Endast det anpassade ämnet exporteras till mallen. Alla prenumerationer för ämnet exporteras inte.
- **Använd mallen för att distribuera det anpassade ämnet** till mål regionen. 
- **Skapa prenumerationer manuellt** i mål regionen. När du exporterade det anpassade avsnittet till en mall i den aktuella regionen exporteras bara avsnittet. Prenumerationer ingår inte i mallen, så skapa dem manuellt när det anpassade avsnittet har skapats i mål regionen. 
- **Verifiera distributionen**. Kontrol lera att det anpassade avsnittet har skapats i mål regionen. 
- Ta bort det anpassade avsnittet från käll regionen för att **slutföra flyttningen**. 

## <a name="prerequisites"></a>Förutsättningar
- Slutför [snabb starten: dirigera anpassade händelser till webb slut punkten](custom-event-quickstart-portal.md) i käll regionen. Gör det här steget så att du kan testa stegen i den här artikeln. 
- Se till att tjänsten Event Grid är tillgänglig i mål regionen. Se [tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall för det anpassade ämnet. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I Sök fältet skriver du **Event Grid ämnen** och väljer **Event Grid ämnen** i resultat listan. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Sök efter och välj Event Grid ämnen":::
3. Välj det **avsnitt** som du vill exportera till en Resource Manager-mall. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Välj det anpassade ämnet":::   
4. På sidan **Event Grid ämne** väljer du **Exportera mall** under **Inställningar** på den vänstra menyn och väljer sedan **Hämta** i verktygsfältet. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Exportera mall – > hämtning":::   

    > [!IMPORTANT]
    > Endast avsnittet exporteras till mallen. Prenumerationer för ämnet exporteras inte. Du måste därför skapa prenumerationer för ämnet när du har flyttat avsnittet till mål regionen. 
5. Leta upp **zip** -filen som du laddade ned från portalen och zippa upp filen till en valfri mapp. Den här zip-filen innehåller mallar och parametrar JSON-filer. 
1. Öppna **template.js** i valfritt redigerings program. 
8. Uppdatering `location` för **ämnes** resursen till mål regionen eller platsen. För att hämta plats koder, se [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet utan mellanslag, till exempel `West US` är lika med `westus` .

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. **Spara** mallen. 

## <a name="recreate"></a>Återskapa 
Distribuera mallen för att skapa ett anpassat ämne i mål regionen. 

1. I Azure Portal väljer du **skapa en resurs**.
2. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.
3. Välj **malldistribution**.
4. Välj **Skapa**.
5. Välj **Bygg en egen mall i redigeraren**.
6. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in **template.jspå** filen som du laddade ned i det sista avsnittet.
7. Spara mallen genom att välja **Spara** . 
8. Följ dessa steg på sidan **Anpassad distribution** : 
    1. Välj en Azure- **prenumeration**. 
    1. Välj en befintlig **resurs grupp** i mål regionen eller skapa en. 
    1. För **region** väljer du mål regionen. Om du har valt en befintlig resurs grupp är den här inställningen skrivskyddad. 
    1. För **ämnes namnet** anger du ett nytt namn för ämnet. 
    1. Välj **Granska + skapa** längst ned på sidan. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Anpassad distribution":::
    1. På sidan **Granska + skapa** granskar du inställningarna och väljer **skapa**. 

## <a name="verify"></a>Verifiera

1. När distributionen har slutförts väljer **du gå till resurs**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Gå till resurs":::
1. Bekräfta att du ser sidan **Event Grid ämne** för det anpassade ämnet.   
1. Följ stegen i skicka [anpassade händelser till en webb slut punkt](custom-event-quickstart-portal.md#send-an-event-to-your-topic) för att skicka händelser till ämnet. Verifiera att händelse hanteraren för webhook anropas. 

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Ta bort det anpassade avsnittet i käll regionen för att slutföra flyttningen.  

Om du vill börja om tar du bort avsnittet i mål regionen och upprepar stegen i avsnitten [förbereda](#prepare) och [Återskapa](#recreate) i den här artikeln.

Ta bort ett anpassat ämne med hjälp av Azure Portal:

1. Skriv **Event Grid ämnen** i fönstret Sök högst upp i Azure Portal och välj **Event Grid ämnen** från Sök resultat. 
2. Välj det ämne som du vill ta bort och välj **ta bort** från verktygsfältet. 
3. På sidan bekräftelse anger du namnet på resurs gruppen och väljer **ta bort**.  

Ta bort resurs gruppen som innehåller det anpassade ämnet med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal, Skriv **resurs grupper** och välj **resurs grupper** från Sök resultat. 
2. Välj den resurs grupp som ska tas bort och välj **ta bort** från verktygsfältet. 
3. På sidan bekräftelse anger du namnet på resurs gruppen och väljer **ta bort**.  

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du flyttar ett Event Grid anpassat ämne från en region till en annan region. I följande artiklar finns information om hur du flyttar system ämnen, domäner och partners namn områden i olika regioner.

- [Flytta system ämnen i flera regioner](move-system-topics-across-regions.md). 
- [Flytta domäner mellan regioner](move-domains-across-regions.md). 
- [Flytta namn rymder för partner över regioner](move-partner-namespaces-across-regions.md).

Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i följande artikel: [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).