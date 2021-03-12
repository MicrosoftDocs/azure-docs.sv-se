---
title: Översikt över Azure on-demand Media Encoder | Microsoft Docs
description: Azure Media Services tillhandahåller flera alternativ för kodning av media i molnet. Den här artikeln ger en översikt över Azure on-demand Media Encoder.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0fcf3aaa830c092dfa225f55fef882e2263b16cd
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013558"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Översikt över Azure on-demand Media Encoder

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services tillhandahåller flera alternativ för kodning av media i molnet.

När du börjar med Media Services är det viktigt att förstå skillnaden mellan codec-och fil format.
Codecenheter är program varan som implementerar algoritmerna för komprimering/expandering, medan fil format är behållare som innehåller den komprimerade videon.

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera din anpassningsbara bit hastighets-MP4 eller Smooth Streaming kodat innehåll i strömnings format som stöds av Media Services (MPEG-streck, HLS Smooth Streaming) utan att du behöver paketera om till dessa strömnings format.

När ditt Media Services-konto skapas, läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. Faktureringen av slut punkter för direkt uppspelning sker när slut punkten är i ett **körnings** tillstånd.

Media Services stöder följande kodare på begäran:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Den här artikeln ger en kort översikt över media-kodare på begäran och länkar till artiklar med mer detaljerad information.

Som standard kan varje Media Services konto ha en aktiv kodnings aktivitet i taget. Du kan reservera kodnings enheter som gör att du kan köra flera kodnings aktiviteter samtidigt, en för varje encoding-reserverad enhet som du köper. Mer information finns i [skala kodnings enheter](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Använd så här
[Så här kodar du med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Format
[Format och codecenheter](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Hands
Media Encoder Standard konfigureras med hjälp av en av kodarens för inställningar som beskrivs [här](./media-services-mes-presets-overview.md).

### <a name="input-and-output-metadata"></a>Metadata för indata och utdata
Kodarens indata-metadata beskrivs [här](media-services-input-metadata-schema.md).

Kodarens utdata metadata beskrivs [här](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Skapa miniatyrbilder
Mer information finns i [så här skapar du miniatyr bilder med hjälp av Media Encoder Standard](media-services-advanced-encoding-with-mes.md).

### <a name="trim-videos-clipping"></a>Trimma videor (Urklipp)
Mer information finns i [så här trimmar du videor med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Skapa överlägg
Mer information finns i [så här skapar du överlägg med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Se även
[Media Services blogg](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>Kända problem
Om din indata-video inte innehåller dold textning innehåller utmatnings till gången fortfarande en tom TTML-fil.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodnings uppgifter genom att anpassa Media Encoder Standard för inställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/