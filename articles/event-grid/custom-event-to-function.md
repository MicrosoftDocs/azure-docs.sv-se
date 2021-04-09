---
title: 'Snabb start: skicka anpassade händelser till Azure Function-Event Grid'
description: 'Snabb start: Använd Azure Event Grid och Azure CLI eller portal för att publicera ett ämne och prenumerera på händelsen. En Azure-funktion används för slut punkten.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 4fe4753de41443a0537636933364c7b69b25cb27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791752"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Snabb start: dirigera anpassade händelser till en Azure-funktion med Event Grid

Azure Event Grid är en händelsetjänst för molnet. Azure Functions är en av de händelse hanterare som stöds. I den här artikeln använder du Azure-portalen för att skapa ett anpassat ämne, prenumerera på det anpassade ämnet och utlösa händelsen för att visa resultatet. Du skickar händelser till en Azure-funktion.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Skapa Azure-funktion
Innan du prenumererar på det anpassade ämnet skapar du en funktion för att hantera händelserna. 

1. Skapa en Function-app med hjälp av instruktioner från [skapa en Function-app](../azure-functions/functions-get-started.md).
2. Skapa en funktion med hjälp av **Event Grid-utlösaren**. Välj om det här är första gången du använder den här utlösaren kan du behöva klicka på installera för att installera tillägget.
    1. På sidan **Funktionsapp** väljer du **funktioner** på den vänstra menyn, söker efter **Event Grid** i mallar och väljer sedan **Azure Event Grid utlösare**. 

        :::image type="content" source="./media/custom-event-to-function/function-event-grid-trigger.png" alt-text="Välj Event Grid utlösare":::
3. På sidan **ny funktion** anger du ett namn för funktionen och väljer **skapa funktion**.

    :::image type="content" source="./media/custom-event-to-function/new-function-page.png" alt-text="Sidan ny funktion":::
4. Använd sidan **kod + test** för att se den befintliga koden för funktionen och uppdatera den. 

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar dina händelser. 

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **alla tjänster** i den vänstra navigerings menyn, sök efter **Event Grid** och välj **Event Grid ämnen**. 

    ![Välj Event Grid ämnen](./media/custom-event-to-function/select-event-grid-topics.png)
