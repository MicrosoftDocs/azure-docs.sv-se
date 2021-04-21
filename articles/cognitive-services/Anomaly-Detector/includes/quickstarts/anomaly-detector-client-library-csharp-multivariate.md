---
title: Avvikelseidentifiering snabbstart för .NET multivariate-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: a657b8f5bf967131a0168dbea5bb1db86b3b559e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799933"
---
Kom igång med Avvikelseidentifiering flervariat klientbibliotek för .NET. Följ de här stegen för att installera paketet och börja använda de algoritmer som tillhandahålls av tjänsten. Med de nya API:erna för multivarierad avvikelseidentifiering kan utvecklare enkelt integrera avancerad AI för att identifiera avvikelser från grupper med mått, utan att behöva maskininlärning eller märkta data. Beroenden och interkorrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. Detta hjälper dig att proaktivt skydda dina komplexa system mot fel.

Använd Avvikelseidentifiering flervariat klientbibliotek för .NET för att:

* Identifiera avvikelser på systemnivå från en grupp med tidsserier.
* När en enskild tidsserie inte berättar så mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predikatiskt underhåll av dyra fysiska tillgångar med tiotals till hundratals olika typer av sensorer som mäter olika aspekter av systemhälsan.

[Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/anomalydetector/Azure.AI.AnomalyDetector)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* När du har din Azure-prenumeration skapar Avvikelseidentifiering en Avvikelseidentifiering resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. Vänta tills den har distribuerats och välj **knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Avvikelseidentifiering-API:et. Klistra in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en `dotnet new` ny konsolapp med namnet `anomaly-detector-quickstart-multivariate` . Det här kommandot skapar ett enkelt "Hello World"-projekt med en enda C#-källfil: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Ändra katalogen till den nyligen skapade appmappen. Du kan skapa programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla några varningar eller fel.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

I programkatalogen installerar du Avvikelseidentifiering för .NET med följande kommando:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

Från projektkatalogen öppnar du filen *program.cs* och lägger till följande med `directives` :

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

I programmets `main()` -metod skapar du variabler för resursens Azure-slutpunkt, DIN API-nyckel och en anpassad datakälla.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Om du vill Avvikelseidentifiering flera api:er måste vi träna vår egen modell innan du använder identifiering. Data som används för träning är en batch med tidsserier. Varje tidsserie ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tidsserier ska zippas upp i en zip-fil och laddas upp till [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Som standard används filnamnet för att representera variabeln för tidsserien. Alternativt kan en extra meta.jspå filen inkluderas i zip-filen om du vill att namnet på variabeln ska vara ett annat än ZIP-filnamnet. När vi har genererat [en BLOB-SAS-URL (signaturer](../../../../storage/common/storage-sas-overview.md)för delad åtkomst) kan vi använda URL:en till ZIP-filen för träning.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med Avvikelseidentifiering multivariate-klientbibliotek för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Träna modellen](#train-the-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera Avvikelseidentifiering klient med din slutpunkt och nyckel.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>Träna modellen

Skapa en ny privat asynkron uppgift enligt nedan för att hantera träningen av din modell. Du kommer att `TrainMultivariateModel` använda för att träna modellen och för att kontrollera när `GetMultivariateModelAysnc` träningen är klar.

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

Om du vill identifiera avvikelser med hjälp av din nyligen tränade modell skapar du en `private async Task` med namnet `detectAsync` . Du skapar en ny och `DetectionRequest` skickar den som en parameter till `DetectAnomalyAsync` .

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

Om du vill exportera modellen som du tränade tidigare skapar du en `private async Task` med namnet `exportAysnc` . Du använder och `ExportModelAsync` skickar modell-ID:t för den modell som du vill exportera.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
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

Ta bort en modell som du har skapat tidigare och `DeleteMultivariateModelAsync` skicka modell-ID för den modell som du vill ta bort. Om du vill hämta ett modell-ID kan du göra `getModelNumberAsync` följande:

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

Nu när du har alla komponentdelar måste du lägga till ytterligare kod i din huvudmetod för att anropa dina nya uppgifter.

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

Kör programmet med kommandot `dotnet run` från programkatalogen.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Nästa steg

* [Avvikelseidentifiering metodtips med flera](../../concepts/best-practices-multivariate.md)
