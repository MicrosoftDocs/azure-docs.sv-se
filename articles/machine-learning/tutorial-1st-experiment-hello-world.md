---
title: 'Självstudie: kör en "Hello World!" Python-skript'
titleSuffix: Azure Machine Learning
description: Del 2 i serien Azure Machine Learning get-started visar hur du skickar en trivial "Hello World!" Python-skript till molnet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
ms.openlocfilehash: 18f76480d1327d6ab41c475395a689f8024d7b25
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369040"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Självstudie: kör en "Hello World!" Python-skript (del 2 av 4)

I den här självstudien får du lära dig hur du använder Azure Machine Learning SDK för python för att skicka och köra en python "Hello World!" över.

Den här självstudien är *del 2 i en själv studie serie i fyra delar* där du får lära dig grunderna i Azure Machine Learning och fullständiga jobbbaserade Machine Learning-uppgifter i Azure. Den här självstudien bygger på det arbete som du avslutade i [del 1: Konfigurera din lokala dator för Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

I de här självstudierna får du:

> [!div class="checklist"]
> * Skapa och kör en "Hello World!" Python-skript lokalt.
> * Skapa ett python-kontroll skript för att skicka "Hello World!" att Azure Machine Learning.
> * Förstå Azure Machine Learning begreppen i kontroll skriptet.
> * Skicka in och kör "Hello World!" över.
> * Visa kodens utdata i molnet.

## <a name="prerequisites"></a>Förutsättningar

- Slut för ande av [del 1](tutorial-1st-experiment-sdk-setup-local.md) om du inte redan har en Azure Machine Learning-arbetsyta.

## <a name="create-and-run-a-python-script-locally"></a>Skapa och köra ett Python-skript lokalt

Skapa en ny `src` under katalog som heter under `tutorial` katalogen för att lagra kod som du vill köra i ett Azure Machine Learning beräknings kluster. `src`Skapa python-skriptet i under katalogen `hello.py` :

```python
# src/hello.py
print("Hello world!")
```

Projekt katalog strukturen kommer nu att se ut så här:

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="Katalog strukturen visar hello.py i under katalogen src":::


### <a name="test-your-script-locally"></a><a name="test"></a>Testa ditt skript lokalt

Du kan köra din kod lokalt genom att använda din favorit-IDE eller Terminal. Att köra kod lokalt har fördelen med interaktiv fel sökning av kod.  Kör python-filen i fönstret som har den aktiverade *tutorial1* Conda-miljön:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Jag körde skriptet lokalt](?success=run-local#control-script) i [ett problem](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Skapa ett kontroll skript

Med ett *kontroll skript* kan du köra `hello.py` skriptet i molnet. Du använder kontroll skriptet för att styra hur och var din maskin inlärnings kod ska köras.  

I själv studie katalogen skapar du en ny python-fil med namnet `03-run-hello.py` och kopierar/klistrar in följande kod i filen:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Förstå koden

Här är en beskrivning av hur kontroll skriptet fungerar:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Arbets ytan](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) ansluter till din Azure Machine Learning-arbetsyta så att du kan kommunicera med dina Azure Machine Learning-resurser.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) är ett enkelt sätt att organisera flera körningar under ett enda namn. Senare kan du se hur experiment gör det enkelt att jämföra måtten mellan dussin tals körningar.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) radbryter `hello.py` koden och skickar den till din arbets yta. Som namnet antyder kan du använda den här klassen för att _Konfigurera_ hur du vill att _skriptet_ ska _köras_ i Azure Machine Learning. Det anger också vilka beräknings mål som skriptet ska köras på. I den här koden är målet det beräknings kluster som du skapade i [installations guiden](tutorial-1st-experiment-sdk-setup-local.md)för.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Skickar ditt skript. Den här sändningen kallas för en [körning](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py). En körning kapslar in en enskild körning av koden. Använd en körning för att övervaka skript förloppet, avbilda utdata, analysera resultaten, visualisera mått med mera.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run`Objektet ger en referens till körningen av koden. Övervakar förloppet från Azure Machine Learning Studio med den URL som skrivs ut från python-skriptet.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Jag skapade kontroll skriptet som](?success=create-control-script#submit) [Jag stötte på ett problem](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Skicka in och kör din kod i molnet

Kör ditt kontroll skript, som i sin tur körs `hello.py` på det beräknings kluster som du skapade i [installations guiden](tutorial-1st-experiment-sdk-setup-local.md)för.


```bash
python 03-run-hello.py
```

> [!TIP]
> Om du kör den här koden får du ett fel meddelande om att du inte har åtkomst till prenumerationen. mer information om autentiseringsalternativ finns i [ansluta till en arbets yta](how-to-manage-workspace.md?tab=python#connect-multi-tenant) .

> [!div class="nextstepaction"]
> [Jag har skickat in kod i molnet som](?success=submit-to-cloud#monitor) [Jag stötte på ett problem](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Övervaka din kod i molnet med hjälp av Studio

Utdata från skriptet innehåller en länk till Studio som ser ut ungefär så här: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Följ länken.  Först ser du statusen **förbereds**.  Den allra första körningen tar 5-10 minuter att slutföra. Detta beror på att följande inträffar:

* En Docker-avbildning är inbyggd i molnet
* Beräknings klustrets storlek ändras från 0 till 1 nod
* Docker-avbildningen har laddats ned till beräkningen. 

Efterföljande körningar är mycket snabbare (~ 15 sekunder) när Docker-avbildningen cachelagras i beräkningen. Du kan testa detta genom att skicka koden nedan igen när den första körningen har slutförts.

När jobbet har slutförts går du till fliken **utdata + loggar** . Där kan du se en `70_driver_log.txt` fil som ser ut så här:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

På rad 8 visas "Hello World!" utdataparametrar.

`70_driver_log.txt`Filen innehåller standard resultatet från en körning. Den här filen kan vara användbar när du felsöker fjärrkörningar i molnet.

> [!div class="nextstepaction"]
> [Jag såg loggen i Studio](?success=monitor-in-studio#next-steps) [Jag stötte på ett problem](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Nästa steg

I den här självstudien tog du en enkel "Hello World!" skript och körde det på Azure. Du har sett hur du ansluter till din Azure Machine Learning arbets yta, skapar ett experiment och skickar in din `hello.py` kod till molnet.

I nästa själv studie kurs bygger du på de här problemen genom att köra något mer intressant än `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Självstudie: Träna en modell](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Kom ihåg att [rensa dina resurser](tutorial-1st-experiment-bring-data.md#clean-up-resources)om du vill slutföra själv studie serien här och inte förloppet till nästa steg.
