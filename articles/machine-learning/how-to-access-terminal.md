---
title: Få åtkomst till en instans av en beräknings instans i din arbets yta
titleSuffix: Azure Machine Learning
description: Använd terminalen på en beräknings instans för git-åtgärder för att installera paket och lägga till kernels.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100101568"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Få åtkomst till en instans av beräknings instanser i din arbets yta

Få åtkomst till terminalen av en beräknings instans i din arbets yta för att:

* Använd filer från git-och version-filer. De här filerna lagras i fil systemet för arbets ytan, inte begränsade till en enda beräknings instans.
* Installera paket på beräknings instansen.
* Skapa extra kernels på beräknings instansen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Åtkomst till en Terminal

För att få åtkomst till terminalen:

1. Öppna din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).
1. På den vänstra sidan väljer du **antecknings böcker**.
1. Välj den **Öppna Terminal** -avbildningen.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Öppna terminalfönstret":::

1. När en beräknings instans körs visas terminalfönstret för den beräknings instansen.
1. När ingen beräknings instans körs använder du avsnittet **Compute** till höger för att starta eller skapa en beräknings instans.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Starta eller skapa en beräknings instans":::

Förutom stegen ovan kan du också komma åt terminalen från:

* RStudio: Välj fliken **Terminal** längst upp till vänster.
* Jupyter Lab: Välj panelen **Terminal** under den **andra** rubriken på fliken Start.
* Jupyter: Välj **ny>Terminal** överst till höger på fliken filer.
* SSH till datorn, om du har aktiverat SSH-åtkomst när beräknings instansen skapades.

## <a name="copy-and-paste-in-the-terminal"></a>Kopiera och klistra in i terminalen

> * Windows: `Ctrl-Insert` för att kopiera och använda `Ctrl-Shift-v` eller `Shift-Insert` Klistra in.
> * Mac OS: `Cmd-c` för att kopiera och `Cmd-v` Klistra in.
> * FireFox/IE kanske inte stöder urklipps behörigheter korrekt.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Använda filer från git-och versions-filer

Få åtkomst till alla git-åtgärder från terminalen. Alla git-filer och mappar kommer att lagras i fil systemet för arbets ytan. Med den här lagringen kan du använda de här filerna från alla beräknings instanser i din arbets yta.

> [!NOTE]
> Lägg till dina filer och mappar var som helst under mappen **~/CloudFiles/Code/Users** så att de visas i alla dina Jupyter-miljöer.

Lär dig mer om [att klona git-databaser till fil systemet för arbets ytan](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Installera paket

 Installera paket från ett terminalfönster. Installera python-paket i **python 3,6-azureml-** miljön.  Installera R-paket i **R** -miljön.

Eller så kan du installera paket direkt i Jupyter Notebook eller RStudio:

* RStudio Använd fliken **paket** längst ned till höger eller fliken **konsol** längst upp till vänster.  
* Python: Lägg till installations kod och kör i en Jupyter Notebook cell.

> [!NOTE]
> För paket hantering i en bärbar dator använder du **% pip** eller **% Conda** Magic Functions för att automatiskt installera paket i den **aktuella kerneln** i stället för **! pip** eller **! Conda** som refererar till alla paket (inklusive paket utanför den aktuella kerneln som körs)

## <a name="add-new-kernels"></a>Lägg till nya kärnor

> [!WARNING]
>  När du anpassar beräknings instansen ser du till att du inte tar bort **azureml_py36** Conda-miljön eller **python 3,6-azureml-** kärnan. Detta krävs för Jupyter/JupyterLab-funktioner

Så här lägger du till en ny Jupyter-kernel till beräknings instansen:

1. Använd terminalfönstret för att skapa en ny miljö.  Koden nedan skapar till exempel `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Aktivera miljön.  Till exempel när du har skapat `newenv` :

    ```shell
    conda activate newenv
    ```

1. Installera pip-och ipykernel-paketet i den nya miljön och skapa en kernel för det Conda-avsnittet

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Alla [tillgängliga Jupyter-kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) kan installeras.

## <a name="manage-terminal-sessions"></a>Hantera Terminal-sessioner

 Välj **Visa aktiva sessioner** i terminalfönstret för att se en lista över alla aktiva terminalserversessioner. När det inte finns några aktiva sessioner kommer den här fliken att inaktive ras.

Stäng eventuella oanvända sessioner för att bevara beräknings instansens resurser.