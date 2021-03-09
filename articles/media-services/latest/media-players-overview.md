---
title: Medie spelare för Media Services översikt
description: Vilka medie spelare kan jag använda med Azure Media Services? Azure Media Player, Shaka och Video.js hittills.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 1a1d415b374818d9a51c87e78e7ac422fa374bc5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509602"
---
# <a name="media-players-for-media-services"></a>Medie spelare för Media Services

Du kan använda flera medie spelare med Media Services.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player är en Videos pelare för många olika webbläsare och enheter. Azure Media Player använder bransch standarder, till exempel HTML5, media source Extensions (MSE) och EME (Encrypted Media Extensions) för att tillhandahålla en omfattande anpassningsbar strömmande upplevelse. När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelnings teknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er. Innehåll som hanteras av Azure Media Services kan spelas upp över en mängd olika enheter och webbläsare utan extra ansträngning.

Se [Azure Media Player-dokumentationen](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-overview).

## <a name="shaka"></a>Shaka

Shaka Player är ett JavaScript-bibliotek med öppen källkod för anpassningsbara medier. Den spelar in anpassade medie format (till exempel bindestreck och HLS) i en webbläsare, utan att använda plugin-program eller Flash. I stället använder Shaka Player de öppna webb standard tilläggen och krypterade medie tillägg.

Se [hur du använder Shaka Player med Azure Media Services](how-to-shaka-player.md).

## <a name="videojs"></a>Video.js

Video.js är en Videos pelare som spelar in anpassade medie format (till exempel bindestreck och HLS) i en webbläsare. Video.js använder tilläggen öppna webb standarder MediaSource och krypterade media. Det stöder videouppspelning på Station ära datorer och mobila enheter. Den officiella dokumentationen finns på https://docs.videojs.com/ .

Se [hur du använder Video.js Player med Azure Media Services](how-to-video-js-player.md).


## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](../azure-media-player/azure-media-player-quickstart.md)