3. På sidan **Event Grid ämnen** väljer du **+ Lägg till** i verktygsfältet. 

    ![Knappen Lägg till Event Grid ämne](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Följ dessa steg på sidan **skapa ämne** :

    1. Ange ett unikt **namn** för det anpassade ämnet. Ämnesnamnet måste vara unikt eftersom det representeras av en DNS-post. Använd inte det namn som visas på bilden. I stället skapar du ett eget namn som måste bestå av 3–50 tecken och enbart får innehålla a-z, A-Z, 0-9 och ”-”.
    2. Välj din Azure- **prenumeration**.
    3. Välj samma resurs grupp i föregående steg.
    4. Välj en **plats** för Event Grid-ämnet.
    5. Behåll standardvärdet **Event Grid schema** för fältet **händelse schema** . 

       ![Sidan skapa ämne](./media/custom-event-to-function/create-custom-topic.png)
    6. Välj **Skapa**. 

5. När du har skapat det anpassade ämnet visas ett meddelande om detta. Välj **gå till resurs grupp**. 

   ![Se meddelandet](./media/custom-event-to-function/success-notification.png)

6. På sidan **resurs grupp** väljer du avsnittet Event Grid. 

   ![Välj ämnes resurs för händelse rutnät](./media/custom-event-to-function/select-event-grid-topic.png)

7. Du ser sidan **Event Grid ämne** för Event Grid. Behåll sidan öppen. Du använder det senare i snabb starten. 

    ![Start sida för Event Grid ämne](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Prenumerera på anpassat ämne

Du prenumererar på ett Event Grid-ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna.

1. På sidan **Event Grid ämne** för ditt anpassade ämne väljer du **+ händelse prenumeration** i verktygsfältet.

   ![Lägga till händelseprenumeration](./media/custom-event-to-function/new-event-subscription.png)

2. Följ dessa steg på sidan **Skapa händelse prenumeration** :
    1. Ange ett **namn** för händelse prenumerationen.
    3. Välj **Azure Function** som **typ av slut punkt**. 
    4. Välj **Välj en slut punkt**. 

       ![Ange värden för händelseprenumerationen](./media/custom-event-to-function/provide-subscription-values.png)

    5. För funktions slut punkten väljer du den Azure-prenumeration och resurs grupp som du Funktionsapp är i och väljer sedan den Funktionsapp och funktion som du skapade tidigare. Välj **Bekräfta val**.

       ![Ange slutpunktens webbadress](./media/custom-event-to-function/provide-endpoint.png)
    6. Det här steget är valfritt, men rekommenderas för produktions scenarier. På sidan **Skapa händelse prenumeration** växlar du till fliken **avancerade funktioner** och anger värden för **Max antal händelser per batch** och **önskad batchstorlek i kilobyte**. 
    
        Satsvis kompilering kan ge dig stora data flöden. För **Max antal händelser per batch** ställer du in maximalt antal händelser som en prenumeration ska inkludera i en batch. Önskad batchstorlek ställer in den önskade övre gränsen för batchstorlek i kilobyte byte, men kan överskridas om en enskild händelse är större än det här tröskelvärdet.
    
        :::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Aktivera batchbearbetning":::
    6. På sidan **Skapa händelse prenumeration** väljer du **skapa**.

## <a name="send-an-event-to-your-topic"></a>Skicka en händelse till ditt ämne

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. Du kan använda antingen Azure CLI eller PowerShell och skicka en testhändelse till det anpassade ämnet. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata.

I det första exemplet används Azure CLI. URL och nyckel för det anpassade ämnet hämtas, och exempeldata för händelsen. Använd ditt anpassade ämnesnamn för `<topic name>`. Exempelhändelsedata skapas. Elementet `data` av JSON är händelsens nyttolast. All välformulerad JSON kan stå i det här fältet. Du kan också använda ämnesfältet för avancerad omdirigering och filtrering. CURL är ett verktyg som skickar HTTP-förfrågningar.


### <a name="azure-cli"></a>Azure CLI
1. I Azure Portal väljer du **Cloud Shell**. Välj **bash** i det övre vänstra hörnet i Cloud Shells fönstret. 

    ![Cloud Shell-bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Kör följande kommando för att hämta **slut punkten** för ämnet: när du har kopierat och klistrat in kommandot uppdaterar du **ämnes namnet** och **resurs gruppens namn** innan du kör kommandot. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Kör följande kommando för att hämta **nyckeln** för det anpassade ämnet: när du har kopierat och klistrat in kommandot uppdaterar du **ämnes namnet** och **resurs gruppens** namn innan du kör kommandot. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Kopiera följande instruktion med händelse definitionen och tryck på **RETUR**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Kör följande **spiral** kommando för att publicera händelsen:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
I det andra exemplet används PowerShell för att utföra liknande steg.

1. I Azure Portal väljer du **Cloud Shell** (du kan också gå till `https://shell.azure.com/` ). Välj **PowerShell** i det övre vänstra hörnet i Cloud Shells fönstret. Se exemplet på **Cloud Shell** fönstret i Azure CLI-avsnittet.
2. Ange följande variabler. När du har kopierat och klistrat in varje kommando uppdaterar du **ämnes namnet** och **resurs gruppens namn** innan du kör kommandot:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Kör följande kommandon för att hämta **slut punkten** och **nycklarna** för ämnet:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Förbered händelsen. Kopiera och kör instruktionerna i fönstret Cloud Shell. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Använd cmdleten **Invoke-WebRequest** för att skicka händelsen. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Verifiera i Event Grid Viewer
Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Navigera till din Event Grid utlöst funktion och öppna loggarna. Du bör se en kopia av händelsens data nytto Last i loggarna. Om du inte är säker på att du först öppnar fönstret loggar eller trycker du på Återanslut igen och försöker sedan att skicka en test händelse igen.

![Lyckad funktions utlösare logg](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. I annat fall tar du bort alla resurser som du har skapat i den här artikeln.

1. Välj **resurs grupper** på den vänstra menyn. Om du inte ser det på den vänstra menyn väljer du **alla tjänster** i den vänstra menyn och väljer **resurs grupper**. 
2. Välj den resurs grupp där du vill starta sidan **resurs grupp** . 
3. Välj **ta bort resurs grupp** i verktygsfältet. 
4. Bekräfta borttagningen genom att ange namnet på resurs gruppen och välj **ta bort**. 

    ![Resursgrupper](./media/custom-event-to-function/delete-resource-groups.png)

    Den andra resurs gruppen som visas i avbildningen har skapats och används av Cloud Shells fönstret. Ta bort det om du inte planerar att använda fönstret Cloud Shell senare. 

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)
