---
title: Skapa och dela en Jupyter Notebook på Azure Notebooks för hands version
description: Skapa och kör snabbt en Jupyter Notebook för Azure Notebooks för hands version och dela sedan den bärbara datorn med andra.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589341"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Snabb start: skapa och dela en antecknings bok i Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

I den här snabb starten skapar du och kör en Jupyter Notebook på Azure Notebooks och delar sedan den bärbara datorn med andra. Med Jupyter kan du enkelt kombinera markdown text, körbar kod, beständiga data, grafik och visualiseringar på en enda delbar arbets yta, antecknings boken. Azure Notebooks är en kostnads fri värdbaserad tjänst för att utveckla och köra Jupyter-anteckningsböcker i molnet utan installation.

## <a name="prerequisites"></a>Förutsättningar
Inga.

## <a name="create-a-new-project-and-notebook"></a>Skapa ett nytt projekt och en ny antecknings bok

1. Gå till [Azure Notebooks-platsen ( https://notebooks.azure.com) ](https://notebooks.azure.com) och logga in. Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Länken Mina projekt överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du **+ nytt projekt** (kortkommando: n). Knappen får bara visas som **+** om webbläsarfönstret är smalare:

    ![Kommandot nytt projekt på sidan mina projekt](media/quickstarts/new-project-command.png)

1. I popup-fönstret **Skapa nytt projekt** som visas anger eller anger du följande information och väljer sedan **skapa**:

   - **Projekt namn**: Hello World i python
   - **Projekt-ID**: Hello – världen – python
   - **Offentligt projekt**: (avmarkerat)
   - **Skapa en README.MD**: (avmarkerad)

     ![Popup-fönstret nytt projekt med ifylld information](media/quickstarts/new-project-popup.png)

1. Efter en liten stund navigerar Azure Notebooks till det nya projektet. Lägg till en antecknings bok i projektet genom att markera List rutan **+ ny** (som endast visas som **+** ) och sedan välja **antecknings bok**:

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="Ett nytt, tomt projekt och kommandot Lägg till antecknings bok." lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. I popup-fönstret **Skapa ny antecknings bok** visas anger du ett namn för din bärbara dator, till exempel *HelloWorldInPython. ipynb* (*. ipynb* betyder ironpython (Jupyter) Notebook) och väljer **python 3,6** för språket (kallas även för *kernel*):

    ![Popup-fönstret Skapa ny antecknings bok](media/quickstarts/new-notebook-popup.png)

1. Välj **ny** för att slutföra skapandet av antecknings boken, som sedan visas i projektets fil lista:

    ![Ny antecknings bok visas i projektets fillista](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Köra anteckningsboken

1. Välj den nya antecknings boken som ska köras i redigeraren. den kernel du valde (python 3,6 i det här exemplet) aktive ras automatiskt för den här antecknings boken:

    ![Vy över en ny antecknings bok i Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Som standard har antecknings boken en tom kod cell. Om du vill ändra cell typen till **markdown**, använder du List rutan cell typ för att välja **markdown**:

    ![Ändra cell typen i en ny antecknings bok](media/quickstarts/create-notebook-cell-type.png)

1. Skriv eller klistra in följande rubrik text i cellen:

    ```markdown
    # Hello World in Python
    ```

1. Eftersom du redigerar markdown visas texten som en rubrik med "#". Om du vill rendera markdown i HTML väljer du knappen **Kör** . Azure Notebooks skapar en ny kod cell automatiskt efteråt:

    ![Knappen Kör för en cell och en renderad markdown](media/quickstarts/run-cell-markdown-render.png)

1. Ange följande python-kod i cellen Code:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Kör koden genom att välja **Kör** (kortkommando: Shift + Retur). Under cellen ser du lyckad utdata som liknar följande text:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Välj ikonen Spara för att spara ditt arbete:

    ![Ikonen Spara i verktygsfältet Jupyter Notebook](media/quickstarts/hello-results-save-icon.png)

1. Välj **File**  >  meny kommandot**Stäng och** stoppa för att stoppa servern och stänga webbläsarfönstret.

## <a name="share-the-notebook"></a>Dela antecknings boken

Om du vill dela din bärbara dator går du tillbaka till projekt sidan om det behövs, högerklickar på anteckningsbok-filen, väljer **Kopiera länk** (kortkommando: y) och klistrar in länken i ett lämpligt meddelande (e-post, snabb meddelanden osv.).

På projekt sidan kan du också använda **delnings** menyn för att hämta en länk, skapa ett e-postmeddelande med länken eller hämta HTML-och markdown inbäddnings kod:

![Projekt resurs kommando](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa och köra en Jupyter Notebook att göra en linjär regression](tutorial-create-run-jupyter-notebook.md)
