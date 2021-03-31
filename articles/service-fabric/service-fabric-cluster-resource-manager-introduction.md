---
title: Introduktion till Service Fabric Cluster Resource Manager
description: Lär dig mer om Service Fabric Cluster Resource Manager, ett sätt att hantera dirigering av programmets tjänster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75563334"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduktion till Service Fabric Cluster Resource Manager
Traditionellt hantera IT-system eller onlinetjänster avsedda att tilldela fysiska eller virtuella datorer till dessa tjänster eller system. Tjänsterna har konstruerats som nivåer. Det skulle finnas en "webb"-nivå och en "data-eller" lagrings "-nivå. Program kan ha en meddelande nivå där förfrågningar flödade in och ut, samt en uppsättning datorer avsedda för cachelagring. Varje nivå eller typ av arbets belastning hade särskilda datorer som är reserverade för IT: databasen fick ett par datorer som är reserverade för den, webb servrarna några. Om en viss typ av arbets belastning gjorde att de datorer som den var på att köras för frekvent, lade du till fler datorer med samma konfiguration på den nivån. Dock kan inte alla arbets belastningar skalas ut så enkelt, särskilt med data nivån som du normalt ersätter datorer med större datorer. Enkelt. Om en dator Miss lyckas kördes den delen av det övergripande programmet med lägre kapacitet tills datorn kunde återställas. Ganska lätt (om det inte nödvändigt vis blir roligt).

Nu har hela världen över tjänsten och program varu arkitekturen ändrats. Det är vanligare att programmen har infört en skalbar design. Att skapa program med behållare eller mikrotjänster (eller båda) är vanligt. Nu kan du fortfarande bara ha några få datorer, utan bara att köra en enda instans av en arbets belastning. De kan till och med köra flera olika arbets belastningar samtidigt. Nu har du dussin tals olika typer av tjänster (ingen förbrukar en fullständig dators resurser), kanske hundratals olika instanser av dessa tjänster. Varje namngiven instans har en eller flera instanser eller repliker för hög tillgänglighet (HA). Beroende på storleken på dessa arbets belastningar och hur upptagen de är, kan du hitta dig med hundratals eller tusentals datorer. 

Att hantera din miljö plötsligt är inte så enkelt som att hantera några datorer som är dedikerade till enskilda typer av arbets belastningar. Dina servrar är virtuella och har inte längre några namn (du har bytt mindsets från [hus djur till boskap](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) efter alla). Konfigurationen är mindre om datorerna och mer om själva tjänsterna. Maskin vara som är dedikerad till en enda instans av en arbets belastning är i stort sett en sak av den senaste. Själva tjänsterna har blivit små distribuerade system som omfattar flera mindre delar av råvaru maskin vara.

Eftersom din app inte längre är en serie Monoliths uppslag på flera nivåer har du nu många fler kombinationer för att hantera. Vem bestämmer vilka typer av arbets belastningar som kan köras på vilken maskin vara eller hur många? Vilka arbets belastningar fungerar bra på samma maskin vara och vilken konflikt? Hur vet du vad som kördes där på datorn? Vem är det som ansvarar för att se till att arbets belastningen börjar köras igen? Väntar du på att datorn (Virtual?) ska komma tillbaka eller att arbets belastningarna automatiskt växlar över till andra datorer och fortsätta att köra? Krävs mänsklig åtgärd? Vad händer om uppgraderingar i den här miljön?

Som utvecklare och operatörer som hanterar i den här miljön kommer vi att vilja hjälpa att hantera den här komplexiteten. Att anlita Bing och försöka dölja komplexiteten med människor är förmodligen inte rätt svar, så vad gör vi?

## <a name="introducing-orchestrators"></a>Introduktion till Orchestrations
En "Orchestrator" är den allmänna termen för en program vara som hjälper administratörer att hantera dessa typer av miljöer. Dirigerare är de komponenter som tar i begär Anden som "Jag vill att fem kopior av den här tjänsten körs i min miljö." De försöker göra så att miljön matchar det önskade läget, oavsett vad som händer.

Dirigerare (inte människor) är vad som händer när en dator kraschar eller om en arbets belastning avslutas av en oväntad orsak. De flesta Dirigerare gör mer än att bara hantera problem. Andra funktioner som de har, hanterar nya distributioner, hanterar uppgraderingar och hanterar resursförbrukning och styrning. Alla dirigeringar är grundläggande om att underhålla ett visst konfigurations tillstånd i miljön. Du vill kunna tala om för en Orchestrator som du vill ha och att den gör den tunga lyftet. Aurora ovanpå Mesos, Docker Data Center/Docker Swarm, Kubernetes och Service Fabric är exempel på dirigerings verktyg. De här dirigeringarna utvecklas aktivt för att uppfylla behoven hos verkliga arbets belastningar i produktions miljöer. 

