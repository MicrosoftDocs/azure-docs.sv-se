---
title: Skapa filter med Azure Media Services v3 .NET SDK
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma vissa delar av en ström. Media Services v3 .NET SDK skapar dynamiska manifest för att uppnå denna selektiva strömning.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 11c65498d5a31c2e2ee997bdaf18037b1f0f9060
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455220"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Skapa filter med Media Services .NET SDK

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du levererar ditt innehåll till kunder (strömma live-händelser eller video på begäran) kan din klient behöva större flexibilitet än vad som beskrivs i standard till gångens manifest fil. Med Azure Media Services kan du definiera konto filter och till gångs filter för ditt innehåll.

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du använder Media Services .NET SDK för att definiera ett filter för en video på begäran till gång och skapa [konto filter](/dotnet/api/microsoft.azure.management.media.models.accountfilter) och [till gångs filter](/dotnet/api/microsoft.azure.management.media.models.assetfilter). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Förutsättningar 

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-overview.md).
- [Skapa ett Media Services-konto](./create-account-howto.md). Kom ihåg att komma ihåg resurs gruppens namn och namnet på Media Services kontot. 
- Hämta information som krävs för att [få åtkomst till API: er](./access-api-howto.md)
- Granska [Ladda upp, koda och strömma med Azure Media Services](stream-files-tutorial-with-api.md) för att se hur du [börjar använda .NET SDK](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-net-sdk)

## <a name="define-a-filter"></a>Definiera ett filter  

I .NET konfigurerar du spåra val med [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection) -och [FilterTrackPropertyCondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition) -klasser. 

Följande kod definierar ett filter som innehåller ljud spår som är EG-3 och alla video spår som har bit hastighet i intervallet 0-1000000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Skapa konto filter

Följande kod visar hur du använder .NET för att skapa ett konto filter som innehåller alla spårnings val som [definierats ovan](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Skapa till gångs filter

Följande kod visar hur du använder .NET för att skapa ett resurs filter som innehåller alla spåra val som [definierats ovan](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streaming Locator

Du kan ange en lista över till gångs-eller konto filter, som gäller för din strömmande positionerare. Den [dynamiska Paketeraren (slut punkt för direkt uppspelning)](dynamic-packaging-overview.md) använder den här listan med filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i de URL: er som du anger på en strömmande positionerare. Vi rekommenderar att du använder den här funktionen om du vill tillämpa filter men inte vill visa filter namnen i URL: en.

Följande C#-kod visar hur du skapar en strömmande lokaliserare och anger `StreamingLocator.Filters` . Detta är en valfri egenskap som tar `IList<string>` med filter namn.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Data ström med filter

När du har definierat filter kan klienterna använda dem i strömnings-URL: en. Filter kan tillämpas på strömnings protokoll med anpassningsbar bit hastighet: Apple HTTP Live Streaming (HLS), MPEG-streck och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nästa steg

[Strömma videor](stream-files-tutorial-with-api.md) 
