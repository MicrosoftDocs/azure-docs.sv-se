---
title: Azure Event Grid leverans och försök igen
description: Beskriver hur Azure Event Grid levererar händelser och hur de hanterar meddelanden som inte levererats.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e24b7540ea1ac41774e2c23781265f9a61940cb1
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276747"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid meddelande leverans och försök igen

I den här artikeln beskrivs hur Azure Event Grid hanterar händelser när leverans inte har bekräftats.

Event Grid tillhandahåller varaktig leverans. Den levererar varje meddelande **minst en gång** för varje prenumeration. Händelser skickas till den registrerade slut punkten för varje prenumeration direkt. Om en slut punkt inte bekräftar mottagandet av en händelse Event Grid försök att leverera händelsen igen.

> [!NOTE]
> Event Grid garanterar inte att en händelse levereras, så prenumeranterna kan ta emot dem i rätt ordning. 

## <a name="batched-event-delivery"></a>Leverans av batch-händelser

Event Grid standard för att skicka varje händelse individuellt till prenumeranter. Prenumeranten tar emot en matris med en enda händelse. Du kan konfigurera Event Grid att batch-händelser ska levereras för bättre HTTP-prestanda i scenarier med stora data flöden.

Batch-leverans har två inställningar:

* **Max antal händelser per batch** -maximalt antal händelser Event Grid skickas per batch. Det här antalet kommer aldrig att överskridas, men färre händelser kan levereras om inga andra händelser är tillgängliga vid tidpunkten för publiceringen. Event Grid fördröjer inte händelser för att skapa en batch om färre händelser är tillgängliga. Måste vara mellan 1 och 5 000.
* **Önskad batchstorlek i kilobyte** – mål tak för batchstorlek i kilobyte. På samma sätt som för högsta antal händelser kan batchstorleken vara mindre om fler händelser inte är tillgängliga vid tidpunkten för publiceringen. Det är möjligt att en batch är större än den önskade batchstorleken *om* en enskild händelse är större än den önskade storleken. Om den önskade storleken till exempel är 4 KB och en 10 KB-händelse skickas till Event Grid, kommer 10 KB-händelsen fortfarande att levereras i sin egen batch i stället för att släppas.

Batch-baserad leverans i som kon figurer ATS per händelse prenumeration via portalen, CLI, PowerShell eller SDK: er.

