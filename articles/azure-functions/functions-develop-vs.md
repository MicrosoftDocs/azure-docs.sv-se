---
title: Utveckla Azure Functions med hjälp av Visual Studio
description: Lär dig hur du utvecklar och testar Azure Functions med hjälp av Azure Functions verktyg för Visual Studio 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 877c82e375b0ea469071402b83fadbd634177f3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655823"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Utveckla Azure Functions med hjälp av Visual Studio  

Med Visual Studio kan du utveckla, testa och distribuera funktioner i C#-klass bibliotek till Azure. Om den här upplevelsen är din första med Azure Functions, se [en introduktion till Azure Functions](functions-overview.md).

Visual Studio ger följande fördelar när du utvecklar dina funktioner: 

* Redigera, skapa och kör funktioner på din lokala utvecklings dator. 
* Publicera Azure Functions-projektet direkt till Azure och skapa Azure-resurser efter behov. 
* Använd C#-attribut för att deklarera funktions bindningar direkt i C#-koden.
* Utveckla och distribuera förkompilerade C#-funktioner. Föreffektivade funktioner ger en bättre kall start prestanda än C#-skriptbaserade funktioner. 
* Koda dina funktioner i C# och få alla fördelar med Visual Studio-utveckling. 

Den här artikeln innehåller information om hur du använder Visual Studio för att utveckla funktioner i C#-klass bibliotek och publicera dem i Azure. Innan du läser den här artikeln bör du överväga att slutföra [snabb starten för Functions för Visual Studio](functions-create-your-first-function-visual-studio.md). 

Om inget annat anges är procedurer och exempel som visas för Visual Studio 2019. 

## <a name="prerequisites"></a>Förutsättningar

- Azure Functions verktyg. Om du vill lägga till Azure Function tools inkluderar du arbets belastningen **Azure Development** i Visual Studio-installationen. Azure Functions-verktyg är tillgängliga i Azure Development-arbetsbelastningen från och med Visual Studio 2017.

