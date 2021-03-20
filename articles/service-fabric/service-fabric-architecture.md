---
title: Arkitektur för Azure Service Fabric
description: I den här artikeln beskrivs arkitekturen i Service Fabric, en distribuerad system plattform som används för att bygga skalbara, pålitliga och enkelt hanterade program för molnet.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76024705"
---
# <a name="service-fabric-architecture"></a>Arkitektur för Service Fabric

Service Fabric har skapats med lager under system. Med dessa under system kan du skriva program som är:

* Högt tillgänglighet
* Skalbarhet
* Hanterbara
* Testable

I följande diagram visas de stora del systemen i Service Fabric.

![Diagram över Service Fabric arkitektur](media/service-fabric-architecture/service-fabric-architecture.png)

I ett distribuerat system är möjligheten att på ett säkert sätt kommunicera mellan noder i ett kluster viktigt. I stack-basen finns transport del systemet, som ger säker kommunikation mellan noder. Ovanför transport del systemet finns del systemet Federation, som klustrar de olika noderna till en enda entitet (med namnet kluster) så att Service Fabric kan identifiera fel, utföra val av ledare och tillhandahålla konsekvent routning. Del systemet tillförlitlighet, som ligger ovanpå del systemet Federation, ansvarar för tillförlitligheten hos Service Fabric tjänster genom mekanismer som replikering, resurs hantering och redundans. Federations del systemet är också underordnat under systemet värd och aktivering, som hanterar livs cykeln för ett program på en enda nod. Del systemet hantering hanterar livs cykeln för program och tjänster. Under systemet för testning kan hjälpa programutvecklare att testa sina tjänster genom simulerade fel innan och efter att du har distribuerat program och tjänster till produktions miljöer. Service Fabric ger möjlighet att lösa tjänst platser via dess under system för kommunikation. Programprogrammerings modeller som exponeras för utvecklare ligger ovanpå dessa under system tillsammans med program modellen för att aktivera verktyg.

## <a name="transport-subsystem"></a>Transport del system

Transport under systemet implementerar en kommunikations kanal för en punkt-till-punkt-datagram. Den här kanalen används för kommunikation inom Service Fabric-kluster och kommunikation mellan Service Fabric-kluster och-klienter. Det stöder envägs-och fråge-och kommunikations mönster, som utgör grunden för att implementera broadcast och multicast i Federations skiktet. Transport del systemet skyddar kommunikationen med hjälp av X509-certifikat eller Windows-säkerhet. Det här del systemet används internt av Service Fabric och är inte direkt tillgängligt för utvecklare för programmering av program.

## <a name="federation-subsystem"></a>Federations del system

Om du vill ha en uppsättning noder i ett distribuerat system måste du ha en konsekvent vy av systemet. Del systemet Federation använder de kommunikations primitiver som tillhandahålls av transport del systemet och häftar ihop de olika noderna i ett enda enhetligt kluster som det kan bero på. Den innehåller de distributioner av distribuerade system som krävs av de andra under systemen – haveri identifiering, ledar val och konsekvent routning. Federations under systemet skapas ovanpå distribuerade hash-tabeller med ett 128-bitars token-utrymme. Under systemet skapar en ringbufferten över noderna, där varje nod i ringen allokeras en delmängd av ett token-utrymme för ägarskapet. Vid identifiering av identifierings problem använder lagret en operationell mekanism baserad på hjärter och skiljedom. Federations under systemet garanterar också genom invecklade anslutnings-och sändnings protokoll som bara en enda ägare av en token finns när som helst. Detta ger möjlighet till val av ledare och konsekventa vägvals garantier.

## <a name="reliability-subsystem"></a>Tillförlitlighets under system

Under systemet tillförlitlighet är en mekanism för att göra en Service Fabric tjänst hög tillgänglig genom att använda *replikerare*, *Redundanshanteraren* och *resurs utjämning*.

