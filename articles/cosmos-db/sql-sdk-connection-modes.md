---
title: Azure Cosmos DB SQL SDK-anslutnings lägen
description: 'Lär dig mer om de olika anslutnings lägena som finns i Azure Cosmos DB SQL-SDK: er.'
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: cf985999bac0cf45eec5d8f0f5f9e921b6f4591c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934959"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK-anslutnings lägen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Hur en klient ansluter till Azure Cosmos DB har viktiga prestanda effekter, särskilt för observerad svars tid på klient sidan. Azure Cosmos DB erbjuder en enkel, öppen RESTful programmerings modell över HTTPS som kallas Gateway-läge. Dessutom erbjuder den ett effektivt TCP-protokoll, som också RESTful i sin kommunikations modell och använder TLS för inledande autentisering och kryptering av trafik, vilket kallas direkt läge.

## <a name="available-connectivity-modes"></a>Tillgängliga anslutnings lägen

De två tillgängliga anslutnings lägena är:

  * Gateway-läge
      
    Gateway-läge stöds på alla SDK-plattformar. Om ditt program körs i ett företags nätverk med strikta brand Väggs begränsningar är Gateway-läget det bästa valet eftersom det använder standard-HTTPS-porten och en enda DNS-slutpunkt. Prestanda kompromissen är dock att Gateway-läget omfattar ytterligare ett nätverks hopp varje gång data läses från eller skrivs till Azure Cosmos DB. Vi rekommenderar också Gateway-anslutnings läge när du kör program i miljöer som har ett begränsat antal socketanslutningar.

    När du använder SDK i Azure Functions, i synnerhet i [förbruknings planen](../azure-functions/consumption-plan.md), var medveten om de aktuella [gränserna för anslutningar](../azure-functions/manage-connections.md).

  * Direktläge

    Direct-läget stöder anslutningar via TCP-protokollet och ger bättre prestanda eftersom det finns färre nätverks hopp. Programmet ansluter direkt till backend-replikerna. Direkt läge stöds för närvarande bara på .NET-och Java SDK-plattformar.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Lägena för Azure Cosmos DB anslutning" border="false":::

Dessa anslutnings lägen är i princip den väg som data planet begär – dokument läsningar och skrivningar – tar från klient datorn till partitioner i Azure Cosmos DB backend. Direkt läge är det bästa alternativet för bästa prestanda – det gör att klienten kan öppna TCP-anslutningar direkt till partitioner i Azure Cosmos DB server dels-och sändnings begär Anden *Direct* ly utan mellanliggande. I Gateway-läge dirigeras begär Anden som görs av klienten till en så kallad "Gateway"-server i Azure Cosmos DB klient delen, som i sin tur ger ut dina begär anden till lämpliga partitioner i Azure Cosmos DB backend.

## <a name="service-port-ranges"></a>Tjänst port intervall

När du använder direkt läge måste du förutom Gateway-portarna se till att port intervallet är mellan 10000 och 20000 är öppen eftersom Azure Cosmos DB använder dynamiska TCP-portar. När du använder direkt läge på [privata slut punkter](./how-to-configure-private-endpoints.md)ska hela intervallet TCP-portar – från 0 till 65535 vara öppen. Om de här portarna inte är öppna och du försöker använda TCP-protokollet, kan det hända att du får ett meddelande om att 503-tjänsten inte är tillgänglig.

I följande tabell visas en översikt över de anslutnings lägen som är tillgängliga för olika API: er och tjänst portar som används för varje API:

|Anslutningsläge  |Protokoll som stöds  |SDK: er som stöds  |API/tjänst-port  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Alla SDK: er    |   SQL (443), MongoDB (10250, 10255, 10256), tabell (443), Cassandra (10350), Graf (443) <br> Port 10250 mappar till ett standard-Azure Cosmos DB-API för MongoDB-instans utan geo-replikering. Portarna 10255 och 10256 mappar till den instans som har geo-replikering.   |
|Direct    |     TCP    |  Java SDK för .NET SDK    | När du använder offentliga/tjänst slut punkter: portar i intervallet 10000 till 20000<br>När du använder privata slut punkter: portar inom intervallet 0 till 65535 |

## <a name="next-steps"></a>Nästa steg

För vissa prestanda optimeringar för SDK-plattform:

* [Prestanda tips för .NET v2 SDK](performance-tips.md)

* [Prestanda tips för .NET v3 SDK](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Prestanda tips för Java v4 SDK](performance-tips-java-sdk-v4-sql.md)