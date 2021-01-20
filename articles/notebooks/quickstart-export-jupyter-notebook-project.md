---
title: Exportera ett Jupyter Notebook-projekt från Azure Notebooks för hands version
description: Exportera snabbt ett Jupyter Notebook-projekt.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: ea16bd61b542217fb6f2d5ba1d926a0bc19d4cce
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606135"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Snabb start: exportera ett Jupyter Notebook projekt i Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

I den här snabb starten ska du ladda ned ett Azure Notebooks-projekt för användning i andra Jupyter Notebook-lösningar. 

## <a name="prerequisites"></a>Förutsättningar

Ett befintligt Azure Notebooks-projekt.

## <a name="export-an-azure-notebooks-project"></a>Exportera ett Azure Notebooks-projekt

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Länken Mina projekt överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. Välj ett projekt.
1. Klicka på Ladda ned "Ladda ned" om du vill utlösa hämtningen av en zip-fil som innehåller alla projektfiler.
1. Du kan också klicka på knappen "Ladda ned projekt" på sidan för att ladda ned alla filer för ett specifikt projekt.

När du har hämtat dina projektfiler kan du använda dem med andra Jupyter Notebook-lösningar. Vissa alternativ som beskrivs i avsnitten nedan är: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Skapa en miljö för antecknings böcker

Om du vill skapa en miljö som matchar Azure Notebooks för hands versionen kan du använda skript filen som anges i GitHub.

1. Navigera till Azure Notebooks [GitHub-lagringsplatsen](https://github.com/microsoft/AzureNotebooks) eller [få direkt åtkomst till mappen miljö](https://aka.ms/aznbrequirementstxt).
1. Från en kommando tolk navigerar du till den katalog som du vill använda för dina projekt.
1. Hämta innehållet i miljömappen och följ anvisningarna i README för att installera Azure Notebooks-paket beroenden.


## <a name="use-notebooks-in-visual-studio-code"></a>Använda antecknings böcker i Visual Studio Code

[Vs Code](https://code.visualstudio.com/) är en kostnads fri kod redigerare som du kan använda lokalt eller ansluten till fjärrdatorer. Tillsammans med python-tillägget erbjuder den en fullständig miljö för python-utveckling, inklusive en omfattande inbyggd upplevelse för att arbeta med Jupyter-anteckningsböcker. 

![VS Code Jupyter Notebook support](media/vs-code-jupyter-notebook.png)

När du har [laddat ned](#export-an-azure-notebooks-project) dina projektfiler kan du använda vs Code. Anvisningar om hur du använder VS Code med Jupyter Notebooks finns i avsnittet [arbeta med Jupyter-anteckningsböcker i Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) och [data science i självstudier för Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial) .

Du kan också använda det [Azure Notebooks miljö skriptet](#create-an-environment-for-notebooks) med Visual Studio Code för att skapa en miljö som matchar Azure Notebooks för hands versionen.

## <a name="use-notebooks-in-github-codespaces"></a>Använda antecknings böcker i GitHub-Codespaces

GitHub Codespaces innehåller molnbaserade miljöer där du kan redigera dina antecknings böcker med Visual Studio Code eller i webbläsaren. Den ger samma fantastiska Jupyter-upplevelse som VS-kod, men utan att behöva installera något på din enhet. Om du inte vill konfigurera en lokal miljö och föredra en molnbaserad lösning, är det ett bra alternativ att skapa en codespace. Så här kommer du igång:
1. [Hämta](#export-an-azure-notebooks-project) dina projektfiler.
1. [Skapa en GitHub-lagringsplats](https://help.github.com/github/getting-started-with-github/create-a-repo) för att lagra dina antecknings böcker.   
1. [Lägg till dina filer](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) till lagrings platsen.
1. [Begär åtkomst till GitHub Codespaces Preview](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Använda antecknings böcker med Azure Machine Learning

Azure Machine Learning tillhandahåller en maskin inlärnings plattform från slut punkt till slut punkt som gör det möjligt för användare att skapa och distribuera modeller snabbare på Azure. Med Azure ML kan du köra Jupyter-anteckningsböcker på en virtuell dator eller en delad kluster dator miljö. Om du behöver en molnbaserad lösning för din ML-arbets belastning med experiment spårning, data hantering med mera, rekommenderar vi Azure Machine Learning. Komma igång med Azure ML:

1. [Hämta](#export-an-azure-notebooks-project) dina projektfiler.
1. [Skapa en arbets yta](../machine-learning/how-to-manage-workspace.md) i Azure Portal.

   ![Skapar en arbetsyta](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Öppna [Azure Studio (för hands version)](https://ml.azure.com/).
1. Välj **antecknings böcker** i navigerings fältet på vänster sida.
1. Klicka på knappen **överför filer** och överför projektfilerna som du laddade ned från Azure Notebooks.

Om du vill ha mer information om Azure ML och köra Jupyter-anteckningsböcker kan du läsa [dokumentationen](../machine-learning/how-to-run-jupyter-notebooks.md) eller testa [introduktionen för att Machine Learning](/learn/modules/intro-to-azure-machine-learning-service/) modul på Microsoft Learn.


## <a name="use-azure-lab-services"></a>Använd Azure Lab Services

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) ge lärare möjlighet att enkelt konfigurera och tillhandahålla åtkomst på begäran till förkonfigurerade virtuella datorer för ett helt klass rum. Om du är ute efter ett sätt att arbeta med Jupyter-anteckningsböcker och moln beräkning i en skräddarsydd klass rums miljö är Lab-tjänster ett bra alternativ.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 När du har [hämtat](#export-an-azure-notebooks-project) dina projektfiler kan du använda dem med Azure Lab Services. Anvisningar om hur du konfigurerar ett labb finns i [Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Använd GitHub

GitHub tillhandahåller ett kostnads fritt, käll kontrolls sätt att lagra antecknings böcker (och andra filer), dela dina antecknings böcker med andra och samar beta. Om du letar efter ett sätt att dela dina projekt och samar beta med andra, är GitHub ett bra alternativ och kan kombineras med [GitHub Codespaces](#use-notebooks-in-github-codespaces) för en bra utvecklings upplevelse. Komma igång med GitHub

1. [Hämta](#export-an-azure-notebooks-project) dina projektfiler.
1. [Skapa en GitHub-lagringsplats](https://help.github.com/github/getting-started-with-github/create-a-repo) för att lagra dina antecknings böcker. 
1. [Lägg till dina filer](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) till lagrings platsen.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om python i Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Lär dig mer om Azure Machine Learning-och Jupyter-anteckningsböcker](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Lär dig mer om GitHub Codespaces](https://github.com/features/codespaces)
- [Läs mer om Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Lär dig mer om GitHub](https://help.github.com/github/getting-started-with-github/)