### <a name="azure-portal"></a>Azure Portal: 
![Inställningar för batch-leverans](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
När du skapar en händelse prenumeration använder du följande parametrar: 

- **Max antal händelser per batch** -maximalt antal händelser i en batch. Måste vara ett tal mellan 1 och 5000.
- **önskad-batch-storlek-in-kilobyte** -önskad batchstorlek i kilobyte. Måste vara ett tal mellan 1 och 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Mer information om hur du använder Azure CLI med Event Grid finns i [dirigera lagrings händelser till webb slut punkter med Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Schema och varaktighet för omförsök

När EventGrid tar emot ett fel för ett händelse leverans försök, bestämmer EventGrid om det ska försöka igen, obeställbara händelsen eller släppa händelsen baserat på typen av fel. 

Om felet som returnerades av den prenumererade slut punkten är ett konfigurations relaterat fel som inte kan åtgärdas med återförsök (till exempel om slut punkten tas bort), kommer EventGrid antingen att utföra obeställbara meddelanden vid händelsen eller släppa händelsen om ingen obeställbara meddelanden har kon figurer ATS.

I följande tabell beskrivs de typer av slut punkter och fel som inte går att utföra:

| Typ av slutpunkt | Felkoder |
| --------------| -----------|
| Azure-resurser | 400 Felaktig begäran, 413 begär ande enhet för stor, 403 förbjuden | 
| Webhook | 400 Felaktig begäran, 413 begär ande enhet för stor, 403 förbjuden, 404 hittades inte, 401 obehörig |
 
> [!NOTE]
> Om Dead-Letter inte har kon figurer ATS för en slut punkt försvinner händelserna när ovanstående fel inträffar. Överväg att konfigurera Dead-Letter om du inte vill att dessa typer av händelser ska släppas.

Om felet som returnerades av den prenumererade slut punkten inte finns i listan ovan, utför EventGrid det nya försöket med principer som beskrivs nedan:

Event Grid väntar 30 sekunder på ett svar när ett meddelande har levererats. Efter 30 sekunder, om slut punkten inte har svarat, placeras meddelandet i kö för att försöka igen. Event Grid använder en princip för en exponentiell backoff-återförsök för händelse leverans. Event Grid försöker leverera enligt följande schema på bästa möjliga sätt:

- 10 sekunder
- 30 sekunder
- 1 minut
- 5 minuter
- 10 minuter
- 30 minuter
- 1 timme
- 3 timmar
- 6 timmar
- Var 12: e timme upp till 24 timmar


Om slut punkten svarar inom 3 minuter försöker Event Grid ta bort händelsen från kön för nya försök på bästa möjliga sätt, men dubbletter kan ändå tas emot.

Event Grid lägger till en liten slumpmässighet för alla återförsöks steg och kan autentiseringsuppsättningarna hoppa över vissa försök om en slut punkt är konsekvent med låg skada, under en längre period eller om den verkar vara överbelastad.

För deterministiskt beteende ställer du in händelse tiden till Live och Max antalet leverans försök i [prenumerationens återförsöks principer](manage-event-delivery.md).

Som standard förfaller Event Grid alla händelser som inte levereras inom 24 timmar. Du kan [anpassa principen för att försöka igen](manage-event-delivery.md) när du skapar en händelse prenumeration. Du anger det maximala antalet leverans försök (Standardvärdet är 30) och händelsens tids till Live (Standardvärdet är 1440 minuter).

## <a name="delayed-delivery"></a>Fördröjd leverans

Som ett slut punkts problem med leverans fel börjar Event Grid fördröja leveransen och nya försök att utföra händelser till den slut punkten. Om till exempel de första 10 händelserna som publicerats till en slut punkt Miss fungerar, kommer Event Grid att anta att slut punkten har problem och kommer att fördröja alla efterföljande försök *och nya* leveranser under en viss tid, i vissa fall upp till flera timmar.

Det funktionella syftet med försenad leverans är att skydda Felaktiga slut punkter och Event Grid systemet. Utan avstängning och fördröjning av leverans till felaktiga slut punkter, kan Event Grids princip för återförsök och volym kapacitet lätt överbelasta ett system.

## <a name="dead-letter-events"></a>Händelser för obeställbara meddelanden
När Event Grid inte kan leverera en händelse inom en viss tids period eller när händelsen försöker leverera händelsen ett visst antal gånger, kan den skicka den ej levererade händelsen till ett lagrings konto. Den här processen kallas för **obeställbara meddelanden**. Event Grid obeställbara meddelanden en händelse när **något av följande** villkor uppfylls. 

- Händelsen har inte levererats inom **Time-to-Live-** perioden. 
- **Antalet försök** att leverera händelsen har överskridit gränsen.

Om något av villkoren är uppfyllt tas händelsen bort eller tas bort från kön.  Som standard aktiverar Event Grid inte obeställbara meddelanden. Om du vill aktivera det måste du ange ett lagrings konto som ska innehålla ej levererade händelser när händelse prenumerationen skapas. Du kan hämta händelser från det här lagrings kontot för att lösa leveranser.

Event Grid skickar en händelse till platsen för obeställbara meddelanden när den har provat alla nya försök. Om Event Grid får en 400 (felaktig begäran) eller 413 (den begärda entiteten för stor) svarskod schemaläggs omedelbart händelsen för obeställbara meddelanden. Dessa svars koder indikerar att händelsen levereras aldrig.

Tiden till Live-utgången kontrol leras bara vid nästa schemalagda leverans försök. Så även om Time-to-Live förfaller före nästa schemalagda leverans försök, kontrol leras händelsen som upphör att gälla vid nästa leverans och sedan i efterhand. 

Det senaste försöket att leverera en händelse är en fördröjning på fem minuter mellan det senaste försöket att leverera en händelse och när den levereras till platsen för obeställbara meddelanden. Den här fördröjningen är avsedd att minska antalet Blob Storage-åtgärder. Om platsen för obeställbara meddelanden inte är tillgänglig i fyra timmar släpps händelsen.

Innan du anger platsen för obeställbara meddelanden måste du ha ett lagrings konto med en behållare. Du anger slut punkten för den här behållaren när du skapar händelse prenumerationen. Slut punkten har formatet: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Du kanske vill bli meddelad när en händelse har skickats till platsen för obeställbara meddelanden. Om du vill använda Event Grid för att svara på Ej levererade händelser [skapar du en händelse prenumeration](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) för blob-lagringen med obeställbara meddelanden. Varje gång en blob-lagring med obeställbara meddelanden tar emot en händelse som inte levererats meddelar Event Grid din hanterare. Hanteraren svarar med åtgärder som du vill vidta för att stämma av ej levererade händelser. Ett exempel på att skapa en plats för obeställbara meddelanden och principer för återförsök finns i [principer för obeställbara brev och nya försök](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Leverans händelse format
Det här avsnittet innehåller exempel på händelser och meddelanden om obeställbara meddelanden i olika leverans schema format (Event Grid schema, CloudEvents 1,0-schema och anpassat schema). Mer information om dessa format finns i [Event Grid schema](event-schema.md) -och [moln händelser 1,0 schema](cloud-event-schema.md) artiklar. 

### <a name="event-grid-schema"></a>Event Grid-schema

#### <a name="event"></a>Händelse 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Händelse för obeställbara meddelanden

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1,0-schema

#### <a name="event"></a>Händelse

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Händelse för obeställbara meddelanden

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Anpassat schema

#### <a name="event"></a>Händelse

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Händelse för obeställbara meddelanden
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Status för meddelande leverans

Event Grid använder HTTP-svars koder för att bekräfta mottagande av händelser. 

### <a name="success-codes"></a>Lyckade koder

Event Grid betraktar **endast** följande HTTP-svarskod som lyckade leveranser. Alla andra status koder betraktas som misslyckade leveranser och kommer att göras om eller deadlettered efter behov. Vid mottagning av en lyckad status kod Event Grid anses leveransen vara klar.

- 200 OK
- 201 har skapats
- 202 accepterad
- 203 icke-auktoritativ information
- 204 inget innehåll

### <a name="failure-codes"></a>Felkoder

Alla andra koder som inte finns i ovanstående uppsättning (200-204) betraktas som felaktiga och kommer att göras om (om det behövs). Vissa har specifika principer för återförsök som är knutna till dem som beskrivs nedan, och alla andra följer standard modellen för exponentiella säkerhets kopiering. Det är viktigt att komma ihåg att på grund av den mycket parallella typen av Event Grids arkitektur, är återförsöket icke-deterministiskt. 

| Statuskod | Omprövnings beteende |
| ------------|----------------|
| 400 – Felaktig begäran | Inte nytt försök |
| 401 – Ej behörig | Försök igen om 5 minuter eller mer för resurs slut punkter i Azure |
| 403 – Förbjuden | Inte nytt försök |
| 404 – Hittades inte | Försök igen om 5 minuter eller mer för resurs slut punkter i Azure |
| 408 Timeout för begäran | Försök igen om 2 minuter eller mer |
| 413 begär ande enheten är för stor | Inte nytt försök |
| 503 Tjänsten är inte tillgänglig | Försök igen om 30 sekunder eller mer |
| Alla andra | Försök igen om 10 sekunder eller mer |

## <a name="custom-delivery-properties"></a>Anpassade leverans egenskaper
Med händelse prenumerationer kan du konfigurera HTTP-huvuden som ingår i levererade händelser. Med den här funktionen kan du ange anpassade huvuden som krävs av ett mål. Du kan ställa in upp till 10 huvuden när du skapar en händelse prenumeration. Varje rubrik värde får inte vara större än 4 096 (4K) byte. Du kan ange anpassade rubriker för de händelser som levereras till följande destinationer:

- Webhooks
- Azure Service Bus ämnen och köer
- Azure Event Hubs
- Relä Hybridanslutningar

Mer information finns i [anpassade leverans egenskaper](delivery-properties.md). 

## <a name="next-steps"></a>Nästa steg

* Information om hur du visar statusen för händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Information om hur du anpassar alternativ för händelse leverans finns i [principer för obeställbara brev och återförsök](manage-event-delivery.md).
