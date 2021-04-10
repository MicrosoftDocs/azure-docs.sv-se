---
title: Publicera Azure Media Services innehåll med hjälp av .NET | Microsoft Docs
description: Lär dig hur du skapar en positionerare som används för att skapa en strömnings-URL. Kod exempel skrivs i C# och använder Media Services SDK för .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a1a06c9a5fdac1082a57bee71cccad271a6aae81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015802"
---
# <a name="publish-media-services-content-using-net"></a>Publicera Media Services innehåll med .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Översikt
Du kan strömma en MP4-uppsättning med anpassad bit hastighet genom att skapa en OnDemand streaming-positionerare och skapa en strömnings-URL. Avsnittet [encoding a Asset](media-services-encode-asset.md) visar hur du kodar i en MP4-uppsättning med anpassad bit hastighet. 

> [!NOTE]
> Om ditt innehåll är krypterat konfigurerar du till gångs leverans princip (enligt beskrivningen i [det här](media-services-dotnet-configure-asset-delivery-policy.md) avsnittet) innan du skapar en positionerare. 
> 
> 

Du kan också använda en OnDemand streaming-positionerare för att bygga URL: er som pekar på MP4-filer som kan laddas ned progressivt.  

Det här avsnittet visar hur du skapar en lokaliserare för OnDemand-direktuppspelning för att publicera till gången och bygga ett smidigt, MPEG-streck och HLS strömmande URL: er. Den visar också snabb hämtnings-URL: er. 

## <a name="create-an-ondemand-streaming-locator"></a>Skapa en lokaliserare för OnDemand-direktuppspelning
Om du vill skapa en OnDemand streaming-positionerare och hämta URL: er måste du göra följande:

1. Om innehållet är krypterat definierar du en åtkomst princip.
2. Skapa en lokaliserare för OnDemand-direktuppspelning.
3. Om du planerar att strömma hämtar du den strömmande manifest filen (. ISM) i till gången. 
   
   Om du planerar att ladda ned progressivt hämtar du namnen på MP4-filer i till gången.  
4. Bygg webb adresser till manifest filen eller MP4-filerna. 


>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomst behörigheter. Till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (principer som inte överförs). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.

### <a name="use-media-services-net-sdk"></a>Använd Media Services .NET SDK
Bygga direkt uppspelnings-URL: er 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

Utdata:

- URL till manifest för klient strömning med Smooth Streaming protokoll: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`
- URL till manifest för klient strömning med HLS-protokoll: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`
- URL som ska Manifestas för klient strömning med MPEG-streck-protokollet: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

> [!NOTE]
> Du kan också strömma ditt innehåll via en TLS-anslutning. För att utföra den här metoden kontrollerar du att dina strömmande URL: er börjar med HTTPS. För närvarande stöder AMS inte TLS med anpassade domäner.
> 
> 

Bygg URL: er för progressiv nedladdning 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
Utdata:

- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4`

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Använd Media Services .NET SDK-tillägg
I följande kod anropas metoder för .NET SDK-tillägg som skapar en lokaliserare och genererar URL: erna Smooth Streaming, HLS och MPEG-streck för anpassningsbar strömning.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
* [Hämta till gångar](media-services-deliver-asset-download.md)
* [Konfigurera till gångs leverans princip](media-services-dotnet-configure-asset-delivery-policy.md)

