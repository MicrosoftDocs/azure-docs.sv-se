---
title: Resursbegränsningar
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln innehåller en översikt över resurs gränserna för Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 08/14/2020
ms.openlocfilehash: 902fa34be149f0b876729409c530186e34c706e5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587318"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Översikt över gränser för resurs gränser för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln innehåller en översikt över de tekniska egenskaperna och resurs gränserna för Azure SQL-hanterad instans och ger information om hur du begär en ökning av dessa gränser.

> [!NOTE]
> Skillnader i funktioner som stöds och T-SQL-uttryck finns i [funktions skillnader](../database/features-comparison.md) och [stöd för t-SQL-uttryck](transact-sql-tsql-differences-sql-server.md). Generella skillnader mellan tjänst nivåer för Azure SQL Database och SQL-hanterad instans finns i [jämförelse av tjänst nivå](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Egenskaper för maskin varu skapande

SQL-hanterad instans har egenskaper och resurs gränser som är beroende av den underliggande infrastrukturen och arkitekturen. SQL-hanterad instans kan distribueras på två maskin varu generationer: Gen4 och Gen5. Maskin varu generationer har olika egenskaper, enligt beskrivningen i följande tabell:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **Maskinvara** | Intel® E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD vCore = 1 PP (fysisk kärna) | Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) och Intel® 8272CL (överlappande sjö) 2,5 GHz-processorer, fast NVMe SSD, vCore = 1 LP (Hyper-Thread) |
| **Antal v-kärnor** | 8, 16, 24 virtuella kärnor | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| **Högsta mängd minne (minne/Core-förhållande)** | 7 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. | 5,1 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| **Högsta minnes intern OLTP-minne** | Instans gräns: 1 – 1,5 GB per vCore| Instans gräns: 0,8 – 1,65 GB per vCore |
| **Högsta reserverade instans lagring** |  Generell användning: 8 TB<br/>Affärskritisk: 1 TB | Generell användning: 8 TB<br/> Affärskritisk 1 TB, 2 TB eller 4 TB beroende på antalet kärnor |

> [!IMPORTANT]
> - Gen4-maskinvaran föråldras och är inte längre tillgänglig för nya distributioner. Alla nya instanser av SQL-hanterad instans måste distribueras på Gen5 maskin vara.
> - Överväg att [Flytta din instans av SQL-hanterad instans till generation 5](../database/service-tiers-vcore.md) -maskin vara för att få en större mängd vCore och skalbarhet för lagring, accelererat nätverk, bästa IO-prestanda och minimal latens.

### <a name="in-memory-oltp-available-space"></a>Tillgängligt utrymme i minnet för OLTP 

Mängden minnes intern OLTP-rymd i [affärskritisk](../database/service-tier-business-critical.md) tjänst nivå beror på antalet virtuella kärnor och maskin varu generering. I följande tabell visas en lista över minnes gränser som kan användas för minnesbaserade OLTP-objekt.

| Minnes intern OLTP-rymd  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 virtuella kärnor  | 3,14 GB | |   
| 8 virtuella kärnor  | 6,28 GB | 8 GB |
| 16 virtuella kärnor | 15,77 GB | 20 GB |
| 24 virtuella kärnor | 25,25 GB | 36 GB |
| 32 virtuella kärnor | 37,94 GB | |
| 40 virtuella kärnor | 52,23 GB | |
| 64 virtuella kärnor | 99,9 GB    | |
| 80 virtuella kärnor | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Egenskaper för tjänst nivå

SQL-hanterad instans har två tjänst nivåer: [generell användning](../database/service-tier-general-purpose.md) och [affärskritisk](../database/service-tier-business-critical.md). Dessa nivåer ger [olika funktioner](../database/service-tiers-general-purpose-business-critical.md), enligt beskrivningen i tabellen nedan.

> [!Important]
> Affärskritisk Service Tier tillhandahåller en ytterligare inbyggd kopia av SQL-hanterad instans (sekundär replik) som kan användas för skrivskyddade arbets belastningar. Om du kan separera Skriv-och analys-/rapporterings frågor får du två gånger virtuella kärnor och minne för samma pris. Den sekundära repliken kan vänta några sekunder bakom den primära instansen, så den är utformad för att avlasta rapporter/analytiska arbets belastningar som inte behöver exakt det aktuella data läget. I tabellen nedan är **skrivskyddade frågor** de frågor som körs på den sekundära repliken.

