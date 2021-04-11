---
title: Koncept – hållbart program varu teknik i Azure Kubernetes Services (AKS)
description: Lär dig mer om hållbara program varu tekniker i Azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011499"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Hållbara principer för program varu teknik i Azure Kubernetes service (AKS)

De hållbara principerna för program varu teknik är en uppsättning kompetenser som hjälper dig att definiera, bygga och köra hållbara program. Det övergripande målet är att minska ditt kol avtryck för varje aspekt av ditt program. [Principerna för en hållbar program varu teknik][principles-sse] har en översikt över principerna för en hållbar program varu teknik.

En hållbar program varu teknik är en förändring i prioriteringar och fokus. I många fall är det bättre att använda de flesta program som är utformade och körs, vilket innebär snabba prestanda och låg latens. Samtidigt fokuserar en hållbar program varu teknik på att minska så mycket kol utsläpp som möjligt. Tänk på att:

* Att tillämpa hållbara principer för program varu teknik kan ge dig snabbare prestanda eller lägre latens, till exempel genom att sänka den totala nätverks resan. 
* Minskning av kol utsläpp kan orsaka sämre prestanda eller ökad latens, till exempel att fördröja arbets belastningar med låg prioritet. 

Innan du tillämpar hållbara policys för program varu teknik för ditt program bör du granska prioriteringar, behov och program varu program.

## <a name="measure-and-optimize"></a>Mät och optimera

Om du vill minska AKS-klusterens kol avtryck måste du förstå hur klustrets resurser används. [Azure Monitor][azure-monitor] innehåller information om klustrets resursanvändning, till exempel minne och CPU-användning. Den här informationen informerar ditt beslut om att minska ditt klusters utmatnings utrymme och observera effekterna av ändringarna. 

Du kan också installera [Microsofts hållbarhets kalkylator][sustainability-calculator] för att se alla dina Azure-resursers kol avtryck.

## <a name="increase-resource-utilization"></a>Öka resursutnyttjande

En metod för att sänka ditt kol avtryck är att minska tiden för inaktivitet. Att minska tiden för inaktivitet innebär att du ökar användningen av dina beräknings resurser. Exempel:
1. Du hade fyra noder i klustret, som var och en körs med 50% kapacitet. Därför har alla fyra noderna 50% oanvänd kapacitet kvar. 
1. Du minskade klustret till tre noder, som körs på 67% kapacitet med samma arbets belastning. Du skulle ha minskat din outnyttjade kapacitet till 33% på varje nod och ökade din användning.

> [!IMPORTANT]
> När du överväger att ändra resurserna i klustret bör du kontrol lera att dina [systempooler][system-pools] har tillräckligt med resurser för att bibehålla stabiliteten för klustrets kärn system komponenter. Minska **aldrig** klustrets resurser till den plats där klustret kan bli instabilt.

När du har granskat klustrets användning kan du överväga att använda de funktioner som erbjuds av [flera noder][multiple-node-pools]i en pool: 

* Storlek på nod

    Använd [Node-storlek][node-sizing] för att definiera resurspooler med vissa CPU-och minnes profiler, så att du kan skräddarsy dina noder efter dina arbets belastnings behov. Genom att ändra storlek på noderna till dina arbets belastnings behov kan du köra några noder vid högre användning. 

* Klusterskalning

    Konfigurera hur klustret [skalas][scale]. Använd den [vågräta Pod autoskalning][scale-horizontal] och [klustrets autoskalning][scale-auto] för att skala klustret automatiskt baserat på din konfiguration. Styr hur klustret skalas för att hålla alla noder som körs vid hög användning samtidigt som de är synkroniserade med ändringar i klustrets arbets belastning. 

* Lagringspooler

    I de fall där en arbets belastning är tolerant till plötsliga avbrott eller uppsägningar kan du använda [lagringspooler][spot-pools]. Lagringspooler drar nytta av inaktive ras kapacitet i Azure. Till exempel kan dekor pooler fungera bra för batch-jobb eller utvecklings miljöer.

> [!NOTE]
>Att öka användningen kan också minska överskotts noder, vilket minskar den energi som används av [resurs reservationer på varje nod][resource-reservations].

Granska slutligen processor-och minnes *förfrågningar* och *begränsningar* i Kubernetes-manifesten för dina program. 
* När du sänker minne och CPU-värden är mer minne och CPU tillgängligt för klustret för att köra andra arbets belastningar. 
* När du kör fler arbets belastningar med lägre CPU och minne blir klustret mer tätt tilldelat, vilket ökar din användning. 

När du minskar CPU och minne för dina program kan dina programs beteende bli försämrade eller instabila om du anger värden för processor och minne för lågt. Innan du ändrar processor-och minnes *förfrågningar* och- *gränser* kan du köra vissa prestandatester för att kontrol lera att värdena är korrekt inställda. Minska aldrig värdena till punkten för application instabilitet.

## <a name="reduce-network-travel"></a>Minska nätverks resor

Genom att minska de begär Anden och svarnas avstånd till och från klustret kan du minska koldioxid utsläppen och elektricitets förbrukningen från nätverks enheter. När du har granskat din nätverks trafik bör du överväga att skapa kluster [i regioner][regions] närmare källan till nätverks trafiken. Du kan använda [azure Traffic Manager][azure-traffic-manager] för att dirigera trafik till närmaste [placerings grupper][proiximity-placement-groups] för kluster och närhet och minska avståndet mellan Azure-resurser.

> [!IMPORTANT]
> När du funderar på att göra ändringar i klustrets nätverk minskar du aldrig nätverks resor till kostnaden för att uppfylla arbets belastnings krav. När till exempel en [tillgänglighets zon][availability-zones] används orsakar fler nätverks resor i klustret kan tillgänglighets zoner vara nödvändiga för att hantera arbets belastnings krav.

## <a name="demand-shaping"></a>Utformning av begäran

Om möjligt bör du överväga att byta efter frågan på klustrets resurser till tider eller regioner där du kan använda överflödig kapacitet. Tänk till exempel:
* Ändra tid eller region för ett batch-jobb som ska köras.
* Använda [lagringspooler][spot-pools]. 
* Omstrukturering av programmet för att använda en kö för att skjuta upp aktiva arbets belastningar som inte behöver omedelbar bearbetning.

## <a name="next-steps"></a>Nästa steg

Läs mer om funktionerna i AKS som nämns i den här artikeln:

* [Flera noder i pooler][multiple-node-pools]
* [Storlek på nod][node-sizing]
* [Skala ett kluster][scale]
* [Horisontell autoskalning av poddar][scale-horizontal]
* [Autoskalning av kluster][scale-auto]
* [Lagringspooler][spot-pools]
* [System pooler][system-pools]
* [Resurs reservationer][resource-reservations]
* [Närhetsplaceringsgrupper][proiximity-placement-groups]
* [Tillgänglighetszoner][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/