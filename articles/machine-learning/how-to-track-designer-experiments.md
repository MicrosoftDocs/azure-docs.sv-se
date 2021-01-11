---
title: Logga mått i designern
titleSuffix: Azure Machine Learning
description: Övervaka dina Azure ML-experiment i designer. Aktivera loggning med hjälp av modulen kör Python-skript och Visa de loggade resultaten i Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: b940f5c9bd14bcec404827daaef666da802d969b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065260"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Aktivera loggning i Azure Machine Learning designer-pipeliner


I den här artikeln får du lära dig hur du lägger till loggnings kod till designer-pipeliner. Du lär dig också hur du visar dessa loggar med hjälp av webb portalen för Azure Machine Learning Studio.

Mer information om hur du loggar mått med hjälp av SDK-redigerings upplevelsen finns i [övervaka Azure ml experiment körningar och mått](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Aktivera loggning med kör Python-skript

Använd modulen [Kör Python-skript](./algorithm-module-reference/execute-python-script.md) om du vill aktivera loggning i designer-pipeliner. Även om du kan logga ett värde med det här arbets flödet är det särskilt användbart att logga mått från modulen __utvärdera modell__ för att spåra modell prestanda i flera körningar.

I följande exempel visas hur du loggar medelvärdet av ett kvadratvärde i två utbildade modeller med hjälp av modellen utvärdera modell och köra Python-skript.

1. Anslut en __köra python-skriptfil__ till utdata från modulen __utvärdera modell__ .

    ![Anslut kör python-skriptfil för att utvärdera modell modulen](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Klistra in följande kod i __Kör Python-skript__ kod redigeraren för att logga medelvärdet för det absoluta felet för din tränade modell. Du kan använda ett liknande mönster för att logga andra värden i designern:

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
    
Den här koden använder Azure Machine Learning python SDK för att logga värden. Den använder Run.get_context () för att hämta kontexten för den aktuella körningen. Den loggar sedan värden till den kontexten med metoden Run. parent. log (). Den använder `parent` för att logga värden i den överordnade pipelinen i stället för att modulen ska köras.

Mer information om hur du använder python SDK för att logga värden finns i [Aktivera loggning i Azure ml-utbildning](how-to-track-experiments.md).

## <a name="view-logs"></a>Visa loggar

När pipeline-körningen är klar kan du se *Mean_Absolute_Error* på sidan experiment.

1. Gå till avsnittet **experiment** .
1. Välj experimentet.
1. Välj den körning i ditt experiment du vill visa.
1. Välj **Mått**.

    ![Visa körnings mått i Studio](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder loggar i designern. För nästa steg, se följande relaterade artiklar:


* Lär dig hur du felsöker designer-pipeliner, finns i [felsöka & FELSÖKA ml-pipelines](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Lär dig hur du använder python SDK för att logga mått i redigerings miljön för SDK, se [Aktivera loggning i Azure ml-utbildningar](how-to-track-experiments.md).
* Lär dig hur du använder [Kör Python-skript](./algorithm-module-reference/execute-python-script.md) i designern.
