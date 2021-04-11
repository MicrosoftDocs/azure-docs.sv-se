---
title: Så här lägger du till datafeeds från olika källor i mått Advisor
titleSuffix: Azure Cognitive Services
description: Lägg till olika data matningar i mått Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: 4fd01256d94fbcb18fe8437be00c84e49d98f7d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606155"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Lägg till datafeeds från olika data källor i mått Advisor

Använd den här artikeln för att hitta inställningar och krav för att ansluta olika typer av data källor till mått Advisor. Läs om hur du kan [publicera dina data](how-tos/onboard-your-data.md) för att lära dig mer om viktiga begrepp för att använda dina data med mått rådgivare. \

## <a name="supported-authentication-types"></a>Autentiseringstyper som stöds

| Autentiseringstyper | Beskrivning |
| ---------------------|-------------|
|**Basic** | Du måste kunna tillhandahålla grundläggande parametrar för åtkomst till data källor. Till exempel en anslutnings sträng eller nyckel. Data flödes administratörer kan visa dessa autentiseringsuppgifter. |
| **AzureManagedIdentity** | [Hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) för Azure-resurser är en funktion i Azure Active Directory. Den tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering.|
| **AzureSQLConnectionString**| Lagra AzureSQL-anslutningssträngen som en **entitet för autentiseringsuppgifter** i Metric Advisor och Använd den direkt varje gång när du registrerar Mät data. Endast administratörer för entiteten autentiseringsuppgifter kan visa dessa autentiseringsuppgifter, men gör det möjligt för behöriga användare att skapa datafeeds utan att behöva känna till information om autentiseringsuppgifterna. |
| **DataLakeGen2SharedKey**| Lagra din data Lake-Kontoreferensen som en **entitet för autentiseringsuppgifter** i mått Advisor och Använd den direkt varje gång när du registrerar Mät data. Endast administratörer för entiteten autentiseringsuppgifter kan visa dessa autentiseringsuppgifter, men gör det möjligt för behöriga användare att skapa datafeed utan att behöva känna till autentiseringsuppgifterna.|
| **Tjänstens huvudnamn**| Lagra tjänstens huvud namn som en **entitet för autentiseringsuppgifter** i mått Advisor och Använd det direkt varje gång när du registrerar Mät data. Endast administratörer för entiteten för autentiseringsuppgifter kan visa autentiseringsuppgifterna, men gör det möjligt för behöriga användare att skapa datafeed utan att behöva känna till autentiseringsuppgifterna.|
| **Tjänstens huvud namn från Key Vault**|Lagra tjänstens huvud namn i ett nyckel valv som en **entitet för autentiseringsuppgifter** i mått Advisor och Använd det direkt varje gång när du registrerar Mät data. Endast administratörer för en **entitet för autentiseringsuppgifter** kan visa autentiseringsuppgifterna, men du kan också lämna användare som har möjlighet att skapa datafeeds utan att behöva känna till detaljerade autentiseringsuppgifter. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Data källor som stöds och motsvarande autentiseringstyper


