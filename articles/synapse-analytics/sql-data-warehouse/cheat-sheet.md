---
title: Lathund-blad för dedikerad SQL-pool (tidigare SQL DW)
description: Hitta länkar och bästa metoder för att snabbt bygga din dedikerade SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: cc54ae66cda6bf8ecde07d1830448ec39a15cc29
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120231"
---
# <a name="cheat-sheet-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytic"></a>Lathund-blad för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse-analys

Det här lathund-bladet innehåller användbara tips och bästa metoder för att skapa dedikerade SQL-pooler (tidigare SQL DW).

Följande bild visar processen för att utforma ett informations lager med dedikerad SQL-pool (tidigare SQL DW):

![Skiss](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Frågor och åtgärder i tabeller

När du vet i förväg vilka primära åtgärder och frågor som ska köras i ditt informationslager kan du prioritera arkitekturen för ditt informationslager för de åtgärderna. Dessa frågor och åtgärder kan innehålla:

* Anslutning till en eller två faktatabeller med dimensionstabeller, filtrering av den kombinerade tabellen och sedan bifogning av resultatet i en data mart.
* Gör stora eller små uppdateringar i din försäljning.
* Lägger endast till data i dina tabeller.

När du vet åtgärdstyperna i förväg kan du optimera tabellernas design.

## <a name="data-migration"></a>Datamigrering

Börja med att läsa in data i [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eller Azure Blob Storage. Använd sedan [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att läsa in dina data i mellanlagrings tabeller. Använd följande konfiguration:

| Design | Rekommendation |
|:--- |:--- |
| Distribution | Resursallokering (round robin) |
| Indexering | Heap |
| Partitionering | Ingen |
| Resursklass | largerc eller xlargerc |

Läs mer om [datamigrering](/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice), [datainläsning](design-elt-data-loading.md) och [ELT-processen (Extract, Load, and Transform)](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Distribuerade eller replikerade tabeller

Använd följande strategier, beroende på tabellens egenskaper:

| Typ | Passar bra för...| Se upp om...|
|:--- |:--- |:--- |
| Replikerad | * Små dimensions tabeller i ett stjärn schema med mindre än 2 GB lagring efter komprimering (~ 5x komprimering) |* Många Skriv transaktioner finns i tabellen (till exempel INSERT, upsert, DELETE, Update)<br></br>* Du ändrar DWU-etableringen (Data Warehouse units) ofta<br></br>* Du använder bara 2-3 kolumner men din tabell har många kolumner<br></br>* Du indexerar en replikerad tabell |
| Resursallokering (standard) | * Tillfällig/uppsamlings tabell<br></br> * Ingen uppenbar kopplings nyckel eller en bra kandidat kolumn |* Prestanda är långsam på grund av data förflyttning |
| Hash | * Fakta tabeller<br></br>* Stora dimensions tabeller |* Det går inte att uppdatera distributions nyckeln |

**Tips**

* Börja med resursallokering, men försök att skapa en hashdistributionsstrategi för att dra nytta av en massiv parallell arkitektur.
* Se till att vanliga hash-nycklar har samma dataformat.
* Distribuera inte i varchar-format.
* Dimensionstabeller med en vanlig hash-nyckel för en faktatabell med många kopplingsåtgärder kan hash-distribueras.
* Använd *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* för att analysera eventuella snedställningar i dina data.
* Använd *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* för att analysera data förflyttningar bakom frågor, övervaka tids sändningen och blanda åtgärder. Det är praktiskt när du granskar din distributionsstrategi.

Läs mer om [replikerade tabeller](design-guidance-for-replicated-tables.md) och [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Indexera tabellen

Indexering är bra när du vill läsa tabeller snabbt. Det finns en unik uppsättning tekniker som du kan använda utifrån dina behov:

| Typ | Passar bra för... | Se upp om...|
|:--- |:--- |:--- |
| Heap | * Mellanlagring/tillfällig tabell<br></br>* Små tabeller med små sökningar |* Sökningen genomsöker hela tabellen |
| Grupperat index | * Tabeller med upp till 100 000 000 rader<br></br>* Stora tabeller (mer än 100 000 000 rader) med endast 1-2 kolumner används kraftigt |* Används i en replikerad tabell<br></br>* Du har komplexa frågor som omfattar flera kopplingar och gruppering efter åtgärder<br></br>* Du gör uppdateringar på indexerade kolumner: det tar minne |
| Grupperade columnstore-index (kolumnlagringsindex) (standard) | * Stora tabeller (mer än 100 000 000 rader) | * Används i en replikerad tabell<br></br>* Du gör enorma uppdaterings åtgärder i din tabell<br></br>* Du överpartitionerar tabellen: rad grupper sträcker sig inte över olika distributionsmappar och partitioner |

**Tips**

* Förutom ett grupperat index kanske du vill lägga till ett icke-grupperat index till en kolumn som används ofta för filtrering.
* Var försiktig med hur du hanterar minnet i en tabell med CCI. När du läser in data ska användaren (eller frågan) dra nytta av en stor resursklass. Undvik att trimma och skapa många små komprimerade radgrupper.
* På Gen2 cachelagras CCI-tabeller lokalt på datornoderna för att maximera prestanda.
* För CCI kan dåliga prestanda uppstå på grund av dålig komprimering av dina radgrupper. Om det sker ska du återskapa eller ordna om din CCI. Du bör ha minst 100 000 rader per komprimerad radgrupp. Det bästa är 1 miljon rader i en radgrupp.
* Baserat på den stegvisa frekvensen och storleken vill du automatisera när du ordnar om eller återskapar dina index. En vårstädning är aldrig fel.
* Var strategisk när du vill trimma en radgrupp. Hur stora är öppna radgrupper? Hur mycket data förväntar du dig att läsa in under de kommande dagarna?

Läs mer om [index](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Partitionering

Du kan partitionera tabellen när du har en stor faktatabell (fler än 1 miljard rader). I 99 procent av fallen ska partitionsnyckeln baseras på datum. Var noga så att du inte överpartitionerar, särskilt när du har ett grupperat kolumnlagringsindex.

Med mellanlagringstabeller som kräver ELT kan du dra nytta av partitionering. Det underlättar hanteringen av datalivscykeln.
Var noga så att du inte överpartitionerar dina data, särskilt när du har ett grupperat kolumnlagringsindex.

Läs mer om [partitioner](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Stegvis inläsning

Om du ska läsa in data stegvis ska du först kontrollera att du allokerar större resursklasser för att läsa in dina data.  Detta är särskilt viktigt när du läser in i tabeller med grupperade columnstore-index.  Se [resurs klasser](resource-classes-for-workload-management.md) för mer information.  

Vi rekommenderar att du använder PolyBase och ADF v2 för att automatisera dina ELT-pipeliner i ditt informations lager.

Överväg att använda en [CTAs](sql-data-warehouse-develop-ctas.md) för att skriva de data som du vill behålla i en tabell i stället för att använda Infoga, uppdatera och ta bort för en stor batch med uppdateringar i historiska data.

## <a name="maintain-statistics"></a>Underhålla statistik

Det är viktigt att uppdatera statistik när *viktiga* ändringar inträffar i dina data. Se [uppdaterings statistik](sql-data-warehouse-tables-statistics.md#update-statistics) för att avgöra om *betydande* ändringar har inträffat. Uppdaterad statistik optimera dina fråge planer. Om du tycker att det tar för lång tid att behålla all statistik kan du vara mer selektiv med vilka kolumner som ska ha statistik.

Du kan även definiera frekvensen för uppdateringarna. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. Du får ut mest genom att använda statistik med kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Läs mer om [statistik](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Resursklass

Resurs grupper används som ett sätt att allokera minne till frågor. Om du behöver mer minne för att förbättra hastigheten för frågor eller inläsning ska du allokera högre resursklasser. Å andra sidan påverkar användning av större klasser samtidigheten. Du bör överväga det innan du flyttar alla dina användare till en stor resursklass.

Om du märker att frågor tar för lång tid kan du kontrollera att dina användare inte körs i stora resursklasser. Stora resursklasser förbrukar många samtidighetsfack. De kan orsaka att andra frågor placeras i kö.

Slutligen, genom att använda Gen2 av [dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-overview-what-is.md)får varje resurs klass 2,5 gånger mer minne än gen1.

Lär dig mer om hur du arbetar med [resursklasser och samtidighet](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Sänk kostnaderna

En viktig funktion i Azure Synapse är möjligheten att [hantera beräknings resurser](sql-data-warehouse-manage-compute-overview.md). Du kan pausa din dedikerade SQL-pool (tidigare SQL DW) när du inte använder den, vilket stoppar faktureringen av beräknings resurser. Du kan skala resurser för att uppfylla dina prestandakrav. Om du vill pausa använder du [Azure-portalen](pause-and-resume-compute-portal.md) eller [PowerShell](pause-and-resume-compute-powershell.md). Använd [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md)eller en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)för att skala.

Autoskala nu för den tid du önskar med Azure Functions:

[![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

## <a name="optimize-your-architecture-for-performance"></a>Optimera din arkitektur för prestanda

Vi rekommenderar att du överväger SQL Database och Azure Analysis Services i en nav-och-eker-arkitektur. Den här lösningen kan isolera arbetsbelastningen mellan olika grupper av användare när du även använder avancerade funktioner från SQL Database och Azure Analysis Services. Det här är också ett sätt att tillhandahålla obegränsad samtidighet till dina användare.

Lär dig mer om [typiska arkitekturer som utnyttjar dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics](/archive/blogs/sqlcat/common-isv-application-patterns-using-azure-sql-data-warehouse).

Distribuera i ett genom att klicka på dina ekrar i SQL-databaser från dedikerad SQL-pool (tidigare SQL DW):

[![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json)