* Replikeraren säkerställer att tillstånds ändringar i den primära tjänst repliken replikeras automatiskt till sekundära repliker, vilket upprätthåller konsekvens mellan de primära och sekundära replikerna i en tjänst replik uppsättning. Replikeringen ansvarar för kvorumkonfigurationen mellan replikerna i replik uppsättningen. Den interagerar med enheten för växling vid fel för att hämta listan över åtgärder som ska replikeras och omkonfigurations agenten ger den konfigurationen av replik uppsättningen. Den konfigurationen anger vilka repliker som åtgärderna behöver replikeras till. Service Fabric tillhandahåller en standardreplikering som heter Fabric Replicator, som kan användas av programmerings modellens API för att göra tjänstens tillstånd hög tillgängligt och tillförlitligt.
* Redundanshanteraren säkerställer att belastningen distribueras automatiskt över tillgängliga noder när noder läggs till eller tas bort från klustret. Om en nod i klustret Miss lyckas, kommer klustret automatiskt att konfigurera om tjänst replikerna för att upprätthålla tillgänglighet.
* Resource Manager placerar tjänst repliker över fel domäner i klustret och säkerställer att alla enheter för växling vid fel fungerar. Resource Manager balanserar också tjänst resurser över den underliggande delade poolen av klusternoder för att uppnå optimal enhetlig belastnings fördelning.

## <a name="management-subsystem"></a>Del system för hantering

Under systemet för hantering tillhandahåller hantering av livs cykeln för tjänst och program från slut punkt till slut punkt. Med PowerShell-cmdletar och administrations-API: er kan du etablera, distribuera, uppdatera, uppgradera och avetablera program utan tillgänglighet. Under systemet för hantering utför detta genom följande tjänster.

* **Kluster hanteraren**: det här är den primära tjänsten som samverkar med Redundanshanteraren från tillförlitlighet för att placera programmen på noderna baserat på tjänst placerings begränsningarna. Resurs hanteraren i under systemet för redundans garanterar att begränsningarna aldrig bryts. Kluster hanteraren hanterar livs cykeln för programmen från etablering till avetablering. Den integreras med hälso chefen för att säkerställa att program tillgänglighet inte går förlorat från ett semantiskt hälso perspektiv under uppgraderingar.
* **Hälso tillstånds hanteraren**: den här tjänsten möjliggör hälso övervakning av program, tjänster och kluster enheter. Kluster enheter (till exempel noder, diskpartitioner och repliker) kan rapportera hälso information, som sedan aggregeras i det centraliserade hälso lagret. Den här hälso informationen ger en övergripande hälso status ögonblicks bild av tjänsterna och noderna som distribueras över flera noder i klustret, så att du kan vidta nödvändiga åtgärder. Med API: er för hälso frågor kan du fråga hälso händelser som rapporter ATS till hälso under systemet. API: erna för hälso frågor returnerar rå hälso data som lagras i hälso lagret eller aggregerade, tolkade hälso data för en speciell kluster enhet.
* **Avbildningsarkiv**: den här tjänsten tillhandahåller lagring och distribution av programmets binärfiler. Den här tjänsten tillhandahåller ett enkelt distribuerat fillager där programmen laddas upp och hämtas från.

## <a name="hosting-subsystem"></a>Värd del system

Kluster hanteraren informerar värd under systemet (körs på varje nod) som de tjänster som krävs för att hantera en viss nod. Värd under systemet hanterar sedan livs cykeln för programmet på noden. Den samverkar med Tillförlitlighets-och hälso komponenterna för att säkerställa att replikerna placeras korrekt och är felfria.

## <a name="communication-subsystem"></a>Under system för kommunikation

Det här under systemet ger Reliable Messaging i kluster-och tjänst identifiering via namngivnings tjänsten. Namngivnings tjänsten matchar tjänst namn till en plats i klustret och gör det möjligt för användare att hantera tjänst namn och egenskaper. Med hjälp av namngivnings tjänsten kan klienter på ett säkert sätt kommunicera med valfri nod i klustret för att matcha ett tjänst namn och hämta metadata för tjänsten. Med ett enkelt namngivnings klient-API kan användare av Service Fabric utveckla tjänster och klienter som kan lösa den aktuella nätverks platsen trots att Node dynamism eller omstorleks ändringen av klustret.

## <a name="testability-subsystem"></a>Under system för testhet

Testning är en uppsättning verktyg som är särskilt utformade för test tjänster som bygger på Service Fabric. Med verktygen kan en utvecklare enkelt inducera meningsfulla fel och köra test scenarier för att genomföra och validera de många tillstånd och över gångar som en tjänst kommer att uppleva under hela sin livs längd, på ett kontrollerat och säkert sätt. Tester är också en mekanism för att köra längre tester som kan iterera genom olika möjliga orsaker utan att det går att förlora tillgänglighet. Det ger dig en test-i-produktions-miljö.
