---
title: Logga händelser till Azure Event Hubs i Azure API Management | Microsoft Docs
description: Lär dig hur du loggar händelser till Azure Event Hubs i Azure API Management. Event Hubs är en mycket skalbar data ingress-tjänst.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 5066169951409fa86aa75a64e8fc6d4189947f27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072415"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Logga händelser till Azure Event Hubs i Azure API Management
Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Event Hubs fungerar som "Front dörren" för händelse pipelinen och när data samlas in i en händelsehubben kan den transformeras och lagras med hjälp av en analys av real tids analys eller batch-/minnes kort. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema.

Den här artikeln är kopplad till [integrera Azure-API Management med Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video och beskriver hur du loggar API Management händelser med Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Detaljerade anvisningar om hur du skapar en Event Hub och hämtar anslutnings strängar som du behöver för att skicka och ta emot händelser till och från Händelsehubben finns i [skapa ett Event Hubs-namnområde och en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md).

## <a name="create-an-api-management-logger"></a>Skapa en API Management-loggare
Nu när du har en Event Hub är nästa steg att konfigurera en [loggare](/rest/api/apimanagement/2019-12-01/logger) i API Management-tjänsten så att den kan logga händelser till händelsehubben.

API Management-loggar konfigureras med hjälp av [API Management REST API](/rest/api/apimanagement/ApiManagementREST/API-Management-REST). Exempel på detaljerad begäran finns i [så här skapar du loggar](/rest/api/apimanagement/2019-12-01/logger/createorupdate).

## <a name="configure-log-to-eventhub-policies"></a>Konfigurera logg-till-eventhub-principer

När din logg har kon figurer ATS i API Management kan du konfigurera din logg-till-eventhub-princip för att logga de önskade händelserna. Du kan använda logg-till-eventhub-principen antingen i avsnittet inkommande princip eller i avsnittet utgående princip.

1. Bläddra till APIM-instansen.
2. Välj fliken API.
3. Välj det API som du vill lägga till principen i. I det här exemplet lägger vi till en princip i **eko-API: et** i den **obegränsade** produkten.
4. Välj **alla åtgärder**.
5. Överst på skärmen väljer du fliken Design.
6. I fönstret inkommande eller utgående bearbetning klickar du på triangeln (bredvid blyertspennan).
7. Välj kod redigeraren. Mer information finns i [så här ställer du in eller redigerar principer](set-edit-policies.md).
8. Placera markören i `inbound` `outbound` avsnittet eller principen.
9. I fönstret till höger väljer du **avancerade principer**  >  **Logga till EventHub**. Detta infogar `log-to-eventhub` princip instruktions mal len.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Ersätt `logger-id` med värdet som du använde för `{loggerId}` i URL: en för begäran för att skapa loggaren i föregående steg.

Du kan använda alla uttryck som returnerar en sträng som värde för `log-to-eventhub` elementet. I det här exemplet loggas en sträng i JSON-format som innehåller datum och tid, tjänst namn, ID för begäran, begär ande-IP-adress och åtgärds namn.

Klicka på **Spara** för att spara den uppdaterade princip konfigurationen. Så snart den har sparats är principen aktiv och händelserna loggas i den angivna Händelsehubben.

> [!NOTE]
> Den största meddelande storlek som kan skickas till en händelsehubben från den här API Managements principen är 200 KB. Om ett meddelande som skickas till en händelsehubben är större än 200 KB trunkeras det automatiskt och det trunkerade meddelandet överförs till Event Hub.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Förhandsgranska loggen i Event Hubs med Azure Stream Analytics

Du kan förhandsgranska loggen i Event Hubs genom att använda [Azure Stream Analytics frågor](../event-hubs/process-data-azure-stream-analytics.md). 

1. I Azure Portal bläddrar du till händelsehubben som loggen skickar händelser till. 
2. Under **funktioner** väljer du fliken **bearbeta data** .
3. På kortet **Aktivera real tids insikter från evenemang** väljer du **utforska**.
4. Du bör kunna förhandsgranska loggen på fliken **förhands granskning av indatakälla** . Om informationen som visas inte är aktuell väljer du **Uppdatera** för att se de senaste händelserna.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Event Hubs
  * [Kom igång med Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Programmerings guide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Läs mer om API Management och Event Hubs-integrering
  * [Referens för entiteten loggning](/rest/api/apimanagement/2019-12-01/logger)
  * [logg-till-eventhub-princip referens](./api-management-advanced-policies.md#log-to-eventhub)
  * [Övervaka dina API: er med Azure API Management, Event Hubs och Moesif](api-management-log-to-eventhub-sample.md)  
* Lär dig mer om [integrering med Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png