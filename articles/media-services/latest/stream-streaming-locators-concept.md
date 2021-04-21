---
title: Positionerare för direktuppspelning i Azure Media Services
description: Den här artikeln ger en förklaring av vad positionerare för direktuppspelning är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: bcee8d0554b9c3349c7efc88c10e9eee8b185acb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773287"
---
# <a name="streaming-locators"></a>Positionerare för direktuppspelning

För att skapa videor i utdatatillgångar som klienter ska kunna spela upp måste du skapa en [positionerare för direktuppspelning](/rest/api/media/streaminglocators) och sedan skapa direktuppspelnings-URL:er. För att skapa en webbadress måste du sammanfoga strömningsslutpunktens värdnamn och sökvägen för positioneraren för direktuppspelning. .NET-exempel finns i [Hämta en positionerare för direktuppspelning](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar **en positionerare för direktuppspelning** måste du ange ett **resursnamn** och ett namn på **direktuppspelningsprincipen.** Mer information finns i följande avsnitt:

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](stream-streaming-policy-concept.md)
* [Principer för innehållsnycklar](drm-content-key-policy-concept.md)

Du kan också ange start- och sluttid för din positionerare för direktuppspelning, vilket endast låter användaren spela upp innehållet mellan dessa tider (till exempel mellan 2019-05-01 till 2019-05-05).  

## <a name="considerations"></a>Överväganden

* **Positionerare för direktuppspelning** kan inte uppdateras. 
* Egenskaperna för **positionerare för direktuppspelning** som är av Datetime-typen är alltid i UTC-format.
* Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina positionerare för direktuppspelning när samma alternativ behövs. Mer information finns i [Kvoter och gränser.](limits-quotas-constraints-reference.md)

## <a name="create-streaming-locators"></a>Skapa positionerare för direktuppspelning  

### <a name="not-encrypted"></a>Inte krypterad

Om du vill strömma filen i klar ordning (icke-krypterad) anger du den fördefinierade principen för rensad direktuppspelning: till "Predefined_ClearStreamingOnly" (i .NET kan du använda uppräkning PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Krypterad 

Om du behöver kryptera ditt innehåll med CENC-kryptering anger du principen till "Predefined_MultiDrmCencStreaming". Widevine-krypteringen tillämpas på en DASH-dataström och PlayReady till Smooth. Nyckeln levereras till en uppspelningsklient baserat på de konfigurerade DRM-licenserna.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Om du även vill kryptera HLS-strömmen med CBCS (FairPlay) använder du "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användningsvillkoren och sekretesspolicyn för Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associera filter med positionerare för direktuppspelning

Se [Filter: associera med positionerare för direktuppspelning.](filters-concept.md#associating-filters-with-streaming-locator)

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrera, ordna, sidlokaliserarentiteter för direktuppspelning

Se [Filtrering, ordning, växling av Media Services entiteter](filter-order-page-entitites-how-to.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lista positionerare för direktuppspelning efter tillgångsnamn

Om du vill hämta positionerare för direktuppspelning baserat på det associerade tillgångsnamnet använder du följande åtgärder:

|Språk|API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](/cli/azure/ams/asset#az_ams_asset_list_streaming_locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Se även

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](stream-streaming-policy-concept.md)
* [Principer för innehållsnycklar](drm-content-key-policy-concept.md)
* [Självstudie: Ladda upp, koda och strömma videor med .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Nästa steg

[Så här skapar du en positionerare för direktuppspelning och skapar URL:er](create-streaming-locator-build-url.md)
