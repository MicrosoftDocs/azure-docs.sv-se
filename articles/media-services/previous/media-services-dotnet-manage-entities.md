---
title: Hantera till gångar och relaterade entiteter med Media Services .NET SDK
description: Lär dig hur du hanterar till gångar och relaterade entiteter med Media Services SDK för .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b859ff49edb495be15cdf76719c3aaaf1ac72a6e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013779"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Hantera till gångar och relaterade entiteter med Media Services .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Det här avsnittet visar hur du hanterar Azure Media Services entiteter med .NET.

Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Den 1 april 2017 tas till exempel alla jobb poster i ditt konto som är äldre än 31 december 2016 bort automatiskt. Om du behöver arkivera jobb-/uppgifts informationen kan du använda koden som beskrivs i det här avsnittet.

## <a name="prerequisites"></a>Förutsättningar

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Hämta en till gångs referens
En frekvent uppgift är att hämta en referens till en befintlig till gång i Media Services. I följande kod exempel visas hur du kan hämta en till gångs referens från till gångs samlingen på objektet Server kontext, baserat på ett till gångs-ID. I följande kod exempel används en LINQ-fråga för att hämta en referens till ett befintligt IAsset-objekt.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Visa lista över alla till gångar
När antalet till gångar som du har i lagrings utrymmet växer är det bra att ange dina till gångar. I följande kod exempel visas hur du itererar genom samlingen till gångar på objektet Server kontext. Med varje till gång skriver kod exemplet också några av dess egenskaps värden till-konsolen. Till exempel kan varje till gång innehålla många mediafiler. Kod exemplet skriver ut alla filer som är associerade med varje till gång.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Hämta en jobb referens

När du arbetar med bearbetnings uppgifter i Media Services kod, behöver du ofta hämta en referens till ett befintligt jobb baserat på ett ID. I följande kod exempel visas hur du hämtar en referens till ett IJob-objekt från jobb samlingen.

Du kan behöva hämta en jobb referens när du startar ett tids krävande kodnings jobb och behöver kontrol lera jobb status på en tråd. I sådana fall när metoden returnerar från en tråd måste du hämta en uppdaterad referens till ett jobb.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Visa lista över jobb och till gångar
En viktig relaterad uppgift är att lista till gångar med tillhör ande jobb i Media Services. I följande kod exempel visas hur du listar varje IJob-objekt, och för varje jobb visas egenskaper för jobbet, alla relaterade aktiviteter, alla indata till gångar och alla utgående till gångar. Koden i det här exemplet kan vara användbar för många andra uppgifter. Om du till exempel vill visa utdata till gångar från ett eller flera kodnings jobb som du körde tidigare, visar den här koden hur du kommer åt utmatnings till gångarna. När du har en referens till en utgående till gång kan du sedan leverera innehållet till andra användare eller program genom att ladda ned det eller tillhandahålla URL: er. 

Mer information om alternativ för att leverera till gångar finns i [leverera till gångar med Media Services SDK för .net](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Visa lista över alla åtkomst principer
I Media Services kan du definiera en åtkomst princip för en till gång eller dess filer. En åtkomst princip definierar behörigheterna för en fil eller en till gång (vilken typ av åtkomst och varaktighet). I din Media Servicess kod definierar du vanligt vis en åtkomst princip genom att skapa ett IAccessPolicy-objekt och sedan associera det med en befintlig till gång. Sedan skapar du ett ILocator-objekt som låter dig ge direkt åtkomst till till gångar i Media Services. Visual Studio-projektet som medföljer den här dokumentations serien innehåller flera kod exempel som visar hur du skapar och tilldelar åtkomst principer och positionerare till till gångar.

I följande kod exempel visas hur du visar en lista över alla åtkomst principer på servern och visar vilken typ av behörighet som är associerad med var och en. Ett annat användbart sätt att visa åtkomst principer är att visa en lista över alla ILocator-objekt på servern och sedan kan du ange den associerade åtkomst principen med hjälp av dess Access policy-egenskap för varje positionerare.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Begränsa åtkomst principer 

>[!NOTE]
> Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). 

Du kan till exempel skapa en allmän uppsättning principer med följande kod som bara skulle köras en gången i ditt program. Du kan logga ID: n till en loggfil för senare användning:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Sedan kan du använda de befintliga ID: na i koden så här:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Lista alla positionerare
En Locator är en URL som ger en direkt sökväg till åtkomst till en till gång, tillsammans med behörigheter till till gången som definieras av platsens associerade åtkomst princip. Varje till gång kan ha en samling av ILocator-objekt som är kopplade till den i egenskapen Locators. Server kontexten har också en lokaliserare-samling som innehåller alla positionerare.

I följande kod exempel visas alla positioner på servern. För varje positionerare visas ID: t för den relaterade till gången och åtkomst principen. Den visar också typ av behörighet, förfallo datum och fullständig sökväg till till gången.

Observera att en lokaliserare till en till gång endast är en bas-URL till till gången. För att skapa en direkt sökväg till enskilda filer som en användare eller ett program kan bläddra till, måste koden lägga till den specifika sökvägen till sökvägen till lokaliseraren. Mer information om hur du gör detta finns i avsnittet [leverera till gångar med Media Services SDK för .net](media-services-deliver-streaming-content.md).

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Räkna upp genom stora samlingar av entiteter
När du frågar entiteter, finns det en gräns på 1000 entiteter som returneras vid en tidpunkt eftersom offentliga REST v2 begränsar frågeresultat till 1000-resultat. Du måste använda hoppa över och ta när du räknar upp genom stora mängder av entiteter. 

Följande funktion upprepas genom alla jobb i det tillhandahållna Media Services kontot. Media Services returnerar 1000 jobb i jobb samlingen. Funktionen använder hoppa över och ta för att se till att alla jobb räknas upp (om du har fler än 1000 jobb i ditt konto).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Ta bort en till gång
I följande exempel tas en till gång bort.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Ta bort ett jobb
Om du vill ta bort ett jobb måste du kontrol lera jobbets status som anges i egenskapen state. Jobb som har avslut ATS eller avbrutits kan tas bort, medan jobb i vissa andra tillstånd, t. ex. köade, schemalagda eller bearbetade, först måste avbrytas och sedan kan de tas bort.

I följande kod exempel visas en metod för att ta bort ett jobb genom att kontrol lera jobb tillstånden och sedan ta bort när tillståndet är klart eller annullerat. Den här koden är beroende av föregående avsnitt i det här avsnittet för att hämta en referens till ett jobb: Hämta en jobb referens.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Ta bort en åtkomst princip
I följande kod exempel visas hur du hämtar en referens till en åtkomst princip baserat på ett princip-ID och sedan tar bort principen.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
