---
title: Microsoft Azure Media Services vanliga scenarier | Microsoft Docs
description: Den här artikeln ger en översikt över Microsoft Azure Media Services scenarier.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: cb7500489cc4516b8cf44dd029c0831103dc53a8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007944"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure Media Services vanliga scenarier

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. Kolla in den senaste versionen [Media Services v3](../latest/media-services-overview.md). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS) gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

Den här artikeln visar vanliga scenarier för att leverera innehåll Live eller på begäran.

## <a name="overview"></a>Översikt

### <a name="prerequisites"></a>Krav

* Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com).
* Ett Azure Media Services-konto. Mer information finns i [Skapa konto](media-services-portal-create-account.md).
* Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**.

    När ditt AMS-konto skapas läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning ha tillståndet **Körs**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Vanliga objekt när du utvecklar mot AMS OData-modellen

Följande bild visar några av de vanligast använda objekten när du utvecklar mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

[![Diagram som visar några av de vanligaste objekten när du utvecklar mot Azure Media Services objekts data modell.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

Du kan visa hela modellen [här](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Skydda lagrat innehåll och leverera strömmande media i klartext (okrypterat)

![VoD-arbetsflöde](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Överför en mediefil av hög kvalitet till en tillgång.

    Att använda alternativet för lagrings kryptering på din till gång för att skydda ditt innehåll under överföringen och i vila i lagring rekommenderas.

1. Koda till en uppsättning MP4-filer med anpassningsbar bithastighet.

    Vi rekommenderar att du använder alternativet för lagrings kryptering i utmatnings till gången för att skydda ditt innehåll i vila.

1. Konfigurera principen för tillgångsleverans (används av dynamisk paketering).

    Om tillgången är lagringskrypterad **måste** du konfigurera principen för tillgångsleverans.
1. Publicera tillgången genom att skapa en OnDemand-positionerare.
1. Strömma publicerat innehåll.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Skydda lagrat innehåll, leverera dynamiskt krypterade strömmande media

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Överför en mediefil av hög kvalitet till en tillgång. Använd krypterat lagringsalternativ för tillgången.
1. Koda till en uppsättning MP4-filer med anpassningsbar bithastighet. Använd krypterat lagringsalternativ för utdatatillgången.
1. Skapa en innehållskrypteringsnyckel för den tillgång du vill kryptera dynamiskt under uppspelning.
1. Konfigurera principen för auktorisering av innehållsnyckel.
1. Konfigurera en princip för tillgångsleveranser (används av dynamisk paketering och dynamisk kryptering).
1. Publicera tillgången genom att skapa en OnDemand-positionerare.
1. Strömma publicerat innehåll.

## <a name="deliver-progressive-download"></a>Leverera progressiv nedladdning

1. Överför en mediefil av hög kvalitet till en tillgång.
1. Koda till en enda MP4-fil.
1. Publicera tillgången genom att skapa en positionerare på begäran eller en SAS-positionerare. Om du använder en SAS-positionerare hämtas innehåll från Azure Blob Storage. Du behöver inte ha slut punkter för direkt uppspelning i Start läge.
1. Progressivt hämtat innehåll

## <a name="delivering-live-streaming-events"></a>Leverera liveuppspelningshändelser

1. Infoga innehåll via olika protokoll för liveuppspelning (till exempel RTMP eller Smooth Streaming).
1. (Valfritt) Koda strömmen till ström med anpassningsbar bithastighet.
1. Förhandsgranska din liveuppspelning.
1. Leverera innehållet via:
    1. Vanliga strömnings protokoll (till exempel MPEG-streck, smidiga, HLS) direkt till dina kunder
    1. Till en Content Delivery Network (CDN) för vidare distribution eller
    1. Spela in och lagra det inmatade innehållet för att strömmas senare (video-on-demand).

Vid liveuppspelning kan du välja någon av följande vägar:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare (genomströmning)

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet **Genomströmning**.

![Diagram som visar de viktigaste delarna av en M S-plattform som ingår i "genom strömning"-arbets flödet.](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Mer information finns i [Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services

Följande diagram visar de viktigaste delarna av AMS-plattformen som ingår i Live streaming-arbetsflödet där en kanal är aktive rad för att göra Live-kodning med Media Services.

![Live-arbetsflöde](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Konsumera innehåll

Azure Media Services innehåller de verktyg du behöver för att skapa innehållsrika och dynamiska klientprogram för uppspelare för de flesta plattformar, bland annat: iOS-enheter, Android-enheter, Windows, Windows Phone, Xbox och digitalboxar.

## <a name="enabling-azure-cdn"></a>Aktivera Azure CDN

Media Services stöder integration med Azure CDN. Information om hur du aktiverar Azure CDN finns i [Hur du hanterar strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Skala ett Media Services-konto

AMS-kunder kan skala slutpunkter för direktuppspelning, mediebearbetning och lagring i sina AMS-konton.

* Media Services-kunder kan antingen välja en **Standard**-slutpunkt för direktuppspelning eller en **Premium**-slutpunkt för direktuppspelning. En **Standard**-slutpunkt för direktuppspelning passar de flesta arbetsbelastningar för direktuppspelning. Innehåller samma funktioner som **Premium**-slutpunkter för direktuppspelning och skalar utgående bandbredd automatiskt.

    **Premium**-slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Kunder som har en **Premium**-slutpunkt för direktuppspelning får som standard en direktuppspelande enhet (SU). Du kan skala slutpunkten för direktuppspelning genom att lägga till direktuppspelande enheter. Varje direktuppspelande enhet ger ytterligare bandbreddskapacitet till programmet. Mer information om hur du skalar **Premium**-slutpunkter för direktuppspelning finns i avsnittet [Scaling streaming endpoints](media-services-portal-scale-streaming-endpoints.md) (Skala slutpunkter för direktuppspelning).

* Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**.

    Förutom att ange typ av reserverad enhet kan du ange att ditt konto ska etableras med **reserverade enheter** (ru: er). Antalet etablerade RU:er anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto.

    > [!NOTE]
    > RU:er fungerar för parallellisera all bearbetning av media, inklusive indexeringsjobb med hjälp av Azure Media Indexer. Men till skillnad från kodning bearbetas inte indexeringsjobb snabbare med snabbare reserverade enheter.

    Mer information finns i [skala medie bearbetning](media-services-portal-scale-media-processing.md).

* Du kan även skala ditt Media Services-konto genom att lägga till lagringskonton. Varje lagringskonto är begränsat till 500 TB. Mer information finns i [Hantera lagringskonton](./media-services-managing-multiple-storage-accounts.md).

## <a name="next-steps"></a>Nästa steg

[Migrera till Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]