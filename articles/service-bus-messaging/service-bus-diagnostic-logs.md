---
title: Azure Service Bus diagnostikloggar | Microsoft Docs
description: Den här artikeln innehåller en översikt över alla operativa och diagnostiska loggar som är tillgängliga för Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f2672d61285025eadb8f742ca2518297107595ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585265"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Aktivera diagnostikloggar för Service Bus

När du börjar använda Azure Service Bus namn rymden kanske du vill övervaka hur och när ditt namn område skapas, tas bort eller nås. Den här artikeln innehåller en översikt över alla operativa och diagnostiska loggar som är tillgängliga.

Azure Service Bus stöder för närvarande aktivitets-och drift loggar som samlar in *hanterings åtgärder* som utförs i Azure Service Bus-namnområdet. Mer specifikt fångar dessa loggar åtgärds typen, inklusive skapande av kö, använda resurser och status för åtgärden.

## <a name="operational-logs-schema"></a>Schema för operativa loggar

Alla loggar lagras i JavaScript Object Notation (JSON)-format på följande två platser:

- **AzureActivity**: visar loggar från åtgärder och åtgärder som utförs mot ditt namn område i Azure Portal eller genom Azure Resource Manager mall distributioner.
- **AzureDiagnostics**: visar loggar från åtgärder och åtgärder som utförs mot ditt namn område med hjälp av API: et, eller via hanterings klienter på språk-SDK: n.

I drift loggens JSON-strängar ingår elementen som anges i följande tabell:

| Name | Beskrivning |
| ------- | ------- |
| ActivityId | Internt ID som används för att identifiera den angivna aktiviteten |
| EventName | Åtgärdsnamn |
| ResourceId | Resurs-ID för Azure Resource Manager |
| SubscriptionId | Prenumerations-ID:t |
| EventTimeString | Åtgärds tid |
| EventProperties | Egenskaper för åtgärd |
| Status | Åtgärdsstatus |
| Anropare | Uppringnings åtgärd (Azure Portal-eller hanterings klient) |
| Kategori | OperationalLogs |

Här är ett exempel på en fungerande logg-JSON-sträng:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Händelser och åtgärder som har registrerats i drift loggar

Drift loggar fångar alla hanterings åtgärder som utförs i Azure Service Bus-namnrymden. Data åtgärder samlas inte in på grund av den stora mängden data åtgärder som utförs på Azure Service Bus.

> [!NOTE]
> För att hjälpa dig att bättre spåra data åtgärder rekommenderar vi att du använder spårning på klient sidan.

Följande hanterings åtgärder samlas in i drift loggar: 

| Omfång | Åtgärd|
|-------| -------- |
| Namnområde | <ul> <li> Skapa namnrymd</li> <li> Uppdatera namnrymd </li> <li> Ta bort namnrymd </li> <li> Uppdatera namn område SharedAccess-princip </li> </ul> | 
| Kö | <ul> <li> Skapa kö</li> <li> Uppdatera kö</li> <li> Ta bort kö </li> <li> Ta bort kön tas bort </li> </ul> | 
| Avsnitt | <ul> <li> Skapa ämne </li> <li> Uppdatera ämne </li> <li> Ta bort ämne </li> <li> Ta bort ta bort ämne </li> </ul> |
| Prenumeration | <ul> <li> Skapa en prenumeration </li> <li> Uppdatera prenumeration </li> <li> Ta bort prenumeration </li> <li> Ta bort borttagnings prenumeration </li> </ul> |

> [!NOTE]
> För närvarande spåras inte *Läs* åtgärder i drifts loggarna.

## <a name="enable-operational-logs"></a>Aktivera drift loggar

Drift loggar är inaktiverade som standard. Så här aktiverar du diagnostikloggar:

1. I [Azure Portal](https://portal.azure.com)går du till ditt Azure Service Bus-namnområde och väljer sedan **diagnostiska inställningar** under **övervakning**.

   ![Länken "diagnostikinställningar"](./media/service-bus-diagnostic-logs/image1.png)

1. I fönstret **diagnostikinställningar** väljer du **Lägg till diagnostisk inställning**.  

   ![Länken "Lägg till diagnostisk inställning"](./media/service-bus-diagnostic-logs/image2.png)

1. Konfigurera diagnostikinställningar genom att göra följande:

   a. I rutan **namn** anger du ett namn för diagnostikinställningar.  

   b. Välj någon av följande tre destinationer för dina diagnostikloggar:  
   - Om du väljer **Arkiv till ett lagrings konto** måste du konfigurera lagrings kontot där diagnostikloggar ska lagras.  
   - Om du väljer **Stream till en Event Hub** måste du konfigurera den händelsehubben som du vill strömma diagnostikloggar till.
   - Om du väljer **Skicka till Log Analytics** måste du ange vilken instans av Log Analytics diagnostiken ska skickas till.  

   c. Markera kryss rutan **OperationalLogs** .

    ![Fönstret diagnostikinställningar](./media/service-bus-diagnostic-logs/image3.png)

1. Välj **Spara**.

De nya inställningarna börjar gälla om 10 minuter. Loggarna visas i det konfigurerade lagrings målet i fönstret **diagnostikloggar** .

Mer information om hur du konfigurerar diagnostikinställningar finns i [översikten över Azure Diagnostics-loggar](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus finns i:

* [Introduktion till Service Bus](service-bus-messaging-overview.md)
* [Kom igång med Service Bus](service-bus-dotnet-get-started-with-queues.md)