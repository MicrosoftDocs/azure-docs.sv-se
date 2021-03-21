---
title: Använd CLI för att skapa filter med Azure Media Services
description: Den här artikeln visar hur du använder CLI för att skapa filter med Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 54e99c645b93bfcb3aee4e81cef3b2b5d6589d95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211472"
---
# <a name="creating-filters-with-cli"></a>Skapa filter med CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du levererar ditt innehåll till kunder (strömma live-händelser eller video på begäran) kan din klient behöva större flexibilitet än vad som beskrivs i standard till gångens manifest fil. Med Azure Media Services kan du definiera konto filter och till gångs filter för ditt innehåll.

Detaljerad beskrivning av den här funktionen och scenarier där den används finns i [dynamiska manifest](filters-dynamic-manifest-overview.md) och [filter](filters-concept.md).

Det här avsnittet visar hur du konfigurerar ett filter för en video på begäran-till gång och använder CLI för Media Services v3 för att skapa [konto filter](/cli/azure/ams/account-filter) och [filter för till gångar](/cli/azure/ams/asset-filter).

> [!NOTE]
> Se till att granska [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](./create-account-howto.md). Kom ihåg att komma ihåg resurs gruppens namn och namnet på Media Services kontot.

## <a name="define-a-filter"></a>Definiera ett filter

I följande exempel definieras villkoren för spåra val som läggs till i det slutliga manifestet. Det här filtret innehåller alla ljud spår som är EC-3 och alla video spår som har bit hastighet i intervallet 0-1000000.

> [!TIP]
> Observera att du måste ta med JSON-objektet "egenskaper" om du planerar att definiera **filter** i vila.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Skapa konto filter

Följande [AZ AMS Account-filter-](/cli/azure/ams/account-filter) kommando skapar ett konto filter med val av filter spårning som [definierades tidigare](#define-a-filter).

Med kommandot kan du skicka en valfri `--tracks` parameter som innehåller JSON som representerar spår valen.  Använd @ {File} för att läsa in JSON från en fil. Om du använder Azure CLI lokalt anger du hela fil Sök vägen:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Se även [JSON-exempel för filter](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Skapa till gångs filter

Följande [AZ AMS Asset Asset filter-](/cli/azure/ams/asset-filter) kommando skapar ett till gångs filter med val av filter spårning som [definierades tidigare](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Se även [JSON-exempel för filter](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Associera filter med streaming Locator

Du kan ange en lista över till gångs-eller konto filter, som gäller för din strömmande positionerare. Den [dynamiska Paketeraren (slut punkt för direkt uppspelning)](dynamic-packaging-overview.md) använder den här listan med filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-overview.md), som baseras på filter i de URL: er som du anger på en strömmande positionerare. Vi rekommenderar att du använder den här funktionen om du vill tillämpa filter men inte vill visa filter namnen i URL: en.

Följande CLI-kod visar hur du skapar en strömmande lokaliserare och anger `filters` . Det här är en valfri egenskap som tar en blankstegsavgränsad lista över till gångs filter namn och/eller konto filter namn.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Data ström med filter

När du har definierat filter kan klienterna använda dem i strömnings-URL: en. Filter kan tillämpas på strömnings protokoll med anpassningsbar bit hastighet: Apple HTTP Live Streaming (HLS), MPEG-streck och Smooth Streaming.

I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Nästa steg

[Strömma videor](stream-files-tutorial-with-api.md)

## <a name="see-also"></a>Se även

[Azure CLI](/cli/azure/ams)
