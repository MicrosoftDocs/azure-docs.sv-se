---
title: Övervaka och hantera Azure Stream Analytics-jobb program mässigt
description: 'Den här artikeln beskriver hur du övervakar Stream Analytics-jobb som skapats via REST-API: er, Azure SDK eller PowerShell program mässigt.'
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dda1b6b652870440e7228e6640bb555f4c2e4a63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015084"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Skapa en Stream Analytics jobb övervakare program mässigt

Den här artikeln visar hur du aktiverar övervakning för ett Stream Analytics jobb. Stream Analytics jobb som skapas via REST-API: er, Azure SDK eller PowerShell har inte övervakning aktiverat som standard. Du kan aktivera det manuellt i Azure Portal genom att gå till jobbets övervaknings sida och klicka på knappen Aktivera, eller så kan du automatisera processen genom att följa stegen i den här artikeln. Övervaknings data visas i mått-avsnittet i Azure Portal för ditt Stream Analytics jobb.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här processen måste du ha följande krav:

* Visual Studio 2019 eller 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) har hämtats och installerats
* Ett befintligt Stream Analytics jobb som kräver övervakning aktive rad

## <a name="create-a-project"></a>Skapa ett projekt

1. Skapa ett Visual Studio C# .NET-konsol program.
2. I Package Manager-konsolen kör du följande kommandon för att installera NuGet-paketen. Den första är Azure Stream Analytics Management .NET SDK. Den andra är den Azure Monitor SDK som ska användas för att aktivera övervakning. Den sista är den Azure Active Directory klienten som ska användas för autentisering.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Lägg till följande appSettings-avsnitt i App.config-filen.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Ersätt värden för *SubscriptionId* och *ActiveDirectoryTenantId* med din Azure-prenumeration och klient-ID. Du kan hämta dessa värden genom att köra följande PowerShell-cmdlet:
   
   ```powershell
   Get-AzureAccount
   ```
4. Lägg till följande genom att använda instruktioner till källfilen (Program.cs) i projektet.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Lägg till en hjälp metod för autentisering.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Skapa hanterings klienter

Följande kod ställer in nödvändiga variabler och hanterings klienter.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Aktivera övervakning för ett befintligt Stream Analytics jobb

Följande kod aktiverar övervakning av ett **befintligt** Stream Analytics-jobb. Den första delen av koden utför en GET-begäran mot tjänsten Stream Analytics för att hämta information om det specifika Stream Analytics jobbet. Den använder *ID-* egenskapen (hämtas från get-begäran) som en parameter för metoden tag i den andra halvan av koden, som skickar en skicka-begäran till tjänsten Insights för att aktivera övervakning av Stream Analyticss jobbet.

> [!WARNING]
> Om du tidigare har aktiverat övervakning för ett annat Stream Analytics jobb, antingen via Azure Portal eller program mässigt via koden nedan, **rekommenderar vi att du anger samma lagrings konto namn som du använde när du tidigare aktiverade övervakningen.**
> 
> Lagrings kontot är länkat till den region där du skapade ditt Stream Analytics jobb i, inte specifikt till själva jobbet.
> 
> Alla Stream Analytics-jobb (och alla andra Azure-resurser) i samma region delar det här lagrings kontot för att lagra övervaknings data. Om du anger ett annat lagrings konto kan det orsaka oönskade sid effekter i övervakningen av dina andra Stream Analytics jobb eller andra Azure-resurser.
> 
> Namnet på det lagrings konto som du använder för att ersätta `<YOUR STORAGE ACCOUNT NAME>` i följande kod ska vara ett lagrings konto som finns i samma prenumeration som det Stream Analytics jobb som du aktiverar övervakning för.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Få support

Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)