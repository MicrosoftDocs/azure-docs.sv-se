---
title: Introduktion till Azure Service Fabric nätverk
description: Lär dig mer om nätverk, gatewayer och intelligent trafik routning i Service Fabric nät.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: ef8b40be366afe24b960f7419d84cc2a9976b592
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625709"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introduktion till nätverk i Service Fabric nätprogram

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

I den här artikeln beskrivs olika typer av belastningsutjämnare, hur gatewayer ansluter nätverket till dina program till andra nätverk och hur trafik dirigeras mellan tjänsterna i dina program.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Layer 4 vs Layer 7 Load Balancer
Belastnings utjämning kan utföras på olika lager i [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model) för nätverk, ofta i skikt 4 (L4) och Layer 7 (L7).  Det finns vanligt vis två typer av belastnings utjämning:

- En L4-belastningsutjämnare fungerar på nätverks transport lagret, som hanterar leverans av paket utan hänsyn till paketets innehåll. Endast paket rubriker inspekteras av belastningsutjämnaren, så Utjämnings kriterierna är begränsade till IP-adresser och portar. En klient gör till exempel en TCP-anslutning till belastningsutjämnaren. Belastningsutjämnaren avslutar anslutningen (genom att svara direkt på SYN), väljer en server del och gör en ny TCP-anslutning till Server delen (skickar en ny SYN). En L4-belastningsutjämnare fungerar vanligt vis endast på nivån för den L4 TCP/UDP-anslutningen eller-sessionen. Belastnings utjämning omdirigerar alltså byte runt och ser till att byte från samma session går upp på samma server del. Den L4-belastningsutjämnaren är inte medveten om några program Detaljer för de byte som den flyttar. Byte kan vara valfritt program protokoll.

- En L7-belastningsutjämnare fungerar på program nivå, som hanterar innehållet i varje paket. Det kontrollerar paket innehållet eftersom det förstår protokoll som HTTP, HTTPS eller WebSockets. Detta ger belastningsutjämnaren möjlighet att utföra avancerad routning. En klient gör till exempel en enda HTTP/2 TCP-anslutning till belastningsutjämnaren. Belastningsutjämnaren fortsätter sedan att göra två server dels anslutningar. När klienten skickar två HTTP/2-strömmar till belastningsutjämnaren skickas data ström till Server del en och strömma två till Server del två. Det innebär att även om Multiplexing-klienter som har stora olika begär ande belastningar bal anse ras effektivt över hela Server delen. 

## <a name="networks-and-gateways"></a>Nätverk och gatewayer
I [Service Fabric resurs modell](service-fabric-mesh-service-fabric-resources.md)är en nätverks resurs en individuellt spridbar resurs, oberoende av ett program eller en tjänst resurs som kan referera till den som ett beroende. Den används för att skapa ett nätverk för dina program som är öppna för Internet. Flera tjänster från olika program kan vara en del av samma nätverk. Det här privata nätverket skapas och hanteras av Service Fabric och är inte ett virtuellt Azure-nätverk (VNET). Program kan läggas till och tas bort dynamiskt från nätverks resursen för att aktivera och inaktivera VNET-anslutning. 

En gateway används för att överbrygga två nätverk. Gateway-resursen distribuerar en [mottagare-proxy](https://www.envoyproxy.io/) som tillhandahåller L4-routning för alla protokoll och L7-routning för avancerad http (S)-routning av program. Gatewayen dirigerar trafik till nätverket från ett externt nätverk och avgör vilken tjänst som trafik ska skickas till.  Det externa nätverket kan vara ett öppet nätverk (i stort sett offentligt Internet) eller ett virtuellt Azure-nätverk, så att du kan ansluta till dina andra Azure-program och-resurser. 

![Nätverk och gateway][Image1]

När nätverks resursen skapas med `ingressConfig` tilldelas en offentlig IP-adress till nätverks resursen. Den offentliga IP-adressen är kopplad till nätverks resursens livs längd.

När ett nätprogram skapas ska det referera till en befintlig nätverks resurs. Nya offentliga portar kan läggas till eller befintliga portar kan tas bort från ingress-konfigurationen. Det går inte att ta bort en nätverks resurs om en program resurs refererar till den. När programmet tas bort tas nätverks resursen bort.

## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh finns i översikten:
- [Service Fabric Mesh-översikt](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png