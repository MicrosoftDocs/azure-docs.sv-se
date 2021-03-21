---
title: Skapa en python-funktion med Visual Studio Code-Azure Functions
description: Lär dig hur du skapar en python-funktion och sedan publicerar det lokala projektet till Server lös värd i Azure Functions med hjälp av Azure Functions tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031743"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Snabb start: skapa en funktion i Azure med python med hjälp av Visual Studio Code

> [!div class="op_single_selector" title1="Välj ditt funktions språk: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Annat (Go/Rust)](create-first-function-vs-code-other.md)

I den här artikeln använder du Visual Studio Code för att skapa en python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till <abbr title="En körnings miljö för körning där all information om servern är transparent för programutvecklare, vilket fören klar processen med att distribuera och hantera kod.">serverlöst</abbr> miljö i <abbr title="En Azure-tjänst som tillhandahåller en enklare data behandlings miljö utan kostnad för program.">Azure Functions</abbr>.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [CLI-baserad version](create-first-function-cli-python.md) av den här artikeln.

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

+ En Azure <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">konto</abbr> med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) version 3. x.

+ [Python 3,8](https://www.python.org/downloads/release/python-381/), [python 3,7](https://www.python.org/downloads/release/python-375/), [python 3,6](https://www.python.org/downloads/release/python-368/) stöds av Azure Functions (x64).

+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code.  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a> skapa ditt lokala projekt

1. Välj Azure-ikonen i <abbr title="Den lodräta gruppen med ikoner till vänster i Visual Studio Code-fönstret.">Aktivitets fältet</abbr>, i avsnittet **Azure: Functions** väljer du ikonen **Skapa nytt projekt...** .

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `Python`.

    + **Välj ett python-alias för att skapa en virtuell miljö**: Välj platsen för din python-tolk. Om platsen inte visas anger du den fullständiga sökvägen till din python-binärfil.  

    + **Välj en mall för projektets första funktion**: Välj `HTTP trigger`.

    + **Ange ett funktionsnamn**: Skriv `HttpExample`.

    + **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten. Mer information om auktoriseringsregler finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

<br/>
<details>
<summary><strong>Kan du inte skapa ett Function-projekt?</strong></summary>

De vanligaste problemen för att lösa när du skapar ett lokalt Functions-projekt är:
* Du har inte installerat Azure Functions tillägget. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

1. Tryck på <kbd>F5</kbd> för att starta projektet för Function-appen.

1. I panelen **Terminal** , se URL-slutpunkten för din funktion som körs lokalt.

    ![Lokal funktion kontra utdata från kod](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Med kärn verktyg som körs går du till **Azure: Functions** -avsnittet. Under **funktioner**, expanderar du **lokala projekt**  >  **funktioner**. Högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på `HttpExample` funktionen och välj **Kör funktion nu..**..

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Kör funktionen nu från Visual Studio Code":::

1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` . Skicka meddelandet till din funktion genom att trycka på RETUR.  

1. När funktionen körs lokalt och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code. Information om funktions körningen visas på panelen **Terminal** .

1. Tryck på <kbd>CTRL + C</kbd> för att stoppa kärn verktygen och koppla från fel söknings programmet.

<br/>
<details>
<summary><strong>Kan du inte köra funktionen lokalt?</strong></summary>

De vanligaste problemen för att lösa när du kör ett lokalt Functions-projekt är:
* Du har inte installerat några huvud verktyg. 
*  Om du har problem med att köra på Windows kontrollerar du att standard-Terminal Shell för Visual Studio Code inte är inställt på **Wsl bash**. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Logga in på Azure

Om du vill publicera din app loggar du in på Azure. Om du redan är inloggad går du till nästa avsnitt.

1. Välj Azure-ikonen i aktivitets fältet och välj sedan **Logga in på Azure** i avsnittet **Azure: Functions** .

    ![Logga in på Azure i VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. När du uppmanas till webbläsaren **väljer du ditt Azure-konto** och loggar in med dina autentiseringsuppgifter **för** Azure-kontot.

1. När du har loggat in stänger du det nya webbläsarfönstret och går tillbaka till Visual Studio Code. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. publicera projektet till Azure

Din första distribution av koden omfattar att skapa en funktions resurs i din Azure-prenumeration.

1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    ![Publicera projektet till Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i meddelanderutorna:

    + **Välj mapp**: Välj den mapp som innehåller din Function-app.

    + **Välj prenumeration**: Välj den prenumeration som ska användas. Du ser inte det här om du bara har en prenumeration.

    + **Välj Funktionsapp i Azure**: Välj `+ Create new Function App`.

    + **Ange ett globalt unikt namn för Function-appen**: Ange ett namn som är giltigt i en URL-sökväg. Det namn som du anger verifieras för att se till att det är <abbr title="Namnet måste vara unikt för alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga eller organisations namn, program namn och en numerisk identifierare, som i Contoso-bizapp-FUNC-20.">unikt i Azure</abbr>. 

    + **Välj en körning**: Välj den version av python som du har kört lokalt. Du kan `python --version` kontrol lera din version med hjälp av kommandot.

    + **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig.

    Tillägget visar status för enskilda resurser när de skapas i Azure i meddelande fältet.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Meddelande om skapande av Azure-resurs":::

1. Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **Visa utdata** om du vill visa skapande-och distributions resultaten. 

    ![Skapa fullständig avisering](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>Kan du inte publicera funktionen?</strong></summary>

Det här avsnittet skapade Azure-resurserna och distribuerade din lokala kod till Function-appen. Om det inte lyckades:

* Granska utdata för fel information. Klock ikonen i det nedre högra hörnet är ett annat sätt att visa utdata. 
* Publicerades du till en befintlig Function-app? Åtgärden skriver över innehållet i appen i Azure.
</details>


<br/>
<details>
<summary><strong>Vilka resurser skapades?</strong></summary>

När det är slutfört skapas följande Azure-resurser i din prenumeration med hjälp av namn baserat på ditt funktions program namn: 
* **Resurs grupp**: en resurs grupp är en logisk behållare för relaterade resurser i samma region.
* **Azure Storage konto**: en lagrings resurs upprätthåller tillstånd och annan information om projektet.
* **Förbruknings plan**: en förbruknings plan definierar den underliggande värden för din server lös Function-app.
* **Function-app**: en Function-app tillhandahåller miljön för att köra funktions koden och grupp funktioner som en logisk enhet.
* **Application Insights**: Application Insights spårar användningen av din server lös funktion.

</details>

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. kör funktionen i Azure

1. I list rutan för **Azure: Functions** , expanderar du den nya Function-appen.
1. Expandera **funktioner**, högerklicka sedan (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på `HttpExample` funktionen och välj **Kör funktion nu...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Kör funktionen nu i Azure från Visual Studio Code":::

1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` .

    Skicka meddelandet till din funktion genom att trycka på RETUR.  

1. När funktionen körs i Azure och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code.

## <a name="7-clean-up-resources"></a>7. Rensa resurser

När du fortsätter till [Nästa steg](#next-steps) och lägger till ett <abbr title="Ett sätt att associera en funktion med en lagrings kö, så att den kan skapa meddelanden i kön.">Utgående bindning för Azure Storage-kö</abbr> till din funktion måste du hålla alla dina resurser på plats för att bygga vidare på det du redan har gjort.

Annars kan du använda följande steg för att ta bort Function-appen och dess relaterade resurser för att undvika ytterligare kostnader.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Mer information om funktions kostnader finns i [uppskatta förbruknings plan kostnader](functions-consumption-costs.md).

## <a name="next-steps"></a>Nästa steg

Expandera funktionen genom att lägga till utdata <abbr title="En deklarativ anslutning mellan en funktion och andra resurser. En indata-bindning tillhandahåller data till funktionen. en utgående bindning tillhandahåller data från funktionen till andra resurser.">enheten</abbr>. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö. 

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Har du problem? Berätta för oss.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
