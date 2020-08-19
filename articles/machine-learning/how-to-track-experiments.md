---
title: Log ML-experiment & mått
titleSuffix: Azure Machine Learning
description: Övervaka dina Azure ML-experiment och övervaka körnings mått för att förbättra skapande processen för modeller. Lägg till loggning i utbildnings skriptet med kör. log, kör. start_logging eller ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7a0fd4178df92cc9102456c1fa2ae4e8927337e4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547332"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Aktivera loggning i Azure ML-utbildningar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Med Azure Machine Learning python SDK kan du logga information i real tid med hjälp av både standard-python-loggnings paketet och SDK-/regionsspecifika funktioner. Du kan logga lokalt och skicka loggar till din arbets yta i portalen.

Loggarna kan hjälpa dig att diagnostisera fel och varningar eller spåra prestanda mått som parametrar och modell prestanda. I den här artikeln får du lära dig hur du aktiverar loggning i följande scenarier:

> [!div class="checklist"]
> * Interaktiva utbildningar
> * Skicka utbildnings jobb med ScriptRunConfig
> * Egna python- `logging` Inställningar
> * Logga från ytterligare källor


> [!TIP]
> Den här artikeln visar hur du övervakar modell inlärnings processen. Om du är intresse rad av att övervaka resursanvändningen och händelserna i Azure Machine Learning, till exempel kvoter, slutförda inlärnings körningar eller slutförda modell distributioner, se [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Datatyper

Du kan logga flera data typer, inklusive skalära värden, listor, tabeller, bilder, kataloger med mera. Mer information och python-kod exempel för olika data typer finns på [referens sidan kör klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

## <a name="interactive-logging-session"></a>Interaktiv Logging-session

Interaktiva loggnings sessioner används vanligt vis i Notebook-miljöer. Metoden [experiment. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) startar en interaktiv inloggningssession. Alla mått som loggas under sessionen läggs till i körnings posten i experimentet. Metoden [Run. Complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) avslutar sessionerna och markerar kör som slutförd.

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig-loggar

I det här avsnittet får du lära dig hur du lägger till loggnings kod i ScriptConfig-körningar. Du kan använda klassen [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) för att kapsla in skript och miljöer för upprepnings bara körningar. Du kan också använda det här alternativet för att Visa widgeten för Visual Jupyter Notebooks för övervakning.

Det här exemplet utför en parameter svep över alpha-värden och fångar in resultaten med hjälp av metoden [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) .

1. Skapa ett utbildnings skript som innehåller loggnings logiken `train.py` .

   [! code-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Skicka ```train.py``` skriptet för körning i en användar hanterad miljö. Hela mappen script skickas för utbildning.

   [! Notebook – python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = src)] [! Notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? namn = kör)]

    `show_output`Parametern aktiverar utförlig loggning så att du kan se information från inlärnings processen samt information om eventuella fjär resurser eller beräknings mål. Använd följande kod för att aktivera utförlig loggning när du skickar experimentet.

```python
run = exp.submit(src, show_output=True)
```

Du kan också använda samma parameter i `wait_for_completion` funktionen på den resulterande körningen.

```python
run.wait_for_completion(show_output=True)
```

En fullständig exempel-anteckningsbok som använder ScriptRunConfigs-loggar finns i [träna en modell lokalt](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local/train-on-local.ipynb).

## <a name="native-python-logging"></a>Intern python-loggning

Vissa loggar i SDK kan innehålla ett fel som uppmanar dig att ange loggnings nivå för fel sökning. Om du vill ange loggnings nivå lägger du till följande kod i skriptet.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Ytterligare loggnings källor

Azure Machine Learning kan också logga information från andra källor under utbildningen, till exempel automatiserade maskin inlärnings körningar eller Docker-behållare som kör jobben. Dessa loggar är inte dokumenterade, men om du stöter på problem och kontaktar Microsoft support kan de använda dessa loggar vid fel sökning.

Information om hur du loggar mått i Azure Machine Learning designer (för hands version) finns i [så här loggar du mått i designern (för hands version)](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Exempelnotebook-filer
Följande antecknings böcker demonstrerar begrepp i den här artikeln:
* [instruktionen att använda – azureml/Training/Train-i-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [instruktionen att använda – azureml/Training/träna-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Track-and-Monitor-experiment/Logging-API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Se de här artiklarna för att lära dig mer om hur du använder Azure Machine Learning:

* Lär dig hur du [loggar mått i Azure Machine Learning designer (för hands version)](how-to-track-designer-experiments.md).

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien, [träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).
