---
title: Skapa en C#-funktion med Visual Studio Code-Azure Functions
description: Lär dig hur du skapar en C#-funktion och sedan publicerar det lokala projektet till Server lös värd i Azure Functions med hjälp av Azure Functions tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121039"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Snabb start: skapa en C#-funktion i Azure med Visual Studio Code

> [!div class="op_single_selector" title1="Välj ditt funktions språk: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Annat (Go/Rust)](create-first-function-vs-code-other.md)

I den här artikeln använder du Visual Studio Code för att skapa en C#-baserad funktion i C#-klassen som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till <abbr title="En data bearbetnings miljö där all information om servern är transparent för programutvecklare, vilket fören klar distributionen och hanteringen av kod.">serverlöst</abbr> miljö i <abbr title="Azures tjänst som tillhandahåller en enklare data behandlings miljö utan kostnad för program.">Azure Functions</abbr>.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [CLI-baserad version](create-first-function-cli-csharp.md) av den här artikeln.
    
## <a name="1-configure-your-environment"></a>1. Konfigurera din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

+ En Azure <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">konto</abbr> med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) version 3. x.

+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio Code.  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. skapa ditt lokala projekt

I det här avsnittet använder du Visual Studio Code för att skapa en lokal <abbr title="En logisk behållare för en eller flera enskilda funktioner som kan distribueras och hanteras tillsammans.">Azure Functions projekt</abbr> i C#. Senare i den här artikeln ska du publicera funktionskoden till Azure.

1. Välj Azure-ikonen i <abbr title="Den lodräta gruppen med ikoner till vänster i Visual Studio Code-fönstret.">Aktivitets fältet</abbr>, i avsnittet **Azure: Functions** väljer du ikonen **Skapa nytt projekt...** .

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `C#`.

    + **Välj en mall för projektets första funktion**: Välj `HTTP trigger`.

    + **Ange ett funktionsnamn**: Skriv `HttpExample`.

    + **Ange ett namnområde**: Skriv `My.Functions`.

    + **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten. Mer information om auktoriseringsregler finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

1. Med hjälp av den här informationen genererar Visual Studio Code ett Azure Functions-projekt med HTTP <abbr title="Den typ av händelse som anropar funktionens kod, till exempel en HTTP-begäran, ett Queue-meddelande eller en angiven tid.">utlösare</abbr>. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [genererade projektfiler](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

När du har kontrollerat att funktionen fungerar korrekt på den lokala datorn är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. publicera projektet till Azure

I det här avsnittet ska du skapa en Function-app och relaterade resurser i din Azure-prenumeration och sedan distribuera din kod. 

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure. 

1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    

1. Ange följande information i meddelanderutorna:

    + **Välj mapp**: Välj en mapp från din arbets yta eller bläddra till en mapp som innehåller din Function-app. Du ser inte det här om du redan har en giltig Function-app öppen.

    + **Välj prenumeration**: Välj den prenumeration som ska användas. Du ser inte det här om du bara har en prenumeration.

    + **Välj Funktionsapp i Azure**: Välj `+ Create new Function App`. (Välj inte `Advanced` alternativet, som inte beskrivs i den här artikeln.)

    + **Ange ett globalt unikt namn för Function-appen**: Ange ett namn som är giltigt i en URL-sökväg. Det namn som du anger verifieras för att se till att det är <abbr title="Namnet måste vara unikt för alla Function-projekt som används av alla Azure-kunder globalt. Normalt använder du en kombination av ditt personliga eller företagets namn, program namn och en numerisk identifierare, som i Contoso-bizapp-FUNC-20">unikt i Azure Functions</abbr>. 

    + **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig.

    Tillägget visar status för enskilda resurser när de skapas i Azure i meddelande fältet.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Meddelande om skapande av Azure-resurs":::

1. När det är slutfört skapas följande Azure-resurser i din prenumeration med hjälp av namn baserat på ditt funktions program namn:

    + En **resurs grupp**, som är en logisk behållare för relaterade resurser.
    + Ett standard **Azure Storage-konto** som upprätthåller tillstånd och annan information om dina projekt.
    + En **förbruknings plan** som definierar den underliggande värden för din server lös Function-app. 
    + En **Function-app** som tillhandahåller miljön för att köra funktions koden. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser inom samma värd plan.
    + En **Application Insights instans** som är ansluten till Function-appen, som spårar användningen av din server lös funktion.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 

    > [!TIP]
    > Som standard skapas de Azure-resurser som krävs av din Function-app baserat på namnet på den Function-app som du anger. Som standard skapas de också i samma nya resurs grupp med Function-appen. Om du antingen vill anpassa namnen på dessa resurser eller återanvända befintliga resurser måste du i stället [publicera projektet med avancerade alternativ för att skapa](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).


1. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat. Om du saknar meddelandet väljer du klock ikonen i det nedre högra hörnet för att se den igen.

    ![Skapa fullständig avisering](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. kör funktionen i Azure

1. Gå tillbaka till avsnittet **Azure: Functions** i sido fältet, expandera din prenumeration, din nya Function-app och **functions.** Högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på `HttpExample` funktionen och välj **Kör funktion nu..**..

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Kör funktionen nu i Azure från Visual Studio Code":::

1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` .

    Skicka meddelandet till din funktion genom att trycka på RETUR.  

1. När funktionen körs i Azure och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code.

## <a name="5-clean-up-resources"></a>5. Rensa resurser

När du fortsätter till [Nästa steg](#next-steps) och lägger till ett <abbr title="Ett sätt att associera en funktion med en lagrings kö, så att den kan skapa meddelanden i kön.">Utgående bindning för Azure Storage-kö</abbr> till din funktion måste du hålla alla dina resurser på plats för att bygga vidare på det du redan har gjort.

Annars kan du använda följande steg för att ta bort Function-appen och dess relaterade resurser för att undvika ytterligare kostnader.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Mer information om funktions kostnader finns i [uppskatta förbruknings plan kostnader](functions-consumption-costs.md).

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio Code för att skapa en funktionsapp med en enkel HTTP-utlöst funktion. I nästa artikel expanderar du den funktionen genom att lägga till utdata <abbr title="En deklarativ anslutning mellan en funktion och andra resurser. En indata-bindning tillhandahåller data till funktionen. en utgående bindning tillhandahåller data från funktionen till andra resurser.">enheten</abbr>. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö. 

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