| Datakällor | Autentiseringstyper |
|-------------| ---------------------|
|[**Azure Application insikter**](#appinsights)|  Grundläggande |
|[**Azure Blob Storage (JSON)**](#blob) | Grundläggande<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Grundläggande |
|[**Azure Data Explorer (Kusto)**](#kusto) | Grundläggande<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Grundläggande<br>DataLakeGen2SharedKey<br>Tjänstens huvudnamn<br>Tjänstens huvud namn från Key Vault<br> |
|[**Azure SQL Database/SQL Server**](#sql) | Grundläggande<br>ManagedIdentity<br>Tjänstens huvudnamn<br>Tjänstens huvud namn från Key Vault<br>AzureSQLConnectionString
|[**Azure Table Storage**](#table) | Grundläggande | 
|[**ElasticSearch**](#es) | Grundläggande |
|[**Http-begäran**](#http) | Grundläggande | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Grundläggande |
|[**MongoDB**](#mongodb) | Grundläggande |
|[**MySQL**](#mysql) | Grundläggande |
|[**PostgreSQL**](#pgsql)| Grundläggande|

Skapa en entitet för autentiseringsuppgifter * * och Använd den för autentisering till dina data källor. I följande avsnitt anges de parametrar som krävs för *grundläggande* autentisering. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application insikter</span>

* **Program-ID**: används för att identifiera det här programmet när du använder Application Insights-API: et. Hämta program-ID: t genom att göra följande:

    1. Klicka på API-åtkomst från Application Insights resurs.

    2. Kopiera program-ID: t som genererats till fältet **program-ID** i mått Advisor. 
    
    Mer information finns i [Azure bot Service-dokumentationen](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) .

* **API-nyckel**: API-nycklar används av program utanför webbläsaren för att få åtkomst till den här resursen. Hämta API-nyckeln genom att göra följande:

    1. Klicka på API-åtkomst från Application Insights resurs.

    2. Klicka på Skapa API-nyckel.

    3. Ange en kort beskrivning, markera alternativet Läs telemetri och klicka på knappen generera nyckel.

    4. Kopiera API-nyckeln till fältet **API-nyckel** i mått Advisor.

* **Fråga**: Azure Application insikter loggar bygger på Azure Datautforskaren och Azure Monitor logg frågor använder en version av samma Kusto. [Dokumentationen för Kusto-frågespråket](/azure/data-explorer/kusto/query/) innehåller all information om språket och bör vara din primära resurs för att skriva en fråga mot Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Anslutnings sträng**: Mer information om hur du hämtar den här strängen finns i artikeln om Azure Blob Storage- [anslutningssträng](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) .

* **Container**: metrics Advisor förväntar sig Time Series-data som lagras som BLOB-filer (en BLOB per tidsstämpel) under en enda behållare. Detta är fältet container Name.

* **BLOB-mall**: det här är mallen för BLOB-filnamn. Exempel: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. Följande parametrar stöds:
  * `%Y` är året formaterat som `yyyy`
  * `%m` är månaden formaterad som `MM`
  * `%d` är dagen formaterad som `dd`
  * `%h` är timmen formaterad som `HH`
  * `%M` är minuten formaterad som `mm`

* **JSON-format version**: definierar data schema i JSON-filerna. För närvarande har Metrics Advisor stöd för två versioner:
  
  * v1 (standardvärde)

      Endast måtten *Name* och *Value* accepteras. Exempel:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      *Mått och* *tidsstämpel* godkänns också. Exempel:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

Endast en tidstämpel tillåts per JSON-fil. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Anslutnings sträng**: anslutnings strängen för att få åtkomst till din Azure Cosmos dB. Detta finns i Cosmos DB-resursen, i **nycklar**. 
* **Databas**: databasen att fråga mot. Du hittar detta på sidan **Bläddra** under **behållare** .
* **Samlings-ID**: samlings-ID för fråga mot. Du hittar detta på sidan **Bläddra** under **behållare** .
* **SQL-fråga**: en SQL-fråga för att hämta och formulera data till flerdimensionella Time Series-data. Du kan använda `@StartTime` `@EndTime` variablerna och i din fråga. De ska formateras: `yyyy-MM-dd HH:mm:ss` .

    Exempel fråga:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Exempel fråga för en data sektor från 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Anslutnings sträng**: mått översikten har stöd för åtkomst till Azure datautforskaren (Kusto) med hjälp av Azure AD Application Authentication. Du måste skapa och registrera ett Azure AD-program och sedan auktorisera det för att få åtkomst till en Azure Datautforskaren-databas. Information om hur du hämtar anslutnings strängen finns i [Azure datautforskaren](/azure/data-explorer/provision-azure-ad-app) -dokumentationen.

* **Fråga**: se [Kusto frågespråk](/azure/data-explorer/kusto/query) för att hämta och formulera data till flerdimensionella Time Series-data. Du kan använda `@StartTime` `@EndTime` variablerna och i din fråga. De ska formateras: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Konto namn**: konto namnet för Azure Data Lake Storage Gen2. Du hittar detta i din Azure Storage konto (Azure Data Lake Storage Gen2) i **åtkomst nycklar**.

* **Konto nyckel**: Ange konto namnet för att få åtkomst till din Azure Data Lake Storage Gen2. Detta finns i inställningen för Azure Storage konto (Azure Data Lake Storage Gen2) resurs i **åtkomst nycklar** .

* **Fil system namn (container)**: metrics Advisor förväntar sig att tids serie data lagras som BLOB-filer (en BLOB per tidsstämpel) under en enda behållare. Detta är fältet container Name. Du hittar detta i Azure Storage-kontots (Azure Data Lake Storage Gen2) instans och klickar på behållare i avsnittet BLOB service.

* **Directory-mall**: det här är katalog mal len för BLOB-filen. Till exempel: */%Y/%m/%d*. Följande parametrar stöds:
  * `%Y` är året formaterat som `yyyy`
  * `%m` är månaden formaterad som `MM`
  * `%d` är dagen formaterad som `dd`
  * `%h` är timmen formaterad som `HH`
  * `%M` är minuten formaterad som `mm`

* **Filmall**: Detta är fil mal len för BLOB-filen. Exempel: *X_% Y-% m-% d-% h-% M.jspå*. Följande parametrar stöds:
  * `%Y` är året formaterat som `yyyy`
  * `%m` är månaden formaterad som `MM`
  * `%d` är dagen formaterad som `dd`
  * `%h` är timmen formaterad som `HH`
  * `%M` är minuten formaterad som `mm`

För närvarande stöder Metric Advisor data schema i JSON-filerna enligt följande. Exempel:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **Anslutnings sträng**: metrics Advisor accepterar en [anslutnings sträng för ADO.net-format](/dotnet/framework/data/adonet/connection-string-syntax) för SQL Server-datakällan.

    Exempel på anslutnings sträng:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Fråga**: en SQL-fråga för att hämta och formulera data till flerdimensionella Time Series-data. Du kan använda en `@StartTime` variabel i din fråga för att få hjälp med att hämta förväntade mått värden.

  * `@StartTime`: ett datum/tid i formatet för `yyyy-MM-dd HH:mm:ss`

    Exempel fråga:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Faktisk fråga har körts för data sektorn 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **Anslutnings sträng**: skapa en SAS-URL (Shared Access signatur) och fyll i här. Det enklaste sättet att skapa en SAS-URL på är att använda Azure-portalen. Med hjälp av Azure Portal kan du navigera grafiskt. Om du vill skapa en SAS-URL via Azure Portal går du först till det lagrings konto som du vill komma åt i avsnittet Inställningar och klickar sedan på signatur för delad åtkomst. Markera kryss rutorna "Tabell" och "objekt" och klicka sedan på knappen Skapa SAS och anslutnings sträng. Table service SAS-URL är vad du behöver kopiera och fylla i text rutan på arbets ytan mått rådgivare.

* **Tabell namn**: Ange en tabell att fråga mot. Du hittar detta i din Azure Storage konto instans. Klicka på **tabeller** i avsnittet **tabell tjänst** .

* **Fråga** Du kan använda `@StartTime` i din fråga. `@StartTime` ersätts med en ÅÅÅÅ-MM-ddTHH: mm: SS-format sträng i skriptet. Tips: Använd Azure Storage Explorer för att skapa en fråga med ett angivet tidsintervall och se till att den körs på rätt avstånd. gör sedan ersättnings processen.

    ``` mssql
    date ge datetime'@StartTime' and date lt datetime'@EndTime'
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">ElasticSearch</span>

* **Värd**: Ange huvud värden för ElasticSearch-kluster.
* **Port**: Ange huvud porten för ElasticSearch-klustret.
* **Authorization-huvud**: Ange värdet för ElasticSearch-klustrets Authorization-huvud.
* **Fråga**: Ange frågan för att hämta data. Plats hållare @StartTime stöds. ( t. ex. När data från 2020-06-21T00:00:00Z matas in, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP-begäran</span>

* **URL för begäran**: en http-URL som kan returnera en JSON. Plats hållarna% Y,% m,% d,% h,% M stöds:% Y = år i formatet åååå,% m = månad i formatet MM,% d = dag i formatet DD,% h = timme i formatet HH,% M = minut i formatet mm. Exempel: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **HTTP-metod för begäran**: Använd Get eller post.
* **Begär ande huvud**: kan lägga till grundläggande autentisering. 
* **Begär nytto Last**: endast JSON-nyttolast stöds. Plats hållaren @StartTime stöds i nytto lasten. Svaret ska vara i följande JSON-format: [{"timestamp": "2018-01-01T00:00:00Z", "marknad": "en-US", "count": 11, "intäkter": 1,23}, {"timestamp": "2018-01-01T00:00:00Z", "marknad": "zh-cn", "antal": 22, "intäkter": med 4,56}]. (t. ex. När data från 2020-06-21T00:00:00Z matas in, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Anslutnings sträng**: anslutnings strängen för åtkomst till din InfluxDB.
* **Databas**: databasen att fråga mot.
* **Fråga**: en fråga för att hämta och formulera data till data för flerdimensionell tids serier för inmatning.
* **Användar namn**: Detta är valfritt för autentisering. 
* **Lösen ord**: Detta är valfritt för autentisering. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Anslutnings sträng**: anslutnings strängen för åtkomst till din MongoDB.
* **Databas**: databasen att fråga mot.
* **Kommando**: ett kommando för att hämta och formulera data till flerdimensionella Time Series-data för inmatning.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Anslutnings sträng**: anslutnings strängen för åtkomst till MySQL-databasen.
* **Fråga**: en fråga för att hämta och formulera data till data för flerdimensionell tids serier för inmatning.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Anslutnings sträng**: anslutnings strängen för åtkomst till POSTGRESQL-databasen.
* **Fråga**: en fråga för att hämta och formulera data till data för flerdimensionell tids serier för inmatning.

## <a name="next-steps"></a>Nästa steg

* När du väntar på att dina mått data ska matas in i systemet bör du läsa om [hur du hanterar datafeed-konfigurationer](how-tos/manage-data-feeds.md).
* När dina mått data matas in kan du [Konfigurera mått och finjustera konfigurationen för identifiering](how-tos/configure-metrics.md).
