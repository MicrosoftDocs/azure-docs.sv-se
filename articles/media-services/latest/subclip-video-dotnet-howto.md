---
title: Under klipp en video vid kodning med Media Services
description: I det här avsnittet beskrivs hur du underklipper en video när du kodar med Azure Media Services med .NET SDK
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a9408adfe33fc71707532f3bf5646f3727fd7dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955062"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Under klipp en video vid kodning med Media Services-.NET

Du kan trimma eller under klipp en video när du kodar den med ett [jobb](/rest/api/media/jobs). Den här funktionen fungerar med alla [transformeringar](/rest/api/media/transforms) som har skapats med antingen [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) för inställningar eller [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) för inställningar.

Följande C#-exempel skapar ett jobb som trimmar en video i en till gång när den skickar ett kodnings jobb. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i det här avsnittet måste du:

- [Skapa ett Azure Media Services-konto](./create-account-howto.md)
- Skapa en transformering och in-och utdata-till gångar. Du kan se hur du skapar en transformering och in-och utdata-till gångar i guiden [Ladda upp, koda och strömma videor med hjälp av .net](stream-files-tutorial-with-api.md) .
- Läs avsnittet [encoding Concept](encoding-concept.md) .

## <a name="example"></a>Exempel

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Nästa steg

[Koda med en anpassad transformering](customize-encoder-presets-how-to.md) 
