---
title: Övervaka Azure Cosmos DB data med hjälp av Azure Diagnostic-inställningar
description: Lär dig hur du använder Azure Diagnostic-inställningar för att övervaka prestanda och tillgänglighet för data som lagras i Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.openlocfilehash: d78ddf983f1c8f2bfeaf733c273afc1cc98b1185
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684866"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Övervaka Azure Cosmos DB data med hjälp av diagnostiska inställningar i Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Diagnostiska inställningar i Azure används för att samla in resurs loggar. Azures resurs loggar genereras av en resurs och ger omfattande, frekventa data om driften av resursen. Dessa loggar samlas in per begäran och kallas även för "data Plans loggar". Några exempel på data Plans åtgärderna är Delete-, INSERT-och readFeed. Innehållet i dessa loggar varierar beroende på resurs typ.

Plattforms mått och aktivitets loggar samlas in automatiskt, medan du måste skapa en diagnostisk inställning för att samla in resurs loggar eller vidarebefordra dem utanför Azure Monitor. Du kan aktivera diagnostikinställningar för Azure Cosmos-konton med hjälp av följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Navigera till ditt Azure Cosmos-konto. Öppna fönstret **diagnostikinställningar** och välj sedan alternativet för att **lägga till diagnostiska** inställningar.

1. Fyll i formuläret med följande information i fönstret **diagnostiska inställningar** : 

    * **Namn**: Ange ett namn för loggarna som ska skapas.

    * Du kan lagra loggarna för att **arkivera i ett lagrings konto**, **strömma till en händelsehubben** eller **Skicka till Log Analytics**

1. När du skapar en diagnostisk inställning anger du vilken kategori av loggar som ska samlas in. De kategorier av loggar som stöds av Azure Cosmos DB anges nedan tillsammans med exempel loggen som samlas in av dem:

 * **DataPlaneRequests**: Välj det här alternativet om du vill logga backend-begäranden till SQL API-konton i Azure Cosmos dB. Nyckel egenskaper att observera är: `Requestcharge` , `statusCode` ,,, `clientIPaddress` `partitionID` `resourceTokenPermissionId` och `resourceTokenPermissionMode` .

   ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372", "resourceTokenPermissionId": "perm-prescriber-app","resourceTokenPermissionMode": "all", "resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
   ```
   
   Använd följande fråga för att hämta loggar som motsvarar data Plans begär Anden:
  
   ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   ```

* **MongoRequests**: Välj det här alternativet om du vill logga användarinitierade begär Anden från klient delen för att betjäna begär anden till Azure Cosmos DB s API för MongoDB. Den här logg typen är inte tillgänglig för andra API-konton. Nyckel egenskaper att observera är: `Requestcharge` , `opCode` . När du aktiverar MongoRequests i diagnostikloggar ser du till att inaktivera DataPlaneRequests. Du ser en logg för varje begäran som gjorts i API: et.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```
  
  Använd följande fråga för att hämta loggar som motsvarar MongoDB-begär Anden:
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="MongoRequests"
  ```

* **CassandraRequests**: Välj det här alternativet om du vill logga användarinitierade begär Anden från klient delen för att betjäna begär anden till Azure Cosmos DB s API för Cassandra. Den här logg typen är inte tillgänglig för andra API-konton. Nyckel egenskaperna som du noterar är `operationName` , `requestCharge` , `piiCommandText` . När du aktiverar CassandraRequests i diagnostikloggar ser du till att inaktivera DataPlaneRequests. Du ser en logg för varje begäran som gjorts i API: et.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```
   
  Använd följande fråga för att hämta loggar som motsvarar Cassandra-begär Anden:
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="CassandraRequests"
  ```

