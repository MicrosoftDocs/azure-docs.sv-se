---
title: Prestanda övervakning av Azure Service Fabric
description: Lär dig mer om prestanda räknare för övervakning och diagnostik av Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: abe1b67bee863f287f148fdd9af13198b67db0f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628874"
---
# <a name="performance-metrics"></a>Prestandamått

Mått ska samlas in för att förstå klustrets prestanda samt de program som körs i det. För Service Fabric kluster rekommenderar vi att du samlar in följande prestanda räknare.

## <a name="nodes"></a>Noder

För datorerna i klustret kan du överväga att samla in följande prestanda räknare för att bättre förstå belastningen på varje dator och fatta lämpliga beslut om kluster skalning.

| Räknar kategori | Räknarens namn |
| --- | --- |
| Logisk disk | Ledigt utrymme på logisk disk |
| Fysisk disk (per disk) | Genomsnittlig längd på disk läsnings kön |
| Fysisk disk (per disk) | Genomsnittlig längd på disk skrivnings kön |
| Fysisk disk (per disk) | Medel s/disk läsning |
| Fysisk disk (per disk) | Medel s/disk skrivning |
| Fysisk disk (per disk) | Disk läsningar/SEK |
| Fysisk disk (per disk) | Disk-lästa byte/s |
| Fysisk disk (per disk) | Disk skrivningar/SEK |
| Fysisk disk (per disk) | Disk-skrivna byte/s |
| Minne | Tillgängliga megabyte |
| PagingFile | Användning av% |
| Processor (totalt) | % processortid |
| Process (per tjänst) | % processortid |
| Process (per tjänst) | Process-ID |
| Process (per tjänst) | Privata byte |
| Process (per tjänst) | Antal trådar |
| Process (per tjänst) | Virtuella byte |
| Process (per tjänst) | Sidmängd |
| Process (per tjänst) | Arbets minne – privat |
| Nätverks gränssnitt (alla instanser) | Byte recd |
| Nätverks gränssnitt (alla instanser) | Skickade byte |
| Nätverks gränssnitt (alla instanser) | Totalt antal byte |
| Nätverks gränssnitt (alla instanser) | Kölängd för utdata |
| Nätverks gränssnitt (alla instanser) | Utgående paket som ignorerats |
| Nätverks gränssnitt (alla instanser) | Mottagna paket som tagits bort |
| Nätverks gränssnitt (alla instanser) | Utgående paket fel |
| Nätverks gränssnitt (alla instanser) | Mottagna paket-fel |

## <a name="net-applications-and-services"></a>.NET-program och-tjänster

Samla in följande räknare om du distribuerar .NET-tjänster till klustret. 

| Räknar kategori | Räknarens namn |
| --- | --- |
| .NET CLR-minne (per tjänst) | Process-ID |
| .NET CLR-minne (per tjänst) | Totalt antal allokerade byte |
| .NET CLR-minne (per tjänst) | Totalt antal reserverade byte |
| .NET CLR-minne (per tjänst) | Antal byte i alla heapar |
| .NET CLR-minne (per tjänst) | Heap-storlek för stora objekt |
| .NET CLR-minne (per tjänst) | Antal GC-referenser |
| .NET CLR-minne (per tjänst) | # Generation 0-samlingar |
| .NET CLR-minne (per tjänst) | # Gen 1-samlingar |
| .NET CLR-minne (per tjänst) | # Gen 2-samlingar |
| .NET CLR-minne (per tjänst) | Tid i GC i procent |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric anpassade prestanda räknare

Service Fabric genererar en betydande mängd anpassade prestanda räknare. Om du har installerat SDK kan du se den omfattande listan på din Windows-dator i prestanda övervaknings programmet (starta > prestanda övervakaren). 

I de program som du distribuerar till klustret, om du använder Reliable Actors, lägger du till räknare från `Service Fabric Actor` och `Service Fabric Actor Method` kategorier (se [Service Fabric Reliable Actors diagnostik](service-fabric-reliable-actors-diagnostics.md)).

Om du använder Reliable Services-eller tjänstens fjärrstyrning har vi samma `Service Fabric Service` och `Service Fabric Service Method` räknar kategorier som du bör samla in räknare från, se [övervakning med service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md) och [Reliable Services-prestandaräknare](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Om du använder pålitliga samlingar rekommenderar vi att du lägger till `Avg. Transaction ms/Commit` från i `Service Fabric Transactional Replicator` för att samla in svars tiden per transaktions mått.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att generera händelser på plattforms nivå](service-fabric-diagnostics-event-generation-infra.md) i Service Fabric
* Samla in prestanda mått via [Log Analytics agent](service-fabric-diagnostics-oms-agent.md)
