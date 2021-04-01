---
title: Jämförelse av Video Indexer och Azure Media Services v3-förinställningar
description: I den här artikeln jämförs Video Indexer-funktioner och Azure Media Services v3-för inställningar.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 0961aa3e573e511a6941bc59ddc335f64799abb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047338"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Jämför Azure Media Services v3 för inställningar och Video Indexer 

I den här artikeln jämförs funktionerna i **video Indexer API** : er och **Media Services v3-API: er**. 

För närvarande finns det en överlappning mellan funktioner som erbjuds av [video Indexer-API](https://api-portal.videoindexer.ai/) : er och [Media Services v3-API: er](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Följande tabell innehåller den aktuella rikt linjen för att förstå skillnaderna och likheter. 

## <a name="compare"></a>Jämför

|Funktion|Video Indexer-API: er |Inställningar för video analys och ljud analys<br/>i Media Services v3-API: er|
|---|---|---|
|Medie insikter|[Optimerad](video-indexer-output-json-v2.md) |[Grunder](../latest/analyzing-video-audio-files-concept.md)|
|Erfarenheter|Se den fullständiga listan över funktioner som stöds: <br/> [Översikt](video-indexer-overview.md)|Returnerar endast video insikter|
|Fakturering|[Media Services priser](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services priser](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Efterlevnad|För de mest aktuella uppdateringarna går du till [Azure compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) och söker efter "video Indexer" för att se om det överensstämmer med ett intresse bevis.|För de mest aktuella uppdateringarna går du till [Azure compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) och söker efter "Media Services" för att se om det överensstämmer med ett intresse bevis.|
|Kostnadsfri utvärdering|East US|Inte tillgängligt|
|Regional tillgänglighet|Se [Cognitive Services tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Se [Media Services tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)

[Översikt över Media Services v3](../latest/media-services-overview.md)
