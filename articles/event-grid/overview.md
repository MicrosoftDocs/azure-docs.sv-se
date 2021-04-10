---
title: Vad är Azure Event Grid?
description: Skicka händelsedata från en källa till hanterare med Azure Event Grid. Skapa händelsebaserade program och integrera med Azure-tjänster.
ms.topic: overview
ms.date: 01/28/2021
ms.openlocfilehash: a410f56da9e28403fb98d92332e6d67c064bac4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934876"
---
# <a name="what-is-azure-event-grid"></a>Vad är Azure Event Grid?

Med Azure Event Grid kan du enkelt skapa program med händelsebaserade arkitekturer. Du börjar med att välja Azure-resursen som du vill prenumerera på och anger sedan händelsehanteraren eller WebHook-slutpunkten som händelsen ska skickas till. Event Grid har inbyggt stöd för händelser som kommer från Azure-tjänster, som lagringsblobar och resursgrupper. Du kan också använda egna händelser med Event Grid, med hjälp av anpassade ämnen. 

Du kan använda filter för att dirigera specifika händelser till olika slutpunkter, multicast-sända till flera slutpunkter och se till att händelserna levereras tillförlitligt.

Azure Event Grid distribueras för att maximera tillgängligheten genom att sprida sig internt över flera fel domäner i varje region, och över tillgänglighets zoner (i regioner som stöder dem). En lista över regioner som stöds av Event Grid finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Den här artikeln innehåller en översikt för Azure Event Grid. Om du vill komma igång med Event Grid kan du läsa [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md). 

:::image type="content" source="./media/overview/functional-model.png" alt-text="Event Grid-modell för källor och hanterare" lightbox="./media/overview/functional-model-big.png":::

> [!NOTE]
> Den här bilden illustrerar hur Event Grid ansluter källor och hanterare och är inte en heltäckande lista över integreringar som stöds. En lista över alla händelse källor som stöds finns i följande avsnitt. 

## <a name="event-sources"></a>Händelsekällor

För närvarande har följande Azure-tjänster stöd för att skicka händelser till Event Grid. Om du vill ha mer information om en källa i listan väljer du länken.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure Policy](./event-schema-policy.md)
- [Azure-resursgrupper](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure-prenumerationer](event-schema-subscriptions.md)
- [Azure Cache for Redis](event-schema-azure-cache.md)

## <a name="event-handlers"></a>Händelsehanterare

Fullständig information om funktionerna för varje hanterare samt relaterade artiklar finns i avsnittet om [händelsehanterare](event-handlers.md). För närvarande stöder följande Azure-tjänster hantering av händelser från Event Grid: 

* [Azure Automation](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [Relä Hybridanslutningar](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Automatisera energi (tidigare Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Queue Storage](handler-storage-queues.md)
* [WebHooks](handler-webhooks.md)

## <a name="concepts"></a>Begrepp

Det finns fem begrepp i Azure Event Grid som hjälper dig igång:

* **Händelser** – Vad hände?
* **Händelsekällor** – där händelsen ägde rum.
* **Ämnen** – Slutpunkten som utgivarna skickar händelser till.
* **Händelseprenumerationer** – Den slutpunkt eller inbyggda mekanism som dirigerar händelser, ibland till fler än en hanterare. Prenumerationer används också av hanterarna för att filtrera inkommande händelser på ett intelligent sätt.
* **Händelsehanterare** – Den app eller tjänst som reagerar på händelsen.

Mer information om de här begreppen finns i [Begrepp i Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Funktioner

Här följer några viktiga funktioner i Azure Event Grid:

* **Enkelhet** – Peka och klicka för att dirigera händelser från Azure-resursen till valfri händelsehanterare eller slutpunkt.
* **Avancerad filtrering** – Filtrera på händelsetyp eller händelsens publiceringssökväg för att säkerställa att händelsehanterarna endast får relevanta händelser.
* **Förgrena** – Prenumerera på flera slutpunkter till samma händelse för att kunna skicka kopior av händelsen till så många platser som det behövs.
* **Tillförlitlighet** – 24-timmars omförsök med exponentiellt intervall för att se till att händelser levereras.
* **Betala per händelse** – Betala endast för hur mycket du använder Event Grid.
* **Högt data flöde** – Bygg arbets belastningar med stora volymer på Event Grid.
* **Inbyggda händelser** – Kom igång snabbt med resursdefinierade inbyggda händelser.
* **Anpassade händelser** – Använd Event Grid för att dirigera, filtrera och på ett tillförlitligt sätt leverera anpassade händelser i din app.

En jämförelse av Event Grid, Event Hubs och Service Bus finns i [Välj mellan Azure-tjänster som levererar meddelanden](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Vad kan jag göra med Event Grid?

Azure Event Grid innehåller flera funktioner som avsevärt förbättrar åtgärdsautomatisering och [integreringsarbete](https://azure.com/integration) utan server: 

### <a name="serverless-application-architectures"></a>Arkitekturer för program utan server

![Arkitektur för program utan server](./media/overview/serverless_web_app.png)

Event Grid kopplar samman datakällor och händelsehanterare. Använd till exempel Event Grid för att utlösa en serverlös funktion som analyserar bilder när de läggs till i en Blob Storage-container. 

### <a name="ops-automation"></a>Automatisering av åtgärder

![Automatisering av åtgärder](./media/overview/Ops_automation.png)

Event Grid ger snabbare automatisering och enklare principtillämpning. Använd till exempel Event Grid för att meddela Azure Automation när en virtuell dator eller databas i Azure SQL skapas. Använd de här händelserna för att automatiskt kontrollera att tjänstkonfigurationer följer standard, placera metadata i åtgärdsverktyg, tagga virtuella datorer eller arkivera arbetsobjekt.

### <a name="application-integration"></a>Integrering av program

![Integrering av program med Azure](./media/overview/app_integration.png)

Event Grid ansluter din app till andra tjänster. Skapa till exempel ett anpassat ämne för att skicka appens händelsedata till Event Grid och dra nytta av dess tillförlitliga leverans, avancerade dirigering och direkta integrering med Azure. Du kan även använda Event Grid med Logic Apps för att bearbeta data var som helst, utan att skriva någon kod. 

## <a name="how-much-does-event-grid-cost"></a>Hur mycket kostar Event Grid?

Azure Event Grid använder en prismodell med betalning per händelse, så du betalar bara för det du använder. De första 100 000 åtgärderna per månad är gratis. Åtgärder definieras som händelsens ingång, leveransförsök av prenumeration, hanteringsanrop och filtrering av ämnessuffix. Se [sidan med våra priser](https://azure.microsoft.com/pricing/details/event-grid/) för mer information.

## <a name="next-steps"></a>Nästa steg

* [Dirigera Storage Blob-händelser](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Svara på Storage Blob-händelser med hjälp av Event Grid.
* [Skapa och prenumerera på anpassade händelser](custom-event-quickstart.md)  
  Kom igång direkt och börja skicka dina egna anpassade händelser till valfri slutpunkt med hjälp av snabbstarten för Azure Event Grid.
* [Använda Logic Apps som händelsehanterare](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  En självstudie om hur du skapar en app med hjälp av Logic Apps, för att kunna reagera på händelser som skickas av Event Grid.
* [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)  
  En själv studie kurs som använder Azure Functions för att strömma data från Event Hubs till Azure Synapse Analytics.
* [REST API-referens för Event Grid](/rest/api/eventgrid)  
  Innehåller referensinnehåll för hantering av händelseprenumerationer, routning och filtrering.