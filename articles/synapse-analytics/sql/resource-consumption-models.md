---
title: Synapse för SQL-resursförbrukning
description: Lär dig mer om Synapse SQL-förbruknings modeller i Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 03144d478be0053ac77709132b08cdf17b062fb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666294"
---
# <a name="synapse-sql-resource-consumption"></a>Synapse för SQL-resursförbrukning

Den här artikeln beskriver resurs förbruknings modeller för Synapse SQL.

## <a name="serverless-sql-pool"></a>Serverlös SQL-pool

SQL-poolen utan server är en betal tjänst per fråga som inte kräver att du väljer rätt storlek. Systemet justeras automatiskt baserat på dina krav, vilket frigör dig från att hantera infrastrukturen och välja rätt storlek för din lösning.

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Dedikerad SQL-pool – informations lager enheter (DWU: er) och beräknings data lager enheter (cDWUs)

Rekommendationer för att välja det idealiska antalet informations lager enheter (DWU: er) för att optimera pris och prestanda och hur du ändrar antalet enheter.

### <a name="data-warehouse-units"></a>Informationslagerenheter

En Synapse SQL-pool representerar en samling analys resurser som tillhandahålls. Analys resurser definieras som en kombination av CPU, minne och IO. Dessa tre resurser paketeras i enheter med beräknings skala som kallas informations lager enheter (DWU: er). En DWU representerar ett abstrakt, normaliserat mått för beräkningsresurser och prestanda. En ändring av Service nivån ändrar antalet DWU: er som är tillgängliga för systemet. I sin tur justerar den här ändringen systemets prestanda och kostnad.

För högre prestanda kan du öka antalet informations lager enheter. Minska data lagrets enheter för mindre prestanda. Lagrings- och beräkningskostnader faktureras separat, så ändringar av informationslagerenheter påverkar inte lagringskostnaderna.

Prestanda för informations lager enheter baseras på dessa data lager arbets belastnings mått:

- Hur snabbt en standard data lager fråga kan genomsöka ett stort antal rader och sedan utföra en komplex agg regering. Den här åtgärden är I/O och processor intensiv.
- Hur snabbt data lagret kan mata in data från Azure Storage blobbar eller Azure Data Lake. Den här åtgärden är nätverks-och processor intensiv.
- Hur snabbt [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) T-SQL-kommandot kan kopiera en tabell. Den här åtgärden innebär att läsa data från lagring, distribuera dem mellan noderna i enheten och skriva till lagrings utrymme igen. Den här åtgärden är CPU, i/o och nätverks intensiv.

Ökande DWU: er:

- Ändrar systemets prestanda linjärt för genomsökningar, agg regeringar och CTAS-uttryck
- Ökar antalet läsare och skribenter för PolyBase-inläsnings åtgärder
- Ökar det maximala antalet samtidiga frågor och samtidiga platser.

### <a name="service-level-objective"></a>Servicenivåmål

Service nivå målet (service nivå målet) är inställningen för skalbarhet som avgör data lagrets kostnad och prestanda nivå. Service nivåerna för Gen2 mäts i beräknings data lager enheter (cDWU), till exempel DW2000c. Gen1 service nivåer mäts i DWU: er, till exempel DW2000.

Service nivå målet (service nivå målet) är inställningen för skalbarhet som avgör data lagrets kostnad och prestanda nivå. Tjänst nivåerna för Gen2 dedikerade SQL-poolen mäts i informations lager enheter (DWU), till exempel DW2000c.

