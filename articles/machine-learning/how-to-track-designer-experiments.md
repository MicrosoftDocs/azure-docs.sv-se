---
title: Loggmått i designern
titleSuffix: Azure Machine Learning
description: Övervaka dina Azure ML Designer-experiment. Aktivera loggning med hjälp av modulen Kör Python-skript och visa de loggade resultaten i studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: 13a3b86514428b0219aaf671260c07b4e197d2de
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817322"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Aktivera loggning i Azure Machine Learning designer-pipelines


I den här artikeln får du lära dig hur du lägger till loggningskod i designerpipelines. Du lär dig också hur du visar dessa loggar med hjälp Azure Machine Learning-studio webbportalen.

Mer information om hur du loggar mått med hjälp av SDK-redigeringsupplevelsen finns i Övervaka Körningar och mått [för Azure ML-experiment.](how-to-log-view-metrics.md)

## <a name="enable-logging-with-execute-python-script"></a>Aktivera loggning med Kör Python-skript

Använd modulen [Kör Python-skript](./algorithm-module-reference/execute-python-script.md) för att aktivera loggning i designerpipelines. Även om du kan logga ett värde med det här arbetsflödet är det särskilt användbart att logga mått från modulen __Utvärdera__ modell för att spåra modellens prestanda mellan körningar.

I följande exempel visas hur du loggar mean squared-felet för två tränade modeller med modulerna Evaluate Model (Utvärdera modell) och Execute Python Script (Kör Python-skript).

1. Anslut en __Execute Python Script-modul__ (Kör Python-skript) till utdata __för modulen Evaluate Model (Utvärdera__ modell).

    ![Anslut modulen Execute Python Script (Kör Python-skript) för att utvärdera modellmodulen](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. Klistra in följande kod i __kodredigeraren Kör Python-skript__ för att logga det absoluta medelvärdesfelet för din tränade modell. Du kan använda ett liknande mönster för att logga andra värden i designern:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Den här koden använder Azure Machine Learning Python SDK för att logga värden. Den använder Run.get_context() för att hämta kontexten för den aktuella körningen. Sedan loggar den värden till den kontexten med metoden run.parent.log(). Den använder `parent` för att logga värden till den överordnade pipelinekörningen i stället för modulkörningen.

Mer information om hur du använder Python SDK för att logga värden finns i [Aktivera loggning i Azure ML-träningskörningar.](how-to-log-view-metrics.md)

## <a name="view-logs"></a>Visa loggar

När pipelinekörningen är klar kan du se *Mean_Absolute_Error* på sidan Experiment.

1. Gå till **avsnittet** Experiment.
1. Välj experimentet.
1. Välj den körning i experimentet som du vill visa.
1. Välj **Mått**.

    ![Visa körningsmått i studio](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder loggar i designern. Nästa steg finns i följande relaterade artiklar:


* Information om hur du felsöker designerpipelines [finns i Felsöka & felsöka ML-pipelines.](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Lär dig hur du använder Python SDK för att logga mått i SDK-redigeringsupplevelsen. Mer information finns i [Aktivera loggning i Azure ML-träningskörningar.](how-to-log-view-metrics.md)
* Lär dig hur du [använder Kör Python-skript](./algorithm-module-reference/execute-python-script.md) i designern.
