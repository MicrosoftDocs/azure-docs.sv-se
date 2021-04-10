---
title: Media Services v3-exempel
description: Den här artikeln innehåller en lista över alla exempel som är tillgängliga för Media Services v3 som är ordnat efter metod och SDK.  Exempel på detta är .NET, Node.JS, python och Java samt REST med Postman.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 401033e210db0a9d65d138fd12c9d3365b311a62
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962160"
---
# <a name="media-services-v3-samples"></a>Media Services v3-exempel

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln innehåller en lista över alla exempel som är tillgängliga för Media Services ordnas efter metod och SDK.  Exempel på detta är .NET, Node.JS, python och Java samt REST med Postman.

## <a name="rest-postman-collection"></a>REST Postman-samling

[Resten Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) -exempel innehåller en Postman-miljö och samling som du kan importera till Postman-klienten.

## <a name="samples-by-sdk"></a>Exempel efter SDK

Du hittar en beskrivning och länkar till de exempel som du kan söka efter på var och en av flikarna.

## <a name="net"></a>[.NET](#tab/net/)

| Mapp | Beskrivning |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Skicka ett jobb med hjälp av en inbyggd för inställning och en HTTP URL-indata, publicera utmatnings till gång för strömning och hämta resultat för verifiering.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Skicka ett jobb med hjälp av en anpassad kodning för H. 264 encoding och en HTTP URL-indata, publicera utmatnings till gång för strömning och hämta resultat för verifiering.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Skicka ett jobb med en anpassad HEVC kodnings för inställning och en HTTP URL-indata, publicera utmatnings till gång för strömning och hämta resultat för verifiering.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Skicka ett jobb med hjälp av JobInputSequence för att häfta ihop 2 eller flera till gångar som kan klippas av start-eller slut tid. Den resulterande kodade filen är en enskild video med alla till gångar som sammanställs tillsammans.  Exemplet kommer också att publicera utdata till gång för strömning och hämta resultat för verifiering.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Skicka ett jobb med hjälp av en anpassad för inställning med en miniatyr bild Sprite och en HTTP URL-indata, publicera utmatnings till gång för strömning och hämta resultat för verifiering.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Så här skapar du en LiveEvent med ett fullständigt Arkiv i upp till 25 timmar och ett filter på till gången med 5 minuters DVR-fönster. Så här använder du ett filter för att skapa en positionerare för direkt uppspelning.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Hur du skapar en video analys omvandling, laddar upp en videofil till en indata-till gång, skickar ett jobb med transformeringen och hämtar resultatet för verifiering.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Hur du skapar en Audio Analyzer-omvandling, laddar upp en mediefil till en indata-till gång, skickar ett jobb med transformeringen och hämtar resultatet för verifiering.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Skapa en transformering med inbyggd AdaptiveStreaming för inställning, skicka ett jobb, skapa en ContentKeyPolicy med en hemlig nyckel, associera ContentKeyPolicy med StreamingLocator, hämta en token och skriv ut en URL för uppspelning i Azure Media Player. När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll med AES-128 och Azure Media Player använder token för att dekryptera.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Skapa en transformering med inbyggd AdaptiveStreaming för inställning, skicka ett jobb, skapa en ContentKeyPolicy med Widevine-konfiguration med hjälp av en hemlig nyckel, associera ContentKeyPolicy med StreamingLocator, hämta en token och skriv ut en URL för uppspelning i en Widevine-spelare. När en användare begär Widevine innehåll begär Player-programmet en licens från Media Services licens tjänsten. Om spelarappen är auktoriserad utfärdar Media Services-licensen en licens till spelaren. En Widevine-licens innehåller den krypterings nyckel som kan användas av klient spelaren för att dekryptera och strömma innehållet.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Skapa en transformering med inbyggd AdaptiveStreaming för inställning, skicka ett jobb, skapa en ContentKeyPolicy med PlayReady-konfiguration med hjälp av en hemlig nyckel, associera ContentKeyPolicy med StreamingLocator, hämta en token och skriv ut en URL för uppspelning i en Azure Media Player. När en användare begär PlayReady-skyddat innehåll begär Player-programmet en licens från Media Services licens tjänsten. Om spelarappen är auktoriserad utfärdar Media Services-licensen en licens till spelaren. En PlayReady-licens innehåller den krypterings nyckel som kan användas av klient spelaren för att dekryptera och strömma innehållet.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Hur du krypterar innehållet dynamiskt med PlayReady och Widevine DRM och spelar upp innehållet utan att begära en licens från licens tjänsten. Det visar hur du skapar en transformering med inbyggd AdaptiveStreaming för inställning, skickar ett jobb, skapar en ContentKeyPolicy med öppen begränsning och PlayReady/Widevine-beständig konfiguration, associerar ContentKeyPolicy med en StreamingLocator och skriver ut en URL för uppspelning.|
| [Strömning/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Så här skapar du en transformering med inbyggd AdaptiveStreaming för inställning, skickar ett jobb, skapar ett till gångs filter och ett konto filter, associerar filtren till strömmande positionerare och skriv ut URL: er för uppspelning.|
| [Strömning/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Så här skapar du en transformering med inbyggd AdaptiveStreaming för inställning, skickar ett jobb, publicerar utmatnings till gång för HLS och streck strömning.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Vägledning och bästa praxis för ett produktions system med kodning eller analys på begäran. Läsarna bör börja med den medföljande artikeln [med hög tillgänglighet med Media Services-och VOD](https://docs.microsoft.com/azure/media-services/latest/architecture-high-availability-encoding-concept). Det finns en separat lösnings fil som tillhandahålls för [HighAvailabilityEncodingStreaming](/HighAvailabilityEncodingStreaming/Readme.md) -exemplet. |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Mapp|Beskrivning|
|---|---|
|[HelloWorld – ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Så här ansluter du och listar till gångar |
|[Direktsändning](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Hur du gör för att utföra grundläggande direkt uppspelning. **Varning**, kontrol lera att alla resurser har rensats och inte längre faktureras i portalen när du använder Live.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Hur du laddar upp en lokal fil eller kodning från en käll-URL, hur du använder Storage SDK för att ladda ned innehåll och hur du strömmar till en spelare. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Koda och strömma med Widevine och PlayReady DRM |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Använda video-och ljud analys för hands inställningarna för att generera metadata och insikter från en video-eller ljudfil. |

## <a name="python"></a>[Python](#tab/python)

För närvarande finns det ett python-exempel, [Basic encoding med python](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Mapp|Beskrivning|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Analysera ljud i en mediefil. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Så här krypterar du innehållet dynamiskt med AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Så här krypterar du innehållet dynamiskt med PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Så här krypterar du innehållet dynamiskt med Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Så här krypterar du innehållet dynamiskt med FairPlay DRM och spelar upp innehållet utan att begära en licens från licens tjänsten.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Hur du krypterar innehållet dynamiskt med PlayReady och Widevine DRM och spelar upp innehållet utan att begära en licens från licens tjänsten.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Filtrera innehåll med hjälp av till gångs-och konto filter.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Hur du paketerar VOD-innehåll dynamiskt i HLS/tank streck för strömning.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Så här skapar du en Live-händelse med ett fullständigt Arkiv i upp till 25 timmar och ett filter på till gången med 5 minuters DVR-fönster och hur du skapar en positionerare för direkt uppspelning och använder filtret.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Så här skapar du en Live-händelse med ett fullständigt Arkiv i upp till 25 timmar och ett filter på till gången med 5 minuters DVR-fönster och hur du skapar en positionerare för direkt uppspelning och använder filtret.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Så här skapar du en anpassad kodnings omvandling med StandardEncoderPreset-inställningarna.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Skicka ett jobb med hjälp av en inbyggd för inställning och en HTTP URL-indata, publicera utmatnings till gång för strömning och hämta resultat för verifiering.|

---
