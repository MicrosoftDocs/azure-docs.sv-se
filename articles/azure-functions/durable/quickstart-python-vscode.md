---
title: Skapa din första tåliga funktion i Azure med python
description: Skapa och publicera en Azure-beständig funktion i python med Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 12/23/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 567388277f5a68ae23e8c806a8581c177c3fd1dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549472"
---
# <a name="create-your-first-durable-function-in-python"></a>Skapa din första varaktiga funktion i python

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

I den här artikeln får du lära dig hur du använder Azure Functions-tillägget för Visual Studio Code för att skapa och testa en beständig ”Hello World”-funktion lokalt.  Den här funktionen orkestrerar och kedjar samman anrop till andra funktioner. Du publicerar sedan funktionskoden till Azure.

![Köra beständiga funktioner i Azure](./media/quickstart-python-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Installera [Visual Studio Code](https://code.visualstudio.com/download).

* Installera [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code-tillägget.

* Kontrol lera att du har den senaste versionen av [Azure Functions Core tools](../functions-run-local.md).

* Durable Functions kräver ett Azure Storage-konto. Du behöver en Azure-prenumeration.

* Kontrol lera att du har version 3,6, 3,7 eller 3,8 av [python](https://www.python.org/) installerat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt 

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt. 

1. Tryck på F1 (eller <kbd>CTRL + cmd + Shift + P</kbd>) i Visual Studio Code för att öppna kommando paletten. I paletten kommando söker du efter och väljer `Azure Functions: Create New Project...` .

    ![Skapa funktion](media/quickstart-python-vscode/functions-create-project.png)

1. Välj en tom mapplats för ditt projekt och välj **Välj**.

1. Ange följande information i instruktionerna nedan:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj ett språk för ditt projekt för Function-appen | Python | Skapa ett lokalt python Functions-projekt. |
    | Välj en version | Azure Functions v3 | Du ser bara det här alternativet när kärn verktygen inte redan har installerats. I det här fallet installeras kärn verktyg första gången du kör appen. |
    | Python-version | Python 3,6, 3,7 eller 3,8 | VS Code kommer att skapa en virtuell miljö med den version du väljer. |
    | Välj en mall för projektets första funktion | Hoppa över så länge | |
    | Välj hur du vill öppna projektet | Öppna i aktuellt fönster | Öppnar VS-kod i den mapp som du har valt. |

Visual Studio Code installerar Azure Functions Core Tools, om det behövs. Det skapar också ett app-projekt i en mapp. Det här projektet innehåller [host.js](../functions-host-json.md) och [local.settings.jsi](../functions-run-local.md#local-settings-file) konfigurationsfiler.

En *requirements.txt* -fil skapas också i rotmappen. Den anger de python-paket som behövs för att köra din Function-app.

## <a name="install-azure-functions-durable-from-pypi"></a>Installera Azure-Functions – tålig från PyPI

När du skapade projektet skapade Azure Functions VS Code-tillägget automatiskt en virtuell miljö med den valda python-versionen. Du kommer att aktivera den virtuella miljön i en Terminal och installera vissa beroenden som krävs för Azure Functions och Durable Functions.

1. Öppna *requirements.txt* i redigeraren och ändra dess innehåll till följande:

    ```
    azure-functions
    azure-functions-durable
    ```

1. Öppna redigerarens integrerade Terminal i den aktuella mappen (<kbd>CTRL + SHIFT + '</kbd>).

1. I den integrerade terminalen aktiverar du den virtuella miljön i den aktuella mappen:

    **Linux eller macOS**

    ```bash
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    .venv\scripts\activate
    ```

    ![Aktivera virtuell miljö](media/quickstart-python-vscode/activate-venv.png)

1. I den integrerade terminalen där den virtuella miljön är aktive rad använder du pip för att installera de paket som du nyss definierade:

    ```bash
    python -m pip install -r requirements.txt
    ```

## <a name="create-your-functions"></a>Skapa dina funktioner

En grundläggande Durable Functions-app innehåller tre funktioner:

* *Orchestrator-funktion* – beskriver ett arbets flöde som dirigerar andra funktioner.
* *Aktivitets funktion* – anropas av Orchestrator-funktionen, utför arbete och kan eventuellt returnera ett värde.
* *Klient funktion* – en vanlig Azure-funktion som startar en Orchestrator-funktion. I det här exemplet används en HTTP-utlöst funktion.

### <a name="orchestrator-function"></a>Orchestrator-funktion

Du använder en mall för att skapa en hållbar funktions kod i projektet.

1. I paletten kommando söker du efter och väljer `Azure Functions: Create Function...` .

1. Ange följande information i instruktionerna nedan:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Durable Functions Orchestrator | Skapa ett Durable Functions Orchestration |
    | Ange ett funktions namn | HelloOrchestrator | Namnet på den varaktiga funktionen |

Du har lagt till en Orchestrator för att koordinera aktivitets funktioner. Öppna *HelloOrchestrator/ \_ \_ init__. py* för att se Orchestrator-funktionen. Varje anrop för att `context.call_activity` anropa en aktivitets funktion med namnet `Hello` .

Sedan lägger du till funktionen refererad `Hello` aktivitet.

### <a name="activity-function"></a>Aktivitets funktion

1. I paletten kommando söker du efter och väljer `Azure Functions: Create Function...` .

1. Ange följande information i instruktionerna nedan:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Durable Functions aktivitet | Skapa en aktivitets funktion |
    | Ange ett funktions namn | Hello | Namn på din aktivitets funktion |

Du har lagt till `Hello` aktivitets funktionen som anropas av Orchestrator. Öppna *Hej/ \_ \_ init__. py* för att se att det tar ett namn som inmatat och returnerar en hälsning. En aktivitets funktion är den plats där du utför åtgärder som att göra ett databas anrop eller utföra en beräkning.

Slutligen lägger du till en HTTP-utlöst funktion som startar dirigeringen.

### <a name="client-function-http-starter"></a>Klient funktion (HTTP starter)

1. I paletten kommando söker du efter och väljer `Azure Functions: Create Function...` .

1. Ange följande information i instruktionerna nedan:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en mall för din funktion | Durable Functions HTTP-startstarter | Skapa en HTTP-startfunktion |
    | Ange ett funktions namn | DurableFunctionsHttpStart | Namn på din aktivitets funktion |
    | Auktoriseringsnivå | Anonym | I demonstrations syfte tillåter du att funktionen anropas utan autentisering |

Du har lagt till en HTTP-utlöst funktion som startar ett dirigering. Öppna *DurableFunctionsHttpStart/ \_ \_ init__. py* för att se att det används `client.start_new` för att starta en ny dirigering. Sedan använder den `client.create_check_status_response` för att returnera ett HTTP-svar som innehåller URL: er som kan användas för att övervaka och hantera den nya dirigeringen.

Nu har du en Durable Functions-app som kan köras lokalt och distribueras till Azure.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Om du inte har det installerat uppmanas du att installera dessa verktyg första gången du startar en funktion från Visual Studio Code.

1. Om du vill testa funktionen ställer du in en Bryt punkt i `Hello` aktivitetens funktions kod (*hej/ \_ \_ init__. py*). Tryck på F5 eller Välj `Debug: Start Debugging` från kommando paletten för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**.

    > [!NOTE]
    > Mer information om fel sökning hittar du i [Durable Functions Diagnostics](durable-functions-diagnostics.md#debugging) .

1. Durable Functions kräver att ett Azure Storage-konto körs. Vid VS-kod kan du välja ett lagrings konto genom att välja **Välj lagrings konto**.

    ![Skapa lagringskonto](media/quickstart-python-vscode/functions-select-storage.png)

1. Följ instruktionerna och ange följande information för att skapa ett nytt lagrings konto i Azure.

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj en prenumeration | *namnet på din prenumeration* | Välj din Azure-prenumeration |
    | Välj ett lagrings konto | Skapa ett nytt lagringskonto |  |
    | Ange namnet på det nya lagrings kontot | *unikt namn* | Namn på det lagrings konto som ska skapas |
    | Välj en resursgrupp | *unikt namn* | Namnet på den resurs grupp som ska skapas |
    | Välja en plats | *nationella* | Välj en region nära dig |

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](media/quickstart-python-vscode/functions-f5.png)

1. Skicka en HTTP-begäran till URL-slutpunkten med hjälp av webbläsaren eller ett verktyg som [Postman](https://www.getpostman.com/) eller [vändning](https://curl.haxx.se/). Ersätt det sista segmentet med namnet på Orchestrator-funktionen ( `HelloOrchestrator` ). URL-adressen bör liknas `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   Svaret är det första resultatet från HTTP-funktionen som gör att du vet att den beständiga dirigeringen har startat. Det är inte ännu slutresultatet av orkestreringen. Svaret innehåller några användbara URL:er. För tillfället kör vi en fråga om orkestreringens status.

1. Kopiera URL-värdet för `statusQueryGetUri` och klistra in det i webbläsarens Adress fält och kör begäran. Alternativt kan du även fortsätta att använda Postman för att utfärda GET-begäran.

   Begäran kör en fråga mot orkestreringsinstansen om statusen. Du bör få ett möjligt svar som visar att instansen har slutförts och innehåller utdata eller resultat från den varaktiga funktionen. Det ser ut så här: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Stoppa fel sökningen genom att trycka på <kbd>SKIFT + F5</kbd> i vs Code.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Den URL som anropar den HTTP-utlösta funktionen ska ha följande format: `http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Du bör få samma statussvar som förut när du använder den publicerade appen.

## <a name="next-steps"></a>Nästa steg

Du har använt Visual Studio Code för att skapa och publicera en python-app med en robust funktion.

> [!div class="nextstepaction"]
> [Läs mer om vanliga mönster för beständiga funktioner](durable-functions-overview.md#application-patterns)
