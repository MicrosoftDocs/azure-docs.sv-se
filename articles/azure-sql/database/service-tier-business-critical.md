---
title: Affärskritisk tjänst nivå
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Lär dig mer om verksamhets kritiska tjänst nivå för Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780231"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Affärskritisk nivå – Azure SQL Database och Azure SQL-hanterad instans 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Affärskritisk nivån kallas Premium i inköps modellen för DTU. En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [Azure SQL Database köpa modeller och resurser](purchasing-models.md).

Azure SQL Database och Azure SQL-hanterad instans baseras båda på SQL Server databas motor arkitektur som har justerats för moln miljön för att säkerställa 99,99% tillgänglighet även i händelse av infrastruktur haverier. Det finns tre arkitektur modeller som används:
- Generell användning/standard 
- Affärskritisk/Premium
- Hyperskala

Premium-/Affärskritisk tjänst nivå modellen är baserad på ett kluster med databas motor processer. Den här arkitektur modellen bygger på ett faktum att det alltid finns ett kvorum med tillgängliga databas motor noder och har minimal påverkan på din arbets belastning även under underhålls aktiviteter. Den storskaliga Service nivån är för närvarande bara tillgänglig för Azure SQL Database (inte SQL-hanterad instans) och är en mycket skalbar lagrings-och beräknings prestanda nivå som utnyttjar Azure-arkitekturen för att skala ut lagrings-och beräknings resurserna för en databas i Azure SQL Database avsevärt utanför de tillgängliga gränserna för Generell användning-och Affärskritisk tjänst nivåerna.

Azure uppgraderar och korrigeringsfiler underliggande operativ system, driv rutiner och SQL Server Database Engine transparent med minimal drift tid för slutanvändare. 

Premium-tillgänglighet är aktiverat i Premium-och Affärskritisk tjänst nivåerna och är utformad för intensiva arbets belastningar som inte kan tolerera prestanda påverkan på grund av pågående underhålls åtgärder.

Compute och Storage är integrerat på den enskilda noden i Premium-modellen. Hög tillgänglighet i den här arkitektur modellen uppnås genom att data replikeras (SQL Server databas motor process) och lagring (lokalt ansluten SSD) distribueras till ett kluster med fyra noder, med teknik som liknar SQL Server [Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Kluster med noder i databas motorn](./media/service-tier-business-critical/business-critical-service-tier.png)

Både processen för databas motorn för SQL Server och de underliggande MDF-/. ldf-filerna placeras på samma nod med lokalt ansluten SSD-lagring som ger låg latens för din arbets belastning. Hög tillgänglighet implementeras med teknik som liknar SQL Server [Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Varje databas är ett kluster med databasnoder med en primär databas som är tillgänglig för kund arbets belastningar och en tre sekundära processer som innehåller kopior av data. Den primära noden skickar konstanter ändringar till de sekundära noderna för att säkerställa att data är tillgängliga på sekundära repliker om den primära noden Miss lyckas av någon anledning. Redundansväxlingen hanteras av SQL Server Database Engine – en sekundär replik blir den primära noden och en ny sekundär replik skapas för att säkerställa att det finns tillräckligt många noder i klustret. Arbets belastningen omdirigeras automatiskt till den nya primära noden.

Dessutom har Affärskritisk Cluster inbyggd [Läs skalbar](read-scale-out.md) funktion som tillhandahåller en inbyggd skrivskyddad skrivskyddad nod som kan användas för att köra skrivskyddade frågor (till exempel rapporter) som inte ska påverka prestandan för din primära arbets belastning.

## <a name="when-to-choose-this-service-tier"></a>När du ska välja den här tjänst nivån

Affärskritisk tjänst nivå är utformad för program som kräver svar med låg latens från den underliggande SSD-lagringen (1-2 MS i genomsnitt), snabb återställning om den underliggande infrastrukturen Miss lyckas eller om du behöver stänga av rapporter, analyser och skrivskyddade frågor till kostnads fri läsbar sekundär replik för den primära databasen.

Den viktigaste anledningen till varför du bör välja Affärskritisk tjänst nivå i stället för Generell användning nivå är:
-   **Krav för låg I/O-latens** – arbets belastningar som behöver ett snabbt svar från lagrings skiktet (1-2 millisekunder i genomsnitt) bör använda affärskritisk nivån. 
-   **Frekvent kommunikation mellan program och databas**. Program som inte kan utnyttja cachelagring av program lager eller [begära batchbearbetning](../performance-improve-use-batching.md) och behöver skicka många SQL-frågor som måste bearbetas snabbt är bra kandidater för affärskritisk nivån.
-   **Stort antal uppdateringar** – INSERT-, Update-och Delete-åtgärder ändra data sidorna i minnet (skadad sida) som måste sparas till datafiler med `CHECKPOINT` åtgärd. Eventuell databas motor process krasch eller en redundansväxling av databasen med ett stort antal skadade sidor kan öka återställnings tiden i Generell användning nivån. Använd Affärskritisk nivå om du har en arbets belastning som orsakar många minnes ändringar. 
-   **Tids krävande transaktioner som ändrar data**. Transaktioner som öppnas under en längre tid förhindrar att logg filen trunkeras, vilket kan öka logg storleken och antalet [virtuella loggfiler (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Ett stort antal VLFs kan sakta ned återställning av databasen efter redundansväxlingen.
-   **Arbets belastning med rapporterings-och analys frågor** som kan omdirigeras till den kostnads fria sekundära skrivskyddade repliken.
- **Högre återhämtning och snabbare återställning från haverier**. Om det uppstår systemfel kommer databasen på den primära instansen att inaktive ras och en av de sekundära replikerna kommer omedelbart att bli en ny skrivskyddad primär databas som är redo att bearbeta frågor. Databas motorn behöver inte analysera och göra om transaktioner från logg filen och läsa in alla data i minnesbufferten.
- **Skydd för avancerad data skada**. Affärskritisk-nivån utnyttjar databas repliker bakom affärs kontinuiteten och så att tjänsten även utnyttjar automatisk sid reparation, vilket är samma teknik som används för att SQL Server databas [speglings-och tillgänglighets grupper](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). Om en replik inte kan läsa en sida på grund av ett data integritets problem hämtas en ny kopia av sidan från en annan replik, vilket ersätter den oläsbarde sidan utan data förlust eller kund avbrott. Den här funktionen gäller i Generell användning nivå om databasen har geo-sekundär replik.
- **Högre tillgänglighet** – affärskritisk nivån i multi-AZ-konfigurationen garanterar 99,995% tillgänglighet, jämfört med 99,99% av generell användning nivån.
- **Snabb geo-återställning** – affärskritisk nivå som kon figurer ATS med geo-replikering har en garanterad återställnings punkt mål på 5 SEK och återställnings tid (RTO) på 30 sek i 100% av de distribuerade timmarna.

## <a name="next-steps"></a>Nästa steg

- Hitta resurs egenskaper (antal kärnor, I/O, minne) för Affärskritisk nivå i SQL- [hanterad instans](../managed-instance/resource-limits.md#service-tier-characteristics), enkel databas i [vCore-modell](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) eller [DTU-modell](resource-limits-dtu-single-databases.md#premium-service-tier)eller elastisk pool i [vCore-modellen](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) och DTU- [modellen](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Lär dig mer om [generell användning](service-tier-general-purpose.md) -och [skalnings](service-tier-hyperscale.md) nivåer.
- Läs mer om [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och haveri beredskap finns i [affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).