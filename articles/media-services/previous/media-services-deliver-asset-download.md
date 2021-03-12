---
title: Ladda ned Media Services-tillgångar till datorn – Azure | Microsoft Docs
description: Lär dig mer om att ladda ned till gångar till din dator. Kod exempel skrivs i C# och använder Media Services SDK för .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 60f91e97a9bce1427b4ed8d251fe297d9eb7d969
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016669"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Gör så här: leverera en till gång genom att ladda ned

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Den här artikeln beskriver alternativ för att leverera media till gångar som har överförts till Media Services. Du kan leverera Media Services innehåll i flera olika program scenarier. Efter kodningen kan du hämta de genererade medie till gångarna eller komma åt dem med hjälp av en strömmande positionerare. För bättre prestanda och skalbarhet kan du även leverera innehåll med hjälp av en Content Delivery Network (CDN).

Det här exemplet visar hur du hämtar medie till gångar från Media Services till din lokala dator. Koden frågar jobben som associeras med Media Services-kontot efter jobb-ID och får åtkomst till sin **OutputMediaAssets** -samling (som är en uppsättning av ett eller flera medie till gångar som resulterar i att ett jobb körs). I det här exemplet visas hur du hämtar utdata till medie till gångar från ett jobb, men du kan använda samma metod för att ladda ned andra till gångar.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomst behörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (principer som inte uppladdas). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Leverera strömmande innehåll](media-services-deliver-streaming-content.md)

