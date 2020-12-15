---
title: Skapa förväntande data pipelines med Azure Data Factory
description: Beskriver hur du skapar skapa förutsägande pipelines med hjälp av Azure Data Factory och Azure Machine Learning Studio (klassisk)
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: c65ef2eb25f330f645048cdc73371d98d8c2ce91
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508480"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Skapa förutsägande pipelines med Azure Machine Learning Studio (klassisk) och Azure Data Factory

> [!div class="op_single_selector" title1="Omvandlings aktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Aktivitet i gris](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Batch-körningsaktivitet i Azure Machine Learning Studio (klassisk)](data-factory-azure-ml-batch-execution-activity.md)
> * [Uppdateringsresursaktivitet i Azure Machine Learning Studio (klassisk)](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad .NET-aktivitet](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduktion
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med hjälp av maskin inlärning i Data Factory](../transform-data-using-machine-learning.md).

### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klassisk)
[Azure Machine Learning Studio (klassisk)](https://azure.microsoft.com/documentation/services/machine-learning/) ger dig möjlighet att bygga, testa och distribuera lösningar för förutsägelse analys. Från en överblick på hög nivå görs det i tre steg:

1. **Skapa ett övnings experiment**. Du gör detta genom att använda Azure Machine Learning Studio (klassisk). Studio (klassisk) är en samarbets miljö för visuell utveckling som du använder för att träna och testa en förutsägelse analys modell med hjälp av tränings data.
2. **Konvertera det till ett förutsägelse experiment**. När din modell har tränats med befintliga data och du är redo att använda den för att skapa nya data, förbereder du och effektiviserar experimentet med poäng.
3. **Distribuera den som en webb tjänst**. Du kan publicera bedömnings experimentet som en Azure-webbtjänst. Du kan skicka data till din modell via den här webb tjänst slut punkten och ta emot resultat förutsägelser från modellen.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory är en molnbaserad data integrerings tjänst som dirigerar och automatiserar **flytt** och **transformering** av data. Du kan skapa lösningar för data integrering med Azure Data Factory som kan mata in data från olika data lager, transformera/bearbeta data och publicera resultat data till data lager.

Med Data Factory-tjänsten kan du skapa datapipelines som flyttar och transformerar data och kör pipelines enligt ett angivet schema (varje timme, varje dag, varje vecka osv.). Den innehåller också omfattande visualiseringar för att visa härkomst och beroenden mellan dina datapipelines och övervaka alla datapipelines från en enda enhetlig vy för att enkelt hitta problem och konfigurera övervakningsaviseringar.

Se [Introduktion till Azure Data Factory](data-factory-introduction.md) och [skapa dina första pipeline](data-factory-build-your-first-pipeline.md) -artiklar för att snabbt komma igång med tjänsten Azure Data Factory.

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory och Machine Learning Studio (klassisk) tillsammans
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad [Azure Machine Learning Studio (klassisk)][azure-machine-learning] webb tjänst för förutsägelse analys. Med hjälp av **aktiviteten kör batch-körning** i en Azure Data Factory pipeline kan du anropa en Studio (klassisk) webb tjänst för att göra förutsägelser för data i batch. Mer information finns i avsnittet om att anropa en Azure Machine Learning Studio (klassisk) webb tjänst med hjälp av aktiviteten för batch-körning.

Med tiden måste förutsägande modeller i de Studio (klassiska) bedömnings experimenten omtränas med nya data uppsättningar för indata. Du kan träna om en Studio modell (klassisk) från en Data Factory pipeline genom att utföra följande steg:

1. Publicera utbildnings experimentet (inte förutsägande experiment) som en webb tjänst. Du gör det här steget i Studio (klassisk) som du gjorde för att exponera ett förutsägelse experiment som en webb tjänst i föregående scenario.
2. Använd aktiviteten Studio (klassisk) batch execution för att anropa webb tjänsten för inlärnings experimentet. I princip kan du använda batch-körningen Studio (klassisk) för att anropa webb tjänsten för webb tjänster och Poäng för utbildning.

När du är färdig med omträningen uppdaterar du bedömnings webb tjänsten (förutsägande experiment som exponeras som en webb tjänst) med den nya modellen genom att använda den **Azure Machine Learning Studio (klassisk) uppdatera resurs aktiviteten**. Mer information finns i uppdatera [modeller med hjälp av artikeln Uppdatera resurs aktivitet](data-factory-azure-ml-update-resource-activity.md) .

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Anropa en webb tjänst med aktivitet för batch-körning
Du använder Azure Data Factory för att dirigera data förflyttning och bearbetning och sedan köra batch-körning med Studio (klassisk). Här följer stegen på översta nivån:

1. Skapa en Azure Machine Learning Studio (klassisk) länkad tjänst. Du behöver följande värden:

   1. **Begär URI** för API: et för batch-körning. Du kan hitta URI: n för begäran genom att klicka på länken för **batch-körning** på sidan webb tjänster.
   2. **API-nyckel** för webb tjänsten publicerad Studio (klassisk). Du kan hitta API-nyckeln genom att klicka på den webb tjänst som du har publicerat.
   3. Använd **AzureMLBatchExecution** -aktiviteten.

      ![Instrument panel för Machine Learning Studio (klassisk)](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch-URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: experiment som använder webb tjänst indata/utdata som refererar till data i Azure Blob Storage
I det här scenariot gör Studio (klassisk)-webb tjänsten förutsägelser med data från en fil i Azure Blob Storage och lagrar förutsägelse resultatet i blob-lagringen. Följande JSON definierar en Data Factory pipeline med en AzureMLBatchExecution-aktivitet. Aktiviteten har data uppsättningen **DecisionTreeInputBlob** som indata och **DecisionTreeResultBlob** som utdata. **DecisionTreeInputBlob** skickas som en inmatare till webb tjänsten med hjälp av JSON-egenskapen **WebServiceInputActivity** . **DecisionTreeResultBlob** skickas som utdata till webb tjänsten med hjälp av JSON-egenskapen **webServiceOutputs** .

> [!IMPORTANT]
> Om webb tjänsten tar flera indata använder du egenskapen **webServiceInputs** i stället för att använda **WebServiceInputActivity**. I avsnittet [webb tjänst krävs flera inmatningar](#web-service-requires-multiple-inputs) finns ett exempel på hur du använder egenskapen webServiceInputs.
>
> Data uppsättningar som refereras till av egenskaperna **WebServiceInputActivity** / **webServiceInputs** och **webServiceOutputs** (i **typeProperties**) måste också inkluderas i aktivitetens **indata** och **utdata**.
>
> I ditt Studio (klassiska) experiment är webb tjänstens indata-och utgående portar och globala parametrar standard namn ("INPUT1", "INPUT2") som du kan anpassa. De namn du använder för inställningarna webServiceInputs, webServiceOutputs och Dublettparameternamnet måste exakt matcha namnen i experimenten. Du kan visa nytto lasten för exempel förfrågan på hjälp sidan för batch-körning för den klassiska Studio-slutpunkten för att verifiera den förväntade mappningen.
>
>

```json
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

> [!NOTE]
> Endast indata och utdata för AzureMLBatchExecution-aktiviteten kan skickas som parametrar till webb tjänsten. I ovanstående JSON-kodfragment är DecisionTreeInputBlob till exempel inpasset i AzureMLBatchExecution-aktiviteten, som skickas som en indatamängd till webb tjänsten via WebServiceInputActivity-parametern.
>
>

### <a name="example"></a>Exempel
I det här exemplet används Azure Storage för att lagra både indata och utdata.

Vi rekommenderar att du går igenom den [första pipelinen med Data Factory][adf-build-1st-pipeline] själv studie kursen innan du går igenom det här exemplet. Använd Data Factory redigeraren för att skapa Data Factory artefakter (länkade tjänster, data uppsättningar, pipeline) i det här exemplet.

1. Skapa en **länkad tjänst** för **Azure Storage**. Om indata-och utdatafilerna finns i olika lagrings konton behöver du två länkade tjänster. Här är ett JSON-exempel:

   ```json
   {
     "name": "StorageLinkedService",
     "properties": {
       "type": "AzureStorage",
       "typeProperties": {
         "connectionString": "DefaultEndpointsProtocol=https;AccountName= [acctName];AccountKey=[acctKey]"
       }
     }
   }
   ```

2. Skapa data **uppsättningen** för **indata** Azure Data Factory. Till skillnad från andra Data Factory data uppsättningar, måste dessa data uppsättningar innehålla både **folderPath** -och **filename** -värden. Du kan använda partitionering för att köra varje batch-körning (varje data sektor) för att bearbeta eller skapa unika indata-och utdatafiler. Du kan behöva ta med en del överströms aktivitet för att transformera indata till CSV-filformat och placera dem i lagrings kontot för varje sektor. I så fall inkluderar du inte de **externa** och **extern Aldata** -inställningarna som visas i följande exempel, och din DecisionTreeInputBlob skulle vara den utgående data uppsättningen för en annan aktivitet.

   ```json
   {
     "name": "DecisionTreeInputBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/input",
         "fileName": "in.csv",
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "external": true,
       "availability": {
         "frequency": "Day",
         "interval": 1
       },
       "policy": {
         "externalData": {
           "retryInterval": "00:01:00",
           "retryTimeout": "00:10:00",
           "maximumRetry": 3
         }
       }
     }
   }
   ```

   CSV-filen med indata måste ha kolumn rubrik raden. Om du använder **kopierings aktiviteten** för att skapa/flytta CSV-filen till blob-lagringen bör du ange egenskapen **blobWriterAddHeader** till **True**. Till exempel:

   ```json
   sink:
   {
     "type": "BlobSink",
     "blobWriterAddHeader": true
     }
   ```

   Om CSV-filen inte har raden rubrik kan du se följande fel: **fel i aktivitet: fel vid läsning av sträng. Oväntad token: StartObject. Sökväg ' ', rad 1, position 1**.

3. Skapa **data uppsättningen** för **utdata** Azure Data Factory. I det här exemplet används partitionering för att skapa en unik utgående sökväg för varje sektor körning. Utan partitionering skulle aktiviteten skriva över filen.

   ```json
   {
     "name": "DecisionTreeResultBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/scored/{folderpart}/",
         "fileName": "{filepart}result.csv",
         "partitionedBy": [
           {
             "name": "folderpart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "yyyyMMdd"
             }
           },
           {
             "name": "filepart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "HHmmss"
             }
           }
         ],
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "availability": {
         "frequency": "Day",
         "interval": 15
       }
     }
   }
   ```

4. Skapa en **länkad tjänst** av typen: **AzureMLLinkedService**, och ange URL: en för API-nyckel och körning av modell grupp.

   ```json
   {
     "name": "MyAzureMLLinkedService",
     "properties": {
       "type": "AzureML",
       "typeProperties": {
         "mlEndpoint": "https://[batch execution endpoint]/jobs",
         "apiKey": "[apikey]"
       }
     }
   }
   ```

5. Slutligen skapar du en pipeline som innehåller en **AzureMLBatchExecution** -aktivitet. Vid körning utför pipelinen följande steg:

   1. Hämtar platsen för indatafilen från dina indata-datauppsättningar.
   2. Anropar API: et för batch-körning i Studio (klassisk)
   3. Kopierar batch-körningens utdata till den blob som angetts i data uppsättningen för utdata.

      > [!NOTE]
      > AzureMLBatchExecution-aktivitet kan ha noll eller flera indata och en eller flera utdata.
      >
      >

      ```json
      {
        "name": "PredictivePipeline",
        "properties": {
          "description": "use AzureML model",
          "activities": [
              {
              "name": "MLActivity",
              "type": "AzureMLBatchExecution",
              "description": "prediction analysis on batch input",
              "inputs": [
                {
                  "name": "DecisionTreeInputBlob"
                }
                ],
              "outputs": [
                {
                  "name": "DecisionTreeResultBlob"
                }
                ],
              "linkedServiceName": "MyAzureMLLinkedService",
              "typeProperties":
                {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                  "output1": "DecisionTreeResultBlob"
                }
                },
              "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
              }
            }
          ],
          "start": "2016-02-13T00:00:00Z",
          "end": "2016-02-14T00:00:00Z"
          }
      }
      ```

      Både **Start** - **och slutdatum måste** vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Slut** tiden är valfri. Om du inte anger värdet för **slut** egenskapen, beräknas det som "**Start + 48 timmar".** Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut** egenskapen. Se [Referens för JSON-skript](/previous-versions/azure/dn835050(v=azure.100)) för information om JSON-egenskaper.

      > [!NOTE]
      > Det är valfritt att ange indata för AzureMLBatchExecution-aktiviteten.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: experiment som använder Reader/Writer-moduler för att referera till data i olika lagrings utrymmen
Ett annat vanligt scenario när du skapar Studio-experiment (klassisk) är att använda Reader-och Writer-moduler. Modulen läsare används för att läsa in data i ett experiment och modulen skrivare är att spara data från experimenten. Mer information om Reader-och Writer-moduler finns i läsa avsnittet om [läsare](/azure/machine-learning/studio-module-reference/import-data) och [skrivare](/azure/machine-learning/studio-module-reference/export-data) i MSDN Library.

När du använder Reader-och Writer-modulerna är det bra att använda en webb tjänst parameter för varje egenskap för dessa läsare/skrivar-moduler. Med de här webb parametrarna kan du konfigurera värdena under körning. Du kan till exempel skapa ett experiment med en läsar modul som använder en Azure SQL Database: XXX.database.windows.net. När webb tjänsten har distribuerats vill du göra det möjligt för användare av webb tjänsten att ange en annan logisk SQL-Server som heter YYY.database.windows.net. Du kan använda en webb tjänst parameter för att tillåta att det här värdet konfigureras.

> [!NOTE]
> Webb tjänstens indata och utdata skiljer sig från webb tjänst parametrarna. I det första scenariot har du sett hur du kan ange indata och utdata för en Studio-webbtjänst (klassisk). I det här scenariot skickar du parametrar för en webb tjänst som motsvarar egenskaperna för Reader/Writer-moduler.
>
>

Nu ska vi titta på ett scenario för att använda webb tjänst parametrar. Du har en distribuerad Studio (klassisk) webb tjänst som använder en läsar modul för att läsa data från en av de data källor som stöds av Studio (klassisk) (till exempel: Azure SQL Database). När batch-körningen har utförts skrivs resultatet med en Writer-modul (Azure SQL Database).  Inga indata och utdata för webb tjänsten definieras i experimenten. I det här fallet rekommenderar vi att du konfigurerar relevanta webb tjänst parametrar för modulerna läsare och skrivare. Med den här konfigurationen kan läsaren/skrivar-modulerna konfigureras när du använder AzureMLBatchExecution-aktiviteten. Du anger webb tjänst parametrar i **Dublettparameternamnet** -avsnittet i AKTIVITETS-JSON på följande sätt.

```json
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Du kan också använda [Data Factory funktioner](data-factory-functions-variables.md) i skicka värden för webb tjänst parametrarna som visas i följande exempel:

