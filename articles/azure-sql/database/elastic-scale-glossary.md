---
title: Ord lista för Elastic Database tools
description: Förklaring av termer som används för Elastic Database-verktyg
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 166e365a4ead8ad6d0f7e543c081161ebff5f027
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330469"
---
# <a name="elastic-database-tools-glossary"></a>Ord lista för Elastic Database tools
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Följande villkor definieras för [Elastic Database-verktygen](elastic-scale-introduction.md). Verktygen används för att hantera [Shard Maps](elastic-scale-shard-map-management.md)och inkluderar [klient biblioteket](elastic-database-client-library.md), [verktyget för delad sammanslagning](elastic-scale-overview-split-and-merge.md), [elastiska pooler](elastic-pool-overview.md)och [frågor](elastic-query-overview.md). 

Dessa villkor används när du [lägger till en Shard med hjälp av Elastic Database verktyg](elastic-scale-add-a-shard.md) och [använder klassen RecoveryManager för att åtgärda Shard Map-problem](elastic-database-recovery-manager.md).

![Villkor för elastisk skala][1]

**Databas**: en databas i Azure SQL Database. 

**Data beroende routning**: funktionen som gör det möjligt för ett program att ansluta till en Shard som har fått en specifik horisontell partitionering-nyckel. Se [data beroende routning](elastic-scale-data-dependent-routing.md). Jämför med **[multi-Shard-fråga](elastic-scale-multishard-querying.md)**.

**Global Shard-karta**: kartan mellan horisontell partitionering-nycklar och deras respektive Shards i en **Shard uppsättning**. Den globala Shard-kartan lagras i **Shard Map Manager**. Jämför med den **lokala Shard-kartan**.

**Visa lista Shard-karta**: en Shard-karta där horisontell partitionering-nycklar mappas individuellt. Jämför med **intervall Shard-kartan**.   

**Lokal Shard-karta**: lagrad på en Shard innehåller den lokala Shard-kartan mappningar för shardletar som finns på Shard.

**Shard fråga**: möjlighet att skicka en fråga till flera Shards; resultat uppsättningar returneras med UNION ALL semantik (kallas även "fläkt-ut-fråga"). Jämför med **data beroende routning**.

**Flera innehavare** och en **enda klient**: här visas en databas för en enda klient och en databas för flera klienter:

![Skärm bild som visar en databas för en enskild klient och en databas för flera innehavare.](./media/elastic-scale-glossary/multi-single-simple.png)

Här är en representation av **shardade** -databaser med en och flera innehavare. 

![Databaser med en och flera klienter](./media/elastic-scale-glossary/shards-single-multi.png)

**Range Shard-karta**: en Shard-karta där Shard distributions strategin baseras på flera intervall av sammanhängande värden. 

**Referens tabeller**: tabeller som inte är shardade men replikeras över Shards. Post nummer kan till exempel lagras i en referens tabell. 

**Shard**: en databas i Azure SQL Database som lagrar data från en shardade-data uppsättning. 

**Shard elastiskhet**: möjlighet att utföra både **horisontell skalning** och **vertikal skalning**.

**Shardade-tabeller**: tabeller som är shardade, d.v.s. vars data fördelas över Shards baserat på deras horisontell partitionering nyckel värden. 

**Horisontell partitionering-nyckel**: ett kolumn värde som avgör hur data distribueras mellan Shards. Värde typen kan vara något av följande: **int**, **bigint**, **varbinary** eller **uniqueidentifier**. 

**Shard set**: den samling av Shards som har attribut till samma Shard-karta i Shard Map Manager.  

**Shardlet**: alla data som är associerade med ett enda värde för en horisontell partitionering-nyckel på en Shard. En shardlet är den minsta enheten av data förflyttning möjlig när du distribuerar om shardade-tabeller. 

**Shard-karta**: uppsättning mappningar mellan horisontell partitionering-nycklar och deras respektive Shards.

**Shard Map Manager**: ett hanterings objekt och data lager som innehåller Shard-karta, Shard-platser och mappningar för en eller flera Shard uppsättningar.

![Diagram visar en Shard kart hanterare som är kopplad till shardmaps_global, shards_global och shard_mappings_global.][2]

## <a name="verbs"></a>Verb
**Horisontell skalning**: funktionen för att skala ut (eller i) en samling av Shards genom att lägga till eller ta bort Shards till en Shard-karta, som du ser nedan.

![Vågrät och lodrät skalning][3]

**Sammanslagning**: Syftet med att flytta shardletar från två Shards till en Shard och uppdatera Shard-kartan enligt detta.

**Shardlet flytt**: åtgärden att flytta en enskild Shardlet till en annan Shard. 

**Shard**: Act av vågrätt strukturerade data över flera databaser baserat på en horisontell partitionering nyckel.

**Dela**: åtgärden att flytta flera shardletar från en Shard till en annan (vanligt vis ny) Shard. En horisontell partitionering-nyckel tillhandahålls av användaren som delnings punkt.

**Vertikal skalning**: Syftet med att skala upp (eller ned) beräknings storleken för enskilda Shard. Du kan till exempel ändra en Shard från standard till Premium (vilket resulterar i mer data bearbetnings resurser). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

