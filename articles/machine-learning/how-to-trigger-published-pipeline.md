---
title: Utlös ML pipelines för nya data
titleSuffix: Azure Machine Learning
description: Lär dig hur du utlöser körningen av en Azure Machine Learning pipeline genom att använda Azure Logic Apps för att svara på nya data.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4
ms.openlocfilehash: 37a18d147d3aca713d0c6bd934e23aa22b2521a5
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028894"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Utlösa en körning av en Machine Learning pipeline från en Logic app

Utlös körning av Azure Machine Learning pipelinen när nya data visas. Du kanske exempelvis vill utlösa pipelinen för att träna en ny modell när nya data visas i Blob Storage-kontot. Konfigurera utlösaren med [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* REST-slutpunkten för en publicerad Machine Learning pipeline. [Skapa och publicera din pipeline](how-to-create-your-first-pipeline.md). Hitta sedan REST-slutpunkten för din PublishedPipeline med hjälp av pipeline-ID:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md) för att lagra dina data.
* [Ett data lager](how-to-access-data.md) på din arbets yta som innehåller information om ditt Blob Storage-konto.

## <a name="create-a-logic-app"></a>Skapa en logikapp

Skapa nu en [Azure Logic app](../logic-apps/logic-apps-overview.md) -instans. Om du vill kan du [använda en integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) och [Konfigurera en kundhanterad nyckel](../logic-apps/customer-managed-keys-integration-service-environment.md) för användning av din Logic app.

När du har etablerat din Logi Kap par kan du använda de här stegen för att konfigurera en utlösare för din pipeline:

1. [Skapa en systemtilldelad hanterad identitet](../logic-apps/create-managed-service-identity.md) för att ge appen åtkomst till din Azure Machine Learning-arbetsyta.

1. Navigera till vyn Logic App Designer och välj den tomma Logic app-mallen. 
    > [!div class="mx-imgBorder"]
    > ![Tom mall](media/how-to-trigger-published-pipeline/blank-template.png)

1. I designern söker du efter **BLOB**. Markera kryss rutan **när en BLOB läggs till eller ändras (endast egenskaper)** utlösas och Lägg till den här utlösaren i din Logic app.
    > [!div class="mx-imgBorder"]
    > ![Lägg till utlösare](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Fyll i anslutnings informationen för det Blob Storage-konto som du vill övervaka för BLOB-tillägg eller-ändringar. Välj den behållare som ska övervakas. 
 
    Välj **intervall** och **frekvens** för att söka efter uppdateringar som fungerar för dig.  

    > [!NOTE]
    > Den här utlösaren övervakar den valda behållaren men övervakar inte undermappar.

1. Lägg till en HTTP-åtgärd som ska köras när en ny eller ändrad BLOB identifieras. Välj **+ nytt steg** och Sök sedan efter och välj http-åtgärd.

  > [!div class="mx-imgBorder"]
  > ![Sök efter HTTP-åtgärd](media/how-to-trigger-published-pipeline/search-http.png)

  Använd följande inställningar för att konfigurera din åtgärd:

  | Inställning | Värde | 
  |---|---|
  | HTTP-åtgärd | POST |
  | URI |slut punkten till den publicerade pipelinen som du hittade som en [förutsättning](#prerequisites) |
  | Autentiseringsläge | Hanterad identitet |

1. Konfigurera ditt schema för att ange värdet för alla [Datapath-PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) som du kan ha:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Använd `DataStoreName` du har lagt till i din arbets yta som en [förutsättning](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![HTTP-inställningar](media/how-to-trigger-published-pipeline/http-settings.png)

1. Välj **Spara** så är ditt schema nu klart.

> [!IMPORTANT]
> Om du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera åtkomst till din pipeline, anger du [behörigheterna för ditt pipeline-scenario (utbildning eller poängsättning)](how-to-assign-roles.md#common-scenarios).

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

> [!div class="nextstepaction"]
> [Använd Azure Machine Learning pipelines för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md)

* Läs mer om [pipelines](concept-ml-pipelines.md)
* Lär dig mer om att [utforska Azure Machine Learning med Jupyter](samples-notebooks.md)

