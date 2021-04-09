---
title: Händelse driven skalning i Azure Functions
description: Förklarar skalnings beteenden för användnings plan och program i Premium plan-funktionen.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937753"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Händelse driven skalning i Azure Functions

I förbruknings-och Premium-planerna skalar Azure Functions processor-och minnes resurser genom att lägga till ytterligare instanser av Functions-värden. Antalet instanser fastställs för antalet händelser som utlöser en funktion. 

Varje instans av Functions-värden i förbruknings planen är begränsad till 1,5 GB minne och en processor.  En instans av värden är hela Function-appen, vilket innebär att alla funktioner i en Function-resurs resurs i en instans och skalas på samma gång. Function-appar som delar samma förbruknings plan skala oberoende av varandra.  I Premium-planen fastställer plan storleken det tillgängliga minnet och CPU: n för alla appar i planen på den instansen.  

Funktions kod filen lagras på Azure Files resurser på funktionens huvud lagrings konto. När du tar bort huvud lagrings kontot för Function-appen tas funktions kod filerna bort och kan inte återställas.

## <a name="runtime-scaling"></a>Körnings skalning

Azure Functions använder en komponent som kallas för *skalnings styrenheten* för att övervaka händelse frekvensen och bestämma om du vill skala ut eller skala in. Skalnings styrenheten använder heuristik för varje utlösnings typ. Om du till exempel använder en Azure Queue Storage-utlösare, skalas den baserat på köns längd och ålder för det äldsta Queue meddelandet.

Skalnings enheten för Azure Functions är Function-appen. När funktions programmet skalas ut allokeras ytterligare resurser för att köra flera instanser av den Azure Functions värden. I takt med att Compute demand minskas, tar skalnings kontrollen bort funktions värd instanser. Antalet instanser är slutligen "skalas" till noll när inga funktioner körs i en Function-app.

![Skala övervakning av kontroll händelser och skapa instanser](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Kall start

När din Function-app har varit inaktiv i ett antal minuter kan plattformen skala antalet instanser som din app körs ned till noll. Nästa begäran har lagt till en fördröjning för skalning från noll till en. Den här fördröjningen kallas _kallstart_. Antalet beroenden som krävs av din Function-app kan påverka kall start tiden. Kall start är ett problem för synkrona åtgärder, till exempel HTTP-utlösare som måste returnera ett svar. Om kall börjar påverka dina funktioner kan du överväga att köra i en Premium-plan eller i en dedikerad plan med inställningen **Always on** Enabled.   

## <a name="understanding-scaling-behaviors"></a>Förstå skalnings beteenden

Skalning kan variera beroende på ett antal faktorer och skala på olika sätt beroende på vilken utlösare och vilket språk som valts. Det finns några erna för skalnings beteenden som kan vara medvetna om:

* **Maximalt antal instanser:** En enda Function-app skalar bara ut till högst 200 instanser. En enskild instans kan bearbeta mer än ett meddelande eller en begäran i taget, så det finns ingen angiven gräns för antalet samtidiga körningar.  Du kan [Ange ett lägre värde](#limit-scale-out) för att begränsa skalningen efter behov.
* **Ny instans hastighet:** För HTTP-utlösare allokeras nya instanser, högst en gång per sekund. För icke-HTTP-utlösare allokeras nya instanser, högst en gång var 30: e sekund. Skalning är snabbare när du kör i en [Premium-plan](functions-premium-plan.md).
* **Skalnings effektivitet:** För Service Bus utlösare använder du _Hantera_ rättigheter för resurser för den mest effektiva skalningen. Med _avlyssnings_ rättigheter är skalning inte lika tillförlitligt eftersom köns längd inte kan användas för att informera om skalnings beslut. Mer information om hur du ställer in rättigheter i Service Bus åtkomst principer finns i [auktoriseringsprincipen för delad åtkomst](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). För Event Hub-utlösare, se [vägledningen för skalning](functions-bindings-event-hubs-trigger.md#scaling) i referens artikeln. 

## <a name="limit-scale-out"></a>Begränsa skala ut

Du kanske vill begränsa det maximala antalet instanser som en app använder för att skala ut.  Detta är vanligt för fall där en underordnad komponent som en databas har begränsat data flöde.  Som standard skalar förbruknings plan ut till så många som 200 instanser, och Premium plan-funktioner skalas ut till så många som 100-instanser.  Du kan ange ett lägre Max värde för en speciell app genom att ändra `functionAppScaleLimit` värdet.  `functionAppScaleLimit`Kan anges till eller vara `0` `null` obegränsad, eller ett giltigt värde mellan `1` och appens max.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Metod tips och mönster för skalbara appar

Det finns många aspekter av en Function-app som påverkar hur den skalas, inklusive värd konfiguration, körnings miljö och resurs effektivitet.  Mer information finns i [avsnittet om skalbarhet i artikeln om prestanda överväganden](functions-best-practices.md#scalability-best-practices). Du bör också vara medveten om hur anslutningar fungerar när din Function-app skalar. Mer information finns i [hantera anslutningar i Azure Functions](manage-connections.md).

Mer information om skalning i python och Node.js finns i [Azure Functions python Developer Guide-skalning och samtidighet](functions-reference-python.md#scaling-and-performance) och [Azure Functions Node.js utvecklare-guide – skalning och samtidighet](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Faktureringsmodell

Faktureringen för olika planer beskrivs i detalj på [sidan Azure Functions prissättning](https://azure.microsoft.com/pricing/details/functions/). Användningen sammanställs på Function-app-nivå och räknar bara den tid som funktions koden körs. Följande är enheter för fakturering:

* **Resursförbrukning i GB-sekunder (GB-s)**. Beräknad som en kombination av minnes storlek och körnings tid för alla funktioner i en Function-app. 
* **Körningar**. Räknas varje gången en funktion körs som svar på en händelse utlösare.

Användbara frågor och information om hur du förstår din förbruknings faktura finns [i vanliga frågor och svar om fakturering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Nästa steg

+ [Azure Functions värd alternativ](functions-scale.md)