## <a name="orchestration-as-a-service"></a>Dirigering som en tjänst
Kluster resurs hanteraren är den system komponent som hanterar dirigering i Service Fabric. Kluster resurs hanterarens jobb är uppdelat i tre delar:

1. Verkställa regler
2. Optimera din miljö
3. Hjälp med andra processer

### <a name="what-it-isnt"></a>Vad det inte är
I traditionella N-nivå program finns det alltid en [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Detta var vanligt vis en nätverks Load Balancer (NLB) eller ett program Load Balancer (ALB) beroende på var den är i nätverks stacken. Vissa belastningsutjämnare är maskinvarubaserade som F5's BigIP-erbjudande, medan andra är program varubaserade som Microsofts utjämning av nätverks belastning. I andra miljöer kan du se något som liknar HAProxy, nginx, Istio eller mottagare i den här rollen. I dessa arkitekturer är jobbet av belastnings utjämning att se till att tillstånds lösa arbets belastningar tar emot (ungefär) samma mängd arbete. Strategier för att balansera belastningen varierade. Vissa saldon skulle skicka varje annat anrop till en annan server. Andra tillhandahöll/varaktighet för session. Mer avancerade saldon använder faktisk belastnings uppskattning eller rapportering för att dirigera ett anrop baserat på den förväntade kostnaden och den aktuella dator belastningen.

Utjämning av nätverks belastning eller Message-routrar försökte se till att webb-/arbetar nivån var någorlunda bal anse rad. Strategier för att balansera data nivån skiljer sig åt och var beroende av data lagrings funktionen. Balansera data skiktet som förlitar sig på data horisontell partitionering, cachelagring, hanterade vyer, lagrade procedurer och andra lagrings bara mekanismer.

Vissa av dessa strategier är intressanta, men Service Fabric Cluster Resource Manager är inte något som en utjämning av nätverks belastning eller en cache. En nätverks Load Balancer balanserar frontend-enheter genom att sprida trafik mellan klient delar. Service Fabric Cluster Resource Manager har en annan strategi. I grunden Service Fabric flyttar *tjänster* till den plats där de är mest begripliga, förväntar sig trafik eller belastning att följa. Det kan till exempel gå att flytta tjänster till noder som är för närvarande kall eftersom de tjänster som inte fungerar mycket fungerar. Noderna kan vara kalla eftersom de tjänster som var närvarande har tagits bort eller flyttats någon annan stans. Som ett annat exempel kan kluster resurs hanteraren också flytta en tjänst från en dator. Datorn kanske håller på att uppgraderas eller överbelastas på grund av en ökning i förbrukningen av de tjänster som körs på den. Alternativt kan tjänstens resurs krav ha ökat. Därför finns det inte tillräckligt med resurser på den här datorn för att fortsätta att köra den. 

Eftersom kluster resurs hanteraren ansvarar för att flytta tjänster runt, innehåller den en annan funktions uppsättning jämfört med vad du skulle hitta i en utjämning av nätverks belastning. Detta beror på att utjämning av nätverks belastning levererar nätverks trafik till de tjänster som redan är, även om den platsen inte är idealisk för att köra själva tjänsten. Service Fabric Cluster Resource Manager använder grundläggande olika strategier för att säkerställa att resurserna i klustret används effektivt.

## <a name="next-steps"></a>Nästa steg
- Mer information om arkitekturen och informations flödet i kluster resurs hanteraren finns i [den här artikeln](service-fabric-cluster-resource-manager-architecture.md)
- Kluster resurs hanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om mått kan du läsa artikeln om hur du [beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mer information om hur du konfigurerar tjänster finns i [så här konfigurerar du tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Mått är hur Service Fabric Cluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem finns i [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Kluster resurs hanteraren fungerar med Service Fabric hanterings funktioner. Läs [den här artikeln](service-fabric-cluster-resource-manager-management-integration.md) om du vill veta mer om denna integrering
- Om du vill veta mer om hur kluster resurs hanteraren hanterar och balanserar belastningen i klustret kan du läsa artikeln om [balansering av belastning](service-fabric-cluster-resource-manager-balancing.md)
