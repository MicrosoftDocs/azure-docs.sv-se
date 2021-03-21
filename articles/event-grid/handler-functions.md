---
title: Använd en funktion i Azure som händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda funktioner som skapats i och finns i Azure Functions som händelse hanterare för Event Grid händelser.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f547b09fe7e62eb3fa9e02bd17298a936350f871
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496549"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Använd en funktion som händelse hanterare för Event Grid händelser

En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar en åtgärd för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Functions** är en av dem. 


Om du vill använda en funktion i Azure som en hanterare för händelser, följer du någon av följande metoder: 

-   Använd [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid-trigger.md).  Ange **Azure Function** som **typ av slut punkt**. Ange sedan Function-appen och funktionen som ska hantera händelser. 
-   Använd [http-utlösare](../azure-functions/functions-bindings-http-webhook.md).  Ange **Web Hook** som **typ av slut punkt**. Ange sedan URL: en för den funktion som ska hantera händelser. 

Vi rekommenderar att du använder den första metoden (Event Grid utlösare) eftersom den har följande fördelar jämfört med den andra metoden:
-   Event Grid validerar automatiskt Event Grid-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifierings svaret](webhook-event-delivery.md) själv.
-   Event Grid justerar automatiskt den hastighet med vilken händelser levereras till en funktion som utlöses av en Event Grid händelse baserat på den uppskattade frekvens som funktionen kan bearbeta händelser för. Den här frekvensen matchar AVERTs leverans fel som härrör från oförmåga att en funktion bearbetar händelser som funktionens händelse bearbetnings takt kan variera över tid. Du kan förbättra effektiviteten vid hög genom strömning genom att aktivera batching av händelse prenumerationen. Mer information finns i [Aktivera batching](#enable-batching).

    > [!NOTE]
    > För närvarande kan du inte använda en Event Grid utlösare för en Function-app när händelsen levereras i **CloudEvents** -schemat. Använd i stället en HTTP-utlösare.

## <a name="tutorials"></a>Självstudier

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: hantera händelser med funktion](custom-event-to-function.md) | Skickar en anpassad händelse till en funktion för bearbetning. |
| [Självstudie: automatisera storleks ändring av överförda bilder med hjälp av Event Grid](resize-images-on-storage-blob-upload-event.md) | Användare laddar upp bilder via webbapp till lagrings kontot. När en lagrings-BLOB skapas skickar Event Grid en händelse till Function-appen, som ändrar storlek på den överförda avbildningen. |
| [Självstudie: strömma Big data till ett informations lager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en Infångnings fil, Event Grid skickar en händelse till en Function-app. Appen hämtar insamlings filen och migrerar data till ett data lager. |
| [Självstudie: Azure Service Bus till Azure Event Grid integrations exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till en Function-app och en Logic-app. |

## <a name="rest-example-for-put"></a>REST-exempel (för placering)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Aktivera batchbearbetning
För ett högre genomflöde aktiverar du batching av prenumerationen. Om du använder Azure Portal kan du ange högsta antal händelser per batch och önskad batchstorlek i kilobyte byte vid tidpunkten för att skapa en prenumeration eller när den har skapats. 

Du kan konfigurera batch-inställningar med hjälp av Azure Portal, PowerShell, CLI eller Resource Manager-mallen. 

### <a name="azure-portal"></a>Azure Portal
När du skapar en prenumeration i användar gränssnittet går du till sidan **Skapa händelse prenumeration** , växlar till fliken **avancerade funktioner** och anger värden för **Max antal händelser per batch** och **önskad batchstorlek i kilobyte**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Aktivera batchbearbetning vid tidpunkten för att skapa en prenumeration":::

Du kan uppdatera de här värdena för en befintlig prenumeration på fliken **funktioner** på ämnes sidan för **Event Grid** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Aktivera batchbearbetning efter skapande":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan ange **maxEventsPerBatch** och **preferredBatchSizeInKilobytes** i en Azure Resource Manager-mall. Mer information finns i [referens för Microsoft. EventGrid eventSubscriptions Template](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI
Du kan använda [AZ eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create&preserve-view=true) eller [AZ eventgrid Event-Subscription Update-](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update&preserve-view=true) kommando för att konfigurera batch-relaterade inställningar med följande parametrar: `--max-events-per-batch` eller `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Du kan använda cmdleten [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) eller [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) för att konfigurera batch-relaterade inställningar med följande parametrar: `-MaxEventsPerBatch` eller `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> När du använder Event Grid-utlösaren hämtar Event Grid tjänsten klient hemligheten för Azure-funktionen och använder den för att leverera händelser till Azure-funktionen. Om du skyddar din Azure-funktion med ett Azure Active Directory-program måste du ta den allmänna webhook-metoden och använda HTTP-utlösaren.

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) .
