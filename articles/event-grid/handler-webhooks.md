---
title: Webhooks som händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Webhooks som händelse hanterare för Azure Event Grid händelser. Azure Automation runbooks och Logic Apps stöds som händelse hanterare via Webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: aa20c75789a18b93f787134dffe165e60ff1ab6f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91875809"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhooks, Automation-runbooks Logic Apps som händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser. Du kan också använda en webhook för att hantera händelser. Webhooken behöver inte vara värd för Azure för att hantera händelser. Event Grid stöder endast HTTPS webhook-slutpunkter.

> [!NOTE]
> - Azure Automation runbooks och Logic Apps stöds som händelse hanterare via Webhooks. 
> - Även om du kan använda **webhook** som en **slut punkts typ** för att konfigurera en Azure-funktion som händelse hanterare, använder du **Azure Function** som typ av slut punkt. Mer information finns i [Azure Function som händelse hanterare](handler-functions.md).

## <a name="webhooks"></a>Webhooks
I följande artiklar finns en översikt och exempel på hur du använder Webhooks som händelse hanterare. 

|Rubrik  |Beskrivning  |
|---------|---------|
| Snabb start: skapa och dirigera anpassade händelser med- [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md)och [Portal](custom-event-quickstart-portal.md). | Visar hur du skickar anpassade händelser till en webhook. |
| Snabb start: dirigera Blob Storage-händelser till en anpassad webb slut punkt med- [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)och [Portal](blob-event-quickstart-portal.md). | Visar hur du skickar Blob Storage-händelser till en webhook. |
| [Snabb start: skicka behållar register händelser](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Container Registry händelser. |
| [Översikt: ta emot händelser till en HTTP-slutpunkt](receive-events.md) | Beskriver hur du verifierar en HTTP-slutpunkt för att ta emot händelser från en händelse prenumeration och ta emot och deserialisera händelser. |


## <a name="azure-automation"></a>Azure Automation
Du kan bearbeta händelser med hjälp av Azure Automation runbooks. Bearbetning av händelser med hjälp av automatiska Runbooks stöds via Webhooks. Du skapar en webhook för runbooken och använder sedan webhook-hanteraren. Se följande självstudie för ett exempel: 

|Rubrik  |Beskrivning  |
|---------|---------|
|[Självstudie: Azure Automation med Event Grid och Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator, som skickar en händelse. Händelsen utlöser en Automation-Runbook som Taggar den virtuella datorn och utlöser ett meddelande som skickas till en Microsoft Teams-kanal. |


## <a name="logic-apps"></a>Logic Apps
Använd **Logic Apps** för att implementera affärs processer för att bearbeta Event Grid händelser. Du behöver inte skapa en webhook uttryckligen i det här scenariot. Webhooken skapas automatiskt när du konfigurerar Logi Kap par för hantering av händelser från Event Grid. Se följande självstudier för exempel: 

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: övervaka ändringar av virtuella datorer med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En Logic app övervakar ändringar i en virtuell dator och skickar e-postmeddelanden om ändringarna. |
| [Självstudie: skicka e-postaviseringar om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En Logic App skickar ett e-postmeddelande varje gång en enhet läggs till i IoT Hub. |
| [Självstudie: svara på Azure Service Bus händelser som tas emot via Azure Event Grid med Azure Functions och Azure Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till att fungera som app-och Logic-appen. |

## <a name="rest-example-for-put"></a>REST-exempel (för placering)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 
