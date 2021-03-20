---
title: Flytta Azure Event Grid system ämnen till en annan region
description: Den här artikeln visar hur du flyttar Azure Event Grid Systems ämnen från en region till en annan region.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89087201"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Flytta Azure Event Grid system ämnen till en annan region
Du kanske vill flytta dina resurser till en annan region av olika anledningar. Till exempel för att dra nytta av en ny Azure-region för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven. 

Här följer de övergripande steg som beskrivs i den här artikeln: 

- **Exportera resurs gruppen** som innehåller det Azure Storage kontot och det associerade system avsnittet till en Resource Manager-mall. Du kan också exportera en mall endast för system avsnittet. Om du använder den här vägen måste du komma ihåg att flytta Azure-händelseloggen (i det här exemplet ett Azure Storage konto) till den andra regionen innan du flyttar system-avsnittet. I avsnittet exporterad mall för system uppdaterar du sedan det externa ID: t för lagrings kontot i mål regionen. 
- **Ändra mallen** för att lägga till `endpointUrl` egenskapen för att peka på en webhook som prenumererar på system-avsnittet. När system avsnittet exporteras, exporteras även dess prenumeration (i det här fallet en webhook) till mallen, men `endpointUrl` egenskapen ingår inte. Så du måste uppdatera den så att den pekar på den slut punkt som prenumererar på ämnet. Uppdatera också `location` egenskapens värde till den nya platsen eller regionen. För andra typer av händelse hanterare behöver du bara uppdatera platsen. 
- **Använd mallen för att distribuera resurser** till mål regionen. Du anger namn för lagrings kontot och system avsnittet som ska skapas i mål regionen. 
- **Verifiera distributionen**. Kontrol lera att webhooken anropas när du laddar upp en fil till blob-lagringen i mål regionen. 
- **Slutför flyttningen** genom att ta bort resurser (avsnittet händelse källa och system) från käll regionen. 

## <a name="prerequisites"></a>Förutsättningar
- Slutför [snabb starten: dirigera Blob Storage-händelser till webb slut punkten med Azure Portal](blob-event-quickstart-portal.md) i käll regionen. Det här steget är **valfritt**. Testa stegen i den här artikeln. Behåll lagrings kontot i en separat resurs grupp från App Service och App Service plan. 
- Se till att tjänsten Event Grid är tillgänglig i mål regionen. Se [tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall för resurs gruppen som innehåller system händelse källan (Azure Storage konto) och det associerade system avsnittet. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** på den vänstra menyn. Välj sedan den resurs grupp som innehåller den händelse källa som systemet ska skapas för. I följande exempel är det **Azure Storage** kontot. Resurs gruppen innehåller avsnittet lagrings konto och det associerade systemet. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Sidan resurs grupp":::        
3. På den vänstra menyn väljer du **Exportera mall** under **Inställningar** och väljer sedan **Hämta** i verktygsfältet. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="Storage konto – exportera mall":::        
5. Leta upp **zip** -filen som du laddade ned från portalen och zippa upp filen till en valfri mapp. Den här zip-filen innehåller mallar och parametrar JSON-filer. 
1. Öppna **template.js** i valfritt redigerings program. 
1. URL: en för webhooken har inte exporter ATS till mallen. Gör så här:
    1. I mallfilen söker du efter **webhook**. 
    1. I avsnittet **Egenskaper** lägger du till ett kommatecken ( `,` )-tangent i slutet av den sista raden. I det här exemplet är det `"preferredBatchSizeInKilobytes": 64` . 
    1. Lägg till `endpointUrl` egenskapen med värdet som angetts till webhook-URL: en som visas i följande exempel. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > För andra typer av händelse hanterare exporteras alla egenskaper till mallen. Du behöver bara uppdatera `location` egenskapen till mål regionen som visas i nästa steg. 
7. Uppdatera `location` **lagrings konto** resursen till mål regionen eller platsen. För att hämta plats koder, se [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet utan mellanslag, till exempel `West US` är lika med `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Upprepa steget för att uppdatera `location` **system ämnes** resursen i mallen. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Spara** mallen. 

## <a name="recreate"></a>Återskapa 
Distribuera mallen för att skapa ett lagrings konto och ett system ämne för lagrings kontot i mål regionen. 

1. I Azure Portal väljer du **skapa en resurs**.
2. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.
3. Välj **malldistribution**.
4. Välj **Skapa**.
5. Välj **Bygg en egen mall i redigeraren**.
6. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in **template.jspå** filen som du laddade ned i det sista avsnittet.
7. Spara mallen genom att välja **Spara** . 
8. Följ de här stegen på sidan **Anpassad distribution** . 
    1. Välj en Azure- **prenumeration**. 
    1. Välj en befintlig **resurs grupp** i mål regionen eller skapa en. 
    1. För **region** väljer du mål regionen. Om du har valt en befintlig resurs grupp är den här inställningen skrivskyddad.
    1. I **ämnes namnet för systemet** anger du ett namn för det system ämne som ska associeras med lagrings kontot.  
    1. För **lagrings konto namnet** anger du ett namn för lagrings kontot som ska skapas i mål regionen. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Distribuera Resource Manager-mall":::
    5. Välj **Granska + skapa** längst ned på sidan. 
    1. På sidan **Granska + skapa** granskar du inställningarna och väljer **skapa**. 

## <a name="verify"></a>Verifiera
1. När distributionen har slutförts väljer du **gå till resurs grupp**. 
1. På sidan **resurs grupp** kontrollerar du att händelse källan (i det här exemplet Azure Storage konto) och system avsnittet skapas. 
1. Ladda upp en fil till en behållare i Azure Blob Storage och kontrol lera att webhooken har tagit emot händelsen. Mer information finns i [skicka en händelse till din slut punkt](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint).

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Ta bort resurs gruppen som innehåller lagrings kontot och det associerade system avsnittet i käll regionen för att slutföra flyttningen.  

Om du vill börja om tar du bort resurs gruppen i mål regionen och upprepar stegen i avsnitten [förbereda](#prepare) och [Återskapa](#recreate) i den här artikeln.

Så här tar du bort en resurs grupp (källa eller mål) med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal, Skriv **resurs grupper** och välj **resurs grupper** från Sök resultat. 
2. Välj den resurs grupp som ska tas bort och välj **ta bort** från verktygsfältet. 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="Ta bort resursgrupp":::
3. På sidan bekräftelse anger du namnet på resurs gruppen och väljer **ta bort**.  

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du flyttar en Azure Event-källa och dess associerade system-avsnitt från en region till en annan region. I följande artiklar finns information om hur du flyttar anpassade ämnen, domäner och partners namn områden i olika regioner.

- [Flytta anpassade ämnen mellan regioner](move-custom-topics-across-regions.md). 
- [Flytta domäner mellan regioner](move-domains-across-regions.md). 
- [Flytta namn rymder för partner över regioner](move-partner-namespaces-across-regions.md). 

Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i följande artikel: [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).
