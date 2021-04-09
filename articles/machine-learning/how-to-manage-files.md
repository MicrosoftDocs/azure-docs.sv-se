---
title: Skapa och hantera filer i din arbets yta
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och hanterar filer i din arbets yta i Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100101592"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Skapa och hantera filer i din arbets yta

Lär dig hur du skapar och hanterar filerna i din Azure Machine Learning-arbetsyta.  De här filerna lagras i standard lagrings ytan. Filer och mappar kan delas med någon annan med Läs behörighet till arbets ytan och kan användas från alla beräknings instanser i arbets ytan.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Skapa filer

Så här skapar du en ny fil i standardmappen ( `Users > yourname` ):

1. Öppna din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).
1. På den vänstra sidan väljer du **antecknings böcker**.
1. Välj **+** avbildningen.
1. Välj avbildningen  **skapa en ny fil** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Skapa en ny fil":::

1. Ge filen ett namn.
1. Välj en filtyp.
1. Välj **Skapa**.

Antecknings böcker och de flesta text fil typer visas i avsnittet för förhands granskning.  De flesta andra filtyper har ingen förhands granskning.

Så här skapar du en ny fil i en annan mapp:
1. Välj "..." i slutet av mappen
1. Välj **Skapa ny fil**.

> [!IMPORTANT]
> Innehåll i antecknings böcker och skript kan potentiellt läsa data från dina sessioner och komma åt data utan din organisation i Azure.  Läs bara in filer från betrodda källor. Mer information finns i [metod tips för säker kod](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Hantera filer med git

[Använd en instans av en beräknings instans](how-to-access-terminal.md#git) för att klona och hantera git-databaser.

## <a name="clone-samples"></a>Klona exempel

Din arbets yta innehåller en **exempel** -mapp med antecknings böcker som har utformats för att hjälpa dig att utforska SDK och fungerar som exempel för dina egna Machine Learning-projekt.   enda de här antecknings böckerna i din egen mapp för att köra och redigera dem.  

Ett exempel finns i [Självstudier: skapa ditt första ml experiment](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Dela filer

Kopiera och klistra in URL: en för att dela en fil.  Endast andra användare av arbets ytan har åtkomst till den här URL: en.  Läs mer om hur [du beviljar åtkomst till din arbets yta](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Ta bort en fil

Du *kan inte* ta bort **exempel filen Notebooks** .  De här filerna är en del av Studio och uppdateras varje gången ett nytt SDK publiceras.  

Du *kan* ta bort filer som finns i avsnittet **filer** på något av följande sätt:

* I Studio väljer du **...** i slutet av en mapp eller fil.  Se till att använda en webbläsare som stöds (Microsoft Edge, Chrome eller Firefox).
* [Använd en Terminal](how-to-access-terminal.md) från valfri beräknings instans i din arbets yta. Mappen **~/CloudFiles** mappas till lagring på ditt lagrings konto för din arbets yta.
* I antingen Jupyter eller JupyterLab med sina verktyg.
