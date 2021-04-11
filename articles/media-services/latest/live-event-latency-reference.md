---
title: LiveEvent inställningar för låg latens i Azure Media Services
description: Det här avsnittet ger en översikt över inställningar för LiveEvent med låg latens och visar hur du ställer in låg latens.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2b612201a5ce1c661c8d6549f2882f86cfaf0e32
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123509"
---
# <a name="live-event-low-latency-settings"></a>Inställningar för låg latens för live event

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du ställer in låg latens för en [Live-händelse](/rest/api/media/liveevents). Den diskuterar också vanliga resultat som visas när du använder inställningarna för låg latens i olika spelare. Resultaten varierar beroende på CDN och nätverks svars tid.

Om du vill använda den nya **LowLatency** -funktionen ställer du in **StreamOptionsFlag** på **LowLatency** på **LiveEvent**. När du skapar [LiveOutput](/rest/api/media/liveoutputs) för HLS-uppspelning ställer du in [LiveOutput. HLS. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) på 1. När strömmen är igång kan du använda [Azure Media Player](https://ampdemo.azureedge.net/) (amp demo-sidan) och ange uppspelnings alternativen för att använda heuristiks profilen för "låg latens".

> [!NOTE]
> För närvarande är LowLatency-HeuristicProfile i Azure Media Player utformad för att spela upp strömmar i MPEG-streck-protokollet, med antingen CSF eller CMAF format (till exempel `format=mdp-time-csf` eller `format=mdp-time-cmaf` ). 

Följande .NET-exempel visar hur du ställer in **LowLatency** på **LiveEvent**:

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#NewLiveEvent)]

Se det fullständiga exemplet: [Live event med DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs).

## <a name="live-events-latency"></a>Svars tid för Live-händelser

Följande tabeller visar typiska resultat för latens (när LowLatency-flaggan är aktive rad) i Media Services, mätt från den tidpunkt då bidrags flödet når tjänsten till när ett visnings program ser uppspelningen av spelaren. Om du vill använda låg latens optimalt bör du justera inställningarna för kodare nedåt till 1 andra "grupp med bilder" (GOP). När du använder en högre GOP-längd minimerar du bandbredds förbrukningen och minskar bit hastigheten under samma bild hastighet. Det är särskilt fördelaktigt i videor med mindre rörelse.

### <a name="pass-through"></a>Direkt 

||2s GOP låg latens har Aktiver ATS|1 GOP låg latens har Aktiver ATS|
|---|---|---|
|**STRECK i AMP**|tiotal|8s|
|**HLS på ursprunglig iOS-spelare**|14s|tiotal|

### <a name="live-encoding"></a>Live Encoding

||2s GOP låg latens har Aktiver ATS|1 GOP låg latens har Aktiver ATS|
|---|---|---|
|**STRECK i AMP**|14s|tiotal|
|**HLS på ursprunglig iOS-spelare**|18s|13s|

> [!NOTE]
> Svars tiden från slut punkt till slut punkt kan variera beroende på lokala nätverks förhållanden eller genom att introducera ett lager för CDN-cachelagring. Du bör testa dina exakta konfigurationer.

## <a name="next-steps"></a>Nästa steg

- [Översikt över direkt uppspelning](stream-live-streaming-concept.md)
- [Själv studie kurs om Live-direktuppspelning](stream-live-tutorial-with-api.md)
