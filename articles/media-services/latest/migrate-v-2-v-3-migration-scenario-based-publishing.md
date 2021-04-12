---
title: Vägledning för att migrera paketering och leverans
description: Den här artikeln ger dig en scenario baserad vägledning för paketering och leverans som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0a0052fa3d78a3b77094cfccbd4c011321ac5925
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279025"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Vägledning om paketering och leverans scenario-baserad migrering

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Den här artikeln innehåller en scenario-baserad vägledning för paketering och leverans som hjälper dig att migrera från Azure Media Services v2 till v3.

Större ändringar av hur innehållet publiceras i v3-API. Den nya publicerings modellen är förenklad och använder färre entiteter för att skapa en strömmande positionerare. API: et sänkt till bara två entiteter jämfört med de fyra entiteter som tidigare krävdes. Nyckel principer för innehåll och strömmande positionerare ersätter nu behovet av `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` och `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Paketering och leverans i v3

1. Skapa [nyckel principer för innehåll](drm-content-key-policy-concept.md).
1. Skapa [strömmande positionerare](stream-streaming-locators-concept.md).
1. Hämta de [strömmande Sök vägarna](create-streaming-locator-build-url.md) 
    1. Konfigurera det för en [tank](encode-dynamic-packaging-concept.md#mpeg-dash-protocol) eller [HLS](encode-dynamic-packaging-concept.md#hls-protocol) spelare.

Mer information finns i publicera koncept, självstudier och rikt linjer nedan för olika steg.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publicera koncept, självstudier och guider

### <a name="concepts"></a>Begrepp

- [Dynamisk paketering i Media Services v3](encode-dynamic-packaging-concept.md)
- [Filter](filters-concept.md)
- [Filtrera dina manifest med hjälp av dynamisk Paketeraren](filters-dynamic-manifest-concept.md)
- [Slut punkter för direkt uppspelning (ursprung) i Azure Media Services](stream-streaming-endpoint-concept.md)
- [Strömma innehåll med CDN-integrering](stream-scale-streaming-cdn-concept.md)
- [Strömmande positionerare](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Guider

- [Hantera slut punkter för direkt uppspelning med Media Services v3](stream-manage-streaming-endpoints-how-to.md)
- [CLI-exempel: Publicera en tillgång](cli-publish-asset.md)
- [Skapa en positionerare för direktuppspelning och skapa URL:er](create-streaming-locator-build-url.md)
- [Hämta resultatet av ett jobb](job-download-results-how-to.md)
- [Signals beskrivande ljud spår](signal-descriptive-audio-howto.md)
- [Fullständig installation av Azure Media Player](../azure-media-player/azure-media-player-full-setup.md)
- [Använda Video.js Player med Azure Media Services](player-how-to-video-js-player.md)
- [Så här använder du Shaka Player med Azure Media Services](player-shaka-player-how-to.md)

## <a name="samples"></a>Exempel

Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).
