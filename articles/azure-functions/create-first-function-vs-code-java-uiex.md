---
title: Skapa en Java-funktion med Visual Studio Code-Azure Functions
description: Lär dig hur du skapar en Java-funktion och sedan publicerar det lokala projektet till Server lös värd i Azure Functions med hjälp av Azure Functions tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 38ed05d8bf9f7714b9cd58449f565cf94cb481e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748824"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Snabb start: skapa en Java-funktion i Azure med Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Använd Visual Studio Code för att skapa en Java-funktion som svarar på HTTP-begäranden. Testa koden lokalt och distribuera den sedan till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i din <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">Azure-konto</abbr>.

Om Visual Studio Code inte är det önskade utvecklings verktyget kan du kolla in våra liknande självstudier för Java-utvecklare med [maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) och [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

+ Ett Azure-konto med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8 eller 11.

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> skapa dina lokala Functions-projekt

1. Välj Azure-ikonen i **aktivitets fältet** och välj sedan ikonen **Skapa nytt projekt** i avsnittet **Azure: Functions** .

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Välj en katalog plats** för projekt arbets ytan och välj sedan **Välj**.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `Java`.

    + **Välj en version av Java**: Välj `Java 8` eller `Java 11` , den Java-version som dina funktioner körs på i Azure. Välj en Java-version som du har verifierat lokalt.

    + **Ange ett grupp-ID**: Välj `com.function` .

    + **Ange ett artefakt-ID**: Välj `myFunction` .

    + **Ange en version**: Välj `1.0-SNAPSHOT` .

    + **Ange ett paket namn**: Välj `com.function` .

    + **Ange ett namn på appen**: Välj `myFunction-12345` .

    + **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten.

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

<br/>

<details>
<summary><strong>Kan du inte skapa ett Function-projekt?</strong></summary>

De vanligaste problemen för att lösa när du skapar ett lokalt Functions-projekt är:
* Du har inte installerat Azure Functions tillägget. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. kör funktionen lokalt

1. Tryck på <kbd>F5</kbd> för att starta projektet för Function-appen.

1. I **terminalen**, se URL-slutpunkten för din funktion som körs lokalt.

    ![Lokal funktion kontra utdata från kod](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Med kärn verktyg som körs går du till **Azure: Functions** -avsnittet. Under **funktioner**, expanderar du **lokala projekt**  >  **funktioner**. Högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på `HttpExample` funktionen och välj **Kör funktion nu..**..

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Kör funktionen nu från Visual Studio Code":::

1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` . Skicka meddelandet till din funktion genom att trycka på <kbd>RETUR</kbd> .  

1. När funktionen körs lokalt och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code. Information om funktions körningen visas på panelen **Terminal** .

1. Tryck på <kbd>CTRL + C</kbd> för att stoppa kärn verktygen och koppla från fel söknings programmet.

<br/>

<details>
<summary><strong>Kan du inte köra funktionen lokalt?</strong></summary>

De vanligaste problemen för att lösa när du kör ett lokalt Functions-projekt är:
* Du har inte installerat några huvud verktyg. 
*  Om du har problem med att köra på Windows kontrollerar du att standard-Terminal Shell för Visual Studio Code inte är inställt på WSL bash. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Logga in på Azure

Om du vill publicera din app loggar du in på Azure. Om du redan är inloggad går du till nästa avsnitt.

1. Välj Azure-ikonen i aktivitets fältet och välj sedan **Logga in på Azure** i avsnittet **Azure: Functions** .

    ![Logga in på Azure i VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. När du uppmanas till webbläsaren **väljer du ditt Azure-konto** och loggar in med dina autentiseringsuppgifter **för** Azure-kontot.

1. När du har loggat in stänger du det nya webbläsarfönstret och går tillbaka till Visual Studio Code.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. publicera projektet till Azure

Din första distribution av koden omfattar att skapa en funktions resurs i din Azure-prenumeration.

1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    ![Publicera projektet till Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i meddelanderutorna:

    + **Välj mapp**: Välj den mapp som innehåller din Function-app. 

    + **Välj prenumeration**: Välj den prenumeration som ska användas. Du ser inte det här om du bara har en prenumeration.

    + **Välj Funktionsapp i Azure**: Välj `- Create new Function App`.

    + **Ange ett globalt unikt namn för Function-appen**: Skriv ett namn som är unikt i Azure i en URL-sökväg. Namnet du skriver verifieras för att säkerställa global unikhet.

    - **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig.

1. Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **Visa utdata** för att se skapande-och distributions resultaten.

    ![Skapa fullständig avisering](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. kör funktionen i Azure

1. Gå tillbaka till avsnittet **Azure: Functions** i sido fältet, expandera din prenumeration, din nya Function-app och **functions.** Högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på `HttpExample` funktionen och välj **Kör funktion nu..**..

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Kör funktionen nu i Azure från Visual Studio Code":::

1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` . Skicka meddelandet till din funktion genom att trycka på RETUR.  

1. När funktionen körs i Azure och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. Rensa resurser

Om du inte planerar att fortsätta till [Nästa steg](#next-steps)tar du bort Function-appen och dess resurser för att undvika ytterligare kostnader.

1. I Visual Studio Code väljer du Azure-ikonen i aktivitets fältet och väljer sedan funktions områden i sido fältet.
1. Välj Function-appen, högerklicka och välj **ta bort funktions program...**.

<br/>
<hr/>

## <a name="next-steps"></a>Nästa steg

Expandera funktionen genom att lägga till en <abbr title="I Azure Storage innebär ett sätt att associera en funktion med en lagrings kö, så att den kan skapa meddelanden i kön.">utgående bindning</abbr>. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö.

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
