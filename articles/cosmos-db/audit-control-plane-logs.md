---
title: Granska Azure Cosmos DB kontroll Plans åtgärder
description: Lär dig hur du granskar kontroll Plans åtgärder som att lägga till en region, uppdatera data flöde, region växling vid fel, lägga till ett VNet osv. i Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 6f3e408343fc75d6587d1a67a0179edf13d56e36
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658256"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Granska Azure Cosmos DB kontroll Plans åtgärder
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Kontroll planet i Azure Cosmos DB är en RESTful-tjänst som gör det möjligt att utföra en mängd olika åtgärder på Azure Cosmos-kontot. Den visar en offentlig resurs modell (till exempel: databas, konto) och olika åtgärder för slutanvändarna för att utföra åtgärder i resurs modellen. Kontroll Plans åtgärderna omfattar ändringar i Azure Cosmos-kontot eller containern. Till exempel åtgärder som att skapa ett Azure Cosmos-konto, lägga till en region, uppdatera data flöde, regions växling vid fel, lägga till ett VNet osv. är några av kontroll Plans åtgärderna. Den här artikeln beskriver hur du granskar kontroll Plans åtgärder i Azure Cosmos DB. Du kan köra kontroll Plans åtgärder på Azure Cosmos-konton med hjälp av Azure CLI, PowerShell eller Azure Portal, medan du använder Azure CLI eller PowerShell för behållare.

Här följer några exempel på scenarier där gransknings kontroll Plans åtgärder är användbara:

* Du vill få en avisering när brand Väggs reglerna för ditt Azure Cosmos-konto ändras. Aviseringen krävs för att hitta otillåtna ändringar i regler som reglerar nätverks säkerheten för ditt Azure Cosmos-konto och vidta snabb åtgärder.

* Du vill få en avisering om en ny region läggs till eller tas bort från ditt Azure Cosmos-konto. Att lägga till eller ta bort regioner har konsekvenser för fakturerings-och data suveränitets krav. Den här aviseringen hjälper dig att identifiera en oavsiktlig tillägg eller borttagning av en region på ditt konto.

* Du vill ha mer information från diagnostikloggar på vad som har ändrats. Till exempel har ett VNet ändrats.

## <a name="disable-key-based-metadata-write-access"></a>Inaktivera nyckelbaserade metadata skriv åtkomst

