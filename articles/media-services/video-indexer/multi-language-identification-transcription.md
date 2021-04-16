---
title: Identifiera och transkribera innehåll på flera språk automatiskt med Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du automatiskt identifierar och transkriberar innehåll på flera språk Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 319bd408943c560622dc3208a6701417b8ca010c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532914"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Identifiera och transkribera innehåll på flera språk automatiskt

Video Indexer har stöd för automatisk språkidentifiering och transkription i innehåll på flera språk. Den här processen innebär att automatiskt identifiera det talade språket i olika segment från ljud, skicka varje segment av mediefilen som ska transkriberas och kombinera transkriptionen tillbaka till en enhetlig transkription. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Välja flerspråkig identifiering vid indexering med portalen

Du kan välja **identifiering på flera språk** när du laddar upp och indexerar videon. Du kan också välja identifiering **på flera språk**  när du indexerar om videon. Följande steg beskriver hur du indexerar om:

1. Gå till [Video Indexer](https://vi.microsoft.com/)-webbplatsen och logga in.
1. Gå till **sidan** Bibliotek och hovra över namnet på videon som du vill indexera om. 
1. Klicka på knappen Indexera om video i det **nedre högra** hörnet. 
1. I dialogrutan **Indexera om video** väljer du identifiering av flera **språk** i **listrutan** Videokällspråk.

    * När en video indexeras som flera språk innehåller insiktssidan det alternativet, och en ytterligare insiktstyp visas, vilket gör det möjligt för användaren att visa vilket segment som transkriberas på vilket språk "Talat språk".
    * Översättning till alla språk är helt tillgängligt från avskriften för flera språk.
    * Alla andra insikter visas på huvudspråket som identifierats – det är det språk som visades mest i ljudet.
    * Undertextning på spelaren finns även på flera språk.

![Portalmiljö](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Välja flerspråkig identifiering vid indexering med API

När du indexerar [eller indexerar om](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) en video med hjälp av API:et väljer `multi-language detection` du alternativet i `sourceLanguage` parametern .

### <a name="model-output"></a>Modell-utdata

Modellen hämtar alla språk som identifierats i videon i en lista

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Dessutom innehåller varje instans i transkriptionsavsnittet det språk som den transkriberas på

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar

* Uppsättning språk som stöds: engelska, franska, tyska, spanska.
* Stöd för innehåll på flera språk med upp till tre språk som stöds.
* Om ljudet innehåller andra språk än listan som stöds ovan blir resultatet oväntat.
* Minsta segmentlängd att identifiera för varje språk – 15 sekunder.
* Språkidentifieringsförskjutning är i genomsnitt 3 sekunder.
* Tal förväntas vara kontinuerligt. Frekventa alternationer mellan språk kan påverka modellens prestanda.
* Tal med icke-inbyggda talare kan påverka modellens prestanda (till exempel när talare använder sina inbyggda talare och de byter till ett annat språk).
* Modellen är utformad för att känna igen ett samtalssamtal med rimlig ljudakustik (inte röstkommandon, telefonsamtal osv.).
* Projektskapande och -redigering är för närvarande inte tillgängligt för videor på flera språk.
* Anpassade språkmodeller är inte tillgängliga när du använder identifiering på flera språk.
* Det går inte att lägga till nyckelord.
* När du exporterar undertextfiler visas inte språkindikeringen.
* API:et för uppdateringsavskrift stöder inte filer med flera språk.

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)
