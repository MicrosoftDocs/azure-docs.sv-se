---
title: Koncept – hållbart program varu teknik i Azure Kubernetes Services (AKS)
description: Lär dig mer om hållbara program varu tekniker i Azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 700723041855fdae4f994480d180292fdfd12e15
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346902"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Hållbara principer för program varu teknik i Azure Kubernetes service (AKS)

De hållbara principerna för program varu teknik är en uppsättning kompetenser som hjälper dig att definiera, bygga och köra hållbara program. Det övergripande målet är att minska ditt kol avtryck för varje aspekt av ditt program. [Principerna för en hållbar program varu teknik][principles-sse] har en översikt över principerna för en hållbar program varu teknik.

En viktig idé att förstå om hållbar program varu teknik är att det är en förändring i prioriteringar och fokus. I många fall är program vara utformat och körs på ett sätt som fokuserar på snabba prestanda och låg latens. En hållbar program varu teknik fokuserar på att minska så mycket kol utsläpp som möjligt. I vissa fall kan du få snabbare prestanda eller lägre svars tid genom att tillämpa hållbara principer för program varu teknik, till exempel genom att sänka den totala nätverks resan. I andra fall kan minskning av kol utsläpp orsaka sämre prestanda eller ökad latens, till exempel att fördröja arbets belastningar med låg prioritet. Innan du överväger att använda hållbara policys för program varu teknik för ditt program bör du granska prioriteringar, behov och program varor.

## <a name="measure-and-optimize"></a>Mät och optimera

Om du vill minska AKS-klusterens kol avtryck måste du förstå hur klustrets resurser används. [Azure Monitor][azure-monitor] innehåller information om klustrets resursanvändning, till exempel minne och CPU-användning. Dessa data kan hjälpa dig att informera dina beslut om att minska ditt klusters utmatnings utrymme och observera effekterna av ändringarna. Du kan också installera [Microsofts hållbarhets kalkylator][sustainability-calculator] för att se alla dina Azure-resursers kol avtryck.

## <a name="increase-resource-utilization"></a>Öka resursutnyttjande

En metod för att sänka ditt kol avtryck är att minska mängden inaktivitetstid för dina beräknings resurser. Att minska tiden för inaktivitet innebär att du ökar användningen av dina beräknings resurser. Om du till exempel har fyra noder i klustret, var och en som körs med 50% kapacitet, har alla fyra noderna 50% oanvänd kapacitet kvar. Om du har minskat klustret till tre noder skulle samma arbets belastning orsaka att dina tre noder körs vid 67% kapacitet, vilket minskar din outnyttjade kapacitet till 33% på varje nod och ökar din användning.

> [!IMPORTANT]
> När du överväger att göra ändringar i resurserna i klustret bör du kontrol lera att dina [systempooler][system-pools] har tillräckligt med resurser för att upprätthålla stabiliteten för kärn systemets komponenter i klustret. Minska aldrig klustrets resurser till den plats där klustret kan bli instabilt.

När du har granskat klustrets användning kan du överväga att använda de funktioner som erbjuds av [flera noder i pooler][multiple-node-pools]. Du kan använda [Node-storlek][node-sizing] för att definiera resurspooler med vissa CPU-och minnes profiler, så att du kan skräddarsy dina noder efter dina arbets belastnings behov. Att ändra storlek på dina noder till dina arbets belastnings behov kan hjälpa dig att köra få noder vid högre användning. Du kan också konfigurera hur klustret [skalas][scale] och använda det [vågräta Pod autoskalning][scale-horizontal] och [klustret autoskalning][scale-auto] för att skala klustret automatiskt baserat på din konfiguration. Genom att kontrol lera hur klustrets skalning kan hjälpa dig att hålla alla noder igång med hög användning samtidigt som du håller på att ändra klustrets arbets belastning. I de fall där en arbets belastning är tolerant till plötsliga avbrott eller uppsägningar kan du använda [lagringspooler][spot-pools] för att dra nytta av den inaktiva kapaciteten i Azure. Till exempel kan dekor pooler fungera bra för batch-jobb eller utvecklings miljöer.

Att öka användningen kan också minska överskotts noder, vilket minskar den energi som används av [resurs reservationer på varje nod][resource-reservations].

Granska även processor-och minnes *förfrågningar* och *begränsningar* i Kubernetes-manifesten för dina program. När du sänker dessa värden för minne och CPU är mer minne och CPU tillgängligt för klustret för att köra andra arbets belastningar. När du kör fler arbets belastningar med lägre processor och minne blir klustret mer tätt allokerat vilket ökar din användning. När du minskar CPU och minne för dina program kan beteendet för dina program bli försämrat eller instabilt om du anger dessa värden för lågt. Innan du ändrar processor-och minnes *förfrågningar* och- *gränser* bör du överväga att köra vissa prestandatester för att förstå om dessa värden är korrekt inställda. Dessutom minskar aldrig värdena till punkten när programmet blir instabilt.

## <a name="reduce-network-travel"></a>Minska nätverks resor

Att minska distans begär Anden och svar till och från klustret måste färdas i allmänhet minska El förbrukningen med nätverks enheter och minska utsläppen av kol. När du har granskat din nätverks trafik bör du överväga att skapa kluster [i regioner][regions] närmare källan till nätverks trafiken. Du kan också använda [Azure-Traffic Manager][azure-traffic-manager] för att dirigera trafik till närmaste [placerings grupper][proiximity-placement-groups] för kluster och närhet för att minska avståndet mellan Azure-resurser.

> [!IMPORTANT]
> När du funderar på att göra ändringar i klustrets nätverk minskar du aldrig nätverks resor till kostnaden för att uppfylla arbets belastnings krav. Med hjälp av [tillgänglighets zoner][availability-zones] kan du till exempel få mer nätverks trafik i klustret, men att använda funktionen kan vara nödvändig för att hantera arbets belastnings krav.

## <a name="demand-shaping"></a>Utformning av begäran

Om möjligt bör du överväga att byta efter frågan på klustrets resurser till tider eller regioner där du kan använda överflödig kapacitet. Överväg till exempel att ändra tid eller region för ett batch-jobb som ska köras eller använda [lagringspooler][spot-pools]. Överväg också att omväga ditt program för att använda en kö för att skjuta upp aktiva arbets belastningar som inte behöver omedelbar bearbetning.

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
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
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