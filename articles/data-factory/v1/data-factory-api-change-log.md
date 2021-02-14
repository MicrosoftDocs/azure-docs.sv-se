---
title: Data Factory-.NET API-ändrings logg
description: Beskriver hur du avbryter ändringar, funktions tillägg, fel korrigeringar och så vidare i en speciell version av .NET API för Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 3f7c80f5818c1353f438a164bf52c777056ad6d4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377490"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-.NET API-ändrings logg
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Den här artikeln innehåller information om ändringar i Azure Data Factory SDK i en angiven version. Du kan hitta det senaste NuGet-paketet för Azure Data Factory [här](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Version 4.11.0
Funktions tillägg:

* Följande länkade tjänst typer har lagts till:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Följande typer av data uppsättningar har lagts till:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Följande kopierings käll typer har lagts till:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Version 4.10.0
* Följande valfria egenskaper har lagts till i text rutan:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Följande länkade tjänst typer har lagts till:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Följande typer av data uppsättningar har lagts till:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Följande kopierings käll typer har lagts till:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Lägg till egenskapen [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) i AzureMLBatchExecutionActivity
  * Aktivera överföring av flera webb tjänst indata till ett Azure Machine Learning experiment

## <a name="version-491"></a>Version 4.9.1
### <a name="bug-fix"></a>Fel korrigering
* Föråldrad WebApi-baserad autentisering för [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice).

## <a name="version-490"></a>Version 4.9.0
### <a name="feature-additions"></a>Funktions tillägg
* Lägg till [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) -och [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) -egenskaper till CopyActivity. Se [mellanlagrad kopia](data-factory-copy-activity-performance.md#staged-copy) för information om funktionen.

### <a name="bug-fix"></a>Fel korrigering
* Introducera en överlagring av [ActivityWindowOperationExtensions. list](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) -metoden, som tar en [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) -instans.
* Markera [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) och [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) som valfritt i CopySink.

## <a name="version-480"></a>Version 4.8.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande valfria egenskaper har lagts till för att kopiera aktivitets typen för att möjliggöra justering av kopierings prestanda:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>Funktions tillägg
* Ny StorageFormat typ av [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) har lagts till för att kopiera filer i optimerade rad kolumner (Orc).
* Lägg till [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) -och PolyBaseSettings-egenskaper till SqlDWSink.
  * Gör det möjligt att använda PolyBase för att kopiera data till Azure Synapse Analytics.

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>Felkorrigeringar
* Korrigerar HTTP-begäran för List aktivitets fönster.
  * Tar bort resurs grupps namnet och data fabriks namnet från nytto lasten för begäran.

## <a name="version-460"></a>Version 4.6.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande egenskaper har lagts till i [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties):
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Datauppsättningar](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* Följande egenskaper har lagts till i [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo):
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* En ny [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) typ av [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) har lagts till för att definiera data uppsättningar vars data är i JSON-format.

## <a name="version-450"></a>Version 4.5.0
### <a name="feature-additions"></a>Funktions tillägg
* Har lagt till [list åtgärder för aktivitets fönstret](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Metoder för att hämta aktivitets fönster med filter baserade på entitetstyper (det vill säga data fabriker, data uppsättningar, pipeliner och aktiviteter) har lagts till.
* Följande länkade tjänst typer har lagts till:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Följande typer av data uppsättningar har lagts till:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Följande kopierings käll typer har lagts till:     
  * [Webbkälla](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Version 4.4.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande länkad tjänst typ har lagts till som data källor och mottagare för kopierings aktiviteter:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Information och exempel finns i [Azure Storage SAS-länkade tjänster](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Version 4.3.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande länkade tjänst typer jag lagts till som data källor för kopierings aktiviteter:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Se [Flytta data från HDFS med hjälp av Data Factory](data-factory-hdfs-connector.md) för konceptuell information och exempel.
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Se [Flytta data från ODBC-datalager med hjälp av Azure Data Factory](data-factory-odbc-connector.md) för konceptuell information och exempel.

## <a name="version-420"></a>Version 4.2.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande nya aktivitets typ har lagts till: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Mer information om aktiviteten finns i [Uppdatera Azure ml-modeller med hjälp av aktiviteten uppdatera resurs](data-factory-azure-ml-batch-execution-activity.md).
* En ny valfri egenskap [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) har lagts till i [AzureMLLinkedService-klassen](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice).
* Egenskaperna [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) och [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) har lagts till i klassen [klassen datafactorymanagementclient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) .
* Tillåt konfiguration av tids gränser för klient anrop till tjänsten Data Factory.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>Funktions tillägg
* Följande länkade tjänst typer har lagts till:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Följande aktivitets typer har lagts till:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Följande typer av data uppsättningar har lagts till:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Följande käll-och mottagar typer för kopierings aktiviteten har lagts till:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Version 4.0.1
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
Följande klasser har bytt namn. De nya namnen är de ursprungliga namnen för klasser innan 4.0.0-versionen släpptes.

| Namn i 4.0.0 | Namn i 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Version 4.0.0
### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
* Följande klasser/gränssnitt har bytt namn.

| Gammalt namn | Nytt namn |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabell |[Data uppsättning](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* **List** metoderna returnerar växlade resultat nu. Om svaret innehåller en icke-tom **Nextlink** -egenskap måste klient programmet fortsätta att hämta nästa sida tills alla sidor har returnerats.  Här är ett exempel:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** pipeline API returnerar bara sammanfattningen av en pipeline i stället för fullständig information. Till exempel innehåller aktiviteter i en pipeline-Sammanfattning endast namn och typ.

### <a name="feature-additions"></a>Funktions tillägg
* [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) -klassen stöder två nya egenskaper, **SliceIdentifierColumnName** och **SqlWriterCleanupScript**, för att stödja Idempotenta-kopiering till Azure Azure Synapse Analytics. Mer information om de här egenskaperna finns i artikeln om [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) .
* Vi har nu stöd för att köra lagrade procedurer mot Azure SQL Database och Azure Synapse Analytics-källor som en del av kopierings aktiviteten. [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) -och [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) -klasserna har följande egenskaper: **SqlReaderStoredProcedureName** och **StoredProcedureParameters**. Mer information om dessa egenskaper finns i artiklarna [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) och [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) på Azure.com.