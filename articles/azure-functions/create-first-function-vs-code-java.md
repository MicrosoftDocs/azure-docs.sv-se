---
title: Skapa en Java-funktion med Visual Studio Code-Azure Functions
description: Lär dig hur du skapar en Java-funktion och sedan publicerar det lokala projektet till Server lös värd i Azure Functions med hjälp av Azure Functions tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-java-uiex
ms.openlocfilehash: 10ac677fb65428e8b8855c43b319bbebe7bbbb71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701440"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Snabb start: skapa en Java-funktion i Azure med Visual Studio Code

> [!div class="op_single_selector" title1="Välj ditt funktions språk: "]
> - [Java](create-first-function-vs-code-java.md)
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Annat (Go/Rust)](create-first-function-vs-code-other.md)

I den här artikeln använder du Visual Studio Code för att skapa en Java-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

> [!NOTE]
> Om Visual Studio Code inte är det önskade utvecklings verktyget kan du kolla in våra liknande självstudier för Java-utvecklare med [maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) och [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="configure-your-environment"></a>Konfigurera din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8 eller 11.

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt i Java. Senare i den här artikeln ska du publicera funktionskoden till Azure. 

1. Välj Azure-ikonen i Aktivitetsfältet och gör sedan följande i **Azure: I området för funktioner** väljer du ikonen **Skapa nytt projekt...**.

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `Java`.

    + **Välj en version av Java**: Välj `Java 8` eller `Java 11` , den Java-version som dina funktioner körs på i Azure. Välj en Java-version som du har verifierat lokalt.

    + **Ange ett grupp-ID**: Välj `com.function` .

    + **Ange ett artefakt-ID**: Välj `myFunction` .

    + **Ange en version**: Välj `1.0-SNAPSHOT` .

    + **Ange ett paket namn**: Välj `com.function` .

    + **Ange ett namn på appen**: Välj `myFunction-12345` .

    + **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten. Mer information om autentiseringsnivå finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

1. Med hjälp av den här informationen genererar Visual Studio Code ett Azure Functions-projekt med en HTTP-utlösare. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [genererade projektfiler](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

När du har kontrollerat att funktionen fungerar korrekt på den lokala datorn är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nästa steg

Du har använt [Visual Studio Code](functions-develop-vs-code.md?tabs=java) för att skapa en Function-app med en enkel http-utlöst funktion. I nästa artikel expanderar du den funktionen genom att ansluta till Azure Storage. Mer information om hur du ansluter till andra Azure-tjänster finns [i lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md?tabs=java). 

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
