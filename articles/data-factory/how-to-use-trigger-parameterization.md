---
title: Skicka utlösnings information till pipeline
description: Lär dig hur du refererar till Utlös ande metadata i pipeline
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 57024d6cb23ab273aa69bf59f4ec436a6f873a05
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193554"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Referens utlöser metadata i pipeline-körningar

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur utlösare metadata, till exempel utlösarens start tid, kan användas i pipeline-körning.

Pipelinen måste ibland förstå och läsa metadata från utlösaren som anropar den. Till exempel, med utlösare för rullande fönster, baserat på fönster start och slut tid, bearbetar pipelinen olika data sektorer eller mappar. I Azure Data Factory använder vi parameterisering och [system variabel](control-flow-system-variables.md) för att skicka meta-data från utlösare till pipeline.

Det här mönstret är särskilt användbart för [utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md), där utlösaren tillhandahåller fönster start-och slut tid.

> [!NOTE]
> En annan utlösnings typ tillhandahåller annan meta data-information. Mer information finns i [system variabel](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

Det här avsnittet visar hur du skickar meta-datainformation från utlösaren till pipelinen, inom Azure Data Factory användar gränssnitt.

1. Gå till **redigerings arbets ytan** och redigera en pipeline

1. Klicka på den tomma arbets ytan för att Visa inställningar för pipeliner. Välj ingen aktivitet. Du kan behöva hämta inställnings panelen från slutet av arbets ytan, eftersom den kan ha komprimerats

1. Välj **parametrar** -avsnittet och välj **+ ny** för att lägga till parametrar

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Skärm bild av pipeline-inställningen som visar hur du definierar parametrar i pipelinen.":::

1. Lägg till utlösare i pipeline genom att klicka på **+ utlösare**.

1. Skapa eller koppla en utlösare till pipelinen och klicka på **OK**

1. På följande sida fyller du i utlösarens meta-data för varje parameter. Använd format som definierats i [system variabel](control-flow-system-variables.md) för att hämta information om utlösare. Du behöver inte fylla i informationen för alla parametrar, bara de som kommer att anta värdena för utlösarens metadata. Här tilldelar vi till exempel start tiden för utlösarens körning till *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Skärm bild av sidan utlösnings definition som visar hur du skickar utlösnings information till pipeline-parametrar.":::

1. Om du vill använda värdena i pipeline använder du parametrarna _@pipeline (). Parameters. parameterName_, __inte__ system variabel, i pipeline-definitioner. I vårt fall kan vi till exempel läsa utlösarens start tid @pipeline () .Parameters.parameter_1.

## <a name="json-schema"></a>JSON-schema

För att skicka in information om utlösare till pipeline-körningar måste både utlösaren och pipeline-JSON uppdateras med _parametrar_ -avsnittet.

### <a name="pipeline-definition"></a>Pipeline-definition

Under avsnittet **Egenskaper** lägger du till parameter definitioner till **parametrar** -avsnittet

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Utlös definition

Under avsnittet **pipelines** tilldelar du parameter värden i avsnittet **parametrar** . Du behöver inte fylla i informationen för alla parametrar, bara de som kommer att anta värdena för utlösarens metadata.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Använd utlösnings information i pipeline

Om du vill använda värdena i pipeline använder du parametrarna _@pipeline (). Parameters. parameterName_, __inte__ system variabel, i pipeline-definitioner.

## <a name="next-steps"></a>Nästa steg

Detaljerad information om utlösare finns i [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#trigger-execution).
