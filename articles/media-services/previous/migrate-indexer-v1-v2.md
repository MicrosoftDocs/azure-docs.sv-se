---
title: Migrera från indexerare v1 och v2 till Azure Media Services Video Indexer | Microsoft Docs
description: I det här avsnittet beskrivs hur du migrerar från Azure Media Indexer v1 och v2 för att Azure Media Services Video Indexer.
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: eb4f662a4b34d748da0e97da9b64a386be3f5c49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016431"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrera från Media Indexer och Media Indexer 2 till Video Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Vi rekommenderar att kunderna migrerar från Indexer v1 och Indexer v2 till att använda [Media Services v3 AudioAnalyzerPreset Basic-läge](../latest/analyzing-video-audio-files-concept.md). [Azure Media Indexer](media-services-index-content.md) medie processor och Azure Media Indexer 2-processorer för för [hands versions](./legacy-components.md) mediet dras tillbaka. Se det här avsnittet om [äldre komponenter](legacy-components.md) för datum för indragningen.

Azure Media Services Video Indexer bygger på Azure-medieanalys, Azure Kognitiv sökning, Cognitive Services (till exempel Ansikts-API, Microsoft Translator, API för visuellt innehåll och Custom Speech Service). Programmet gör det möjligt att extrahera insikter från dina videor med video- och ljudmodeller för Video Indexer. Om du vill se vilka scenarier Video Indexer kan användas i, vilka funktioner det erbjuder och hur du kommer igång, se [video Indexer video-och ljud modeller](../video-indexer/video-indexer-overview.md). 

Du kan extrahera insikter från dina video-och ljudfiler med hjälp av [Azure Media Services v3 Analyzer-för inställningar](../latest/analyzing-video-audio-files-concept.md) eller direkt med hjälp av [video Indexer API: er](https://api-portal.videoindexer.ai/). För närvarande finns det en överlappning mellan funktioner som erbjuds av Video Indexer-API: er och Media Services v3-API: er.

> [!NOTE]
> Om du vill förstå skillnaderna mellan Video Indexer jämfört med Media Services Analyzer-för inställningar, kan du läsa [jämförelse dokumentet](../video-indexer/compare-video-indexer-with-media-services-presets.md).

I den här artikeln beskrivs stegen för att migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Migreringsalternativ

|Om du behöver  |sedan |
|---|---|
|en lösning som ger en tal-till-text-avskrift för alla media fil format i fil format med fil namns tillägg: VTT, SRT eller TTML<br/>samt ytterligare ljud insikter som: nyckelord, ämne inferencing, akustiska händelser, talare diarization, extrahering och översättning av enheter| uppdatera dina program så att de använder Azure Video Indexer-funktionerna via Video Indexer v2-REST API eller Azure Media Services v3-inställningarna för ljud analys.|
|tal till text-funktioner| Använd Cognitive Services Speech API direkt.|  

## <a name="getting-started-with-video-indexer"></a>Komma igång med Video Indexer

Följande avsnitt pekar på relevanta länkar: [Hur kan jag komma igång med video Indexer?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Komma igång med Media Services v3-API: er

Med Azure Media Services v3 API kan du extrahera insikter från dina video-och ljudfiler via [Azure Media Services v3 Analyzer-för hands inställningarna](../latest/analyzing-video-audio-files-concept.md).

Med **AudioAnalyzerPreset** kan du extrahera flera ljud insikter från en ljud-eller video fil. Utdata innehåller en VTT-eller TTML-fil för ljud avskriften och en JSON-fil (med alla ytterligare ljud insikter). Ljud insikter innehåller nyckelord, talare indexering och analys av tal sentiment. AudioAnalyzerPreset har även stöd för språk identifiering för specifika språk. Detaljerad information finns i [transformeringar](/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Kom igång

För att komma igång, se:

* [Självstudie](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset-exempel: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) eller [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset-exempel: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) eller [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Komma igång med Cognitive Services Speech Services

[Azure Cognitive Services](../../cognitive-services/index.yml) tillhandahåller en tal-till-text-tjänst som beskrivar ljud strömmar till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Du kan använda tal-till-text för att [Anpassa din egen akustiska modell, språk modell eller uttals modell](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Mer information finns i [Cognitive Services tal-till-text](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Tal-till-text-tjänsten tar inte med video fil format och tar bara med [vissa ljud format](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats). 

Mer information om tjänsten för text till tal och hur du kommer igång finns i [Vad är tal-till-text?](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>Kända skillnader jämfört med inaktuella tjänster

Du kommer att se att Video Indexer, Azure Media Services v3-AudioAnalyzerPreset och Cognitive Services Speech Services-tjänster är mer pålitliga och ger bättre kvalitet på utdata än de pensionerade Azure Media Indexer 1-och Azure Media Indexer 2-processorerna.  

Några kända skillnader är:

* Cognitive Services Speech Services stöder inte nyckelords extrahering. Men Video Indexer och Media Services v3-AudioAnalyzerPreset erbjuder båda en robust uppsättning nyckelord i JSON-filformat.

## <a name="support"></a>Support

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Nästa steg

* [Äldre komponenter](legacy-components.md)
* [Prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/#encoding)