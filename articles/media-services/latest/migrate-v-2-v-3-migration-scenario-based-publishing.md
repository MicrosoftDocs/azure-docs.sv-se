---
title: Vägledning för att migrera paketering och leverans
description: Den här artikeln ger dig en scenario baserad vägledning för paketering och leverans som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: f28a95ce6ef5e87eed6e5efcd013cc40b102fcba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721097"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Vägledning om paketering och leverans scenario-baserad migrering

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Den här artikeln innehåller en scenario-baserad vägledning för paketering och leverans som hjälper dig att migrera från Azure Media Services v2 till v3.

Större ändringar av hur innehållet publiceras i v3-API. Den nya publicerings modellen är förenklad och använder färre entiteter för att skapa en strömmande positionerare. API: et sänkt till bara två entiteter jämfört med de fyra entiteter som tidigare krävdes. Nyckel principer för innehåll och strömmande positionerare ersätter nu behovet av `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` och `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Paketering och leverans i v3

1. Skapa [nyckel principer för innehåll](content-key-policy-concept.md).
1. Skapa [strömmande positionerare](streaming-locators-concept.md).
1. Hämta de [strömmande Sök vägarna](create-streaming-locator-build-url.md) 
    1. Konfigurera det för en [tank](dynamic-packaging-overview.md#mpeg-dash-protocol) eller [HLS](dynamic-packaging-overview.md#hls-protocol) spelare.

Mer information finns i publicera koncept, självstudier och rikt linjer nedan för olika steg.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publicera koncept, självstudier och guider

### <a name="concepts"></a>Begrepp

- [Dynamisk paketering i Media Services v3](dynamic-packaging-overview.md)
- [Filter](filters-concept.md)
- [Filtrera dina manifest med hjälp av dynamisk Paketeraren](filters-dynamic-manifest-overview.md)
- [Slut punkter för direkt uppspelning (ursprung) i Azure Media Services](streaming-endpoint-concept.md)
- [Strömma innehåll med CDN-integrering](scale-streaming-cdn.md)
- [Strömmande positionerare](streaming-locators-concept.md)

### <a name="how-to-guides"></a>Guider

- [Hantera slut punkter för direkt uppspelning med Media Services v3](manage-streaming-endpoints-howto.md)
- [CLI-exempel: Publicera en tillgång](cli-publish-asset.md)
- [Skapa en positionerare för direktuppspelning och skapa URL:er](create-streaming-locator-build-url.md)
- [Hämta resultatet av ett jobb](download-results-howto.md)
- [Signals beskrivande ljud spår](signal-descriptive-audio-howto.md)
- [Fullständig installation av Azure Media Player](../azure-media-player/azure-media-player-full-setup.md)
- [Använda Video.js Player med Azure Media Services](how-to-video-js-player.md)
- [Så här använder du Shaka Player med Azure Media Services](how-to-shaka-player.md)

## <a name="samples"></a>Exempel

Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]