---
title: Snabb start för klient bibliotek för avvikelse detektor i .NET multivarierad
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 1318a8c410f14f4a1dc91072d66f18e39f7ca7e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316052"
---
Kom igång med multivarierad klient bibliotek för avvikelse detektor för .NET. Följ de här stegen för att installera paketet och börja använda de algoritmer som tillhandahålls av tjänsten. De nya API: erna för avvikelse identifiering i multivarierad gör det möjligt för utvecklare att enkelt integrera avancerad AI för att identifiera avvikelser från grupper av mått, utan att det behövs några kunskaper om Machine Learning eller märkta data. Beroenden och Inter-korrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. Detta hjälper dig att proaktivt skydda dina komplexa system från haverier.

Använd klient biblioteket för avvikelse detektor multivarierad för .NET för att:

* Identifiera avvikelser på system nivå från en grupp tids serier.
* När en enskild tid serie inte meddelar dig mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predicative-underhåll av dyra fysiska till gångar med flera till hundratals olika typer av sensorer som mäter olika aspekter av system hälsan.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och välj knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Klistra in din nyckel och slut punkt i koden nedan senare i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `anomaly-detector-quickstart-multivariate` . Det här kommandot skapar ett enkelt "Hello World"-projekt med en enda C#-källfil: *program. cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla varningar eller fel.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du klient biblioteket för avvikelse detektor för .NET med följande kommando:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

Öppna filen *program. cs* från projekt katalogen och Lägg till följande med hjälp av `directives` :

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

I programmets `main()` metod skapar du variabler för resursens Azure-slutpunkt, din API-nyckel och en anpassad data källa.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 För att kunna använda multivarierad-API: erna för avvikelse detektor måste vi träna vår egen modell innan identifieringen används. Data som används för utbildning är en batch över tids serier, varje gång serien ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tids serier ska vara zippade till en zip-fil och överföras till [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Som standard används fil namnet för att representera variabeln för tids serien. Alternativt kan en extra meta.jsi filen inkluderas i zip-filen om du vill att namnet på variabeln ska skilja sig från. zip-filens namn. När vi genererar en [BLOB SAS-URL (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL: en till zip-filen för utbildning.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med klient biblioteket för avvikelse detektor multivarierad för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Träna modellen](#train-the-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en klient med avvikelse detektor med din slut punkt och nyckel.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>Träna modellen

Skapa en ny privat asynkron uppgift enligt nedan för att hantera utbildningen för din modell. Du kommer att använda `TrainMultivariateModel` för att träna modellen och `GetMultivariateModelAysnc` kontrol lera när inlärningen är klar.

```csharp
private async Task trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        ModelStatus? model_status = null;
        int tryout_count = 0;
        TimeSpan create_limit = new TimeSpan(0, 3, 0);
        while (tryout_count < max_tryout & model_status != ModelStatus.Ready)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            ModelInfo model_info = get_response.Value.ModelInfo;
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, model_info.Status));

            if (model_info != null)
            {
                model_status = model_info.Status;
            }
            tryout_count += 1;
        };
        get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);

        if (model_status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>Identifiera avvikelser

Skapa ett namngivet namn om du vill identifiera avvikelser med den nyligen utbildade modellen `private async Task` `detectAsync` . Du kommer att skapa en ny `DetectionRequest` och skicka den som en parameter till `DetectAnomalyAsync` .

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        int tryout_count = 0;
        while (result.Value.Summary.Status != DetectionStatus.Ready & tryout_count < max_tryout)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
            tryout_count += 1;
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>Exportera modell

Om du vill exportera modellen som du har tränat tidigare skapar du en `private async Task` namngiven `exportAysnc` . Du kommer att använda `ExportModelAsync` och skicka modell-ID: t för den modell som du vill exportera.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Response model_response = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        Stream model;
        if (model_response.ContentStream != null)
        {
            model = model_response.ContentStream;
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>Ta bort modell

Ta bort en modell som du har skapat tidigare `DeleteMultivariateModelAsync` och skicka modell-ID: t för den modell som du vill ta bort. Du kan hämta ett modell-ID som du kan oss på `getModelNumberAsync` :

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Main-metoden

Nu när du har alla komponent delar måste du lägga till ytterligare kod i huvud metoden för att anropa dina nyligen skapade uppgifter.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `dotnet run` kommandot från program katalogen.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Nästa steg

* [Metod tips för multivarierad av avvikelse detektor](../../concepts/best-practices-multivariate.md)
