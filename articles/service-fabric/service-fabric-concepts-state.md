---
title: Hantera tillstånd i Azure Service Fabric Services
description: Läs mer om tillstånd i Azure Service Fabric, inklusive hur du definierar och hanterar tjänst tillstånd i Service Fabric Services.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614578"
---
# <a name="service-state"></a>Tjänsttillstånd
**Tjänst status** avser de indata-eller disk data som en tjänst behöver för att fungera. Den inkluderar till exempel de data strukturer och medlems variabler som tjänsten läser och skriver för att utföra arbetet. Beroende på hur tjänsten är konstruerad kan den även innehålla filer eller andra resurser som lagras på disk. Till exempel används filerna som en databas för att lagra data och transaktions loggar.

Som exempel tjänst ska vi överväga en kalkylator. En grundläggande beräknings tjänst tar två siffror och Returnerar summan. Att utföra den här beräkningen innebär inte några medlemsvariabler eller annan information.

Överväg att använda samma kalkylator, men med ytterligare en metod för att lagra och returnera den sista summan som har beräknats. Den här tjänsten är nu tillstånds känslig. Tillstånds känslig innebär att den innehåller ett tillstånd som den skriver till när den beräknar en ny summa och läser från när du ber den att returnera den senaste beräknade summan.

I Azure Service Fabric kallas den första tjänsten för en tillstånds lös tjänst. Den andra tjänsten kallas för en tillstånds känslig tjänst.

## <a name="storing-service-state"></a>Lagra tjänst tillstånd
Status kan antingen vara extern eller samordnad med den kod som ändrar tillstånd. Externalization utförs vanligt vis med hjälp av en extern databas eller annat data lager som körs på olika datorer över nätverket eller utanför processen på samma dator. I vårt kalkylator exempel kan data lagret vara en SQL-databas eller en instans av Azure Table Store. Varje begäran om att beräkna summan utför en uppdatering av dessa data och begär anden till tjänsten för att returnera värdet resulterar i att det aktuella värdet hämtas från butiken. 

Tillstånd kan också vara Samplacerat med den kod som ändrar tillstånd. Tillstånds känsliga tjänster i Service Fabric skapas vanligt vis med den här modellen. Service Fabric tillhandahåller infrastrukturen för att säkerställa att det här läget är högt tillgängligt, konsekvent och varaktigt, och att tjänsterna som skapats på det här sättet enkelt kan skalas.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

* [Tillgänglighet för Service Fabric tjänster](service-fabric-availability-services.md)
* [Skalbarhet för Service Fabric tjänster](service-fabric-concepts-scalability.md)
* [Partitionera Service Fabric tjänster](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
