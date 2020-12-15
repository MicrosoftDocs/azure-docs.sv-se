---
title: Generella användnings nivåer och affärs kritiska tjänst nivåer
titleSuffix: Azure SQL Database & SQL Managed Instance
description: I artikeln beskrivs generella användnings nivåer och affärs kritiska tjänst nivåer i den vCore-baserade inköps modellen som används av Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 12/14/2020
ms.openlocfilehash: 9ee7440b10bc348d3ba87a4779208791a7b0e9ac
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97512036"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database-och Azure SQL-hanterade instans tjänst nivåer
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database och Azure SQL-hanterad instans baseras på SQL Server databas motor arkitektur som justeras för moln miljön för att säkerställa 99,99 procent tillgänglighet, även om det uppstår ett infrastruktur haveri. Två tjänst nivåer används av Azure SQL Database och Azure SQL-hanterad instans, var och en med en annan arkitektur modell. Dessa tjänst nivåer är:

- [Generell användning](service-tier-general-purpose.md), som är utformad för budgetorienterade arbets belastningar.
- [Affärs kritisk](service-tier-business-critical.md), som är utformad för arbets belastningar med låg latens med hög återhämtnings kapacitet till fel och snabba redundanser.

Azure SQL Database har ytterligare en tjänst nivå: 

- [Storskalig](service-tier-hyperscale.md), som är utformad för de flesta företags arbets belastningar, vilket ger mycket skalbar lagring, Läs skalbarhet och snabb återställning av databas.

Den här artikeln beskriver skillnaderna mellan tjänst nivåerna, lagrings-och säkerhets kopierings överväganden för allmänna syften och affärs kritiska tjänst nivåer i den vCore-baserade inköps modellen.

## <a name="service-tier-comparison"></a>Jämförelse av service nivå

I följande tabell beskrivs viktiga skillnader mellan tjänst nivåer för den senaste generationen (Gen5). Observera att tjänst nivå egenskaperna kan vara olika i SQL Database-och SQL-hanterade instanser.

