---
title: Skapa en JavaScript-funktion med Visual Studio Code-Azure Functions
description: Lär dig hur du skapar en JavaScript-funktion och sedan publicerar det lokala Node.js projektet till Server lös värd i Azure Functions med hjälp av Azure Functions tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom:
- devx-track-js
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cf47d04da51db898e667ef8b31d42d79c9f354e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748848"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Snabb start: skapa en JavaScript-funktion i Azure med Visual Studio Code

> [!div class="op_single_selector" title1="Välj ditt funktions språk: "]
> - [JavaScript](create-first-function-vs-code-node.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Annat (Go/Rust)](create-first-function-vs-code-other.md)

Använd Visual Studio Code för att skapa en JavaScript-funktion som svarar på HTTP-begäranden. Testa koden lokalt och distribuera den sedan till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i din <abbr title="Azure-kontot är en global unik entitet som ger dig åtkomst till Azure-tjänster och dina Azure-prenumerationer.">Azure-konto</abbr>.

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

+ Ett Azure-konto med en <abbr title="En Azure-prenumeration är en logisk container som används för att etablera resurser i Azure. Den innehåller information om alla dina resurser, till exempel virtuella datorer, databaser och så vidare.">aktiv prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1 +](https://nodejs.org/)

+ [Visual Studio Code](https://code.visualstudio.com/)

+ [Azure Functions tillägg](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

+ [Azure Functions Core-verktyg](functions-run-local.md?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)

<hr/>
<br/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> skapa dina lokala Functions-projekt

1. Välj Azure-ikonen i <abbr title="">Aktivitets fältet</abbr>, i avsnittet **Azure: Functions** väljer du ikonen **Skapa nytt projekt...** .

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Välj en katalog plats** för projekt arbets ytan och välj sedan **Välj**. 

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `JavaScript`.

    + **Välj en mall för projektets första funktion**: Välj `HTTP trigger`.

    + **Ange ett funktionsnamn**: Skriv `HttpExample`.

    + **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten.

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.




<br/>
<details>
<summary><strong>Kan du inte skapa ett Function-projekt?</strong></summary>

De vanligaste problemen för att lösa när du skapar ett lokalt Functions-projekt är:
* Du har inte installerat Azure Functions tillägget. 
</details>

<hr/>
<br/>

## <a name="3-run-the-function-locally"></a>3. kör funktionen lokalt


1. Tryck på <kbd>F5</kbd> för att starta projektet för Function-appen. 

1. I **terminalen**, se URL-slutpunkten för din funktion som körs lokalt.

    ![Lokal funktion kontra utdata från kod](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Kopiera följande URL och klistra in den i en webbläsare och tryck sedan på RETUR.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Visa returnerat svar.


    ![Webbläsare – exempel på localhost-utdata](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Visa information på **Terminal** -panelen om begäran.

    ![Start-VS Code-utdata för aktivitets värd](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Tryck på <kbd>CTRL + C</kbd> för att stoppa kärn verktygen och koppla från fel söknings programmet.

<br/>
<details>
<summary><strong>Kan du inte köra funktionen lokalt?</strong></summary>

De vanligaste problemen för att lösa när du kör ett lokalt Functions-projekt är:
* Du har inte installerat några huvud verktyg. 
*  Om du har problem med att köra på Windows kontrollerar du att standard-Terminal Shell för Visual Studio Code inte är inställt på WSL bash. 
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

    + **Ange ett globalt unikt namn för Function-appen**: Skriv ett namn som är unikt i Azure i en URL-sökväg. Namnet du skriver verifieras för att säkerställa global unikhet.

    + **Välj en körnings miljö**: Välj den version av Node.js som du har kört lokalt. Du kan `node --version` kontrol lera din version med hjälp av kommandot.

    + **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig. 

1. Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **Visa utdata** för att se skapande-och distributions resultaten. 
    
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
1. Expandera den nya Function-appen i det högra fältet i **Azure: Functions** . 
1. Expandera **funktioner**, högerklicka på **HttpExample** och välj sedan **Kör funktion nu..**..

    ![Kopiera funktions webb adressen för den nya HTTP-utlösaren](../../includes/media/functions-vs-code-run-remote/execute-function-now.png)

1. **Tryck på RETUR** för att skicka standard meddelandet till din funktion. 

1. Ett meddelande aktive ras i Visual Studio Code när du kör funktionen slutförd.

<br/>
<details>
<summary><strong>Kunde du inte köra den molnbaserade Function-appen?</strong></summary>

* Kom du ihåg att lägga till QueryString i slutet av URL: en?

</details>

<hr/>
<br/>

## <a name="7-clean-up-resources"></a>7. Rensa resurser

Ta bort Function-appen och dess resurser för att undvika ytterligare kostnader.

1. I Visual Studio Code väljer du Azure-ikonen i aktivitets fältet och väljer sedan funktions områden i sido fältet. 
1. Välj Function-appen, högerklicka och välj **ta bort funktions program...**.

<hr/>
<br/>

## <a name="next-steps"></a>Nästa steg

Expandera funktionen genom att lägga till en <abbr title="Bindning till en funktion är ett sätt att på ett sätt ansluta en annan resurs till funktionen.">utgående bindning</abbr>. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö. 

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
