---
title: Generell användning tjänst nivå
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Lär dig mer om Generell användning tjänst nivå för Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85986649"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Generell användning tjänst nivå – Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Generell användning tjänst nivån i den vCore-baserade inköps modellen kallas standard tjänst nivån i den DTU-baserade inköps modellen. En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [köpa modeller och resurser](purchasing-models.md).

Azure SQL Database-och Azure SQL-hanterad instans baseras på den SQL Server databas motor arkitekturen som är anpassad för moln miljön för att säkerställa 99,99% tillgänglighet även i händelse av infrastruktur haverier. 

Det finns två tjänst nivåer som används av Azure SQL Database och SQL-hanterad instans: 

- Generell användning
- Affärskritisk

Azure SQL Database har också en tredje tjänst nivå, som för närvarande inte är tillgänglig för Azure SQL-hanterad instans:

- Hyperskala

Arkitektur modellen för Generell användning tjänst nivån baseras på en separation av data bearbetning och lagring. Den här arkitektur modellen är beroende av hög tillgänglighet och tillförlitlighet för Azure Blob Storage som transparent replikerar databasfiler och garanterar ingen data förlust om det uppstår ett underliggande infrastruktur haveri.

Följande bild visar fyra noder i standard arkitektur modellen med de avgränsade beräknings-och lagrings lagren.

![Separering av beräkning och lagring](./media/service-tier-general-purpose/general-purpose-service-tier.png)

I arkitektur modellen för Generell användning tjänst nivån finns det två lager:

- Ett tillstånds löst beräknings lager som kör `sqlservr.exe` processen och endast innehåller tillfälliga och cachelagrade data (till exempel plan cache, resurspool, Column Store pool). Den här tillstånds lösa noden drivs av Azure Service Fabric som initierar process, kontrollerar hälsan för noden och utför redundans till en annan plats vid behov.
- Ett tillstånds känsligt data lager med databasfiler (. MDF/. ldf) som lagras i Azure Blob Storage. Azure Blob Storage garanterar att det inte finns någon data förlust för poster som placeras i en databas fil. Azure Storage har inbyggd data tillgänglighet/redundans som säkerställer att alla poster i logg filen eller i data filen kommer att bevaras även om processen kraschar.

När databas motorn eller operativ systemet uppgraderas Miss lyckas en del av den underliggande infrastrukturen, eller om ett kritiskt problem upptäcks i `sqlservr.exe` processen, flyttar Azure Service Fabric den process lösa processen till en annan tillstånds lös beräknings nod. Det finns en uppsättning reserv noder som väntar på att köra en ny beräknings tjänst om en redundansväxling av den primära noden sker för att minimera redundansväxlingen. Data i Azure Storage Layer påverkas inte, och data/loggfiler bifogas till nyinitierad process. Den här processen garanterar 99,99% tillgänglighet, men kan ha vissa prestanda påverkan på stora arbets belastningar som körs på grund av över gångs tiden och det faktum att den nya noden börjar med kall cache.

## <a name="when-to-choose-this-service-tier"></a>När du ska välja den här tjänst nivån

Generell användning tjänst nivå är en standard tjänst nivå i Azure SQL Database och Azure SQL-hanterad instans som är utformad för de flesta allmänna arbets belastningar. Om du behöver en fullständigt hanterad databas motor med 99,99% SLA med lagrings fördröjningen mellan 5 och 10 MS som matchar SQL Server på en virtuell Azure-dator i de flesta fall, är Generell användning nivån alternativet för dig.

## <a name="next-steps"></a>Nästa steg

- Hitta resurs egenskaper (antal kärnor, I/O, minne) för Generell användning/standard-nivån i SQL- [hanterad instans](../managed-instance/resource-limits.md#service-tier-characteristics), enkel databas i [vCore-modellen](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) eller [DTU-modellen](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)eller elastisk pool i [vCore-modellen](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) och DTU- [modellen](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Lär dig mer om [affärskritisk](service-tier-business-critical.md) -och [skalnings](service-tier-hyperscale.md) nivåer.
- Läs mer om [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och haveri beredskap finns i [affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).