- Andra resurser som du behöver, till exempel ett Azure Storage konto, skapas i prenumerationen under publicerings processen.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> I Visual Studio 2017 installerar Azure Development-arbetsbelastningen Azure Functions verktyg som ett separat tillägg. När du uppdaterar Visual Studio 2017-installationen ser du till att du använder den [senaste versionen](#check-your-tools-version) av Azure Functionss verktygen. I följande avsnitt visas hur du kontrollerar och (vid behov) uppdaterar du Azure Functions tools-tillägget i Visual Studio 2017. 
>
> Hoppa över de här avsnitten om du använder Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Kontrol lera din verktygs version i Visual Studio 2017

1. Från **verktyg** -menyn väljer du **tillägg och uppdateringar**. Expandera **installerade**  >  **verktyg** och välj sedan **Azure Functions-och webb jobb verktyg**.

    ![Kontrol lera version av funktions verktyg](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Observera den installerade **versionen** och jämför den här versionen med den senaste versionen som anges i [viktig information](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Om versionen är äldre uppdaterar du verktygen i Visual Studio enligt följande avsnitt.

### <a name="update-your-tools-in-visual-studio-2017"></a>Uppdatera dina verktyg i Visual Studio 2017

1. I dialog rutan **tillägg och uppdateringar** expanderar **du uppdateringar**  >  **Visual Studio Marketplace**, väljer **Azure Functions och webb jobb verktyg** och väljer **Uppdatera**.

    ![Uppdatera funktions verktyg versionen](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. När verktygs uppdateringen har laddats ned väljer du **Stäng** och stänger sedan Visual Studio för att utlösa verktyg-uppdateringen med vsix Installer.

1. I installations programmet för VSIX väljer du **modifiera** för att uppdatera verktygen. 

1. När uppdateringen är klar väljer du **Stäng** och startar sedan om Visual Studio.

> [!NOTE]  
> I Visual Studio 2019 och senare uppdateras tillägget Azure Functions verktyg som en del av Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

När du har skapat ett Azure Functions-projekt skapar projekt mal len ett C#-projekt, installerar `Microsoft.NET.Sdk.Functions` NuGet-paketet och anger mål ramverket. Det nya projektet har följande filer:

* **host.jspå**: låter dig konfigurera funktions värden. Dessa inställningar gäller både när de körs lokalt och i Azure. Mer information finns i [host.jsom referens](functions-host-json.md).

* **local.settings.jspå**: behåller inställningar som används när funktioner körs lokalt. De här inställningarna används inte när de körs i Azure. Mer information finns i [filen med lokala inställningar](#local-settings-file).

    >[!IMPORTANT]
    >Eftersom local.settings.jspå filen kan innehålla hemligheter måste du undanta den från projekt käll kontrollen. Se till att inställningen **Kopiera till utdata-katalog** för den här filen är inställt på **Kopiera om nyare**. 

Mer information finns i [funktions klass biblioteks projekt](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio överför inte automatiskt inställningarna i local.settings.jsnär du publicerar projektet. För att se till att dessa inställningar också finns i din Function-app i Azure laddar du upp dem när du har publicerat projektet. Mer information finns i [funktions program inställningar](#function-app-settings). Värdena i en `ConnectionStrings` samling publiceras aldrig.

Koden kan också läsa inställnings värden för funktionen app som miljövariabler. Mer information finns i [miljövariabler](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-your-build-output-settings"></a>Konfigurera inställningarna för att bygga utdata

När du skapar ett Azure Functions-projekt optimerar build-verktygen utdata så att endast en kopia av alla sammansättningar som delas med Functions-körningen bevaras. Resultatet är en optimerad version som sparar så mycket utrymme som möjligt. Men när du flyttar till en senare version av någon av dina projekt sammansättningar, kanske inte build-verktygen vet att dessa sammansättningar måste bevaras. För att se till att dessa sammansättningar bevaras under optimerings processen kan du ange dem med hjälp av `FunctionsPreservedDependencies` element i projekt filen (. CSPROJ):

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Konfigurera projektet för lokal utveckling

Functions-körningen använder ett Azure Storage-konto internt. För alla utlösare typer än HTTP och Webhooks anger du `Values.AzureWebJobsStorage` nyckeln till en giltig Azure Storage konto anslutnings sträng. Din Function-app kan också använda [Azure Storage emulatorn](../storage/common/storage-use-emulator.md) för `AzureWebJobsStorage` anslutnings inställningen som krävs av projektet. Om du vill använda emulatorn ställer du in värdet `AzureWebJobsStorage` till `UseDevelopmentStorage=true` . Ändra den här inställningen till en faktisk lagrings konto anslutnings sträng före distributionen.

Så här anger du anslutnings strängen för lagrings kontot:

1. I Visual Studio väljer du **Visa**  >  **Cloud Explorer**.

2. Expandera **lagrings konton** i **Cloud Explorer** och välj sedan ditt lagrings konto. På fliken **Egenskaper** kopierar du värdet för **primär anslutnings sträng** .

2. I ditt projekt öppnar du local.settings.jspå filen och anger värdet för `AzureWebJobsStorage` nyckeln till den anslutnings sträng som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar i `Values` matrisen för andra anslutningar som krävs av dina funktioner. 

## <a name="add-a-function-to-your-project"></a>Lägg till en funktion i projektet

I klass biblioteks funktionerna i C# definieras de bindningar som används av funktionen genom att använda attribut i koden. När du skapar funktions utlösare från de angivna mallarna tillämpas utlösarens attribut. 

1. I **Solution Explorer** högerklickar du på noden projekt och väljer **Lägg till**  >  **nytt objekt**. 

2. Välj **Azure Function**, ange ett **namn** för klassen och välj sedan **Lägg till**.

3. Välj utlösaren, ange bindnings egenskaper och välj sedan **OK**. I följande exempel visas inställningarna för att skapa en kö Storage-utlösnings funktion. 

    ![Skapa en funktion för kö Storage-utlösare](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Detta utlösare exempel använder en anslutnings sträng med en nyckel med namnet `QueueStorage` . Definiera den här inställningen för anslutnings strängen i [local.settings.jsi filen](functions-run-local.md#local-settings-file).

4. Granska den nyligen tillagda klassen. Du ser en statisk `Run()` metod som har attribut `FunctionName` . Det här attributet anger att metoden är start punkten för funktionen.

    Följande C#-klass representerar till exempel en grundläggande funktion för kö Storage-utlösare:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

Ett bindande attribut används för varje bindnings parameter som anges till Start punkt metoden. Attributet använder bindnings informationen som parametrar. I det föregående exemplet har den första parametern ett `QueueTrigger` attribut som används för att ange en utlösnings funktion i kö. Könamnet och anslutnings strängens inställnings namn skickas som parametrar till `QueueTrigger` attributet. Mer information finns i [Azure Queue Storage-bindningar för Azure Functions](functions-bindings-storage-queue-trigger.md).

Använd proceduren ovan för att lägga till fler funktioner till ditt Function app-projekt. Varje funktion i projektet kan ha en annan utlösare, men en funktion måste ha exakt en utlösare. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Lägga till bindningar

Som med utlösare läggs indata-och utgående bindningar till i funktionen som binding-attribut. Lägg till bindningar till en funktion på följande sätt:

1. Kontrol lera att du har [konfigurerat projektet för lokal utveckling](#configure-the-project-for-local-development).

2. Lägg till lämpligt paket för NuGet-tillägg för den angivna bindningen. 

   Mer information finns i [klass biblioteket i C# med Visual Studio](./functions-bindings-register.md#local-csharp). Hitta de bindande NuGet-paket kraven i referens artikeln för bindningen. Sök till exempel efter paket krav för Event Hubs-utlösaren i [referens artikeln Event Hubs bindning](functions-bindings-event-hubs.md).

3. Om det finns appinställningar som bindningen kräver, lägger du till dem i `Values` samlingen i den [lokala inställnings filen](functions-run-local.md#local-settings-file). 

   Funktionen använder dessa värden när den körs lokalt. När funktionen körs i Function-appen i Azure använder den [funktionen app-inställningar](#function-app-settings).

4. Lägg till lämpligt binding-attribut i Metodsignaturen. I följande exempel utlöser ett Queue meddelande funktionen och utgående bindning skapar ett nytt meddelande i kö med samma text i en annan kö.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Anslutningen till Queue Storage hämtas från `AzureWebJobsStorage` inställningen. Mer information finns i referens artikeln för den angivna bindningen. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testa funktioner

Med Azure Functions Core Tools kan du köra Azure Functions-projekt på din lokala utvecklingsdator. Mer information finns i [arbeta med Azure Functions Core tools](functions-run-local.md). Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio. 

Så här testar du funktionen i Visual Studio:

1. Tryck på F5. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg. Du kan också behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-begäranden.

2. Testa din kod när projektet körs, så att du testar en distribuerad funktion. 

   Mer information finns i [strategier för att testa koden i Azure Functions](functions-test-a-function.md). När du kör Visual Studio i fel söknings läge, trycks Bryt punkter som förväntat.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Publicera till Azure

När du publicerar från Visual Studio används en av två distributions metoder:

* [Webb distribution](functions-deployment-technologies.md#web-deploy-msdeploy): paket och distribuerar Windows-appar till valfri IIS-server.
* [Zip-distribution med kör från-paket aktiverat](functions-deployment-technologies.md#zip-deploy): rekommenderas för Azure Functions distributioner.

Använd följande steg för att publicera projektet till en Function-app i Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Funktionsappinställningar

Eftersom Visual Studio inte överför dessa inställningar automatiskt när du publicerar projektet, måste du också lägga till alla inställningar som du lägger till local.settings.jsi appen i Azure.

Det enklaste sättet att överföra de nödvändiga inställningarna till din Function-app i Azure är att välja länken **hantera Azure App Service inställningar** som visas när du har publicerat projektet.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Inställningar i publicerings fönstret":::

Om du väljer den här länken visas dialog rutan **program inställningar** för Function-appen där du kan lägga till nya program inställningar eller ändra befintliga.

![Programinställningar](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Lokal** visar ett inställnings värde i local.settings.jspå filen **, och** fjärrvisar ett aktuellt inställnings värde i Function-appen i Azure. Välj **Lägg till inställning** för att skapa en ny app-inställning. Använd **värdet infoga värde från lokal** länk för att kopiera ett inställnings värde till fältet **Remote** . Väntande ändringar skrivs till den lokala inställnings filen och Function-appen när du väljer **OK**.

> [!NOTE]
> Som standard kontrol leras inte local.settings.jspå filen i käll kontrollen. Det innebär att om du klonar ett lokalt Functions-projekt från käll kontrollen har projektet ingen local.settings.jspå filen. I så fall måste du manuellt skapa local.settings.jspå filen i projekt roten så att dialog rutan **program inställningar** fungerar som förväntat. 

Du kan också hantera program inställningar på något av följande sätt:

* [Använd Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Använd `--publish-local-settings` alternativet publicera i Azure Functions Core tools](functions-run-local.md#publish).
* [Använd Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Övervaknings funktioner

Det rekommenderade sättet att övervaka körningen av dina funktioner är genom att integrera din Function-app med Azure Application insikter. När du skapar en Function-app i Azure Portal görs denna integrering som standard. Men när du skapar en Function-app under Visual Studio-publiceringen är integreringen i din Function-app i Azure inte färdig. Information om hur du ansluter Application Insights till din Function-app finns i [aktivera Application Insights-integrering](configure-monitoring.md#enable-application-insights-integration).

Mer information om övervakning med hjälp av Application Insights finns i [övervaka Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Core Tools finns i [arbeta med Azure Functions Core tools](functions-run-local.md).

Mer information om hur du utvecklar funktioner som .NET-klass bibliotek finns i [referens för Azure Functions C#-utvecklare](functions-dotnet-class-library.md). Den här artikeln länkar också till exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    
