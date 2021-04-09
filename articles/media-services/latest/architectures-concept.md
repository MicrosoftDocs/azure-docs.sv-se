---
title: Media Services-arkitekturer
description: I den här artikeln beskrivs arkitekturer för Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: ad464eb1c0b6dec694c7c40868a0f95fcfeaf6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98891496"
---
# <a name="media-services-architectures"></a>Media Services-arkitekturer

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Liveuppspelning av digitala media

Med en live streaming-lösning kan du samla in video i real tid och skicka den till konsumenter i real tid, till exempel strömmande intervjuer, konferenser och idrotts evenemang online. I den här lösningen samlas videon in av en video kamera och skickas till en slut punkt för kanal ingång. Kanalen tar emot Live-indataströmmen och gör den tillgänglig för strömning via en slut punkt för direkt uppspelning till en webbläsare eller mobilapp. Kanalen tillhandahåller också en slut punkt för för hands versions övervakning för att förhandsgranska och validera data strömmen innan vidare bearbetning och leverans. Kanalen kan även registrera och lagra det inmatade innehållet för att kunna strömma senare (video på begäran).

Den här lösningen bygger på de Azure-hanterade tjänsterna: Media Services och Content Delivery Network. De här tjänsterna körs i en miljö med hög tillgänglighet, korrigeras och stöds, så att du kan fokusera på din lösning i stället för den miljö som de körs i.

Se [direkt uppspelning av digitala media](/azure/architecture/solution-ideas/articles/digital-media-live-stream) i Azure Architecture Center.

## <a name="video-on-demand-digital-media"></a>Video på begäran i digitala media

En grundläggande lösning för video på begäran som ger dig möjlighet att strömma inspelade video innehåll, t. ex. filmer, nyhets klipp, idrotts segment, utbildnings videor och kund support-självstudier till en video-kompatibel slut punkts enhet, mobil program eller Skriv bords webbläsare. Videofiler laddas upp till Azure Blob Storage, kodas till ett standardformat med flera bit hastigheter och distribueras sedan via alla större, anpassningsbara bit hastighets protokoll (HLS, MPEG-streck, mjuk) till den Azure Media Player klienten.

Den här lösningen bygger på de Azure-hanterade tjänsterna: Blob Storage, Content Delivery Network och Azure Media Player. De här tjänsterna körs i en miljö med hög tillgänglighet, korrigeras och stöds, så att du kan fokusera på din lösning i stället för den miljö som de körs i.

Se [digitala media på begäran](/azure/architecture/solution-ideas/articles/digital-media-video) i Azure Architecture Center.

## <a name="gridwich-media-processing-system"></a>Gridwich-mediabearbetningssystem

Gridwich system återspeglar metod tips för att bearbeta och leverera medie till gångar på Azure. Även om Gridwich-systemet är mediefel, kan meddelande bearbetnings-och händelse ramverket tillämpas på alla tillstånds lösa arbets flöden för händelse bearbetning.

Se [Gridwich Media Processing System](/azure/architecture/reference-architectures/media-services/gridwich-architecture) i Azure Architecture Center.

## <a name="next-steps"></a>Nästa steg

> [Översikt över Azure Media Services](media-services-overview.md)