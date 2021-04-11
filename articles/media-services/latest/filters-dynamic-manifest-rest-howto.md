---
title: Skapa filter med Azure Media Services v3 REST API
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma vissa delar av en ström. Media Services v3 REST API skapar dynamiska manifest för att uppnå denna selektiva strömning.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c2d081ded07b1d32ee7525855c1756e13dfd57aa
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277512"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Skapa filter med Media Services REST API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du levererar ditt innehåll till kunder (strömma live-händelser eller video på begäran) kan din klient behöva större flexibilitet än vad som beskrivs i standard till gångens manifest fil. Med Azure Media Services kan du definiera konto filter och till gångs filter för ditt innehåll. 

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [dynamiska manifest](filters-dynamic-manifest-concept.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du definierar ett filter för en video på begäran till gång och använder REST-API: er för att skapa [konto filter](/rest/api/media/accountfilters) och [filter för till gångar](/rest/api/media/assetfilters). 

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Förutsättningar 

För att slutföra stegen som beskrivs i det här avsnittet måste du:

- Granska [filter och dynamiska manifest](filters-dynamic-manifest-concept.md).
- [Konfigurera PostMan för Azure Media Services REST API-anrop](setup-postman-rest-how-to.md).

    Kontrol lera att du följer det sista steget i avsnittet [Hämta Azure AD-token](setup-postman-rest-how-to.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definiera ett filter  

Följande är exempel på **begär ande text** som definierar de villkor för spårnings val som läggs till i manifestet. Det här filtret innehåller alla ljud spår som är EC-3 och alla video spår som har bit hastighet i intervallet 0-1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Skapa konto filter

I Postman-samlingen som du laddade ned väljer du **konto filter** -> **skapa eller uppdatera ett konto filter**.

Metoden för att **Skicka** http-begäran liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Välj fliken **brödtext** och klistra in den JSON-kod som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Filtret har skapats.

Mer information finns i [skapa eller uppdatera](/rest/api/media/accountfilters/createorupdate). Se även [JSON-exempel för filter](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Skapa till gångs filter  

I Postman-samlingen "Media Services v3" som du laddade ned väljer du **till gångar** -> **skapa eller uppdatera till gångs filter**.

Metoden för att **Skicka** http-begäran liknar:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Välj fliken **brödtext** och klistra in den JSON-kod som du [definierade tidigare](#define-a-filter).

Välj **Skicka**. 

Till gångs filtret har skapats.

Mer information om hur du skapar eller uppdaterar till gångs filter finns i [skapa eller uppdatera](/rest/api/media/assetfilters/createorupdate). Se även [JSON-exempel för filter](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streaming Locator

Du kan ange en lista över till gångs-eller konto filter, som gäller för din strömmande positionerare. Den [dynamiska Paketeraren (slut punkt för direkt uppspelning)](encode-dynamic-packaging-concept.md) använder den här listan med filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-concept.md), som baseras på filter i de URL: er som du anger på en strömmande positionerare. Vi rekommenderar att du använder den här funktionen om du vill tillämpa filter men inte vill visa filter namnen i URL: en.

Om du vill skapa och associera filter med en strömmande positionerare med hjälp av REST använder du [strömmande positionerare-skapa](/rest/api/media/streaminglocators/create) API och anger `properties.filters` i [begär ande texten](/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Data ström med filter

När du har definierat filter kan klienterna använda dem i strömnings-URL: en. Filter kan tillämpas på strömnings protokoll med anpassningsbar bit hastighet: Apple HTTP Live Streaming (HLS), MPEG-streck och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Nästa steg

[Strömma videor](stream-files-tutorial-with-rest.md) 