Innan du granskar kontroll Plans åtgärderna i Azure Cosmos DB inaktiverar du den nyckelbaserade metadata-Skriv åtkomsten på ditt konto. När Key-baserade metadata skriv åtkomst är inaktive rad förhindras klienter som ansluter till Azure Cosmos-kontot via konto nycklar från åtkomst till kontot. Du kan inaktivera skriv åtkomst genom att ställa in `disableKeyBasedMetadataWriteAccess` egenskapen på True. När du har angett den här egenskapen kan ändringar av en resurs ske från en användare med rätt Azure-roll och-autentiseringsuppgifter. Mer information om hur du ställer in den här egenskapen finns i artikeln [förhindra ändringar från SDK](role-based-access-control.md#prevent-sdk-changes) : er. 

När `disableKeyBasedMetadataWriteAccess` är aktive rad, om SDK-baserade klienter kör Create eller Update-åtgärder, så *tillåts inte "ÅTGÄRDs post" på resursen "ContainerNameorDatabaseName" via Azure Cosmos DB slut punkten* returneras. Du måste aktivera åtkomst till sådana åtgärder för ditt konto eller utföra åtgärderna skapa/uppdatera via Azure Resource Manager, Azure CLI eller Azure PowerShell. Om du vill växla tillbaka anger du disableKeyBasedMetadataWriteAccess till **falskt** med hjälp av Azure CLI enligt beskrivningen i artikeln [förhindra ändringar från Cosmos SDK](role-based-access-control.md#prevent-sdk-changes) . Se till att ändra värdet `disableKeyBasedMetadataWriteAccess` till falskt i stället för sant.

Tänk på följande när du inaktiverar skriv åtkomst till metadata:

* Utvärdera och se till att dina program inte gör några metadata som ändrar ovanstående resurser (till exempel skapa samling, uppdaterings data flöde,...) med hjälp av SDK eller konto nycklar.

* För närvarande kommer Azure Portal att använda konto nycklar för metadata-åtgärder och därmed blockeras dessa åtgärder. Du kan också använda Azure CLI-, SDK-eller Resource Manager-mallens distributioner för att utföra sådana åtgärder.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Aktivera diagnostikloggar för kontroll Plans åtgärder

Du kan aktivera diagnostikloggar för kontroll Plans åtgärder med hjälp av Azure Portal. När den har Aktiver ATS registrerar diagnostikloggar åtgärden som ett par med start-och slut händelser med relevant information. *RegionFailoverStart* och *RegionFailoverComplete* kommer till exempel att slutföra redundansväxlingen av regionen.

Använd följande steg för att aktivera loggning av kontroll Plans åtgärder:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till ditt Azure Cosmos-konto.

1. Öppna fönstret **diagnostiska inställningar** och ange ett **namn** för loggarna som ska skapas.

1. Välj **ControlPlaneRequests** som logg typ och välj alternativet **Skicka till Log Analytics** .

Du kan också lagra loggarna i ett lagrings konto eller en data ström till en händelsehubben. Den här artikeln visar hur du skickar loggar till Log Analytics och frågar dem sedan. När du har aktiverat det tar det några minuter innan diagnostikloggar börjar gälla. Alla kontroll Plans åtgärder som utförs efter den punkten kan spåras. Följande skärm bild visar hur du aktiverar kontroll Plans loggar:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Aktivera loggning av begär Anden för kontroll plan":::

## <a name="view-the-control-plane-operations"></a>Visa kontroll Plans åtgärder

När du har aktiverat loggning följer du stegen nedan för att spåra åtgärder för ett bestämt konto:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Öppna fliken **övervakning** i den vänstra navigeringen och välj sedan fönstret **loggar** . Det öppnar ett användar gränssnitt där du enkelt kan köra frågor med det aktuella kontot i omfånget. Kör följande fråga för att Visa kontroll Plans loggar:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Följande skärm bilder fångar loggar när en konsekvens nivå ändras för ett Azure Cosmos-konto:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Kontroll Plans loggar när ett VNet läggs till":::

Följande skärm dum par fångar loggar när ett tecken utrymme eller en tabell med ett Cassandra-konto skapas och när data flödet uppdateras. Kontroll planet loggar för att skapa och uppdatera-åtgärder på databasen och behållaren loggas separat, som visas på följande skärm bild:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Kontrol lera plan loggar när data flödet uppdateras":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifiera den identitet som är kopplad till en speciell åtgärd

Om du vill felsöka ytterligare kan du identifiera en åtgärd i **aktivitets loggen** med hjälp av aktivitets-ID eller tidsstämpel för åtgärden. Timestamp används för vissa Resource Manager-klienter där aktivitets-ID: t inte uttryckligen skickas. Aktivitets loggen innehåller information om den identitet som åtgärden initierades med. Följande skärm bild visar hur du använder aktivitets-ID: t och hittar de åtgärder som är kopplade till den i aktivitets loggen:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Använd aktivitets-ID och hitta åtgärderna":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Kontroll Plans åtgärder för Azure Cosmos-konto

Följande är de kontroll Plans åtgärder som finns tillgängliga på konto nivå. De flesta åtgärder spåras på konto nivå. Dessa åtgärder är tillgängliga som mått i Azure Monitor:

* Region tillagt
* Region borttagen
* Kontot har tagits bort
* Regionen har redundansväxlats
* Kontot har skapats
* Virtuellt nätverk borttaget
* Kontots nätverks inställningar har uppdaterats
* Kontots replikeringsinställningar har uppdaterats
* Konto nycklar har uppdaterats
* Inställningarna för säkerhets kopiering av kontot har uppdaterats
* Inställningarna för konto diagnostik har uppdaterats

## <a name="control-plane-operations-for-database-or-containers"></a>Kontroll Plans åtgärder för databas eller behållare

Följande är de kontroll Plans åtgärder som finns tillgängliga på databas-och behållar nivån. Dessa åtgärder är tillgängliga som mått i Azure Monitor:

* SQL Database skapats
* SQL Database uppdaterad
* SQL Database data flöde uppdaterat
* SQL Database borttagen
* SQL-behållare har skapats
* SQL-behållare har uppdaterats
* SQL container data flöde uppdaterat
* SQL-behållare borttagen
* Cassandra-tecken avstånd har skapats
* Cassandra-disk utrymme uppdaterat
* Cassandra-dataflöde har uppdaterats
* Cassandra-tecken utrymme borttaget
* Cassandra-tabellen har skapats
* Cassandra-tabellen har uppdaterats
* Cassandra tabell data flöde har uppdaterats
* Cassandra-tabellen har tagits bort
* Gremlin-databas har skapats
* Gremlin-databasen har uppdaterats
* Gremlin Database-genomflöde har uppdaterats
* Gremlin-databasen har tagits bort
* Gremlin graf har skapats
* Gremlin-diagrammet har uppdaterats
* Gremlin Graph-genomflöde har uppdaterats
* Gremlin Graph borttagen
* Mongo-databas har skapats
* Mongo-databasen har uppdaterats
* Mongo Database-genomflöde har uppdaterats
* Mongo-databasen har tagits bort
* Mongo-samling har skapats
* Mongo-samlingen har uppdaterats
* Mongo Collection-genomflöde har uppdaterats
* Mongo-samlingen har tagits bort
* AzureTable-tabellen har skapats
* AzureTable-tabellen har uppdaterats
* AzureTable tabell data flöde har uppdaterats
* AzureTable-tabellen har tagits bort

## <a name="diagnostic-log-operations"></a>Diagnostiska logg åtgärder

Följande är åtgärds namnen i diagnostikloggar för olika åtgärder:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

För API-speciella åtgärder heter åtgärden med följande format:

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "data flöde" + operationType

**Exempel** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

Egenskapen *ResourceDetails* innehåller hela resurs bröd texten som en begäran om nytto last och innehåller alla egenskaper som begärs för uppdatering

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Diagnostiska logg frågor för kontroll Plans åtgärder

Här följer några exempel på hur du kan hämta diagnostikloggar för kontroll Plans åtgärder:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersThroughputUpdate"
```

Fråga för att hämta activityId och anroparen som initierade borttagnings åtgärden för behållaren:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Fråga för att hämta index-eller TTL-uppdateringar. Du kan sedan jämföra utdata från den här frågan med en tidigare uppdatering för att se ändringen i index eller TTL.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**utdataparametrar**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Nästa steg

* [Utforska Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)
