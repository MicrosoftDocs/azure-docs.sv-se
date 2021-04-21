---
title: Vad hände med Workbench?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning är en integrerad datavetenskapslösning för att modellera och distribuera ML-program i molnskala. Workbench-funktionen har dragits tillbaka.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: 4c680be897c4c1bf2ccf20df1d34ab6f59f559f2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816160"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Vad hände med Azure Machine Learning Workbench?

Programmet Azure Machine Learning Workbench och några andra tidiga funktioner blev inaktuella och ersattes i versionen från **september 2018** för att skapa plats för en förbättrad [arkitektur.](concept-azure-machine-learning-architecture.md)

För att förbättra upplevelsen innehåller versionen många viktiga uppdateringar som baseras på feedback från kunder. Grundläggande funktioner från experimentkörningar till modelldistribution har inte förändrats. Men nu kan du använda robust <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK,</a>R SDK och [Azure CLI](reference-azure-machine-learning-cli.md) för att utföra dina maskininlärningsuppgifter och pipelines.

De flesta artefakter som skapades i den tidigare versionen av Azure Machine Learning lagras i din egen lokala lagring eller molnlagring. Dessa artefakter försvinner aldrig.

I den här artikeln får lära du dig om vad som ändrats och hur det påverkar ditt befintliga arbete med Azure Machine Learning Workbench och dess API:er.

>[!Warning]
>Den här artikeln är inte avsedd för Azure Machine Learning Studio-användare. Det är till Azure Machine Learning kunder som har installerat Workbench-programmet (förhandsversion) och/eller har konton för experimentering och modellhantering i förhandsversionen.


## <a name="what-changed"></a>Vad har ändrats?

Den senaste versionen Azure Machine Learning innehåller följande funktioner:
+ En [förenklad Azure-resursmodell](concept-azure-machine-learning-architecture.md).
+ Ett [nytt användargränssnitt för portalen](how-to-log-view-metrics.md) för att hantera experiment och beräkningsmål.
+ En ny, mer omfattande Python <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK.</a>
+ Det nya, utökade [Azure CLI-tillägget](reference-azure-machine-learning-cli.md) för maskininlärning.

[Arkitekturen](concept-azure-machine-learning-architecture.md) har gjorts om för enklare användning. I stället för flera Azure-resurser och konton behöver du bara en [Azure Machine Learning-arbetsyta](concept-workspace.md). Du kan skapa arbetsytor snabbt i [Azure-portalen](how-to-manage-workspace.md). Genom att använda en arbetsyta kan flera användare lagra tränings- och distributionsberäkningsmål, modellexperiment, Docker-avbildningar, distribuerade modeller och så vidare.

Även om det finns nya förbättrade CLI- och SDK-klienter i den aktuella versionen, har själva Workbench-skrivbordsprogrammet tagits ur bruk. Experiment kan hanteras på arbetsytans [instrumentpanel i Azure Machine Learning-studio](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal). Använd instrumentpanelen för att hämta din experimenthistorik, hantera beräkningsmål som är kopplade till din arbetsyta, hantera modeller och Docker-avbildningar och även distribuera webbtjänster.

<a name="timeline"></a>

## <a name="support-timeline"></a>Supporttidslinje

Den 9 januari 2019 upphörde stödet för Machine Learning Workbench, Azure Machine Learning-experimentering och Modellhantering-konton och deras associerade SDK och CLI.

Alla nya funktioner är tillgängliga med hjälp av detta <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>, [kommandoradsgränssnittet](reference-azure-machine-learning-cli.md) och [portalen](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>Vad händer med körhistorik?

Äldre körhistorik är inte längre tillgänglig, men du kan fortfarande visa dina körningar i den senaste versionen.

Körhistorik kallas nu **experiment**. Du kan samla in modellens experiment och utforska dem med hjälp av SDK, CLI eller Azure Machine Learning-studio.

Portalens instrumentpanel för arbetsytor stöds endast i webbläsarna Microsoft Edge, Chrome och Firefox:

[![Onlineportal](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Börja träna dina modeller och spåra körhistorik med hjälp av nya CLI och SDK. Du kan lära dig hur du gör med [Självstudie: träna modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Kommer projekt att bevaras?

Du förlorar inte någon kod eller något arbete. I den äldre versionen är projekt molnentiteter med en lokal katalog. I den senaste versionen kopplar du lokala kataloger till arbetsytan Azure Machine Learning med hjälp av en lokal konfigurationsfil. Se ett [diagram över den senaste arkitekturen](concept-azure-machine-learning-architecture.md).

Mycket av projektinnehållet finns redan på den lokala datorn. Så du behöver bara skapa en konfigurationsfil i denna katalog och referera till den i koden för att ansluta till din arbetsyta. Om du vill fortsätta att använda den lokala katalogen som innehåller dina filer och skript anger du katalogens namn i Python-kommandot ["experiment.submit"](/python/api/azureml-core/azureml.core.experiment.experiment) eller använder `az ml project attach` CLI-kommandot.  Exempel:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Skapa en arbetsyta](how-to-manage-workspace.md) för att komma igång.

## <a name="what-about-my-registered-models-and-images"></a>Vad händer med mina registrerade modeller och bilder?

De modeller som du registrerade i ditt gamla modellregister måste migreras till din nya arbetsyta om du vill fortsätta att använda dem. För att migrera dina modeller hämtar du modellerna och registrerar dem på nytt i den nya arbetsytan.

Avbildningarna som du skapade i ditt gamla avbildningsregister kan inte migreras direkt till den nya arbetsytan. I de flesta fall kan modellen distribueras utan att du behöver skapa en avbildning. Om det behövs kan du skapa en avbildning för modellen i den nya arbetsytan. Mer information finns i [Hantera, registrera, distribuera och övervaka maskininlärningsmodeller.](concept-model-management-and-deployment.md)

## <a name="what-about-deployed-web-services"></a>Vad händer med distribuerade webbtjänster?

När nu stödet för det äldre kommandoradsgränssnittet har upphört kan du inte längre distribuera om modeller eller hantera webbtjänster som du ursprungligen har distribuerat med ditt modellhanteringskonto. Dessa webbtjänster kommer dock fortsätta att fungera så länge det finns stöd för Azure Container Service (ACS).

I den senaste versionen distribueras modeller som webbtjänster till Azure Container Instances (ACI) eller Azure Kubernetes Service-kluster (AKS). Du kan även distribuera till FPGA och till Azure IoT Edge.

Mer information finns i följande artiklar:
+ [Var och hur du distribuerar modeller](how-to-deploy-and-where.md)
+ [Självstudie: Distribuera modeller med Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer [om den senaste arkitekturen för Azure Machine Learning](concept-azure-machine-learning-architecture.md).

En översikt över tjänsten finns i [Vad är Azure Machine Learning?](overview-what-is-azure-ml.md).

Skapa ditt första experiment med den metod du föredrar:

  + [Använd din egen miljö](tutorial-1st-experiment-sdk-setup-local.md)
  + [Använda Python-anteckningsböcker](tutorial-1st-experiment-sdk-setup.md)
  + [Använda R Markdown](https://github.com/Azure/azureml-sdk-for-r) 
  + [Använda automatiserad maskininlärning](tutorial-designer-automobile-price-train-score.md) 
  + [Använd designerns dra och släpp-funktioner & dra och släpp](tutorial-first-experiment-automated-ml.md) 
  + [Använda ML-tillägget till CLI](tutorial-train-deploy-model-cli.md)
