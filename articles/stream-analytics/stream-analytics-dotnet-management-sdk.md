---
title: Hantering av .NET SDK för Azure Stream Analytics
description: Kom igång med Stream Analytics Management .NET SDK. Lär dig hur du konfigurerar och kör analys jobb. Skapa ett projekt, indata, utdata och transformeringar.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/12/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9adc4c92e3e637b9d3e18249b5de00782a94baab
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232893"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Hantering av .NET SDK: Konfigurera och kör analys jobb med hjälp av Azure Stream Analytics API för .NET
Lär dig hur du konfigurerar och kör analys jobb med hjälp av Stream Analytics API för .NET med hjälp av Management .NET SDK. Konfigurera ett projekt, skapa indata och utdata, transformeringar och start-och stopp jobb. För dina analys jobb kan du strömma data från Blob Storage eller från en Event Hub.

Se [hanterings referens dokumentationen för Stream Analytics API för .net](/previous-versions/azure/dn889315(v=azure.100)).

Azure Stream Analytics är en helt hanterad tjänst som tillhandahåller låg latens, skalbar, komplex händelse bearbetning med hög tillgänglighet för strömning av data i molnet. Stream Analytics gör det möjligt för kunderna att ställa in strömmande jobb för att analysera data strömmar och göra det möjligt för dem att köra analyser i nära real tid.  

> [!NOTE]
> Vi har uppdaterat exempel koden i den här artikeln med Azure Stream Analytics Management .NET SDK v2. x-version. Exempel kod som använder SDK-versionen för lagecy (1. x) finns i [Använd Management .NET SDK v1. x för Stream Analytics]().

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här artikeln måste du ha följande krav:

* Installera Visual Studio 2019 eller 2015.
* Hämta och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Skapa en Azure-resurs grupp i din prenumeration. Följande exempel är ett exempel på ett Azure PowerShell skript. Azure PowerShell information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Konfigurera en indatakälla och ett utgående mål för det jobb som du vill ansluta till.

## <a name="set-up-a-project"></a>Konfigurera ett projekt
Om du vill skapa ett analys jobb använder du Stream Analytics API för .NET, först konfigurerar du ditt projekt.

1. Skapa ett Visual Studio C# .NET-konsol program.
2. I Package Manager-konsolen kör du följande kommandon för att installera NuGet-paketen. Den första är Azure Stream Analytics Management .NET SDK. Den andra är för Azure-klientautentisering.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Lägg till följande **appSettings** -avsnitt i App.config-filen:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Ersätt värden för **SubscriptionId** och **ActiveDirectoryTenantId** med din Azure-prenumeration och klient-ID. Du kan hämta dessa värden genom att köra följande Azure PowerShell-cmdlet:

   ```powershell
      Get-AzureAccount
   ```

4. Lägg till följande referens i din. CSPROJ-fil:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Lägg till följande **using** -instruktioner till käll filen (program.CS) i projektet:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Lägg till en hjälp metod för autentisering:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Skapa en Stream Analytics hanterings klient
Med ett **StreamAnalyticsManagementClient** -objekt kan du hantera jobbet och jobb komponenterna, till exempel indata, utdata och omvandling.

Lägg till följande kod i början av **main** -metoden:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

**ResourceGroupName** -variabelns värde ska vara samma som namnet på den resurs grupp som du skapade eller valde i de nödvändiga stegen.

Om du vill automatisera presentation av autentiseringsuppgifter för att skapa jobb, referera till [autentisering av ett tjänst objekt med Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

I de återstående avsnitten i den här artikeln förutsätts att koden är i början av **main** -metoden.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Följande kod skapar ett Stream Analytics jobb under den resurs grupp som du har definierat. Du kommer att lägga till en indata-, utdata-och omvandling till jobbet senare.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Skapa en indatakälla för Stream Analytics
I följande kod skapas en Stream Analytics indatakälla med typ av BLOB-datakälla och CSV-serialisering. Om du vill skapa en indatakälla för Event Hub använder du **EventHubStreamInputDataSource** i stället för **BlobStreamInputDataSource**. På samma sätt kan du anpassa serialiserings typen för Indatakällan.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Indata-källor, oavsett om de är i Blob Storage eller en händelsehubben, är kopplade till ett speciellt jobb. Om du vill använda samma indatakälla för olika jobb måste du anropa metoden igen och ange ett annat jobbnamn.

## <a name="test-a-stream-analytics-input-source"></a>Testa en indatakälla för Stream Analytics
Metoden **TestConnection** testar om Stream Analyticss jobbet kan ansluta till Indatakällan samt andra aspekter som är relaterade till typen av indatakälla. Till exempel i BLOB-Indatakällan som du skapade i ett tidigare steg, kontrollerar metoden att lagrings kontots namn och nyckel par kan användas för att ansluta till lagrings kontot samt kontrol lera att den angivna behållaren finns.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```
Resultatet av TestConnection-anropet är ett *ResourceTestResult* -objekt som innehåller två egenskaper:

- *status*: det kan vara en av följande strängar: ["TestNotAttempted", "TestSucceeded", "TestFailed"]
- *fel*: det är av typen ErrorResponse som innehåller följande egenskaper:
   - *Code*: en obligatorisk egenskap av typen sträng. Värdet är standard system .net. HttpStatusCode tas emot vid testning.
   - *meddelande*: en obligatorisk egenskap av typen sträng som representerar felet. 

## <a name="create-a-stream-analytics-output-target"></a>Skapa ett Stream Analytics utgående mål
Att skapa ett utgående mål liknar att skapa en indatakälla för Stream Analytics. Precis som indata källor är mål för utdata knutna till ett speciellt jobb. Om du vill använda samma utgående mål för olika jobb måste du anropa metoden igen och ange ett annat jobbnamn.

Följande kod skapar ett utgående mål (Azure SQL Database). Du kan anpassa utmatnings målets datatyp och/eller serialiserings typ.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testa ett Stream Analytics utgående mål
Ett Stream Analytics utgående mål har också **TestConnection** -metoden för att testa anslutningar.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Skapa en Stream Analytics-transformering
Följande kod skapar en Stream Analytics-omvandling med frågan "Välj * från indata" och anger att en strömmande enhet ska tilldelas för Stream Analytics jobb. Mer information om hur du justerar strömnings enheter finns i [skala Azure Stream Analytics jobb](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Precis som indata och utdata är en omvandling också kopplad till det aktuella Stream Analyticss jobb som den skapades under.

## <a name="start-a-stream-analytics-job"></a>Starta ett Stream Analytics-jobb
När du har skapat ett Stream Analytics jobb och dess indata, utdata och transformering, kan du starta jobbet genom att anropa **Start** metoden.

Följande exempel kod startar ett Stream Analytics jobb med en anpassad start tid för utdata angivet till 12 december 2012 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Stoppa ett Stream Analytics jobb
Du kan stoppa ett pågående Stream Analytics jobb genom att anropa metoden **Stop** .

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Ta bort ett Stream Analytics jobb
Metoden **Delete** tar bort jobbet och de underliggande under resurserna, inklusive indata, utdata och jobbets omvandling...

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Få support
Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg
Du har lärt dig grunderna i att använda en .NET SDK för att skapa och köra analys jobb. Mer information finns i följande artiklar:

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics hantering av .NET SDK](/previous-versions/azure/dn889315(v=azure.100)).
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