```json
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Webb tjänst parametrarna är Skift läges känsliga, så se till att namnen som du anger i aktivitets-JSON matchar de som exponeras av webb tjänsten.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Använda en läsar modul för att läsa data från flera filer i Azure Blob
Stora datapipelines med aktiviteter som till exempel gris och Hive kan producera en eller flera utdatafiler utan tillägg. Om du till exempel anger en extern Hive-tabell kan data för den externa Hive-tabellen lagras i Azure Blob Storage med följande namn 000000_0. Du kan använda modulen läsare i ett experiment för att läsa flera filer och använda dem för förutsägelser.

När du använder modulen läsare i ett Studio (klassiskt) experiment kan du ange Azure blob som inmatade. Filerna i Azure Blob Storage kan vara utdatafilerna (exempel: 000000_0) som skapas av ett gris-och Hive-skript som körs på HDInsight. Med modulen läsare kan du läsa filer (utan tillägg) genom att konfigurera **sökvägen till container, Directory/BLOB**. **Sökvägen till container** pekar på behållaren och **katalogen/blobben** som pekar på den mapp som innehåller filerna som visas i följande bild. Asterisken är, \* ) **anger att alla filer i behållaren/mappen (det vill säga data/aggregateddata/Year = 2014/month-6/ \* )** läses som en del av experimentet.

![Egenskaper för Azure-Blob](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exempel
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline med AzureMLBatchExecution-aktivitet med webb tjänst parametrar

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

I ovanstående JSON-exempel:

* Den distribuerade Studio-webbtjänsten använder en läsare och en Writer-modul för att läsa/skriva data från/till en Azure SQL Database. Den här webb tjänsten exponerar följande fyra parametrar: databas server namn, databas namn, konto namn för Server användare och lösen ord för användar konto för Server.
* Både **Start** - **och slutdatum måste** vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Slut** tiden är valfri. Om du inte anger värdet för **slut** egenskapen, beräknas det som "**Start + 48 timmar".** Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut** egenskapen. Se [Referens för JSON-skript](/previous-versions/azure/dn835050(v=azure.100)) för information om JSON-egenskaper.

### <a name="other-scenarios"></a>Andra scenarier
#### <a name="web-service-requires-multiple-inputs"></a>Webb tjänsten kräver flera indata
Om webb tjänsten tar flera indata använder du egenskapen **webServiceInputs** i stället för att använda **WebServiceInputActivity**. Data uppsättningar som **webServiceInputs** refererar till måste också tas med i aktivitetens **indata**.

I ditt Azure Machine Learning Studio (klassiska) experiment är webb tjänstens indata-och utgående portar och globala parametrar standard namn ("INPUT1", "INPUT2") som du kan anpassa. De namn du använder för inställningarna webServiceInputs, webServiceOutputs och Dublettparameternamnet måste exakt matcha namnen i experimenten. Du kan visa nytto lasten för exempel förfrågan på hjälp sidan för batch-körning för den klassiska Studio-slutpunkten för att verifiera den förväntade mappningen.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Webb tjänsten kräver ingen indatamängd
Azure Machine Learning Studio (klassisk) batch-körning webb tjänster kan användas för att köra arbets flöden, till exempel R-eller Python-skript, som kanske inte kräver några indata. Eller så kan experimentet konfigureras med en kontrollmodul som inte exponerar någon Dublettparameternamnet. I så fall skulle AzureMLBatchExecution-aktiviteten konfigureras på följande sätt:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webb tjänsten kräver ingen indata/utdata
Webb tjänsten för batch-körning av Azure Machine Learning Studio (klassisk) kanske inte har något konfigurerat webb tjänstens utdata. I det här exemplet finns det inga webb tjänst indata eller utdata, och inga Dublettparameternamnet har kon figurer ATS. Det finns fortfarande en utmatning som kon figurer ATS för själva aktiviteten, men den anges inte som en webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Webb tjänsten använder läsare och skrivare, och aktiviteten körs bara när andra aktiviteter har genomförts
Modulerna för webb tjänst läsare och skrivare i Azure Machine Learning Studio (klassisk) kan konfigureras för att köras med eller utan Dublettparameternamnet. Men du kanske vill bädda in tjänst anrop i en pipeline som använder data uppsättnings beroenden för att anropa tjänsten endast när en del av den överordnade bearbetningen har slutförts. Du kan också utlösa en annan åtgärd när batch-körningen har slutförts med den här metoden. I så fall kan du uttrycka beroenden med hjälp av aktivitetens indata och utdata, utan att byta namn på dem som webb tjänst indata eller utdata.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**Takeaways** är:

* Om din experiment-slutpunkt använder en WebServiceInputActivity: den representeras av en BLOB-datauppsättning och ingår i aktivitetens indata och egenskapen WebServiceInputActivity. Annars utelämnas egenskapen WebServiceInputActivity.
* Om din experiment-slutpunkt använder webServiceOutput: de representeras av BLOB-datauppsättningar och ingår i aktivitetens utdata och i egenskapen webServiceOutputs. Aktivitetens utdata och webServiceOutputs mappas efter namnet på varje utdata i experimentet. Annars utelämnas egenskapen webServiceOutputs.
* Om din experiment-slutpunkt exponerar globalParameter anges de i egenskapen Dublettparameternamnet för aktiviteten som nyckel, värdepar. Annars utelämnas egenskapen Dublettparameternamnet. Nycklarna är Skift läges känsliga. [Azure Data Factory funktioner](data-factory-functions-variables.md) kan användas i värdena.
* Ytterligare data uppsättningar kan inkluderas i egenskaperna för aktiviteter och utdata, utan att de refereras till i aktiviteten typeProperties. Dessa data uppsättningar styr körningen med hjälp av segment beroenden men ignoreras annars av AzureMLBatchExecution-aktiviteten.


## <a name="updating-models-using-update-resource-activity"></a>Uppdatera modeller med uppdatering av resurs aktivitet
När du är färdig med omträningen uppdaterar du bedömnings webb tjänsten (förutsägande experiment som exponeras som en webb tjänst) med den nya modellen genom att använda den **Azure Machine Learning Studio (klassisk) uppdatera resurs aktiviteten**. Mer information finns i uppdatera [modeller med hjälp av artikeln Uppdatera resurs aktivitet](data-factory-azure-ml-update-resource-activity.md) .

### <a name="reader-and-writer-modules"></a>Reader-och Writer-moduler
Ett vanligt scenario för att använda webb tjänst parametrar är användningen av Azure SQL-läsare och-skrivare. Modulen läsare används för att läsa in data i ett experiment från data Management Services utanför Studio (klassisk). Modulen skrivare är att spara data från dina experiment i data hanterings tjänster utanför Studio (klassisk).

Mer information om Azure Blob/Azure SQL Reader [/Writer finns](/azure/machine-learning/studio-module-reference/export-data) i [läsa läsa](/azure/machine-learning/studio-module-reference/import-data) och skriva ämnen i MSDN Library. Exemplet i föregående avsnitt använde Azure Blob Reader och Azure Blob Writer. I det här avsnittet beskrivs hur du använder Azure SQL Reader och Azure SQL Writer.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**F:** Jag har flera filer som genereras av mina Big data-pipeliner. Kan jag använda AzureMLBatchExecution-aktiviteten för att arbeta med alla filer?

**S:** Ja. Mer information finns i avsnittet **använda en läsar modul för att läsa data från flera filer i Azure Blob** .

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Azure Machine Learning Studio (klassisk) batch-bedömnings aktivitet
Om du använder **AzureMLBatchScoring** -aktiviteten för att integrera med Azure Machine Learning Studio (klassisk) rekommenderar vi att du använder den senaste **AzureMLBatchExecution** -aktiviteten.

AzureMLBatchExecution-aktiviteten introduceras i augusti 2015-versionen av Azure SDK och Azure PowerShell.

Fortsätt att läsa igenom det här avsnittet om du vill fortsätta använda AzureMLBatchScoring-aktiviteten.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning Studio (klassisk) batch-bedömnings aktivitet med Azure Storage för in-/utdata

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Webb tjänst parametrar
Om du vill ange värden för webb tjänst parametrar lägger du till ett **typeProperties** -avsnitt i **AzureMLBatchScoringActivity** -avsnittet i pipeline-JSON, som du ser i följande exempel:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Du kan också använda [Data Factory funktioner](data-factory-functions-variables.md) i skicka värden för webb tjänst parametrarna som visas i följande exempel:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Webb tjänst parametrarna är Skift läges känsliga, så se till att namnen som du anger i aktivitets-JSON matchar de som exponeras av webb tjänsten.
>
>

## <a name="see-also"></a>Se även
* [Azure blogg inlägg: komma igång med Azure Data Factory och Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/