|-| Resurstyp | Generell användning |  Hyperskala | Affärskritisk |
|:---:|:---:|:---:|:---:|:---:|
| **Bäst för** | |  Erbjuder budget orienterade balanserade beräknings-och lagrings alternativ. | De flesta företags arbets belastningar. Automatisk skalning av lagrings storlek upp till 100 TB, flytande lodrät och vågrät beräknings skalning, snabb databas återställning. | OLTP-program med hög transaktions frekvens och låg IO-latens. Erbjuder högsta möjliga återhämtning till fel och snabba växlingar med hjälp av flera synkront uppdaterade repliker.|
|  **Tillgängligt i resurs typ:** ||SQL Database-/SQL-hanterad instans | Enstaka Azure SQL Database | SQL Database-/SQL-hanterad instans |
| **Beräknings storlek**| SQL Database | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor |
| | SQL-hanterad instans | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Saknas | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| | SQL-hanterade instans pooler | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Saknas | Saknas |
| **Lagringstyp** | Alla | Premium-Fjärrlagring (per instans) | Fristående lagring med lokal SSD-cache (per instans) | Super-fast lokal SSD-lagring (per instans) |
| **Databasstorlek** | SQL Database | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | SQL-hanterad instans  | 32 GB – 8 TB | Saknas | 32 GB – 4 TB |
| **Lagrings storlek** | SQL Database | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | SQL-hanterad instans  | 32 GB – 8 TB | Saknas | 32 GB – 4 TB |
| **TempDB-storlek** | SQL Database | [32 GB per vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL-hanterad instans  | [24 GB per vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | Saknas | Upp till 4 TB – [begränsas av lagrings storlek](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Logg skrivnings data flöde** | SQL Database | [1,875 MB/s per vCore (max 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (max 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL-hanterad instans | [3 MB/s per vCore (högst 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | Saknas | [4 MB/s per vCore (max 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Tillgänglighet**|Alla| 99,99 % |  [99,95% med en sekundär replik, 99,99% med fler repliker](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995% med redundant zon i en enskild databas](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Säkerhetskopior**|Alla|RA-GRS, 7-35 dagar (7 dagar som standard). Maximal kvarhållning för Basic-nivån är 7 dagar. | RA-GRS, 7 dagar, konstant tidpunkts återställning (PITR) | RA-GRS, 7-35 dagar (7 dagar som standard) |
|**Minnesintern OLTP** | | Saknas | Saknas | Tillgängligt |
|**Skrivskyddade repliker**| | 0 inbyggd <br> 0-4 med [geo-replikering](active-geo-replication-overview.md) | 0-4 inbyggd | 1 inbyggd, ingår i priset <br> 0-4 med [geo-replikering](active-geo-replication-overview.md) |
|**Priser/fakturering** | SQL Database | [vCore, reserverad lagring och lagring av säkerhets kopior](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS debiteras inte. | [vCore för varje replik och använt lagrings utrymme](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS har ännu inte debiterats. | [vCore, reserverad lagring och lagring av säkerhets kopior](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS debiteras inte. |
|| SQL-hanterad instans | [vCore, reserverad lagring och lagring av säkerhets kopior](https://azure.microsoft.com/pricing/details/sql-database/managed/) debiteras. <br/>IOPS debiteras inte| Saknas | [vCore, reserverad lagring och lagring av säkerhets kopior](https://azure.microsoft.com/pricing/details/sql-database/managed/) debiteras. <br/>IOPS debiteras inte.| 
|**Rabatt modeller**| | [Reserverade instanser](reserved-capacity-overview.md)<br/>[Azure Hybrid-förmån](../azure-hybrid-benefit.md) (inte tillgängligt för utveckling/testning-prenumerationer)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) och [betala](https://azure.microsoft.com/offers/ms-azr-0023p/) per användning-prenumerationer för utveckling och testning| [Azure Hybrid-förmån](../azure-hybrid-benefit.md) (inte tillgängligt för utveckling/testning-prenumerationer)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) och [betala](https://azure.microsoft.com/offers/ms-azr-0023p/) per användning-prenumerationer för utveckling och testning| [Reserverade instanser](reserved-capacity-overview.md)<br/>[Azure Hybrid-förmån](../azure-hybrid-benefit.md) (inte tillgängligt för utveckling/testning-prenumerationer)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) och [betala](https://azure.microsoft.com/offers/ms-azr-0023p/) per användning-prenumerationer för utveckling och testning|

Mer information finns i de detaljerade skillnaderna mellan tjänst nivåerna i [Azure SQL Database (vCore)](resource-limits-vcore-single-databases.md), [Single Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md), [pooled Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md)och [Azure SQL Managed instance](../managed-instance/resource-limits.md) Pages.

> [!NOTE]
> Information om den storskaliga tjänst nivån i den vCore-baserade inköps modellen finns i [storskalig Service Tier](service-tier-hyperscale.md). En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [köpa modeller och resurser](purchasing-models.md).

## <a name="data-and-log-storage"></a>Data-och logg lagring

Följande faktorer påverkar mängden lagrings utrymme som används för data-och loggfiler och gäller för Generell användning och Affärskritisk. Mer information om data och logg lagring i stor skala finns i [storskalig Service Tier](service-tier-hyperscale.md).

- Det allokerade lagrings utrymmet används av data filer (MDF) och loggfiler (LDF).
- Varje beräknings storlek för en enskild databas stöder en maximal databas storlek, med en maximal standard storlek på 32 GB.
- När du konfigurerar den nödvändiga storleken för enskild databas (storleken på MDF-filen) läggs 30 procent ytterligare lagrings utrymme automatiskt till för att ge stöd för LDF-filer.
- Du kan välja en enskild databas storlek mellan 10 GB och den maximala storleken som stöds.
  - Öka eller minska storleken i steg om 10 GB för lagring i tjänst nivåerna standard eller allmän användning.
  - Öka eller minska storleken i steg om 250 GB för lagring på tjänst nivåerna Premium eller affärs kritisk.
- I tjänst nivån generell användning `tempdb` används en ansluten SSD och den här lagrings kostnaden ingår i vCore-priset.
- På nivån verksamhets kritisk tjänst `tempdb` delar den anslutna SSD med MDF-och ldf-filerna och `tempdb` lagrings kostnaden ingår i vCore-priset.
- Lagrings storleken för en SQL-hanterad instans måste anges i multipler av 32 GB.


> [!IMPORTANT]
> Du debiteras för den totala lagring som allokerats för MDF-och LDF-filer.

Använd [sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)för att övervaka den aktuella totala storleken på dina MDF-och LDF-filer. Använd [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)för att övervaka den aktuella storleken på de enskilda MDF-och ldf-filerna.

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

## <a name="backups-and-storage"></a>Säkerhets kopiering och lagring

Lagring för säkerhets kopiering av databaser allokeras till stöd för PITR (Point-in-Time Restore) och [LTR (långsiktig kvarhållning)](long-term-retention-overview.md) i SQL Database-och SQL-hanterad instans. Den här lagringen allokeras separat för varje databas och faktureras som två separata avgifter per databas.

- **PITR**: enskilda databas säkerhets kopior kopieras till [lagring med Läs åtkomst till geo-REDUNDANT lagring (RA-GRS)](../../storage/common/geo-redundant-design.md) automatiskt. Lagrings storleken ökar dynamiskt när nya säkerhets kopior skapas. Lagringen används av varje veckovis fullständig säkerhets kopiering, dagliga differentiella säkerhets kopieringar och säkerhets kopior av transaktions loggar, som kopieras var femte minut. Lagrings användningen beror på ändrings takten för databasen och kvarhållningsperioden för säkerhets kopieringar. Du kan konfigurera en separat kvarhållningsperiod för varje databas mellan 7 och 35 dagar. En minsta lagrings mängd som motsvarar 100 procent (1x) av databas storleken tillhandahålls utan extra kostnad. För de flesta databaser räcker det att lagra 7 dagars säkerhets kopieringar.
- **Brv**: du har också möjlighet att konfigurera långsiktig kvarhållning av fullständiga säkerhets kopieringar i upp till 10 år (den här funktionen finns i [begränsad offentlig för hands version för SQL-hanterad instans](long-term-retention-overview.md#sql-managed-instance-support). Om du konfigurerar en LTR-princip lagras dessa säkerhets kopior i RA-GRS-lagring automatiskt, men du kan styra hur ofta säkerhets kopiorna ska kopieras. För att uppfylla olika krav på efterlevnad kan du välja olika kvarhållningsperiod för säkerhets kopiering varje vecka, månatlig och per år. Konfigurationen du väljer avgör hur mycket lagrings utrymme som ska användas för säkerhets kopieringar med LTR. Om du vill uppskatta kostnaden för den LTR Storage-lagringen kan du använda pris listan för vanlig prissättning. Mer information finns i [SQL Database långsiktig kvarhållning](long-term-retention-overview.md).

## <a name="next-steps"></a>Nästa steg

Mer information om de specifika beräknings-och lagrings storlekar som är tillgängliga i nivån generell användning och affärs kritiska tjänster finns i: 

- [vCore resurs gränser för Azure SQL Database](resource-limits-vcore-single-databases.md).
- [vCore resurs gränser för databaser i pooler i Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [vCore resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md).
