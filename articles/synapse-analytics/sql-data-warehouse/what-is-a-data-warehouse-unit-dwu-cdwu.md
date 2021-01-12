---
title: 'Informations lager enheter (DWU: er) för dedikerad SQL-pool (tidigare SQL DW)'
description: 'Rekommendationer för att välja det idealiska antalet informations lager enheter (DWU: er) för att optimera pris och prestanda och hur du ändrar antalet enheter.'
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5b33f10a0cb969d5fc0118eee0be371929f918a9
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117651"
---
# <a name="data-warehouse-units-dwus-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Informations lager enheter (DWU: er) för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

Rekommendationer för att välja det idealiska antalet informations lager enheter (DWU: er) för att optimera pris och prestanda och hur du ändrar antalet enheter.

## <a name="what-are-data-warehouse-units"></a>Vad är informations lager enheter

En [dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-overview-what-is.md) representerar en samling av analys resurser som är etablerade. Analys resurser definieras som en kombination av CPU, minne och IO.

Dessa tre resurser paketeras i enheter med beräknings skala som kallas informations lager enheter (DWU: er). En DWU representerar ett abstrakt, normaliserat mått för beräkningsresurser och prestanda.

En ändring av Service nivån ändrar antalet DWU: er som är tillgängliga för systemet, vilket i sin tur justerar prestandan och kostnaden för systemet.

För högre prestanda kan du öka antalet informations lager enheter. Minska data lagrets enheter för mindre prestanda. Lagrings- och beräkningskostnader faktureras separat, så ändringar av informationslagerenheter påverkar inte lagringskostnaderna.

Prestanda för informations lager enheter baseras på dessa data lager arbets belastnings mått:

