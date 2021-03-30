---
title: Azure Functions geo-haveri beredskap och hög tillgänglighet
description: Hur du använder geografiska regioner för redundans och för att redundansväxla i Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86506289"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geo-haveri beredskap

När hela Azure-regioner eller data Center upplever drift stopp är det viktigt att data bearbetningen fortsätter i en annan region.  I den här artikeln förklaras några av de strategier som du kan använda för att distribuera funktioner som möjliggör katastrof återställning.

## <a name="basic-concepts"></a>Grundläggande begrepp

Azure Functions köras i en angiven region.  Om du vill få högre tillgänglighet kan du distribuera samma funktioner till flera regioner.  När i flera regioner kan du köra dina funktioner i det *aktiva/aktiva* mönstret eller *aktivt/passivt* mönster.  

* Aktiv/aktiv. Båda regionerna är aktiva och tar emot händelser (dubbletter eller rotering). Active/Active rekommenderas för HTTPS-funktioner i kombination med Azures frontend-dörr.
* Aktiv/passiv. En region är aktiv och tar emot händelser, medan en sekundär är inaktiv.  När redundans krävs aktive ras den sekundära regionen och bearbetningen sker.  Detta rekommenderas för icke-HTTP-funktioner som Service Bus och Event Hubs.

Läs hur du [Kör appar i flera regioner](/azure/architecture/reference-architectures/app-service-web-app/multi-region) för mer information om distributioner i flera regioner.

## <a name="activeactive-for-https-functions"></a>Aktiva/aktiva för HTTPS-funktioner

För att kunna uppnå aktiva/aktiva distributioner av funktioner krävs en del komponenter som kan koordinera händelserna mellan båda regionerna.  För HTTPS-funktioner utförs denna samordning med [Azures frontend-dörr](../frontdoor/front-door-overview.md).  Azures front dörr kan dirigera och avrunda HTTPS-begäranden mellan flera regionala funktioner.  Det kontrollerar också regelbundet hälso tillståndet för varje slut punkt.  Om en regional funktion slutar svara på hälso kontroller tar Azure-hemdörren bort den från rotationen och vidarebefordrar bara trafik till friska funktioner.  

![Arkitektur för Azures främre dörr och funktion](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktiva/aktiva för icke-HTTPS-funktioner

Du kan fortfarande nå aktiva/aktiva distributioner för icke-HTTPS-funktioner.  Du måste dock överväga hur de två regionerna ska interagera eller koordineras med varandra.  Om du har distribuerat samma Function-app i två regioner, som varje utlöses i samma Service Bus kö, agerar de som konkurrerande konsumenter om att köa kön.  Det innebär att varje meddelande endast bearbetas av en av instanserna, men det innebär också att det fortfarande finns en enskild felpunkt på den enskilda Service Bus.  Om du distribuerar två Service Bus köer (en i en primär region, en i en sekundär region) och de två Function-appar som pekas på sin regions Queue, kommer utmaningen nu att visas i hur köa meddelanden distribueras mellan de två regionerna.  Det innebär ofta att varje utgivare försöker publicera ett meddelande till *båda* regionerna och varje meddelande bearbetas av båda aktiva funktions appar.  Även om detta skapar ett aktivt/aktivt mönster, skapar den andra utmaningar kring duplicering av beräkning och när eller hur data konsol IDE ras.  Av dessa skäl rekommenderar vi att icke-HTTPS-utlösare använder det aktiva/passiva mönstret.

## <a name="activepassive-for-non-https-functions"></a>Aktiva/passiva för icke-HTTPS-funktioner

Active/passivt är ett sätt för endast en enda funktion att bearbeta varje meddelande, men tillhandahåller en mekanism för att redundansväxla till en sekundär region i händelse av en katastrof.  Azure Functions fungerar tillsammans med [Azure Service Bus geo-återställning](../service-bus-messaging/service-bus-geo-dr.md) och [Azure Event Hubs geo-återställning](../event-hubs/event-hubs-geo-dr.md).

Med hjälp av Azure Event Hubs-utlösare som exempel skulle det aktiva/passiva mönstret omfatta följande:

* Azure Event Hub distribueras till både en primär och en sekundär region.
* Geo-katastrof aktiverat för att koppla den primära och sekundära Händelsehubben.  Detta skapar också ett "alias" som du kan använda för att ansluta till händelse hubbar och växla från primär till sekundär utan att ändra anslutnings informationen.
* Function-appar som distribueras till både en primär och en sekundär region.
* Function-apparna utlöses på den *direkta* (icke-alias) anslutnings strängen för dess respektive Event Hub. 
* Utgivare till händelsehubben ska publicera till aliasets anslutnings sträng. 

![Arkitektur för aktiv-passiv exempel](media/functions-geo-dr/active-passive.png)

Före redundans dirigeras utgivare som skickar till det delade aliaset till den primära händelsehubben.  Den primära Function-appen lyssnar uteslutande till den primära händelsehubben.  Den sekundära Function-appen kommer att vara passiv och inaktiv.  Så snart redundansväxlingen initieras kommer utgivare som skickar till det delade aliaset att dirigeras till den sekundära händelsehubben.  Den sekundära Function-appen aktive ras nu som aktiv och börjar utlösas automatiskt.  Effektiv redundans till en sekundär region kan köras helt från händelsehubben, och funktionerna blir bara aktiva när respektive händelsehubben är aktiv.

Läs mer om information och överväganden för redundans med [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) och [Event Hub](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa Azure-front dörr](../frontdoor/quickstart-create-front-door.md)
* [Överväganden vid Event Hubs redundans](../event-hubs/event-hubs-geo-dr.md#considerations)
