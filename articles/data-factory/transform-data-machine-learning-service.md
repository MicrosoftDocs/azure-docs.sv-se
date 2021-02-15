---
title: Köra Azure Machine Learning pipelines
description: Lär dig hur du kör Azure Machine Learning pipelines i Azure Data Factory pipeliner.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.date: 07/16/2020
ms.openlocfilehash: db1816baf0ebc476a132b344d8db3cdbdd170e50
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373937"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Köra Azure Machine Learning pipelines i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kör Azure Machine Learning pipelines som ett steg i Azure Data Factory pipeliner. Med aktiviteten kör pipeline för Machine Learning kan du definiera scenarier för batch-förutsägelse, till exempel identifiera möjliga låne standarder, bestämma sentiment och analysera kund beteende mönster.

Den här videon innehåller sex minuters introduktion och demonstration av den här funktionen.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntax

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
name | Namn på aktiviteten i pipelinen | Sträng | Ja
typ | Typ av aktivitet är ' AzureMLExecutePipeline ' | Sträng | Ja
linkedServiceName | Länkad tjänst till Azure Machine Learning | Länkad tjänst referens | Ja
mlPipelineId | ID för den publicerade Azure Machine Learning pipelinen | Sträng (eller uttryck med resultType för sträng) | Ja
experimentName | Körnings experimentets experiment namn för Machine Learning pipeline-körningen | Sträng (eller uttryck med resultType för sträng) | Inga
mlPipelineParameters | Nyckel, värdepar som ska skickas till den publicerade Azure Machine Learning pipeline-slutpunkten. Nycklar måste matcha namnen på de pipeline-parametrar som definierats i den publicerade Machine Learning pipelinen | Objekt med nyckel värdes par (eller uttryck med resultType-objekt) | Inga
mlParentRunId | ID för pipeline-körning för överordnad Azure Machine Learning | Sträng (eller uttryck med resultType för sträng) | Inga
continueOnStepFailure | Om körningen av andra steg i Machine Learning pipelinen ska fortsätta köras om ett steg Miss lyckas | boolean | Inga

> [!NOTE]
> För att fylla i list rutorna i Machine Learning pipelinens namn och ID måste användaren ha behörighet att lista ML-pipeliner. ADF UX anropar AzureMLService-API: er direkt med den inloggade användarens autentiseringsuppgifter.  

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt:

* [Kör data flödes aktivitet](control-flow-execute-data-flow-activity.md)
* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)