* **GremlinRequests**: Välj det här alternativet om du vill logga användarinitierade begär Anden från klient delen för att betjäna begär anden till Azure Cosmos DB s API för Gremlin. Den här logg typen är inte tillgänglig för andra API-konton. Nyckel egenskaperna som du noterar är `operationName` och `requestCharge` . När du aktiverar GremlinRequests i diagnostikloggar ser du till att inaktivera DataPlaneRequests. Du ser en logg för varje begäran som gjorts i API: et.

  ```json
  { "time": "2021-01-06T19:36:58.2554534Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "GremlinRequests", "operationName": "eval", "properties": {"activityId": "b16bd876-0e5c-4448-90d1-7f3134c6b5ff", "errorCode": "200", "duration": "9.6036", "requestCharge": "9.059999999999999", "databaseName": "GraphDemoDatabase", "collectionName": "GraphDemoContainer", "authorizationTokenType": "PrimaryMasterKey", "address": "98.225.2.189", "estimatedDelayFromRateLimitingInMilliseconds": "0", "retriedDueToRateLimiting": "False", "region": "Australia East", "requestLength": "266", "responseLength": "364", "userAgent": "<empty>"}}
  ```
  
  Använd följande fråga för att hämta loggar som motsvarar Gremlin-begär Anden:
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="GremlinRequests"
  ```

* **QueryRuntimeStatistics**: Välj det här alternativet om du vill logga frågetexten som kördes. Den här logg typen är endast tillgänglig för SQL API-konton.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Välj det här alternativet om du vill logga statistik för partitionens nycklar. Detta visas för närvarande med lagrings storleken (KB) för partition nycklarna. Se avsnittet [fel söknings problem med hjälp av Azure-diagnostiska frågor](#diagnostic-queries) i den här artikeln. Till exempel frågor som använder "PartitionKeyStatistics". Loggen genereras mot de första tre partitionsuppsättningar som upptar merparten data lagring. Den här loggen innehåller data som prenumerations-ID, regions namn, databas namn, samlings namn, partitionsnyckel och lagrings storlek i KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: den här loggen rapporterar den sammanställda per sekund ru/s-förbrukningen av partitionsnyckel. Azure Cosmos DB rapporterar för närvarande bara partitionerings nycklar för SQL API-konton och för åtgärder vid punkt läsning/skrivning och lagrade procedurer. andra API: er och åtgärds typer stöds inte. För andra API: er är partitionerings nyckel kolumnen i tabellen Diagnostic Log tom. Den här loggen innehåller data, till exempel prenumerations-ID, regions namn, databas namn, samlings namn, partitionsnyckel, åtgärds typ och avgift för begäran. Se avsnittet [fel söknings problem med hjälp av Azure-diagnostiska frågor](#diagnostic-queries) i den här artikeln. Till exempel frågor som använder "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: den här loggen innehåller information om kontroll Plans åtgärder som att skapa ett konto, lägga till eller ta bort en region, uppdatera inställningarna för konto replikering osv. Den här logg typen är tillgänglig för alla API-typer som innehåller SQL (Core), MongoDB, Gremlin, Cassandra, Tabell-API.

* **Begär Anden**: Välj det här alternativet om du vill samla in mått data från Azure Cosmos dB till målen i den diagnostiska inställningen. Detta är samma data som samlas in automatiskt i Azure-mått. Samla in Mät data med resurs loggar för att analysera båda typerna av data och skicka mått data utanför Azure Monitor.

Detaljerad information om hur du skapar en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](../azure-monitor/platform/diagnostic-settings.md) -artikeln.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> Felsöka problem med diagnostiska frågor

1. Så här frågar du efter åtgärder som tar längre än tre millisekunder att köra:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. Så här frågar du efter användar agenten som kör åtgärderna:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. Så här frågar du efter tids krävande åtgärder:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. Så här hämtar du statistik för partitionsnyckel för att utvärdera snedheten mellan de tre översta partitionerna för ett databas konto:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

1. Hur får du förfrågningar om avgifter för dyra frågor?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Hur ser du vilka åtgärder som tar det mesta av RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. Så här hämtar du alla frågor som tar upp mer än 100 RU/s tillsammans med data från **DataPlaneRequests** och **QueryRunTimeStatistics**.

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Så här hämtar du begär ande avgifter och körnings tid för en fråga?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. Hur skaffar du fördelningen för olika åtgärder?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Vad är det maximala data flödet som en partition har använt?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Så här hämtar du information om partitionernas nycklar RU/s-förbrukning per sekund?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Så här hämtar du begär ande avgiften för en speciell partitionsnyckel

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Hur hämtar man de främsta sessionsnycklarna med de flesta RU/s-förbrukade under en viss period? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Hur du hämtar loggarna för de partitionsuppsättningar vars lagrings storlek är större än 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Hur får du P99-eller P50-replikeringsfördröjning för åtgärder, begär ande avgift eller svars tid?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. Hur skaffar jag ControlPlane-loggar?
 
   Kom ihåg att växla till flaggan enligt beskrivningen i artikeln [inaktivera nyckelbaserad metadata skriv åtkomst](audit-control-plane-logs.md#disable-key-based-metadata-write-access) och utföra åtgärderna med hjälp av Azure PowerShell, Azure CLI eller Azure Resource Manager.
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)
