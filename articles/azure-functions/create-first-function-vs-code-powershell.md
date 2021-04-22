---
title: Skapa en PowerShell-funktion med Visual Studio Code – Azure Functions
description: Lär dig hur du skapar en PowerShell-funktion och sedan publicerar det lokala projektet till en serverlös värd i Azure Functions med hjälp av Azure Functions-tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 58a2b8a5fb386603361afa9fc26b4d323c487691
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862569"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Snabbstart: Skapa en PowerShell-funktion i Azure med Visual Studio Kod

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

I den här artikeln använder du Visual Studio Code för att skapa en PowerShell-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa Azure Functions.

Den här snabbstarten medför en liten kostnad på några cent eller mindre för ditt Azure-konto.

Det finns också en [CLI-baserad version av](create-first-function-cli-powershell.md) den här artikeln.

## <a name="configure-your-environment"></a>Konfigurera din miljö

Innan du börjar kontrollerar du att du har följande krav:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Den [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) version 3.x.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Både [.NET Core 3.1-körning](https://dotnet.microsoft.com/download) och [.NET Core 2.1-körning](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).    

+ [PowerShell-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt i PowerShell. Senare i den här artikeln ska du publicera funktionskoden till Azure.

1. Välj Azure-ikonen i Aktivitetsfältet och gör sedan följande i **Azure: I området för funktioner** väljer du ikonen **Skapa nytt projekt...**.

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `PowerShell`.

    + **Välj en mall för projektets första funktion**: Välj `HTTP trigger`.

    + **Ange ett funktionsnamn**: Skriv `HttpExample`.

    + **Auktoriseringsnivå:** `Anonymous` Välj , som gör att alla kan anropa din funktionsslutpunkt. Mer information om auktoriseringsnivå finns i [Auktoriseringsnycklar.](functions-bindings-http-webhook-trigger.md#authorization-keys)

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

1. Med den här informationen Visual Studio Code ett Azure Functions med en HTTP-utlösare. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [Genererade projektfiler.](functions-develop-vs-code.md#generated-project-files) 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nästa steg

Du har använt [Visual Studio Code för](functions-develop-vs-code.md?tabs=powershell) att skapa en funktionsapp med en enkel HTTP-utlöst funktion. I nästa artikel expanderar du funktionen genom att ansluta till Azure Storage. Mer information om hur du ansluter till andra Azure-tjänster finns i [Lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=powershell).  

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
