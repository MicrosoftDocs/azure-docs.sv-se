---
title: Kapacitets begränsningar för dedikerad SQL-pool
description: Högsta tillåtna värden för olika komponenter i dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d778844fee8cad9359532ffa23e177bf7b13c4b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117698"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Kapacitets begränsningar för dedikerad SQL-pool i Azure Synapse Analytics

Högsta tillåtna värden för olika komponenter i dedikerad SQL-pool i Azure Synapse Analytics.

## <a name="workload-management"></a>Arbetsbelastningshantering

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| [Informations lager enheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max DWU för en enskild dedikerad SQL-pool  | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Informations lager enheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standard-DTU per server |54 000<br></br>Varje SQL Server (t. ex. myserver.database.windows.net) har som standard en DTU-kvot på 54 000, vilket ger upp till DW6000c. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka kvoten genom att [skapa ett support ärende](sql-data-warehouse-get-started-create-support-ticket.md) och välja *kvot* som typ av begäran.  För att beräkna dina DTU-behov multiplicerar du 7,5 med den totala DWU som krävs, eller multiplicerar 9 med den totala cDWU som behövs. Exempel:<br></br>DW6000 x 7,5 = 45 000 DTU: er<br></br>DW7500c x 9 = 67 500 DTU: er.<br></br>Du kan visa den aktuella DTU-förbrukningen från SQL Server-alternativet i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. |
| Databas anslutning |Maximalt antal samtidiga öppna sessioner |1024<br/><br/>Antalet samtidiga öppna sessioner varierar beroende på den valda DWU. DWU600c och högre stöder högst 1024 öppna sessioner. DWU500c och nedan stöder maximalt antal samtidiga sessioner av öppen session på 512. Obs! det finns gränser för hur många frågor som kan köras samtidigt. Om samtidigheten överskrids hamnar begäran i en intern kö där den väntar på att bearbetas. |
| Databas anslutning |Maximalt minne för för beredda uttryck |20 MB |
| [Arbetsbelastningshantering](resource-classes-for-workload-management.md) |Maximalt antal samtidiga frågor |128<br/><br/>  Högst 128 samtidiga frågor kommer att köras och återstående frågor placeras i kö.<br/><br/>Antalet samtidiga frågor kan minska när användare tilldelas högre resurs klasser eller när inställningen för [data lagrets enhet](memory-concurrency-limits.md) sänks. Vissa frågor, som DMV-frågor, tillåts alltid att köras och påverkar inte gränsen för samtidiga frågor. Mer information om körning av samtidiga frågor finns i artikeln om [högsta antal](memory-concurrency-limits.md) samtidiga frågor. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximalt GB |399 GB per DW100c. I DWU1000c är tempdb storleken 3,99 TB. |
||||

## <a name="database-objects"></a>Databas objekt

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Databas |Max storlek | Gen1:240 TB komprimerat på disk. Detta utrymme är oberoende av tempdb eller logg utrymme, och därför är det här utrymmet dedikerat för permanenta tabeller.  Komprimering av klustrad columnstore uppskattas till 5X.  Med den här komprimeringen kan databasen växa till ungefär 1 PB när alla tabeller är grupperade columnstore (standard tabell typen). <br/><br/> Gen2: obegränsad lagring för columnstore-tabeller.  Rowstore delen av databasen är fortfarande begränsad till 240 TB komprimerat på disk. |
| Tabell |Max storlek |Obegränsad storlek för columnstore-tabeller. <br>60 TB för rowstore tabeller komprimerade på disk. |
| Tabell |Tabeller per databas | 100 000 |
| Tabell |Kolumner per tabell |1024 kolumner |
| Tabell |Byte per kolumn |Beroende av kolumnens [datatyp](sql-data-warehouse-tables-data-types.md). Limit är 8000 för char-datatyper, 4000 för nvarchar eller 2 GB för högsta antal data typer. |
| Tabell |Byte per rad, definierad storlek |8060 byte<br/><br/>Antalet byte per rad beräknas på samma sätt som för SQL Server med sid komprimering. Precis som SQL Server stöds rad spill i lagring, vilket gör att **kolumner med varierande längd** kan flyttas bort från rad. När rader med varierande längd flyttas från-raden, lagras bara 24 byte-roten i huvud posten. Mer information finns i [rad spills data som överstiger 8 KB](/previous-versions/sql/sql-server-2008-r2/ms186981(v=sql.105)). |
| Tabell |Partitioner per tabell |15 000<br/><br/>För hög prestanda rekommenderar vi att du minimerar antalet partitioner som du behöver samtidigt som du fortfarande har stöd för dina affärs behov. Allteftersom antalet partitioner växer växer och ger sämre prestanda när det gäller DDL (Data Definition Language) och DML-åtgärder (Data Manipulation Language). |
| Tabell |Avgränsnings värde för tecken per partition. |4000 |
| Index |Icke-grupperade index per tabell. |50<br/><br/>Gäller endast för rowstore-tabeller. |
| Index |Grupperade index per tabell. |1<br><br/>Gäller både rowstore-och columnstore-tabeller. |
| Index |Index nyckel storlek. |900 byte.<br/><br/>Gäller endast för rowstore-index.<br/><br/>Index på varchar-kolumner med en maximal storlek på fler än 900 byte kan skapas om befintliga data i kolumnerna inte överskrider 900 byte när indexet skapas. Du kan dock infoga eller uppdatera åtgärder i kolumner som orsakar den totala storleken till överskrida 900 byte. |
| Index |Nyckel kolumner per index. |16<br/><br/>Gäller endast för rowstore-index. Grupperade columnstore-index inkluderar alla kolumner. |
| Statistik |Storlek på kombinerade kolumn värden. |900 byte. |
| Statistik |Kolumner per statistik objekt. |32 |
| Statistik |Statistik som skapats på kolumner per tabell. |30 000 |
| Lagrade procedurer |Maximala kapslings nivåer. |8 |
| Visa |Kolumner per vy |1 024 |
||||

## <a name="loads"></a>Övriga

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| PolyBase-belastningar |MB per rad |1<br/><br/>PolyBase läser in rader som är mindre än 1 MB. Det går inte att läsa in LOB-datatyper i tabeller med ett grupperat columnstore-index (CCI).<br/> |
|PolyBase-belastningar|Totalt antal filer|1,000,000<br/><br/>PolyBase-inläsningar får inte överstiga 1 miljon filer. Följande fel kan uppstå: **åtgärden misslyckades eftersom delat antal överskrider den övre gränsen på 1000000**.|

## <a name="queries"></a>Frågor

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Fråga |Köade frågor för användar tabeller. |1000 |
| Fråga |Samtidiga frågor i systemvyer. |100 |
| Fråga |Köade frågor om systemvyer |1000 |
| Fråga |Maximalt antal parametrar |2098 |
| Batch |Maximal storlek |65536 * 4096 |
| Välj resultat |Kolumner per rad |4096<br/><br/>Du kan aldrig ha fler än 4096 kolumner per rad i Välj resultat. Det finns ingen garanti för att du alltid kan ha 4096. Om din frågeplan kräver en temporär tabell, kan kolumnerna 1024 per tabell gälla. |
| SELECT |Kapslade under frågor |32<br/><br/>Du kan aldrig ha fler än 32 kapslade under frågor i en SELECT-instruktion. Det finns ingen garanti för att du alltid kan ha 32. Till exempel kan en koppling introducera en under fråga i frågeuttrycket. Antalet under frågor kan också begränsas av tillgängligt minne. |
| SELECT |Kolumner per koppling |1024 kolumner<br/><br/>Du kan aldrig ha fler än 1024 kolumner i kopplingen. Det finns ingen garanti för att du alltid kan ha 1024. Om JOIN-planen kräver en temporär tabell med fler kolumner än KOPPLINGs resultatet gäller gränsen 1024 för den temporära tabellen. |
| SELECT |Byte per grupp efter kolumner. |8060<br/><br/>Kolumnerna i GROUP BY-satsen får innehålla högst 8060 byte. |
| SELECT |Byte per sortering efter kolumner |8060 byte<br/><br/>Kolumnerna i ORDER BY-satsen får innehålla högst 8060 byte |
| Identifierare per instruktion |Antalet refererade identifierare |65 535<br/><br/> Antalet identifierare som kan ingå i ett enda uttryck i en fråga är begränsat. Om antalet överskrids i SQL Server fel 8632. Mer information finns i [internt fel: en gräns för uttrycks tjänster har nåtts](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Strängliteraler | Antal sträng litteraler i en instruktion | 20 000 <br/><br/>Antalet sträng konstanter i ett enda uttryck i en fråga är begränsat. Om antalet överskrids i SQL Server fel 8632.|
||||

## <a name="metadata"></a>Metadata

| System visning | Maximalt antal rader |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Totalt antal DMS-arbetare för de senaste 1000 SQL-begärandena. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Totalt antal steg för de senaste 1000 SQL-begäranden som lagras i sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |De senaste 1000 SQL-begäranden som lagras i sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Nästa steg

Rekommendationer om hur du använder Azure Synapse finns i [lathund-bladet](cheat-sheet.md).