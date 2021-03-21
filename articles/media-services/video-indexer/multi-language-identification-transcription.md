---
title: Identifiera och automatisk identifiering av innehåll på flera språk med Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du automatiskt identifierar och beskrivar innehåll på flera språk med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015563"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Identifiera och transkribera innehåll på flera språk automatiskt

Video Indexer stöder automatisk språk identifiering och avskrift i innehåll på flera språk. Den här processen innebär att automatiskt identifiera det talade språket i olika segment från ljud, vilket innebär att varje segment i medie filen har tilldelats och kombinera avskriften till en enhetlig avskrift. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Välja flerspråkig identifiering vid indexering med portalen

Du kan välja att **upptäcka flera språk** när du laddar upp och indexerar videon. Alternativt kan du välja **flerspråkig identifiering**  när du indexerar videon igen. Följande steg beskriver hur du Omindexerar:

1. Gå till [Video Indexer](https://vi.microsoft.com/)-webbplatsen och logga in.
1. Gå till sidan **bibliotek** och hovra över namnet på videon som du vill indexera om. 
1. Klicka på knappen för att **Indexera om videon** i det högra hörnet. 
1. I dialog rutan **Indexera om video** väljer du stöd för **flera språk** från List rutan för **video källans språk** .

    * När en video indexeras som flera språk, kommer insikts sidan att innehålla det alternativet och ytterligare en insikts typ visas, så att användaren kan se vilket segment som har tilldelats på vilket språk "talade språk".
    * Översättning till alla språk är helt tillgängligt från avskriften med flera språk.
    * Alla andra insikter visas på huvud språket som identifieras – det är det språk som förekom mest i ljudet.
    * Dold textning i spelaren är även tillgänglig i flera språk.

![Portalmiljö](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Välja flerspråkig identifiering vid indexering med API

När du ska indexera eller [Indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) om en video med hjälp av API: et väljer du `multi-language detection` alternativet i `sourceLanguage` parametern.

### <a name="model-output"></a>Modell-utdata

Modellen kommer att hämta alla språk som identifierats i videon i en lista

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Dessutom innehåller varje instans i avskrifts avsnittet det språk som den har tilldelats till

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

## <a name="guidelines-and-limitations"></a>Rikt linjer och begränsningar

* Uppsättning språk som stöds: engelska, franska, tyska, spanska.
* Stöd för innehåll med flera språk med upp till tre språk som stöds.
* Om ljudet innehåller andra språk än de som stöds ovan, är resultatet oväntat.
* Minsta segment längd att identifiera för varje språk – 15 sekunder.
* Offset för språk identifiering är 3 sekunder i genomsnitt.
* Talet förväntas vara kontinuerlig. Frekventa alternerar mellan språk kan påverka modellens prestanda.
* Tal för icke-inbyggda högtalare kan påverka modell prestanda (till exempel när högtalare använder sina egna tunga och de byter till ett annat språk).
* Modellen är utformad för att identifiera ett spontant samtals tal med en god ljud akustiskhet (inte röst kommandon, loggar osv.).
* Det går inte att skapa och redigera projekt för närvarande för videor med flera språk.
* Anpassade språk modeller är inte tillgängliga när du använder identifiering på flera språk.
* Det finns inte stöd för att lägga till nyckelord.
* När du exporterar filer med dold textning visas inte språk indikeringen.
* Uppdaterings avskrifts-API: t stöder inte filer med flera språk.

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)
