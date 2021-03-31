---
title: Kom igång med WebJobs SDK
description: 'Introduktion till WebJobs-SDK: n för händelse driven bakgrunds bearbetning. Lär dig hur du kommer åt data i Azure-tjänster och tjänster från tredje part.'
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91652048"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Komma igång med Azure WebJobs SDK för händelsedriven bakgrundsbearbetning

Den här artikeln visar hur du använder Visual Studio 2019 för att skapa ett Azure WebJobs SDK-projekt, köra det lokalt och sedan distribuera det till [Azure App Service](overview.md). Version 3. x av WebJobs-SDK: n stöder både .NET Core och .NET Framework-konsol program. Mer information om hur du arbetar med WebJobs SDK finns i [så här använder du Azure WEBJOBS SDK för händelse driven bakgrunds bearbetning](webjobs-sdk-how-to.md).

Den här artikeln visar hur du distribuerar WebJobs som en .NET Core-konsolprogram. Om du vill distribuera WebJobs som en .NET Framework-konsolsession, se [WebJobs som .NET Framework-konsol program](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Om du är intresse rad av WebJobs SDK version 2. x, som endast stöder .NET Framework, se [utveckla och distribuera WebJobs med Visual Studio-Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Förutsättningar

* [Installera Visual Studio 2019](/visualstudio/install/) med arbets belastningen **Azure Development** . Om du redan har Visual Studio men inte har den arbets belastningen lägger du till arbets belastningen genom att välja **verktyg > hämta verktyg och funktioner**.

* Du måste ha [ett Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) för att publicera dina WEBjobs SDK-projekt till Azure.

## <a name="create-a-project"></a>Skapa ett projekt

1. I Visual Studio väljer du **skapa ett nytt projekt**.

2. Välj **konsol program (.net Core)**.

3. Ge projektet namnet *WebJobsSDKSample* och välj sedan **skapa**.

   ![Dialogrutan Nytt projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet-paket

1. Installera den senaste stabila 3. x-versionen av [ `Microsoft.Azure.WebJobs.Extensions` NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/), som innehåller `Microsoft.Azure.WebJobs` .

     Här är kommandon för **Package Manager-konsolen** :

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Ersätt `<3_X_VERSION>` med en version av paketet som stöds i det här kommandot. 

## <a name="create-the-host"></a>Skapa värden

Värden är körnings behållare för funktioner som lyssnar efter utlösare och anropar funktioner. Följande steg skapar en värd som implementerar [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) , som är den allmänna värden i ASP.net Core.

1. Lägg till följande instruktioner i *program. cs* `using` :

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Ersätt metoden `Main` med följande kod:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

I ASP.NET Core anges värd konfigurationerna genom att anropa metoder på [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instansen. Mer information finns i [allmän .net-värd](/aspnet/core/fundamentals/host/generic-host). `ConfigureWebJobs`Tilläggs metoden initierar WebJobs-värden. I `ConfigureWebJobs` initierar du vissa WebJobs-tillägg och anger egenskaperna för dessa tillägg.  

## <a name="enable-console-logging"></a>Aktivera konsol loggning

I det här avsnittet ställer du in konsol loggning som använder [ASP.net Core loggnings ramverket](/aspnet/core/fundamentals/logging).

1. Installera den senaste stabila versionen av [ `Microsoft.Extensions.Logging.Console` NuGet-paketet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), som innehåller `Microsoft.Extensions.Logging` .

   Här är kommandon för **Package Manager-konsolen** :

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   I det här kommandot ersätter `<3_X_VERSION>` du med en 3. x-version som stöds av paketet.

1. Lägg till en instruktion i *program. cs* `using` :

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Anropa [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metoden på [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole)Metoden lägger till konsol loggning i konfigurationen.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main`Metoden ser nu ut så här:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Den här uppdateringen gör följande:

    * Inaktiverar [instrument panels loggning](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Instrument panelen är ett bakåtkompatibelt övervaknings verktyg och instrument panels loggning rekommenderas inte för produktions scenarier med stora data flöden.
    * Lägger till-konsol leverantören med standard [filtrering](webjobs-sdk-how-to.md#log-filtering).

Nu kan du lägga till en funktion som utlöses av meddelanden som kommer i en Azure Storage kö.

## <a name="install-the-storage-binding-extension"></a>Installera tillägget för Storage-bindning

Från och med version 3. x måste du uttryckligen installera lagrings bindnings tillägget som krävs av WebJobs SDK. I tidigare versioner inkluderades lagrings bindningarna i SDK.

1. Installera den senaste stabila versionen av  [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3. x. 

    Här är kommandon för **Package Manager-konsolen** :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Ersätt `<3_X_VERSION>` med en version av paketet som stöds i det här kommandot. 

2. I `ConfigureWebJobs` tilläggs metoden anropar du `AddAzureStorage` metoden på [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instansen för att initiera lagrings tillägget. I det här läget `ConfigureWebJobs` ser metoden ut som i följande exempel:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Skapa en funktion

1. Högerklicka på projektet, Välj **Lägg till**  >  **nytt objekt...**, Välj **klass**, namnge den nya C#-klass filen *functions. cs* och välj **Lägg till**.

1. I functions. cs ersätter du den genererade mallen med följande kod:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   `QueueTrigger`Attributet instruerar körningen att anropa den här funktionen när ett nytt meddelande skrivs i en Azure Storage kö som kallas `queue` . Innehållet i Queue-meddelandet anges till metod koden i `message` parametern. I den här delen av metoden bearbetas utlösarens data. I det här exemplet loggar koden bara meddelandet.

   `message`Parametern behöver inte vara en sträng. Du kan också binda till ett JSON-objekt, en byte mat ris eller ett [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) -objekt. [Se användning av kö-utlösare](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Varje bindnings typ (till exempel köer, blobbar eller tabeller) har en annan uppsättning parameter typer som du kan binda till.

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Azure Storage emulatorn som körs lokalt har inte alla funktioner som WebJobs-SDK: n behöver. Så i det här avsnittet skapar du ett lagrings konto i Azure och konfigurerar projektet att använda det. Om du redan har ett lagrings konto går du vidare till steg 6.

1. Öppna **Server Explorer** i Visual Studio och logga in på Azure. Högerklicka på **Azure** -noden och välj sedan **Anslut till Microsoft Azure prenumeration**.

   ![Logga in på Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Under **Azure** -noden i **Server Explorer** högerklickar du på **lagring** och väljer sedan **skapa lagrings konto**.

   ![Menyn skapa lagrings konto](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. I dialog rutan **skapa lagrings konto** anger du ett unikt namn för lagrings kontot.

1. Välj samma **region** som du skapade din app service-app i, eller en region nära dig.

1. Välj **Skapa**.

   ![Skapa lagrings konto](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Under noden **lagring** i **Server Explorer** väljer du det nya lagrings kontot. I fönstret **Egenskaper** väljer du ellipsen (**...**) till höger om värde fältet för **anslutnings strängen** .

   ![Anslutnings sträng, ellips](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Kopiera anslutnings strängen och spara värdet någonstans så att du snabbt kan kopiera det.

   ![Kopiera anslutnings sträng](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurera lagring för att köras lokalt

WebJobs-SDK: n letar efter lagrings anslutnings strängen i program inställningarna i Azure. När du kör lokalt söker det efter det här värdet i den lokala konfigurations filen eller i miljövariabler.

1. Högerklicka på projektet, Välj **Lägg till**  >  **nytt objekt...**, Välj **Java Script JSON-konfigurationsfil**, ge den nya filen *appsettings.jspå* fil och välj **Lägg till**. 

1. I den nya filen lägger du till ett `AzureWebJobsStorage` fält, som i följande exempel:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Ersätt *{Storage Connection String}* med anslutnings strängen som du kopierade tidigare.

1. Välj *appsettings.js* filen i Solution Explorer och i fönstret **Egenskaper** väljer du **Kopiera till utdata-katalog** för att **Kopiera om nyare**.

Senare kommer du att lägga till samma inställning för anslutnings strängen i appen i Azure App Service.

## <a name="test-locally"></a>Testa lokalt

I det här avsnittet ska du skapa och köra projektet lokalt och utlösa funktionen genom att skapa ett meddelande i kön.

1. Tryck på **CTRL + F5** för att köra projektet.

   Konsolen visar att körningen har hittat din funktion och väntar på att köa meddelanden ska utlösa den. Följande utdata genereras av v3. x-värden:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Stäng konsol fönstret.

1. I **Server Explorer** i Visual Studio expanderar du noden för ditt nya lagrings konto och högerklickar sedan på **köer**.

1. Välj **skapa kö**.

1. Ange *kö* som namn för kön och välj sedan **OK**.

   ![Skärm bild som visar var du skapar kön och namnger den "kö". ](./media/webjobs-sdk-get-started/create-queue.png)

1. Högerklicka på noden för den nya kön och välj sedan **Visa kö**.

1. Välj ikonen **Lägg till meddelande** .

   ![Skärm bild som visar ikonen Lägg till meddelande.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. I dialog rutan **Lägg till meddelande** anger du *Hello World!* som **meddelande text** och välj sedan **OK**. Nu finns det ett meddelande i kön.

   ![Skapa kö](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Kör projektet igen.

   Eftersom du använde `QueueTrigger` attributet i `ProcessQueueMessage` funktionen lyssnar WeJobs SDK-körningen efter Queue meddelanden när den startas. Det hittar ett nytt Queue-meddelande i kön med namnet *Queue* och anropar funktionen.

   På grund av [backoff exponentiella](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)kan det ta upp till två minuter för körningen att hitta meddelandet och anropa funktionen. Den här vänte tiden kan minskas genom att köras i [utvecklings läge](webjobs-sdk-how-to.md#host-development-settings).

   Konsolens utdata ser ut så här:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Stäng konsol fönstret. 

1. Gå tillbaka till Queue-fönstret och uppdatera det. Meddelandet är borta eftersom det har bearbetats av din funktion som körs lokalt. 

## <a name="add-application-insights-logging"></a>Lägg till Application Insights loggning

När projektet körs i Azure kan du inte övervaka funktions körning genom att Visa konsolens utdata. Den övervaknings lösning som vi rekommenderar är [Application Insights](../azure-monitor/app/app-insights-overview.md). Mer information finns i [övervaka Azure Functions](../azure-functions/functions-monitoring.md).

I det här avsnittet ska du utföra följande åtgärder för att konfigurera Application Insights loggning innan du distribuerar till Azure:

* Se till att du har en App Service-app och en Application Insights-instans att arbeta med.
* Konfigurera App Service-appen så att den använder den Application Insights instansen och det lagrings konto som du skapade tidigare.
* Konfigurera projektet för att logga in på Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Skapa App Service-app och Application Insights instans

1. Om du inte redan har en App Service app som du kan använda [skapar du en](quickstart-dotnet-framework.md). När du skapar din app kan du också skapa en ansluten Application Insights-resurs. När du gör det är det `APPINSIGHTS_INSTRUMENTATIONKEY` konfigurerat för dig i din app.

1. Om du inte redan har en Application Insights resurs som du kan använda [skapar du en](../azure-monitor/app/create-new-resource.md ). Ange **program typen** **Allmänt** och hoppa över avsnitten som följer **Kopiera Instrumentation-nyckeln**.

1. Om du redan har en Application Insights resurs som du vill använda kopierar du [Instrumentation-tangenten](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurera appinställningar 

1. I **Server Explorer** i Visual Studio expanderar du noden **App Service** under **Azure**.

1. Expandera resurs gruppen som App Service-appen finns i och högerklicka sedan på din App Service-app.

1. Välj **visnings inställningar**.

1. I rutan **anslutnings strängar** lägger du till följande post.

   |Name  |anslutnings sträng  |Databas typ|
   |---------|---------|------|
   |AzureWebJobsStorage | {anslutnings strängen för lagring som du kopierade tidigare}|Anpassad|

1. Om rutan **program inställningar** inte har en Application Insights Instrumentation-nyckel, lägger du till den som du kopierade tidigare. (Instrumentation-nyckeln kanske redan finns där, beroende på hur du skapade App Service-appen.)

   |Name  |Värde  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {Instrumentation-nyckel} |

1. Ersätt *{Instruments Key}* med Instrumentation-nyckeln från den Application Insights resurs som du använder.

1. Välj **Spara**.

1. Lägg till Application Insights anslutningen till projektet så att du kan köra den lokalt. Lägg till ett fält i filen *appsettings.jspå* `APPINSIGHTS_INSTRUMENTATIONKEY` , som i följande exempel:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Ersätt *{Instruments Key}* med Instrumentation-nyckeln från den Application Insights resurs som du använder.

1. Spara ändringarna.

### <a name="add-application-insights-logging-provider"></a>Lägg till Application Insights Logging Provider

Om du vill dra nytta av [Application Insights](../azure-monitor/app/app-insights-overview.md) loggning uppdaterar du loggnings koden för att göra följande:

* Lägg till en Application Insights Logging-Provider med standard [filtrering](webjobs-sdk-how-to.md#log-filtering). När du kör lokalt skrivs all information och loggar på högre nivå till både-konsolen och Application Insights.
* Placera [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) -objektet i ett `using` block för att säkerställa att logg utdata töms när värden avslutas.

1. Installera den senaste stabila 3. x-versionen av [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

   Här är kommandon för **Package Manager-konsolen** :

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Ersätt `<3_X_VERSION>` med en version av paketet som stöds i det här kommandot.

1. Öppna *programmet. cs* och Ersätt koden i- `Main` metoden med följande kod:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Detta lägger till Application Insights-providern till loggningen med hjälp av den nyckel som du lade till tidigare i appinställningar.

## <a name="test-application-insights-logging"></a>Testa Application Insights loggning

I det här avsnittet ska du köra lokalt igen för att kontrol lera att loggnings data nu kommer till Application Insights och till-konsolen.

1. Använd **Server Explorer** i Visual Studio för att skapa ett köat meddelande som du gjorde [tidigare](#test-locally), förutom att ange *Hello App Insights!* som meddelande text.

1. Kör projektet.

   WebJobs-SDK bearbetar Queue-meddelandet och du ser loggarna i konsol fönstret.

1. Stäng konsol fönstret.

1. Gå till [Azure Portal](https://portal.azure.com/) för att visa din Application Insights-resurs. Sök efter och välj **Application Insights**.

1. Välj din Application Insights-instans.

1. Välj **Sök**.

   ![Välj sökning](./media/webjobs-sdk-get-started/select-search.png)

1. Om du inte ser *Hej appen Insights!* meddelande väljer du **Uppdatera** regelbundet i flera minuter. (Loggar visas inte direkt, eftersom det tar en stund för Application Insights-klienten att rensa loggarna som den bearbetar.)

   ![Loggar i Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Stäng konsol fönstret.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Distribuera till Azure

Under distributionen skapar du en app service-instans där funktionerna ska köras. När du publicerar en .NET Core-konsolfil till App Service i Azure körs den automatiskt som ett webb jobb. Mer information om hur du publicerar finns i [utveckla och distribuera WebJobs med Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Utlös funktionen i Azure

1. Se till att du inte kör lokalt (Stäng konsol fönstret om det fortfarande är öppet). Annars kan den lokala instansen vara den första att bearbeta eventuella Kömeddelanden som du skapar.

1. På sidan **kö** i Visual Studio lägger du till ett meddelande i kön som tidigare.

1. Uppdatera sidan **kö** så försvinner det nya meddelandet eftersom det har bearbetats av funktionen som körs i Azure.

   > [!TIP]
   > När du testar i Azure ska du använda [utvecklings läget](webjobs-sdk-how-to.md#host-development-settings) för att se till att en kö utlösnings funktion anropas direkt och undvika fördröjningar på grund av att [kön avsöker exponentiell backoff](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Visa loggar i Application Insights

1. Öppna [Azure Portal](https://portal.azure.com/)och gå till din Application Insights-resurs.

1. Välj **Sök**.

1. Om du inte ser *Hej Azure!* meddelande väljer du **Uppdatera** regelbundet i flera minuter.

   Du ser loggarna från funktionen som körs i ett webbjobb, inklusive *Hej Azure!* text som du angav i föregående avsnitt.

## <a name="add-an-input-binding"></a>Lägg till en inbindning

Data bindningar fören klar kod som läser data. I det här exemplet är Queue-meddelandet ett BLOB-namn och du använder BLOB-namnet för att hitta och läsa en BLOB i Azure Storage.

1. I *functions. cs* ersätter du `ProcessQueueMessage` metoden med följande kod:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   I den här koden `queueTrigger` är ett [bindnings uttryck](../azure-functions/functions-bindings-expressions-patterns.md), vilket innebär att det matchar ett annat värde vid körning.  Vid körning har den innehållet i Queue-meddelandet.

1. Lägg till `using` :

   ```cs
   using System.IO;
   ```

1. Skapa en BLOB-behållare i ditt lagrings konto.

   a. I **Server Explorer** i Visual Studio expanderar du noden för ditt lagrings konto, högerklickar på **blobbar** och väljer sedan **skapa BLOB-behållare**.

   b. I dialog rutan **skapa BLOB-behållare** anger du *container* som behållar namn och klickar sedan på **OK**.

1. Ladda upp filen *program. cs* till BLOB-behållaren. (Filen används här som ett exempel. du kan ladda upp en textfil och skapa ett Queue-meddelande med filens namn.)

   a. I **Server Explorer** dubbelklickar du på noden för den behållare som du skapade.

   b. I fönstret **behållare** väljer du knappen **överför** .

   ![Knappen BLOB-uppladdning](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Leta upp och välj *program. cs* och välj sedan **OK**.

1. Skapa ett Queue-meddelande i kön som du skapade tidigare, med *programmet. cs* som meddelande text.

   ![Köa meddelande program. CS](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Kör projektet lokalt.

   Queue-meddelandet utlöser funktionen, som sedan läser blobben och loggar dess längd. Konsolens utdata ser ut så här:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

Utgående bindningar fören klar kod som skriver data. Det här exemplet ändrar den tidigare filen genom att skriva en kopia av blobben i stället för att logga dess storlek. Blob Storage-bindningar ingår i Azure Storage tilläggs paketet som vi installerade tidigare.

1. Ersätt metoden `ProcessQueueMessage` med följande kod:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Skapa ett annat Queue-meddelande med *program. cs* som meddelande text.

1. Kör projektet lokalt.

   Queue-meddelandet utlöser funktionen, som sedan läser bloben, loggar dess längd och skapar en ny blob. Konsolens utdata är samma, men när du går till fönstret BLOB container och väljer **Uppdatera** visas en ny BLOB med namnet *copy-program. cs.*

## <a name="republish-the-updates-to-azure"></a>Publicera uppdateringarna på Azure igen

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I dialog rutan **publicera** ser du till att den aktuella profilen är markerad och väljer sedan **publicera**. Resultaten av publiceringen beskrivs i fönstret **utdata** .
 
1. Verifiera funktionen i Azure genom att återigen Ladda upp en fil till BLOB-behållaren och lägga till ett meddelande i kön som är namnet på den överförda filen. Du ser meddelandet ta bort från kön och en kopia av filen som skapats i BLOB-behållaren. 

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du skapar, kör och distribuerar ett WebJobs SDK 3. x-projekt.

> [!div class="nextstepaction"]
> [Läs mer om WebJobs SDK](webjobs-sdk-how-to.md)