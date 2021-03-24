---
title: 'Snabb start: skapa din första funktion i Azure med Visual Studio'
description: I den här snabb starten får du lära dig hur du skapar och publicerar en HTTP-utlösare Azure-funktion med hjälp av Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9e3144738bd259ab9be75059af00f125581bb37c
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "102050128"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Snabb start: skapa din första funktion i Azure med Visual Studio

I den här artikeln använder du Visual Studio för att skapa en C#-baserad funktion i C#-klassen som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till <abbr title="En körnings miljö för körning där all information om servern är transparent för programutvecklare, vilket fören klar processen med att distribuera och hantera kod.">serverlöst</abbr> miljö i <abbr title="En Azure-tjänst som tillhandahåller en enklare data behandlings miljö utan kostnad för program.">Azure Functions</abbr>.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i din <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">Azure-konto</abbr>.

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

+ Skapa en Azure <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">konto</abbr> med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Installera [Visual Studio 2019](https://azure.microsoft.com/downloads/) och välj arbets belastningen **Azure Development** under installationen. 

![Installera Visual Studio med arbets belastningen Azure Development](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<br/>
<details>
<summary><strong>Använd ett Azure Functions-projekt i stället</strong></summary>
Om du vill skapa en <abbr title="En logisk behållare för en eller flera enskilda funktioner som kan distribueras och hanteras tillsammans.">Azure Functions projekt</abbr> genom att använda Visual Studio 2017 i stället måste du först installera de [senaste Azure Functions verktygen](functions-develop-vs.md#check-your-tools-version).
</details>

## <a name="2-create-a-function-app-project"></a>2. skapa ett Function app-projekt

1. Från Visual Studio-menyn väljer du **fil**  >  **nytt**  >  **projekt**.

1. I **skapa ett nytt projekt**, ange *funktioner* i sökrutan, Välj mallen **Azure Functions** och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** anger du ett **<abbr title=" namn på Function-appen måste vara giltigt som ett C#-namn område, så Använd inte under streck, bindestreck eller andra tecken som inte är alfanumeriska. "> Projekt namn </abbr>** för projektet och välj sedan **skapa**. 

1. Ange följande information för program inställningarna **skapa en ny Azure Functions** :

    + Välj **<abbr title=" det här värdet för att skapa ett funktions projekt som använder version 3. x-körningen av Azure Functions, som stöder .net Core 3. x. Azure Functions 1. x stöder .NET Framework. "> Azure Functions v3 (.NET Core) </abbr>** från List rutan Functions Runtime. (Mer information finns i [Översikt över Azure Functions körnings versioner](functions-versions.md).)
    
    + Välj **<abbr title=" det här värdet skapar en funktion som utlöses av en http-begäran. "> HTTP- </abbr> utlösare** som funktions mal len.
    
    + Välj **<abbr title=" eftersom en Azure-funktion kräver ett lagrings konto, en tilldelas eller skapas när du publicerar projektet till Azure. En HTTP-utlösare använder inte en anslutnings sträng för Azure Storage konto. alla andra utlösare kräver en giltig anslutnings sträng för Azure Storage konto. "> Storage- </abbr> emulator** från List rutan lagrings konto.
        
    + Välj **Anonym** från <abbr title="Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen.">Auktoriseringsnivå</abbr> Rute. (Mer information om nycklar och auktorisering finns i [auktoriseringsarkiv](functions-bindings-http-webhook-trigger.md#authorization-keys) och [http-och webhook-bindningar](functions-bindings-http-webhook.md).)

    + Välj **Skapa**
        
## <a name="3-rename-the-function"></a>3. Byt namn på funktionen

`FunctionName`Attributet Method anger namnet på funktionen, som som standard genereras som `Function1` . Eftersom verktyget inte tillåter att du åsidosätter standard funktions namnet när du skapar projektet ska du ta en minut för att skapa ett bättre namn för funktions klassen, filen och metadata.

1. Högerklicka på filen Function1. cs i **Utforskaren** och Byt namn på den till *HttpExample. cs*.

1. I koden byter du namn på Function1-klassen till ' HttpExample '.

1. I `HttpTrigger` metoden med namnet `Run` byter du namn på `FunctionName` attributet Method till `HttpExample` .


## <a name="4-run-the-function-locally"></a>4. kör funktionen lokalt

1. Tryck på <kbd>F5</kbd> i Visual Studio för att köra funktionen.

1. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Lokal Azure-körningsmiljö](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Vill du lägga till frågesträngen **? namn =<YOUR_NAME>** till denna URL och köra begäran. 

    ![Svar för funktion-localhost i webbläsaren](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. Stoppa fel sökningen genom att trycka på <kbd>SKIFT</kbd> + <kbd>F5</kbd> i Visual Studio.

<br/>
<details>
<summary><strong>Felsökning</strong></summary>
 Du kan behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-förfrågningar. Tillstånds nivåer tillämpas aldrig när du kör en funktion lokalt.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. publicera projektet till Azure

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I **mål** väljer du **Azure**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Välj Azure-mål":::

1. I ett **särskilt mål** väljer du **Azure Funktionsapp (Windows)**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Välj Azure-Funktionsapp":::

1. I **funktions instans** väljer du **skapa en ny Azure function...** och använder sedan värdena som anges i följande:

    + För **namn** anger du <abbr title="Använd ett namn som unikt identifierar din nya Function-app. Acceptera det här namnet eller ange ett nytt namn. Giltiga tecken är: `a-z` , `0-9` , och `-` ..">Globalt unikt namn</abbr>
    
    + **Välj** en prenumeration i list rutan.
    
    + **Välj** en befintlig <abbr title="En logisk behållare för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr> i list rutan eller väljer du **nytt** för att skapa en ny resurs grupp.
    
    + **Välj** <abbr title="När du publicerar projektet till en Function-app som körs i en förbruknings plan betalar du bara för körningar av Functions-appen. Andra värd planer ådrar sig högre kostnader.">Förbrukning</abbr> i list rutan uppspelnings typ. (Mer information finns i [förbruknings plan](consumption-plan.md).)
    
    + **Välj** en  <abbr title="En geografisk referens till ett särskilt Azure-datacenter där resurser allokeras. Se [regioner](https://azure.microsoft.com/regions/) för en lista över tillgängliga regioner.">location</abbr> i list rutan.
    
    + **Välj** en <abbr = "ett Azure Storage konto krävs av Functions-körningen. Välj nytt om du vill konfigurera ett allmänt lagrings konto. Du kan också välja ett befintligt konto som uppfyller kraven för lagrings kontot. " >Azure Storage</abbr> konto från List rutan

    ![Dialogrutan Skapa App Service](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Välj **Skapa** 

1. I **Functions-instansen** kontrollerar du att **Kör från paket filen** är markerat. 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Slutför skapande av profil":::

    <br/>
    <details>
    <summary><strong>Vad gör den här inställningen?</strong></summary>
    När du använder **Kör från paketfil** distribueras din Function-app med hjälp av [zip Deploy](functions-deployment-technologies.md#zip-deploy) med läget [Kör-inpaket](run-functions-from-deployment-package.md) aktiverat. Detta är den rekommenderade distributions metoden för ditt Functions-projekt, eftersom den resulterar i bättre prestanda.    
    </details>   

1. Välj **Slutför**.

1. På sidan Publicera väljer du **Publicera**.

1. På sidan publicera granskar du rot-URL: en för Function-appen.

1. På fliken publicera väljer **du hantera i <abbr title=" Cloud Explorer för att visa innehållet på platsen, starta och stoppa Function-appen, och bläddra direkt för att använda program resurser i Azure och i Azure Portal. "> Cloud Explorer </abbr>**.
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Meddelande som anger att publiceringen lyckades":::
    

## <a name="6-test-your-function-in-azure"></a>6. testa din funktion i Azure

1. I Cloud Explorer ska din nya Function-app väljas. Om inte, expanderar du din prenumeration, expanderar **app Services** och väljer sedan den nya Function-appen.

1. Högerklicka på Function-appen och välj **Öppna i webbläsare**. Detta öppnar roten för din Function-app i din standard webbläsare och visar sidan som visar att funktions programmet körs. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Function-appen körs":::

1. I adress fältet i webbläsaren lägger du till strängen **/API/HttpExample? Name = Functions** i bas-URL: en och kör begäran.

    Den URL som anropar funktionen HTTP-utlösare har följande format:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Gå till denna URL så ser du ett svar i webbläsaren till den fjärranslutna GET-begäran som returnerades av funktionen, som ser ut som i följande exempel:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Funktionssvar i webbläsaren":::

## <a name="7-clean-up-resources"></a>7. Rensa resurser

Ta bort Function-appen och dess resurser för att undvika ytterligare kostnader.

1. I Cloud Explorer expanderar du din prenumeration, expanderar **app Services**, högerklickar på din Function-app och väljer **Öppna i portalen**. 

1. På sidan Function-app väljer du fliken **Översikt** och väljer sedan länken under **resurs grupp**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Välj den resurs grupp som ska tas bort från sidan funktions program":::

1. På sidan **resurs grupp** granskar du listan över resurser som ingår och kontrollerar att de är de som du vill ta bort.
 
1. Välj **Ta bort resursgrupp** och följ instruktionerna.

    Borttagningen kan ta några minuter. När du är färdig visas ett meddelande i några sekunder. Du kan även välja klockikonen längst upp på sidan för att se meddelandet.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du lägger till en <abbr title="Ett sätt att associera en funktion med en lagrings kö, så att den kan skapa meddelanden i kön. Bindningar är deklarativ anslutningar mellan en funktion och andra resurser. En indata-bindning tillhandahåller data till funktionen. en utgående bindning tillhandahåller data från funktionen till andra resurser.">Utgående bindning för Azure Storage-kö</abbr> till din funktion:

> [!div class="nextstepaction"]
> [Lägg till en Azure Storage Queue-bindning till din funktion](functions-add-output-binding-storage-queue-vs.md)
