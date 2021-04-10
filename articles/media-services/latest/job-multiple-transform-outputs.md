---
title: Skapa ett jobb med flera transformera utdata
description: I det här avsnittet visas hur du skapar ett Azure Media Services jobb med flera Transform-utdata.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: fc3ab64a35c24df2af9d90a5ad348f840610d188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102635849"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Skapa ett jobb med flera transformera utdata

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Det här avsnittet visar hur du skapar en transformering med två transformera utdata. Det första anropet för indata som ska kodas för strömning med anpassad bit hastighet med en inbyggd [AdaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset) för inställning. Det andra anropet för ljud signalen i indata-videon som ska bearbetas med [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets). När transformeringen har skapats kan du skicka ett jobb som bearbetar din video på motsvarande sätt. Eftersom vi i det här exemplet anger två transformera utdata måste vi ange två jobb-utdata. Du kan välja att dirigera både jobbets utdata till samma till gång (enligt nedan), eller så kan du få resultaten att skrivas till separata till gångar.

> [!TIP]
> Innan du börjar utveckla bör du läsa [utveckla med Media Services v3-API: er](media-services-apis-overview.md) (innehåller information om hur du kommer åt API: er, namngivnings konventioner osv.)

## <a name="create-a-transform"></a>Skapa en transformering

Följande kod visar hur du skapar en transformering som producerar två utdata.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```

## <a name="submit-a-job"></a>Skicka ett jobb

Skapa ett jobb med en HTTPS-URL-indata och med två jobb utdata.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Azure Media Services v3-exempel med .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
