---
title: Ansluta till beräknings instans i Visual Studio Code (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du ansluter till en Azure Machine Learning beräknings instans i Visual Studio Code för att köra interaktiva Jupyter Notebook och arbets belastningar för fjärrutveckling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028598"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Ansluta till en Azure Machine Learning beräknings instans i Visual Studio Code (för hands version)

I den här artikeln får du lära dig hur du ansluter till en Azure Machine Learning beräknings instans med hjälp av Visual Studio Code.

En [Azure Machine Learning beräknings instans](concept-compute-instance.md) är en fullständigt hanterad molnbaserad arbets station för data forskare och innehåller funktioner för hantering och företags beredskap för IT-administratörer.

Det finns två sätt som du kan ansluta till en beräknings instans från Visual Studio Code:

* Fjärran sluten beräknings instans. Med det här alternativet får du en komplett utvecklings miljö för att skapa dina Machine Learning-projekt.
* Fjärr Jupyter Notebook Server. Med det här alternativet kan du ange en beräknings instans som en fjärr Jupyter Notebook Server.

## <a name="configure-a-remote-compute-instance"></a>Konfigurera en fjärran sluten beräknings instans

Om du vill konfigurera en fjärran sluten beräknings instans för utveckling behöver du några krav.

* Azure Machine Learning Visual Studio Code-tillägg. Mer information finns i [installations guiden för Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md).
* Azure Machine Learning arbets yta. [Använd tillägget Azure Machine Learning Visual Studio Code för att skapa en ny arbets yta](how-to-manage-resources-vscode.md#create-a-workspace) om du inte redan har en.
* Azure Machine Learning beräknings instans. [Använd tillägget Azure Machine Learning Visual Studio Code för att skapa en ny beräknings instans](how-to-manage-resources-vscode.md#create-compute-instance) om du inte har någon.

Så här ansluter du till din fjärrinstans:

# <a name="vs-code"></a>[VS-kod](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning tillägg

1. I VS Code startar du Azure Machine Learning-tillägget.
1. Expandera noden **Compute instances** i tillägget.
1. Högerklicka på den beräknings instans som du vill ansluta till och välj **Anslut till beräknings instans**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Ansluta till beräknings instans Visual Studio Code Azure ML-tillägg" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Kommando palett

1. I VS Code öppnar du paletten kommando genom att välja **visa > kommando palett**.
1. Ange i text rutan **Azure ml: Anslut till Compute-instansen**.
1. Välj din prenumeration.
1. Välj din arbets yta.
1. Välj din beräknings instans eller skapa en ny.

# <a name="studio"></a>[Studio](#tab/studio)

Navigera till [ml.Azure.com](https://ml.azure.com)

> [!IMPORTANT]
> För att kunna ansluta till din fjärrinstans från Visual Studio Code kontrollerar du att kontot du är inloggad i Azure Machine Learning Studio är samma som du använder i Visual Studio Code.

### <a name="compute"></a>Compute

1. Välj fliken **Compute**
1. I kolumnen *program-URI* väljer du **vs-kod** för den beräknings instans som du vill ansluta till.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Anslut till Compute instance VS Code Azure ML Studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Notebook-fil

1. Välj fliken **Notebook**
1. På fliken *Notebook* väljer du den fil som du vill redigera.
1. Välj **redigerare > redigera i vs Code (för hands version)**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Anslut till Compute instance VS Code Azure ML Notebook" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Ett nytt fönster öppnas för fjärrinstansen av beräkningen. När du försöker upprätta en anslutning till en fjärran sluten beräknings instans sker följande uppgifter:

1. Auktorisering. Vissa kontroller utförs för att se till att användaren som försöker upprätta en anslutning har behörighet att använda beräknings instansen.
1. VS Code-fjärrservern installeras på beräknings instansen.
1. En WebSocket-anslutning upprättas för interaktion med real tid.

När anslutningen har upprättats är den bestående. En token utfärdas i början av sessionen, som uppdateras automatiskt för att upprätthålla anslutningen till beräknings instansen.

När du har anslutit till fjärrberäknings instansen använder du redigeraren för att:

* Skapa [och hantera filer på en fjärran sluten beräknings instans eller fil resurs](https://code.visualstudio.com/docs/editor/codebasics).
* Använd [vs Code Integrated Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) för att [köra kommandon och program på fjärrinstansen av beräkningen](how-to-access-terminal.md).
* [Felsöka skript och program](https://code.visualstudio.com/Docs/editor/debugging)
* [Använd VS Code för att hantera dina git-databaser](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Konfigurera beräknings instans som fjärran sluten Notebook Server

För att kunna konfigurera en beräknings instans som en fjärran sluten Jupyter Notebook server behöver du några krav:

* Azure Machine Learning Visual Studio Code-tillägg. Mer information finns i [installations guiden för Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md).
* Azure Machine Learning arbets yta. [Använd tillägget Azure Machine Learning Visual Studio Code för att skapa en ny arbets yta](how-to-manage-resources-vscode.md#create-a-workspace) om du inte redan har en.

Så här ansluter du till en beräknings instans:

1. Öppna en Jupyter Notebook i Visual Studio Code.
1. När den integrerade Notebook-upplevelsen läses in väljer du **Jupyter-Server**.

    > [!div class="mx-imgBorder"]
    > ![Starta Azure Machine Learning listruta för Jupyter Notebook Server](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Du kan också använda kommando-paletten:

    1. Öppna paletten kommando genom att välja **visa > kommando paletten** på Meny raden.
    1. Ange i text rutan `Azure ML: Connect to Compute instance Jupyter server` .

1. Välj i `Azure ML Compute Instances` listan över Jupyter-Server alternativ.
1. Välj din prenumeration i listan över prenumerationer. Om du tidigare har konfigurerat standard arbets ytan Azure Machine Learning hoppas det här steget över.
1. Välj din arbets yta.
1. Välj din beräknings instans i listan. Om du inte har en sådan, väljer du **Skapa ny Azure ml-beräkning-instans** och följer anvisningarna för att skapa en.
1. För att ändringarna ska börja gälla måste du läsa in Visual Studio Code igen.
1. Öppna en Jupyter Notebook och kör en cell.

> [!IMPORTANT]
> Du **måste** köra en cell för att kunna upprätta anslutningen.

Nu kan du fortsätta att köra celler i Jupyter Notebook.

> [!TIP]
> Du kan också arbeta med python-skriptfiler (. py) som innehåller Jupyter-liknande kod celler. Mer information finns i den [interaktiva dokumentationen för Visual Studio Code python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat Visual Studio Code-fjärrhantering kan du använda en beräknings instans som fjärrberäkning från Visual Studio Code för att [interaktivt felsöka din kod](how-to-debug-visual-studio-code.md).

[Självstudie: träna din första ml-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräknings instans med en integrerad antecknings bok.