> [!NOTE]
> Azure Synapse Analytics Gen2 nyligen tillagda ytterligare skalnings funktioner som stöd för beräknings nivåer så lågt som 100 cDWU. Befintliga data lager för närvarande på gen1 som kräver lägre beräknings nivåer kan nu uppgraderas till Gen2 i de regioner som för närvarande är tillgängliga utan extra kostnad.  Om din region inte stöds ännu kan du fortfarande uppgradera till en region som stöds. Mer information finns i [Uppgradera till Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

I T-SQL bestämmer inställningen för SERVICE_OBJECTIVE tjänst nivå och prestanda nivå för din dedikerade SQL-pool.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Prestanda nivåer och informations lager enheter

Varje prestanda nivå använder en något annorlunda mått enhet för sina informations lager enheter. Den här skillnaden visas på fakturan eftersom enhets skalan direkt översätts till fakturering.

- Gen1 informations lager mäts i informations lager enheter (DWU: er).
- Gen2-datalager mäts i data lager enheter för beräkning (cDWUs).

Både DWU: er och cDWUs har stöd för att skala upp eller ned och pausa beräkningen när du inte behöver använda data lagret. De här åtgärderna är alla på begäran. Gen2 använder en lokal diskbaserad cache på Compute-noderna för att förbättra prestandan. När du skalar eller pausar systemet blir cacheminnet inställt och så en period av cache-uppvärmning krävs innan optimala prestanda uppnås.  

När du ökar informations lager enheter ökar du linjärt data bearbetnings resurser. Gen2 tillhandahåller bästa prestanda för frågor och högsta skalning. Gen2-system gör också den mest använda cachen.

#### <a name="capacity-limits"></a>Kapacitetsbegränsningar

Varje SQL Server (till exempel myserver.database.windows.net) har en kvot för [databas transaktions enhet (DTU)](../../azure-sql/database/service-tiers-dtu.md) som tillåter ett angivet antal informations lager enheter. Mer information finns i [kapacitets gränser för arbets belastnings hantering](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>Utvärdera antalet data lager enheter som du behöver

Det idealiska antalet data lager enheter är beroende av arbets belastningen och mängden data som du har läst in i systemet.

Steg för att hitta den bästa DWU för din arbets belastning:

1. Börja med att välja en mindre DWU.
2. Övervaka program prestanda när du testar data inläsningar i systemet och som beaktar antalet DWU: er som valts jämfört med den prestanda du har.
3. Identifiera eventuella ytterligare krav för periodiska perioder med hög belastnings aktivitet. Arbets belastningar som visar betydande toppar och troughs i aktivitet kan behöva skalas ofta.

SQL-poolen är ett skalbart system som kan etablera stora mängder data bearbetning och fråga betydande mängder data. Om du vill se de verkliga funktionerna för skalning, särskilt vid större DWU: er, rekommenderar vi att du skalar data uppsättningen när du skalar för att säkerställa att du har tillräckligt med data för att kunna mata in processorerna. För skalnings testning rekommenderar vi att du använder minst 1 TB.

> [!NOTE]
>
> Frågans prestanda ökar bara med fler parallellisering om arbetet kan delas mellan datornoder. Om du upptäcker att skalning inte ändrar prestandan kan du behöva justera tabell designen och/eller dina frågor. Anvisningar om hur du ställer frågor finns i [hantera användar frågor](../overview-terminology.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

### <a name="permissions"></a>Behörigheter

Om du ändrar data lagrets enheter krävs de behörigheter som beskrivs i [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true).

Inbyggda Azure-roller som SQL DB-deltagare och SQL Server deltagare kan ändra DWU-inställningar.

#### <a name="view-current-dwu-settings"></a>Visa aktuella inställningar för DWU

Så här visar du den aktuella DWU-inställningen:

1. Öppna SQL Server Object Explorer i Visual Studio.
2. Anslut till huvud databasen som är kopplad till den logiska SQL-servern.
3. Välj från vyn sys.database_service_objectives dynamisk hantering. Här är ett exempel:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Ändra informations lager enheter

#### <a name="azure-portal"></a>Azure Portal

Ändra DWU: er:

1. Öppna [Azure Portal](https://portal.azure.com), öppna databasen och välj **skala**.

2. Under **skala** flyttar du skjutreglaget åt vänster eller höger för att ändra DWU-inställningen.

3. Välj **Spara**. Ett bekräftelsemeddelande visas. Välj **Ja** för att bekräfta eller **Nej** för att avbryta.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ändra DWU: er använder du PowerShell-cmdleten [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . I följande exempel anges service nivå målet till DW1000 för databasen MySQLDW som finns på Server-servern.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Mer information finns i [PowerShell-cmdletar för Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

Med T-SQL kan du Visa den aktuella DWUsettings, ändra inställningarna och kontrol lera förloppet.

Så här ändrar du DWU: er:

1. Anslut till huvud databasen som är associerad med servern.
2. Använd instruktionen [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) tsql. I följande exempel anges service nivå målet till DW1000c för databasen MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST API:er

Om du vill ändra DWU: er använder du REST API [skapa eller uppdatera databas](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) . I följande exempel anges service nivå målet till DW1000c för databasen MySQLDW, som finns på Server-servern. Servern finns i en Azure-resurs grupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Fler REST APIs exempel finns i [REST-API: er för Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Kontrol lera status för DWU-ändringar

DWU ändringar kan ta flera minuter att slutföra. Om du skalar automatiskt bör du överväga att implementera logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd.

Genom att kontrol lera databas statusen via olika slut punkter kan du implementera Automation korrekt. Portalen visar ett meddelande när en åtgärd har slutförts och databasens aktuella tillstånd, men tillåter inte program kontroll av status.

Du kan inte kontrol lera databasens tillstånd för skalnings åtgärder med Azure Portal.

Så här kontrollerar du status för DWU-ändringar:

1. Anslut till huvud databasen som är associerad med servern.
2. Skicka följande fråga för att kontrol lera databasens tillstånd.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Skicka följande fråga för att kontrol lera status för åtgärden

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

I denna DMV returneras information om olika hanterings åtgärder på din dedikerade SQL-pool, till exempel åtgärden och status för åtgärden, som antingen IN_PROGRESS eller har SLUTFÖRts.

### <a name="the-scaling-workflow"></a>Arbets flödet för skalning

När du startar en skalnings åtgärd omsorg systemet först alla öppna sessioner, vilket återställer eventuella öppna transaktioner för att säkerställa ett konsekvent tillstånd. För skalnings åtgärder sker skalningen endast efter att den här transaktions återställningen har slutförts.  

- För en skalnings åtgärd tar systemet bort alla datornoder, etablerar ytterligare datornoder och återansluter sedan till lagrings skiktet.
- För en nedskalning åtgärd tar systemet bort alla datornoder och återansluter sedan bara de noder som behövs till lagrings lagret.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar prestanda finns i [resurs klasser för hantering av arbets belastning](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) och [minnes-och samtidiga gränser](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).