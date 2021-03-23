---
title: Uppdatera Machine Learning modeller med Azure Data Factory
description: Beskriver hur du skapar förutsägbara pipelines med Azure Data Factory v1 och Azure Machine Learning Studio (klassisk)
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 2d9156bfcc11817a647c33053a2d04c653c0a706
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785503"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Uppdatera Azure Machine Learning Studio-modeller (klassiska) med uppdatering av resurs aktivitet

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


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory kan du läsa mer [i uppdatera maskin inlärnings modeller i Data Factory](../update-machine-learning-models.md).

Den här artikeln kompletterar huvud artikeln Azure Data Factory-Azure Machine Learning Studio (klassisk): [skapa förutsägbara pipelines med hjälp av Azure Machine Learning Studio (klassisk) och Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Läs igenom huvud artikeln innan du läser igenom den här artikeln, om du inte redan gjort det. 

## <a name="overview"></a>Översikt
Med tiden måste förutsägande modeller i de Azure Machine Learning Studio (klassiska) bedömnings experimenten återskapas med hjälp av nya data uppsättningar för indata. När du är färdig med omträningen vill du uppdatera bedömnings-webbtjänsten med den retränade ML-modellen. De vanligaste stegen för att aktivera omtrainering och uppdatering av Studio (klassiska) modeller via webb tjänster är:

1. Skapa ett experiment i [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net).
2. När du är nöjd med modellen använder du Azure Machine Learning Studio (klassisk) för att publicera webb tjänster för både **utbildnings experimentet** och poängsättningen/**förutsägande experiment**.

I följande tabell beskrivs de webb tjänster som används i det här exemplet.  Mer information finns i avsnittet [omträna Azure Machine Learning Studio (klassiska) modeller program mässigt](../../machine-learning/classic/retrain-machine-learning-model.md) .

- **Utbildning-webbtjänst** – tar emot utbildnings data och genererar utbildade modeller. Resultatet av omträningen är en. ilearner-fil i en Azure Blob Storage. **Standard slut punkten** skapas automatiskt åt dig när du publicerar övnings experimentet som en webb tjänst. Du kan skapa fler slut punkter, men exemplet använder bara standard slut punkten.
- **Bedömnings webb tjänst** – tar emot omärkta data exempel och gör förutsägelser. Resultatet av förutsägelsen kan ha olika former, till exempel en CSV-fil eller rader i Azure SQL Database, beroende på hur experimentet är. Standard slut punkten skapas automatiskt åt dig när du publicerar ett förutsägelse experiment som en webb tjänst. 

Följande bild illustrerar förhållandet mellan utbildning och poäng slut punkter i Azure Machine Learning Studio (klassisk).

![Webbtjänster](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Du kan anropa **utbildnings webb tjänsten** med hjälp av den **Azure Machine Learning Studio (klassiska) batch execution Activity**. Att anropa en utbildnings webb tjänst är detsamma som att anropa en Azure Machine Learning Studio (klassisk) webb tjänst (en webb tjänst för bedömning) för poängsättnings data. Föregående avsnitt beskriver hur du anropar en Azure Machine Learning Studio (klassisk) webb tjänst från en Azure Data Factory pipeline i detalj. 

Du kan anropa **bedömnings webb tjänsten** med hjälp av den **Azure Machine Learning Studio (klassisk) uppdatera resurs aktiviteten** för att uppdatera webb tjänsten med den nyligen utbildade modellen. I följande exempel finns länkade tjänst definitioner: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Webb tjänsten poängsättning är en klassisk webb tjänst
Om bedömnings webb tjänsten är en **klassisk webb tjänst** skapar du den andra **icke-standard-och uppdaterings bara slut punkten** med hjälp av Azure Portal. Se artikeln [skapa slut punkter](../../machine-learning/classic/create-endpoint.md) för steg. När du har skapat den uppdaterings bara slut punkten som inte är standard, utför följande steg:

* Klicka på **batch-körning** för att hämta URI-värdet för **mlEndpoint** JSON-egenskapen.
* Klicka på **Uppdatera resurs** länk för att hämta URI-värdet för **updateResourceEndpoint** JSON-egenskapen. API-nyckeln finns på slut punkts sidan (i det nedre högra hörnet).

![uppdaterings bar slut punkt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

I följande exempel visas ett exempel på en JSON-definition för den länkade tjänsten AzureML. Den länkade tjänsten använder apiKey för autentisering.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Webb tjänsten poängsättning är Azure Resource Manager-webbtjänst 
Om webb tjänsten är den nya typen av webb tjänst som exponerar en Azure Resource Manager-slutpunkt behöver du inte lägga till den andra slut punkten som **inte är standard** . **UpdateResourceEndpoint** i den länkade tjänsten har formatet: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Du kan hämta värden för plats hållare i URL: en när du frågar webb tjänsten på den [Azure Machine Learning Studio (klassiska) webb tjänst portalen](https://services.azureml.net/). Den nya typen av uppdaterings resurs slut punkt kräver en AAD-token (Azure Active Directory). Ange **servicePrincipalId** och **servicePrincipalKey** i den länkade tjänsten Studio (klassisk). Se [hur du skapar tjänstens huvud namn och tilldelar behörigheter för att hantera Azure-resurser](../../active-directory/develop/howto-create-service-principal-portal.md). Här är ett exempel på en AzureML länkad tjänst definition: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Följande scenario innehåller mer information. Det finns ett exempel på att omträna och uppdatera Studio (klassiska) modeller från en Azure Data Factory pipeline.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Scenario: omträning och uppdatera en Studio (klassisk) modell
Det här avsnittet innehåller en exempel-pipeline som använder **aktiviteten Azure Machine Learning Studio (klassisk) batch-körning** för att omträna en modell. Pipelinen använder också den **Azure Machine Learning Studio (klassisk) uppdatera resurs aktiviteten** för att uppdatera modellen i poängsättnings webb tjänsten. Avsnittet innehåller också JSON-kodfragment för alla länkade tjänster, data uppsättningar och pipeline i exemplet.

Här är diagramvyn för exempel pipelinen. Som du kan se tar aktiviteten för batch-körning i Studio (klassisk) till inlärningen och genererar en utbildnings utmatning (iLearner-fil). Den Studio (klassisk) uppdatering av resurs aktiviteten tar den här utbildningen och uppdaterar modellen i poängsättnings webb tjänstens slut punkt. Aktiviteten uppdatera resurs genererar inga utdata. PlaceholderBlob är bara en data uppsättning för en dummy-datauppsättning som krävs av Azure Data Factorys tjänsten för att köra pipelinen.

![Pipeline-diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Länkad Azure Blob Storage-tjänst:
Azure Storage innehåller följande data:

* tränings data. Indata för webb tjänsten Studio (klassisk) utbildning.  
* iLearner-fil. Utdata från Studio-webbtjänsten (klassisk). Den här filen är också indata för resurs aktiviteten uppdatera.  

Här är exempel-JSON-definitionen för den länkade tjänsten:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Data uppsättning för utbildning:
Följande data uppsättning representerar indata för tränings data för webb tjänsten Studio (klassisk). Aktiviteten för batch-körning i Studio (klassisk) använder den här data uppsättningen som indata.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
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

### <a name="training-output-dataset"></a>Data uppsättning för utbildning av utdata:
Följande data uppsättning representerar den utgående iLearner-filen från webb tjänsten Azure Machine Learning Studio (klassisk) utbildning. Den Azure Machine Learning Studio (klassiska) batch execution-aktiviteten genererar den här data uppsättningen. Den här data uppsättningen är också indata till den Azure Machine Learning Studio (klassisk) uppdatera resurs aktiviteten.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Utbildnings slut punkt för länkad tjänst för Studio (klassisk)
Följande JSON-kodfragment definierar en Studio (klassisk) länkad tjänst som pekar på standard slut punkten för utbildnings webb tjänsten.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

I **Azure Machine Learning Studio (klassisk)** gör du följande för att hämta värden för **mlEndpoint** och **apiKey**:

1. Klicka på **webb tjänster** på den vänstra menyn.
2. Klicka på **webb tjänsten utbildning** i listan med webb tjänster.
3. Klicka på Kopiera bredvid text rutan **API-nyckel** . Klistra in nyckeln i Urklipp i Data Factory JSON-redigeraren.
4. I **Azure Machine Learning Studio (klassisk)** klickar du på länken för **batch-körning** .
5. Kopiera **begärd URI** från avsnittet **begäran** och klistra in den i Data Factory JSON-redigeraren.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Länkad slut punkt för länkad tjänst för Studio (klassisk):
Följande JSON-kodfragment definierar en Studio (klassisk) länkad tjänst som pekar på den icke-standardinställda slut punkten för poängsättnings webb tjänsten.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Data uppsättning för placeholder-utdata:
Uppdaterings resurs aktiviteten i Studio (klassisk) genererar inga utdata. Azure Data Factory kräver dock en data uppsättning för utdata för att driva schemat för en pipeline. Vi använder därför en data uppsättning med dummy/placeholder i det här exemplet.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Den Azure Machine Learning Studio (klassiska) batch-körningen använder tränings data som indata och skapar en iLearner-fil som utdata. Aktiviteten anropar webb tjänsten utbildning (inlärnings experiment som visas som en webb tjänst) med indata och tar emot ilearner-filen från webb tjänsten. PlaceholderBlob är bara en data uppsättning för en dummy-datauppsättning som krävs av Azure Data Factorys tjänsten för att köra pipelinen.

![Pipeline-diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```