| **Funktion** | **Generell användning** | **Affärskritisk** |
| --- | --- | --- |
| Antal v-kärnor\* | Gen4:8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4:8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Samma antal virtuella kärnor är dedicerat för skrivskyddade frågor. |
| Högsta mängd minne | Gen4:56 GB-168 GB (7GB/vCore)<br/>Gen5:20,4 GB-408 GB (5,1 GB/vCore)<br/>Lägg till fler virtuella kärnor för att få mer minne. | Gen4:56 GB-168 GB (7GB/vCore)<br/>Gen5:20,4 GB-408 GB (5,1 GB/vCore) för Läs-och skriv frågor<br/>+ ytterligare 20,4 GB-408 GB (5,1 GB/vCore) för skrivskyddade frågor.<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| Maximal instans lagrings storlek (reserverad) | -2 TB för 4 virtuella kärnor (endast Gen5)<br/>– 8 TB för andra storlekar | Gen4:1 TB <br/> Gen5 <br/>-1 TB för 4, 8, 16 virtuella kärnor<br/>– 2 TB för 24 virtuella kärnor<br/>– 4 TB för 32, 40, 64, 80 virtuella kärnor |
| Maximal databasstorlek | Upp till tillgänglig instans storlek (max 2 TB-8 TB beroende på antalet virtuella kärnor). | Upp till tillgänglig instans storlek (max 1 TB – 4 TB beroende på antalet virtuella kärnor). |
| Maximal tempDB-storlek | Begränsad till 24 GB/vCore (96-1 920 GB) och för tillfället tillgänglig instans lagrings storlek.<br/>Lägg till fler virtuella kärnor för att få mer TempDB-utrymme.<br/> Logg filens storlek är begränsad till 120 GB.| Upp till tillgänglig instans lagrings storlek för närvarande. |
| Maximalt antal databaser per instans | 100, om inte storleks gränsen för instans lagring har uppnåtts. | 100, om inte storleks gränsen för instans lagring har uppnåtts. |
| Maximalt antal databasfiler per instans | Upp till 280, om inte instansens lagrings storlek eller [Azure Premium disk](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) utrymmes gräns har nåtts. | 32 767 filer per databas, om inte storleks gränsen för instans lagring har uppnåtts. |
| Maximal data fil storlek | Begränsad till tillgänglig instans lagrings storlek (högst 2 TB-8 TB) och [lagrings utrymme för Azure Premium-disk](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | Begränsad till tillfället tillgänglig instans lagrings storlek (upp till 1 TB-4 TB). |
| Största logg fils storlek | Begränsad till 2 TB och tillgänglig instans lagrings storlek för närvarande. | Begränsad till 2 TB och tillgänglig instans lagrings storlek för närvarande. |
| Data/logga IOPS (ungefärligt) | Upp till 30-40 kB IOPS per instans *, 500-7500 per fil<br/>\*[Öka fil storleken för att få mer IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 k – 200 KB (2500 IOPS/vCore)<br/>Lägg till fler virtuella kärnor för att få bättre IO-prestanda. |
| Logg Skriv data flödes gräns (per instans) | 3 MB/s per vCore<br/>Högst 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Data flöde (ungefärligt) | 100-250 MB/s per fil<br/>\*[Öka fil storleken för att få bättre IO-prestanda](#file-io-characteristics-in-general-purpose-tier) | Inte begränsat. |
| IO-latens för lagring (ungefärligt) | 5-10 MS | 1-2 MS |
| Minnesintern OLTP | Stöds inte | Tillgängligt, [storleken beror på antalet vCore](#in-memory-oltp-available-space) |
| Maximalt antal sessioner | 30000 | 30000 |
| Maximalt antal samtidiga arbetare (begär Anden) | Gen4:210 * antal virtuella kärnor + 800<br>Gen5:105 * antal virtuella kärnor + 800 | Gen4:210 * vCore antal + 800<br>Gen5:105 * vCore antal + 800 |
| [Skrivskyddade repliker](../database/read-scale-out.md) | 0 | 1 (ingår i priset) |
| Beräknings isolering | Gen5<br/>– stöds för 80 virtuella kärnor<br/>– stöds inte för andra storlekar<br/><br/>Gen4 stöds inte på grund av inaktuellitet|Gen5<br/>– stöds för 60, 64, 80 virtuella kärnor<br/>– stöds inte för andra storlekar<br/><br/>Gen4 stöds inte på grund av inaktuellitet|


Några ytterligare överväganden: 

- **För närvarande är tillgänglig instans lagrings storlek** skillnaden mellan reserverad instans och använt lagrings utrymme.
- Både data-och logg fils storleken i användar-och system databaserna ingår i instans lagrings storleken som jämförs med den maximala lagrings storleks gränsen. Använd vyn [sys. master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) system för att fastställa det totala använda utrymmet för databaser. Fel loggarna är inte bestående och ingår inte i storlek. Säkerhets kopieringar ingår inte i lagrings storleken.
- Data flödet och IOPS i Generell användnings nivån beror också på den [fil storlek](#file-io-characteristics-in-general-purpose-tier) som inte uttryckligen begränsas av SQL-hanterad instans.
  Du kan skapa en annan läsbar replik i en annan Azure-region med hjälp av [grupper för automatisk redundans](../database/auto-failover-group-configure.md)
- Högsta instans-IOPS beror på fillayouten och distributionen av arbets belastningen. Exempel: om du skapar 7 x 1 TB-filer med högst 5 K IOPS per och 7 små filer (mindre än 128 GB) med 500 IOPS var, kan du hämta 38500 IOPS per instans (7x5000 + 7x500) om din arbets belastning kan använda alla filer. Observera att vissa IOPS också används för automatisk säkerhets kopiering.

Hitta mer information om [resurs gränserna i SQL Managed instance Pools i den här artikeln](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Egenskaper för fil-i/o i Generell användning nivån

I Generell användning tjänst nivån varje databas fil får dedikerad IOPS och data flöde som är beroende av fil storleken. Större datafiler får mer IOPS och data flöde. I/o-egenskaperna för databasfilerna visas i följande tabell:

| Filstorlek | >= 0 och <= 128 GiB | >128 och <= 256 GiB | >256 och <= 512 GiB | >0,5 och <= 1 TiB    | >1 och <= 2 TiB    | >2 och <= 4 TiB | >4 och <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per fil       | 500   | 1100 | 2 300              | 5000              | 7 500              | 7 500              | 12 500   |
| Data flöde per fil | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Om du märker en lång IO-fördröjning på en viss databas fil eller om du ser att IOPS/data flödet når gränsen kan du förbättra prestandan genom [att öka fil storleken](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Det finns också en gräns på instans nivå av det maximala logg skrivnings flödet (som är 22 MB/s), så du kanske inte kan komma åt den maximala filen i logg filen eftersom du påträffar instans data flödes gränsen.

## <a name="supported-regions"></a>Regioner som stöds

SQL-hanterad instans kan bara skapas i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en SQL-hanterad instans i en region som för närvarande inte stöds kan du [skicka en supportbegäran via Azure Portal](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

SQL-hanterad instans har för närvarande endast stöd för distribution av följande typer av prenumerationer:

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud Service Provider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Prenumerationer med månatlig Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Begränsningar för regionala resurser

> [!Note]
> Den senaste informationen om region tillgänglighet för prenumerationer får du först [Markera en region](https://aka.ms/sqlcapacity).

Prenumerations typer som stöds kan innehålla ett begränsat antal resurser per region. SQL-hanterad instans har två standard gränser per Azure-region (som kan ökas på begäran genom att skapa en särskild [supportbegäran i Azure Portal](../database/quota-increase-request.md) beroende på typ av prenumerations typ:

- **Under näts gräns**: det maximala antalet undernät där instanser av SQL-hanterad instans distribueras i en enda region.
- **vCore-enhets gräns**: det maximala antalet vCore-enheter som kan distribueras över alla instanser i en enda region. En GP-vCore använder en vCore-enhet och en BC-vCore tar 4 vCore-enheter. Det totala antalet instanser är inte begränsat så länge det ligger inom gränsen för vCore-enheten.

> [!Note]
> Dessa gränser är standardinställningar och inte tekniska begränsningar. Gränserna kan ökas på begäran genom att en särskild [supportbegäran skapas i Azure Portal](../database/quota-increase-request.md) om du behöver fler instanser i den aktuella regionen. Alternativt kan du skapa nya instanser av SQL-hanterad instans i en annan Azure-region utan att skicka support förfrågningar.

Följande tabell visar de **regionala standard gränserna** för prenumerations typer som stöds (standard gränser kan utökas med hjälp av en support förfrågan som beskrivs nedan):

|Prenumerationstyp| Maximalt antal SQL-hanterade instans under nät | Max antal vCore-enheter * |
| :---| :--- | :--- |
|Betala per användning|3|320|
|CSP |8 (15 i vissa regioner * *)|960 (1440 i vissa regioner * *)|
|Betala per användning – utveckling/testning|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 i vissa regioner * *)|960 (1440 i vissa regioner * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional och MSDN-plattformar|2|32|

\* Vid planering av distributioner bör du tänka på att Affärskritisk (BC) tjänst nivån kräver fyra (4) gånger mer vCore-kapacitet än Generell användning (GP). Exempel: 1 GP-vCore = 1 vCore-enhet och 1 BC-vCore = 4 vCore-enheter. För att förenkla förbruknings analysen mot standard gränserna, sammanfatta vCore-enheterna över alla undernät i den region där SQL-hanterad instans distribueras och jämför resultaten med instans enhets gränserna för din prenumerations typ. Gränsen **för antalet vCore-enheter** gäller för varje prenumeration i en region. Det finns ingen gräns per enskilda undernät förutom att summan av alla virtuella kärnor som distribueras över flera undernät måste vara lägre eller lika med **Max antalet vCore-enheter**.

\*\* Större gränser för undernät och vCore är tillgängliga i följande regioner: östra Australien, östra USA, östra USA 2, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Västeuropa, västra USA 2.

> [!IMPORTANT]
> Om gränsen för vCore och under nätet är 0, innebär det att den regionala gränsen för din prenumerations typ inte har angetts. Du kan också använda kvoten för att öka begäran om att få åtkomst till prenumerationen i en bestämd region enligt samma procedur – som anger nödvändiga vCore-och under näts värden.

## <a name="request-a-quota-increase"></a>Begär en kvot ökning

Om du behöver fler instanser i dina aktuella regioner skickar du en supportbegäran för att utöka kvoten med hjälp av Azure Portal. Mer information finns i [begäran om kvot ökning för Azure SQL Database](../database/quota-increase-request.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om SQL-hanterad instans finns i [Vad är en SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- Pris information finns i [priser för SQL-hanterad instans](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Information om hur du skapar din första SQL-hanterade instans finns i [snabb starts guiden](instance-create-quickstart.md).
