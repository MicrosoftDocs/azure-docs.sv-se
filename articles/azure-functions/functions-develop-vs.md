---
title: Utveckla Azure Functions med hjälp av Visual Studio
description: Lär dig hur du utvecklar och testar Azure Functions med Azure Functions Tools för Visual Studio 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 2cba0a9ad63e319af0a5eaa1c1c018c3b285c28a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765583"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Utveckla Azure Functions med hjälp av Visual Studio  

Visual Studio kan du utveckla, testa och distribuera C#-klassbiblioteksfunktioner till Azure. Om den här upplevelsen är din första med Azure Functions kan du [gå till En introduktion till Azure Functions](functions-overview.md).

Visual Studio ger följande fördelar när du utvecklar dina funktioner: 

* Redigera, skapa och kör funktioner på din lokala utvecklingsdator. 
* Publicera ditt Azure Functions direkt till Azure och skapa Azure-resurser efter behov. 
* Använd C#-attribut för att deklarera funktionsbindningar direkt i C#-koden.
* Utveckla och distribuera förkompilerade C#-funktioner. Förinspelade funktioner ger bättre kallstartsprestanda än skriptbaserade C#-funktioner. 
* Koda dina funktioner i C# samtidigt som du får alla fördelar med Visual Studio utveckling. 

Den här artikeln innehåller information om hur du använder Visual Studio för att utveckla C#-klassbiblioteksfunktioner och publicera dem till Azure. Innan du läser den här artikeln bör du slutföra [functions-snabbstarten för Visual Studio](functions-create-your-first-function-visual-studio.md). 

Om inget annat anges gäller procedurer och exempel som Visual Studio 2019. 

## <a name="prerequisites"></a>Förutsättningar

- Azure Functions Tools. Om du vill lägga till Azure-funktionsverktyg inkluderar **du arbetsbelastningen Azure-utveckling** i Visual Studio installation. Azure Functions Tools är tillgängligt i arbetsbelastningen Azure Development från och med Visual Studio 2017.