- Hur snabbt en dedikerad SQL-pool (tidigare SQL DW) kan genomsöka ett stort antal rader och sedan utföra en komplex agg regering. Den här åtgärden är I/O och processor intensiv.
- Hur snabbt den dedikerade SQL-poolen (tidigare SQL DW) kan mata in data från Azure Storage blobbar eller Azure Data Lake. Den här åtgärden är nätverks-och processor intensiv.
- Hur snabbt [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-kommandot kan kopiera en tabell. Den här åtgärden innebär att läsa data från lagring, distribuera dem mellan noderna i enheten och skriva till lagrings utrymme igen. Den här åtgärden är CPU, i/o och nätverks intensiv.

Ökande DWU: er:

- Ändrar systemets prestanda linjärt för genomsökningar, agg regeringar och CTAS-uttryck
- Ökar antalet läsare och skribenter för PolyBase-inläsnings åtgärder
- Ökar det maximala antalet samtidiga frågor och samtidiga platser.

## <a name="service-level-objective"></a>Servicenivåmål

Service nivå målet (service nivå målet) är inställningen för skalbarhet som avgör data lagrets kostnad och prestanda nivå. Service nivåerna för Gen2 mäts i beräknings data lager enheter (cDWU), till exempel DW2000c. Gen1 service nivåer mäts i DWU: er, till exempel DW2000.

Service nivå målet (service nivå målet) är den skalbarhets inställning som avgör den dedikerade SQL-poolens kostnad och prestanda nivå (tidigare SQL DW). Tjänst nivåerna för Gen2-dedikerad SQL-pool (tidigare SQL DW) mäts i informations lager enheter (DWU), till exempel DW2000c.

> [!NOTE]
> Dedikerad SQL-pool (tidigare SQL DW) Gen2 nyligen tillagda ytterligare skalnings funktioner som stöd för beräknings nivåer så lågt som 100 cDWU. Befintliga data lager för närvarande på gen1 som kräver lägre beräknings nivåer kan nu uppgraderas till Gen2 i de regioner som för närvarande är tillgängliga utan extra kostnad.  Om din region inte stöds ännu kan du fortfarande uppgradera till en region som stöds. Mer information finns i [Uppgradera till Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

I T-SQL bestämmer inställningen för SERVICE_OBJECTIVE tjänst nivå och prestanda nivå för din dedikerade SQL-pool (tidigare SQL DW).

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Prestanda nivåer och informations lager enheter

Varje prestanda nivå använder en något annorlunda mått enhet för sina informations lager enheter. Den här skillnaden visas på fakturan eftersom enhets skalan direkt översätts till fakturering.

- Gen1 informations lager mäts i informations lager enheter (DWU: er).
- Gen2-datalager mäts i data lager enheter för beräkning (cDWUs).

Både DWU: er och cDWUs har stöd för att skala upp eller ned och pausa beräkningen när du inte behöver använda data lagret. De här åtgärderna är alla på begäran. Gen2 använder en lokal diskbaserad cache på Compute-noderna för att förbättra prestandan. När du skalar eller pausar systemet blir cacheminnet inställt och så en period av cache-uppvärmning krävs innan optimala prestanda uppnås.  

Varje SQL Server (till exempel myserver.database.windows.net) har en kvot för [databas transaktions enhet (DTU)](../../azure-sql/database/service-tiers-dtu.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) som tillåter ett angivet antal informations lager enheter. Mer information finns i [kapacitets gränser för arbets belastnings hantering](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="capacity-limits"></a>Kapacitetsbegränsningar

Varje SQL Server (till exempel myserver.database.windows.net) har en kvot för [databas transaktions enhet (DTU)](../../azure-sql/database/service-tiers-dtu.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) som tillåter ett angivet antal informations lager enheter. Mer information finns i [kapacitets gränser för arbets belastnings hantering](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Hur många data lager enheter behöver jag

Det idealiska antalet data lager enheter är beroende av arbets belastningen och mängden data som du har läst in i systemet.

Steg för att hitta den bästa DWU för din arbets belastning:

1. Börja med att välja en mindre DWU.
2. Övervaka program prestanda när du testar data inläsningar i systemet och som beaktar antalet DWU: er som valts jämfört med den prestanda du har.
3. Identifiera eventuella ytterligare krav för periodiska perioder med hög belastnings aktivitet. Arbets belastningar som visar betydande toppar och troughs i aktivitet kan behöva skalas ofta.

Dedikerad SQL-pool (tidigare SQL DW) är ett skalbart system som kan etablera stora mängder data bearbetning och fråga betydande mängder data.

Om du vill se de verkliga funktionerna för skalning, särskilt vid större DWU: er, rekommenderar vi att du skalar data uppsättningen när du skalar för att säkerställa att du har tillräckligt med data för att kunna mata in processorerna. För skalnings testning rekommenderar vi att du använder minst 1 TB.

> [!NOTE]
>
> Frågans prestanda ökar bara med fler parallellisering om arbetet kan delas mellan datornoder. Om du upptäcker att skalning inte ändrar prestandan kan du behöva justera tabell designen och/eller dina frågor. Anvisningar om hur du ställer frågor finns i [hantera användar frågor](cheat-sheet.md).

## <a name="permissions"></a>Behörigheter

Om du ändrar data lagrets enheter krävs de behörigheter som beskrivs i [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Inbyggda Azure-roller som SQL DB-deltagare och SQL Server deltagare kan ändra DWU-inställningar.

## <a name="view-current-dwu-settings"></a>Visa aktuella inställningar för DWU

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

## <a name="change-data-warehouse-units"></a>Ändra informations lager enheter

### <a name="azure-portal"></a>Azure Portal

Ändra DWU: er:

1. Öppna [Azure Portal](https://portal.azure.com), öppna databasen och klicka på **skala**.

2. Under **skala** flyttar du skjutreglaget åt vänster eller höger för att ändra DWU-inställningen.

3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Klicka på **Ja** för att bekräfta eller **Nej** för att avbryta.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ändra DWU: er använder du PowerShell-cmdleten [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . I följande exempel anges service nivå målet till DW1000 för databasen MySQLDW som finns på Server-servern.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Mer information finns i [PowerShell-cmdletar för dedikerad SQL-pool (tidigare SQL DW)](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="t-sql"></a>T-SQL

Med T-SQL kan du Visa den aktuella DWUsettings, ändra inställningarna och kontrol lera förloppet.

Så här ändrar du DWU: er:

1. Anslut till huvud databasen som är associerad med servern.
2. Använd instruktionen [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) tsql. I följande exempel anges service nivå målet till DW1000c för databasen MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API:er

Om du vill ändra DWU: er använder du REST API [skapa eller uppdatera databas](/rest/api/sql/databases/createorupdate) . I följande exempel anges service nivå målet till DW1000c för databasen MySQLDW, som finns på Server-servern. Servern finns i en Azure-resurs grupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": "DW1000c"
    }
}
```

Fler REST APIs exempel finns i [REST-API: er för dedikerad SQL-pool (tidigare SQL DW)](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="check-status-of-dwu-changes"></a>Kontrol lera status för DWU-ändringar

DWU ändringar kan ta flera minuter att slutföra. Om du skalar automatiskt bör du överväga att implementera logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd.

Genom att kontrol lera databas statusen via olika slut punkter kan du implementera Automation korrekt. Portalen visar ett meddelande när en åtgärd har slutförts och databasens aktuella tillstånd, men tillåter inte program kontroll av status.

Det går inte att kontrol lera databasens tillstånd för skalnings åtgärder med Azure Portal.

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

Denna DMV returnerar information om olika hanterings åtgärder i din dedikerade SQL-pool (tidigare SQL DW), till exempel åtgärden och status för åtgärden, som antingen IN_PROGRESS eller har SLUTFÖRts.

## <a name="the-scaling-workflow"></a>Arbets flödet för skalning

När du startar en skalnings åtgärd omsorg systemet först alla öppna sessioner, vilket återställer eventuella öppna transaktioner för att säkerställa ett konsekvent tillstånd. För skalnings åtgärder sker skalningen endast efter att den här transaktions återställningen har slutförts.  

- För en skalnings åtgärd tar systemet bort alla datornoder, etablerar ytterligare datornoder och återansluter sedan till lagrings skiktet.
- För en nedskalning åtgärd tar systemet bort alla datornoder och återansluter sedan bara de noder som behövs till lagrings lagret.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar prestanda finns i [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md) och [minnes-och samtidiga gränser](memory-concurrency-limits.md).