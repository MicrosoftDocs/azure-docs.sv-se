---
title: Ordlista
titleSuffix: Azure SQL
description: En ord lista med termer för att arbeta med Azure SQL Database, Azure SQL-hanterad instans och SQL på virtuella Azure-datorer.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/09/2020
ms.openlocfilehash: 8bd1e312463cf89ae30d54bc4a32a497fc7a9a78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249660"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database ord lista med villkor
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Kontext|Period|Mer information|
|:---|:---|:---|
|Azure-tjänst|Azure SQL Database eller SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|Köpmodell|DTU-baserad inköps modell|[DTU-baserad inköps modell](database/service-tiers-dtu.md)|
||Köpmodell baserad på virtuell kärna|[Köpmodell baserad på virtuell kärna](database/service-tiers-vcore.md)|
|Distributions alternativ |Enskild databas|[Enkla databaser](database/single-database-overview.md)|
||Elastisk pool|[Elastisk pool](database/elastic-pool-overview.md)|
|Tjänstenivå|Basic, standard, Premium, Generell användning, storskalig Affärskritisk|För tjänst nivåer i vCore-modellen, se [SQL Database tjänst nivåer](database/service-tiers-vcore.md#service-tiers). För tjänst nivåer i DTU-modellen, se [DTU-modellen](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Beräkningsnivå|Serverlös databearbetning|[Serverlös databearbetning](database/service-tiers-vcore.md#compute-tiers)
||Allokerad beräkning|[Allokerad beräkning](database/service-tiers-vcore.md#compute-tiers)
|Beräknings generation|Gen5, M-serien, Fsv2-serien, DC-serien|[Maskin varu generationer](database/service-tiers-vcore.md#hardware-generations)
|Server entitet| Server |[Logiska SQL-servrar](database/logical-servers.md)|
|Resurstyp|V-kärna|En processor kärna som tillhandahålls för beräknings resursen för en enskild databas, elastisk pool. |
||Beräknings storlek och lagrings belopp|Compute-storlek är den maximala mängden processor, minne och andra icke-lagrings resurser som är tillgängliga för en enskild databas eller elastisk pool.  Lagrings storleken är den maximala mängden lagring som är tillgänglig för en enskild databas eller elastisk pool. För storleks alternativ i vCore-modellen, se [vCore-enkla databaser](database/resource-limits-vcore-single-databases.md)och [vCore elastiska pooler](database/resource-limits-vcore-elastic-pools.md).  (.. /managed-instance/resource-limits.md).  Storleks alternativ i DTU-modellen finns i både [DTU-databaser](database/resource-limits-dtu-single-databases.md) och [elastiska DTU-pooler](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

|Kontext|Period|Mer information|
|:---|:---|:---|
|Azure-tjänst|Azure SQL Managed Instance|[SQL-hanterad instans](managed-instance/sql-managed-instance-paas-overview.md)|
|Köpmodell|Köpmodell baserad på virtuell kärna|[Köpmodell baserad på virtuell kärna](database/service-tiers-vcore.md)|
|Distributions alternativ |Enskild instans|[Enskild instans](managed-instance/sql-managed-instance-paas-overview.md)|
||Instans-pool (för hands version)|[Instanspooler](managed-instance/instance-pools-overview.md)|
|Tjänstenivå|Generell användning Affärskritisk|[SQL-hanterad instans tjänst nivåer](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Beräkningsnivå|Allokerad beräkning|[Allokerad beräkning](database/service-tiers-vcore.md#compute-tiers)|
|Beräknings generation|Gen5|[Maskin varu generationer](database/service-tiers-vcore.md#hardware-generations)
|Server entitet|Hanterad instans eller instans| Ej tillämpligt eftersom SQL-hanterad instans är i sig själva servern |
|Resurstyp|V-kärna|En processor kärna som tillhandahålls för beräknings resursen för SQL-hanterad instans.|
||Beräknings storlek och lagrings belopp|Compute-storlek är den maximala mängden processor, minne och andra icke-lagrings resurser för SQL-hanterad instans.  Lagrings storleken är den maximala lagrings mängden som är tillgänglig för en SQL-hanterad instans.  För storleks alternativ, [SQL-hanterade instanser](managed-instance/resource-limits.md). |
