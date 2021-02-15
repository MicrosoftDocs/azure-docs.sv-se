---
title: 'Självstudie: kom igång med Machine Learning – python'
titleSuffix: Azure Machine Learning
description: I den här självstudien kommer du igång med Azure Machine Learning SDK för python som körs i din personliga utvecklings miljö.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: 5d61f330c823aff755a25e637bb58c132aa58100
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369157"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Självstudie: kom igång med Azure Machine Learning i utvecklings miljön (del 1 av 4)

I den här *själv studie serien i fyra delar* får du lära dig grunderna i Azure Machine Learning och fullständiga jobbbaserade python-uppgifter för fin inlärning på Azure Cloud-plattformen. 

I del 1 av den här själv studie serien kommer du att:

> [!div class="checklist"]
> * Installera Azure Machine Learning SDK.
> * Konfigurera katalog strukturen för kod.
> * Skapa en Azure Machine Learning-arbetsyta.
> * Konfigurera din lokala utvecklings miljö.
> * Konfigurera ett beräknings kluster.

> [!NOTE]
> Den här själv studie serien fokuserar på Azure Machine Learning begrepp som krävs för att skicka **batch-jobb** – det är här som koden skickas till molnet för att köras i bakgrunden utan någon användar interaktion. Detta är användbart för färdiga skript eller kod som du vill köra upprepade gånger, eller för beräknings intensiva Machine Learning-uppgifter. Om du är mer intresse rad av ett exempel arbets flöde kan du i stället använda [Jupyter eller RStudio på en Azure Machine Learning beräknings instans](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Försök [Azure Machine Learning](https://aka.ms/AMLFree).
- [Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://www.anaconda.com/download/) för att hantera virtuella python-miljöer och installera paket.  
- Om du inte är bekant med att använda Conda, se [komma igång med Conda](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html).

## <a name="install-the-azure-machine-learning-sdk"></a>Installera Azure Machine Learning SDK

I den här självstudien kommer du att använda Azure Machine Learning SDK för python. För att undvika python-beroenden skapar du en isolerad miljö. I den här själv studie serien används Conda för att skapa miljön. Om du föredrar att använda andra lösningar, till exempel `venv` , `virtualenv` eller Docker, kontrollerar du att du använder en python-version >= 3,5 och < 3,9.

Kontrol lera om du har Conda installerat i systemet:
    
```bash
conda --version
```
    
Om det här kommandot returnerar ett `conda not found` fel kan du [Hämta och installera Miniconda](https://docs.conda.io/en/latest/miniconda.html). 

När du har installerat Conda kan du använda ett terminalfönster-eller Anaconda-kommandotolk-fönster för att skapa en ny miljö:

```bash
conda create -n tutorial python=3.8
```

Sedan installerar du Azure Machine Learning SDK i Conda-miljön som du skapade:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Det tar ungefär 2 minuter innan installationen av Azure Machine Learning SDK slutförts.
>
> Om du får ett timeout-fel kan du försöka `pip install --default-timeout=100 azureml-core` i stället.


> [!div class="nextstepaction"]
> [Jag har installerat SDK: n som](?success=install-sdk#dir) [Jag stötte på ett problem](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Skapa en katalog struktur för kod

Vi rekommenderar att du konfigurerar följande enkla katalog struktur för den här självstudien:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="katalog struktur: självstudie översta nivån med under katalogen. azureml":::


- `tutorial`: Katalogen på den översta nivån i projektet.
- `.azureml`: Dold under katalog för lagring av Azure Machine Learning konfigurationsfiler.

För att till exempel skapa detta i ett bash-fönster:

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Om du vill skapa eller Visa strukturen i ett grafiskt fönster aktiverar du först möjligheten att se och skapa dolda filer och mappar:
>
> * I ett Mac Finder-fönster använder du **Kommando + Skift +.** för att växla visning av dolda filer/mappar.  
> * I en Windows 10-fil Utforskare, se [så här visar du dolda filer och mappar](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * I det grafiska Linux-gränssnittet använder du **CTRL + h** eller **Visa** -menyn och markerar kryss rutan för att **Visa dolda filer**.




> [!div class="nextstepaction"]
> [Jag har skapat en katalog](?success=create-dir#workspace) som [Jag stötte på ett problem](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Skapa en Azure Machine Learning-arbetsyta

En arbets yta är en resurs på den översta nivån för Azure Machine Learning och är en central plats för att:

- Hantera resurser, till exempel Compute.
- Lagra till gångar som antecknings böcker, miljöer, data uppsättningar, pipeliner, modeller och slut punkter.
- Samar beta med andra team medlemmar.

I katalogen på den översta nivån `tutorial` lägger du till en ny python-fil `01-create-workspace.py` med namnet med hjälp av följande kod. Anpassa parametrarna (namn, prenumerations-ID, resurs grupp och [plats](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) med dina inställningar.

Du kan köra koden i en interaktiv session eller som en python-fil.

>[!NOTE]
> När du använder en lokal utvecklings miljö (till exempel datorn) uppmanas du att autentisera till din arbets yta genom att använda en *enhets kod* första gången du kör följande kod. Följ anvisningarna på skärmen.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Kör den här koden från katalogen i fönstret som har den aktiverade *tutorial1* Conda-miljön `tutorial` .

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Om du kör den här koden får du ett fel meddelande om att du inte har åtkomst till prenumerationen. mer information om autentiseringsalternativ finns i [skapa en arbets yta](how-to-manage-workspace.md?tab=python#create-multi-tenant) .


När du har kört *01-Create-Workspace.py* kommer mappstrukturen att se ut så här:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="Filen config.jspå visas i. azureml-underkatalogen när 01-create-workspace.py har körts":::

Filen `.azureml/config.json` innehåller de metadata som krävs för att ansluta till din Azure Machine Learning-arbetsyta. Den innehåller alltså ditt prenumerations-ID, resurs grupp och namn på arbets ytan. 

> [!NOTE]
> Innehållet i `config.json` är inte hemligheter. Det är bra att dela den här informationen.
>
> Autentisering krävs fortfarande för att interagera med din Azure Machine Learning-arbetsyta.

> [!div class="nextstepaction"]
> [Jag har skapat en arbets yta](?success=create-workspace#cluster) som [jag råkat ut för ett problem](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Skapa ett Azure Machine Learning beräknings kluster

Skapa ett Python-skript i `tutorial` katalogen på den översta nivån som heter `02-create-compute.py` . Fyll i det med följande kod för att skapa ett Azure Machine Learning beräknings kluster som automatiskt skalar mellan noll och fyra noder:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Kör python-filen i fönstret som har den aktiverade *tutorial1* Conda-miljön:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> När klustret skapas har 0 noder etablerades. Klustret debiteras *inga* kostnader förrän du skickar ett jobb. Klustret kommer att skalas ned när det har varit inaktivt i 2 400 sekunder (40 minuter).

Mappstrukturen kommer nu att se ut så här:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="Lägg till 02-create-compute.py i själv studie katalogen":::

> [!div class="nextstepaction"]
> [Jag har skapat ett beräknings kluster](?success=create-compute-cluster#next-steps) som [Jag stötte på ett problem](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Visa i Studio

Logga in på [Azure Machine Learning Studio](https://ml.azure.com) om du vill visa arbets ytan och den beräknings instans som du har skapat.

1. Välj den **prenumeration** som du använde för att skapa arbets ytan.
1. Välj **arbets ytan Machine Learning** som du har skapat, *självstudie – WS*.
1. När arbets ytan har lästs in väljer du **beräkning** på vänster sida.
1. Välj fliken **Compute Clusters** överst.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Skärm bild: Visa beräknings instansen i din arbets yta.":::

I den här vyn visas det etablerade beräknings klustret, tillsammans med antalet inaktiva noder, upptagna noder och avetablerade noder.  Eftersom du inte har använt klustret ännu, är alla noder för närvarande inte etablerade.

## <a name="next-steps"></a>Nästa steg

I den här själv studie kursen har du:

- En Azure Machine Learning-arbetsyta har skapats.
- Konfigurera din lokala utvecklings miljö.
- Skapade ett Azure Machine Learning beräknings kluster.

I de andra delarna av den här självstudien får du lära dig:

* Del 2. Kör kod i molnet med hjälp av Azure Machine Learning SDK för python.
* Del 3. Hantera python-miljön som du använder för modell träning.
* Del 4. Ladda upp data till Azure och använda dessa data i utbildningen.

Fortsätt till nästa självstudie och gå igenom sändningen av ett skript till Azure Machine Learning beräknings kluster.

> [!div class="nextstepaction"]
> [Självstudie: kör en "Hello World!" Python-skript i Azure](tutorial-1st-experiment-hello-world.md)