- Andra resurser som du behöver, till exempel Azure Storage ett konto, skapas i din prenumeration under publiceringsprocessen.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> I Visual Studio 2017 installerar arbetsbelastningen Azure Development Azure Functions Tools som ett separat tillägg. När du uppdaterar Visual Studio 2017-installationen kontrollerar du att du använder den senaste [versionen](#check-your-tools-version) av Azure Functions verktyg. Följande avsnitt visar hur du kontrollerar och (om det behövs) uppdaterar ditt Azure Functions Tools-tillägg i Visual Studio 2017. 
>
> Hoppa över de här avsnitten om du använder Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Kontrollera verktygsversionen i Visual Studio 2017

1. I menyn **Verktyg** väljer du **Tillägg och uppdateringar.** Expandera **Installerade**  >  **verktyg** och välj sedan **Azure Functions och Web Jobs Tools**.

    ![Verifiera Functions-verktygsversionen](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Observera den installerade **versionen och** jämför den här versionen med den senaste versionen som anges i [versionsanteckningen.](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md) 

1. Om din version är äldre uppdaterar du dina verktyg i Visual Studio enligt följande avsnitt.

### <a name="update-your-tools-in-visual-studio-2017"></a>Uppdatera dina verktyg i Visual Studio 2017

1. I dialogrutan **Tillägg och uppdateringar** expanderar du **Uppdateringar**  >  **Visual Studio Marketplace,** **väljer verktyg Azure Functions och webbjobb** och väljer **Uppdatera.**

    ![Uppdatera Functions-verktygsversionen](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. När verktygsuppdateringen har laddats ned väljer du **Stäng** och stänger Visual Studio för att utlösa verktygsuppdateringen med VSIX-installationsprogrammet.

1. I VSIX-installationsprogrammet väljer **du Ändra** för att uppdatera verktygen. 

1. När uppdateringen är klar väljer du **Stäng** och startar sedan om Visual Studio.

> [!NOTE]  
> I Visual Studio 2019 och senare uppdateras Azure Functions verktygstillägget som en del av Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

När du har Azure Functions ett projekt skapar projektmallen ett C#-projekt, `Microsoft.NET.Sdk.Functions` installerar NuGet-paketet och anger målramverket. Det nya projektet har följande filer:

* **host.jspå**: Låter dig konfigurera Functions-värden. De här inställningarna gäller både när du kör lokalt och i Azure. Mer information finns i [host.jsi referensen](functions-host-json.md).

* **local.settings.jspå**: Underhåller inställningar som används när funktioner körs lokalt. De här inställningarna används inte när du kör i Azure. Mer information finns i [Filen med lokala inställningar.](#local-settings-file)

    >[!IMPORTANT]
    >Eftersom filen local.settings.jskan innehålla hemligheter måste du undanta den från din projektkällkontroll. Se till **att inställningen Kopiera till utdatakatalog** för den här filen är inställd på Kopiera om **nyare**. 

Mer information finns i [Functions-klassbiblioteksprojekt](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio laddar inte automatiskt upp inställningarna i local.settings.jsnär du publicerar projektet. Om du vill kontrollera att de här inställningarna även finns i funktionsappen i Azure laddar du upp dem när du har publicerat projektet. Mer information finns i [Funktionsappinställningar.](#function-app-settings) Värdena i en `ConnectionStrings` samling publiceras aldrig.

Koden kan också läsa funktionsappens inställningsvärden som miljövariabler. Mer information finns i [Miljövariabler.](functions-dotnet-class-library.md#environment-variables)

## <a name="configure-your-build-output-settings"></a>Konfigurera inställningarna för byggutdata

När du skapar Azure Functions-projekt optimerar byggverktygen utdata så att endast en kopia av alla sammansättningar som delas med funktionernas körning bevaras. Resultatet är en optimerad version som sparar så mycket utrymme som möjligt. Men när du flyttar till en nyare version av någon av dina projektsammansättningar kanske inte byggverktygen vet att dessa sammansättningar måste bevaras. För att säkerställa att dessa sammansättningar bevaras under optimeringsprocessen kan du ange dem med hjälp av element i projektfilen `FunctionsPreservedDependencies` (.csproj):

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Konfigurera projektet för lokal utveckling

Functions-körningen använder ett Azure Storage internt. För alla utlösartyper förutom HTTP och webhooks anger du nyckeln till en giltig `Values.AzureWebJobsStorage` Azure Storage-kontoanslutningssträng. Funktionsappen kan också använda [Azure Storage-emulatorn](../storage/common/storage-use-emulator.md) `AzureWebJobsStorage` för den anslutningsinställning som krävs av projektet. Om du vill använda emulatorn anger du värdet `AzureWebJobsStorage` för till `UseDevelopmentStorage=true` . Ändra den här inställningen till en faktisk anslutningssträng för lagringskontot före distributionen.

Så här anger du anslutningssträngen för lagringskontot:

1. I Visual Studio du **Visa**  >  **Cloud Explorer.**

2. I **Cloud Explorer** expanderar du **Lagringskonton** och väljer sedan ditt lagringskonto. På fliken **Egenskaper** kopierar du värdet **för Primär anslutningssträng.**

2. I projektet öppnar du filen local.settings.jsoch anger värdet för nyckeln till `AzureWebJobsStorage` den anslutningssträng som du kopierade.

3. Upprepa föregående steg för att lägga till unika nycklar i `Values` matrisen för alla andra anslutningar som krävs av dina funktioner. 

## <a name="add-a-function-to-your-project"></a>Lägga till en funktion i projektet

I C#-klassbiblioteksfunktioner definieras de bindningar som används av funktionen genom att tillämpa attribut i koden. När du skapar funktionsutlösare från de angivna mallarna tillämpas utlösarattributen åt dig. 

1. I **Solution Explorer** högerklickar du på projektnoden och väljer Lägg **till**  >  **nytt objekt.** 

2. Välj **Azure-funktion,** ange **ett Namn** för klassen och välj sedan Lägg **till**.

3. Välj utlösaren, ange bindningsegenskaperna och välj sedan **OK.** I följande exempel visas inställningarna för att skapa en queue storage-utlösarfunktion. 

    ![Skapa en utlösarfunktion för Queue Storage](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Det här utlösarexempel använder en anslutningssträng med en nyckel med namnet `QueueStorage` . Definiera den här inställningen för anslutningssträngen [ ilocal.settings.jspå filen](functions-run-local.md#local-settings-file).

4. Granska den nyligen tillagda klassen. Du ser en `Run()` statisk metod som tillskrivs med `FunctionName` attributet . Det här attributet anger att metoden är startpunkten för funktionen.

    Följande C#-klass representerar till exempel en grundläggande queue storage-utlösarfunktion:

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

Ett bindningsspecifikt attribut tillämpas på varje bindningsparameter som anges till startpunktsmetoden. Attributet tar bindningsinformationen som parametrar. I föregående exempel har den första parametern ett tillämpat `QueueTrigger` attribut som anger en queue storage-utlösarfunktion. Könamnet och inställningsnamnet för anslutningssträngen skickas som parametrar till `QueueTrigger` attributet. Mer information finns i [Azure Queue Storage-bindningar för Azure Functions](functions-bindings-storage-queue-trigger.md).

Använd proceduren ovan för att lägga till fler funktioner i funktionsappsprojektet. Varje funktion i projektet kan ha olika utlösare, men en funktion måste ha exakt en utlösare. Mer information finns i Azure Functions [utlösare och bindningar.](functions-triggers-bindings.md)

## <a name="add-bindings"></a>Lägga till bindningar

Precis som med utlösare läggs indata- och utdatabindningar till i funktionen som bindningsattribut. Lägg till bindningar till en funktion på följande sätt:

1. Kontrollera att du har [konfigurerat projektet för lokal utveckling.](#configure-the-project-for-local-development)

2. Lägg till lämpligt NuGet-tilläggspaket för den specifika bindningen. 

   Mer information finns i [C#-klassbibliotek med Visual Studio](./functions-bindings-register.md#local-csharp). Hitta de bindningsspecifika NuGet-paketkraven i referensartikeln för bindningen. Hitta till exempel paketkraven för utlösaren Event Hubs i artikeln [Event Hubs bindningsreferens](functions-bindings-event-hubs.md).

3. Om bindningen behöver appinställningar lägger du till dem i samlingen `Values` i den [lokala inställningsfilen](functions-run-local.md#local-settings-file). 

   Funktionen använder dessa värden när den körs lokalt. När funktionen körs i funktionsappen i Azure används inställningarna [för funktionsappen](#function-app-settings).

4. Lägg till lämpligt bindningsattribut i metodsignaturen. I följande exempel utlöser ett kömeddelande funktionen och utdatabindningen skapar ett nytt kömeddelande med samma text i en annan kö.

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
   Anslutningen till Queue Storage hämtas från `AzureWebJobsStorage` inställningen. Mer information finns i referensartikeln för den specifika bindningen. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testa funktioner

Med Azure Functions Core Tools kan du köra Azure Functions-projekt på din lokala utvecklingsdator. Mer information finns i [Arbeta med Azure Functions Core Tools](functions-run-local.md). Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio. 

Så här testar du funktionen i Visual Studio:

1. Tryck på F5. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg. Du kan också behöva aktivera ett brandväggsfel så att verktygen kan hantera HTTP-begäranden.

2. När projektet är igång testar du koden på samma sätt som du testar en distribuerad funktion. 

   Mer information finns i [Strategier för att testa din kod i Azure Functions](functions-test-a-function.md). När du kör Visual Studio i felsökningsläge kommer brytpunkter att kommas till som förväntat.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Publicera till Azure

När du publicerar från Visual Studio den en av två distributionsmetoder:

* [Webb distribuera:](functions-deployment-technologies.md#web-deploy-msdeploy)Paketerar och distribuerar Windows-appar till valfri IIS-server.
* [Zip-distribution med run-From-package aktiverat:](functions-deployment-technologies.md#zip-deploy)Rekommenderas för Azure Functions distributioner.

Använd följande steg för att publicera projektet till en funktionsapp i Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Funktionsappinställningar

Eftersom Visual Studio inte överför de här inställningarna automatiskt när du publicerar projektet måste du även lägga till alla inställningar som du lägger till i local.settings.jspå i funktionsappen i Azure.

Det enklaste sättet att ladda upp de nödvändiga inställningarna till funktionsappen i Azure är att välja länken **Hantera Azure App Service-inställningar** som visas när du har publicerat projektet.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Inställningar i fönstret Publicera":::

Om du väljer den här **länken visas dialogrutan** Programinställningar för funktionsappen, där du kan lägga till nya programinställningar eller ändra befintliga.

![Programinställningar](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Lokalt** visar ett inställningsvärde i local.settings.jspå filen och **Remote** visar ett aktuellt inställningsvärde i funktionsappen i Azure. Välj **Lägg till inställning** för att skapa en ny appinställning. Använd länken **Infoga värde från lokal** för att kopiera ett inställningsvärde till **fältet** Fjärr. Väntande ändringar skrivs till den lokala inställningsfilen och funktionsappen när du väljer **OK.**

> [!NOTE]
> Som standard checkas local.settings.jspå filen inte in i källkontrollen. Det innebär att om du klonar ett lokalt Functions-projekt från källkontrollen så har inte projektet någon local.settings.jspå filen. I det här fallet måste du manuellt skapa local.settings.jspå filen i projektroten så att dialogrutan **Programinställningar** fungerar som förväntat. 

Du kan också hantera programinställningar på något av följande sätt:

* [Använd Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Använd `--publish-local-settings` publiceringsalternativet i Azure Functions Core Tools](functions-run-local.md#publish).
* [Använd Azure CLI.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)

## <a name="monitoring-functions"></a>Övervakningsfunktioner

Det rekommenderade sättet att övervaka körningen av dina funktioner är genom att integrera funktionsappen med Azure Application Insights. När du skapar en funktionsapp i Azure Portal görs den här integreringen åt dig som standard. Men när du skapar din funktionsapp Visual Studio publiceringen är integreringen i funktionsappen i Azure inte klar. Information om hur du ansluter Application Insights till funktionsappen finns i [Aktivera Application Insights integrering.](configure-monitoring.md#enable-application-insights-integration)

Mer information om övervakning med hjälp Application Insights finns i [Övervaka Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Core Tools finns i [Arbeta med Azure Functions Core Tools](functions-run-local.md).

Mer information om hur du utvecklar funktioner som .NET-klassbibliotek finns [i Azure Functions C#-utvecklarreferens.](functions-dotnet-class-library.md) Den här artikeln innehåller även länkar till exempel på hur du använder attribut för att deklarera de olika typerna av bindningar som stöds av Azure Functions.    
