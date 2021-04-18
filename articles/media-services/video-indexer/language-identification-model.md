---
title: Använda Video Indexer för att automatiskt identifiera talade språk – Azure
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs Video Indexer en modell för språkidentifiering används för att automatiskt identifiera det talade språket i en video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600645"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifiera det talade språket automatiskt med modellen för språkidentifiering

Video Indexer har stöd för automatisk språkidentifiering (LID), vilket är en process för att automatiskt identifiera talat språkinnehåll från ljud och skicka mediefilen som ska transkriberas på det dominerande identifierade språket. 

För närvarande stöder LID: engelska, spanska, franska, tyska, italienska, mandarin, japanska, ryska och portugisiska (Brasilien). 

Läs avsnittet Riktlinjer [och begränsningar](#guidelines-and-limitations) nedan.

## <a name="choosing-auto-language-identification-on-indexing"></a>Välja automatisk språkidentifiering vid indexering

När du indexerar [eller indexerar om en](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) video med hjälp av API:et väljer du alternativet i `auto detect` `sourceLanguage` parametern .

När du använder portalen  går du till [dina kontovideor på startsidan Video Indexer](https://www.videoindexer.ai/) och hovrar över namnet på videon som du vill indexera om. Klicka på indexeringsknappen igen i det nedre högra hörnet. I dialogrutan **Indexera om video** väljer du Automatisk identifiering *i* **listrutan** Videokällspråk.

![automatisk identifiering](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Modell-utdata

Video Indexer transkriberar videon enligt det mest sannolika språket om konfidensen för det språket är `> 0.6` . Om språket inte kan identifieras med förtroende förutsätter det att det talade språket är engelska. 

Modelldominerande språk är tillgängligt i insikterna JSON som `sourceLanguage` attributet (under root/videos/insights). Motsvarande förtroendepoäng är också tillgängligt under `sourceLanguageConfidence` attributet .

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar

* Automatisk språkidentifiering (LID) stöder följande språk: 

    Engelska, spanska, franska, tyska, italienska, mandarina hakar, japanska, ryska och portugisiska (Brasilien).
* Även om Video Indexer har stöd för arabiska (modern standard och Levantine), hindi och koreanska, stöds inte dessa språk i LOCK.
* Om ljudet innehåller andra språk än listan som stöds ovan blir resultatet oväntat.
* Om Video Indexer kan identifiera språket med tillräckligt hög konfidens ( `>0.6` ) är återställningsspråket engelska.
* Det finns för närvarande inget stöd för filer med ljud med blandade språk. Om ljudet innehåller blandade språk blir resultatet oväntat. 
* Ljud med låg kvalitet kan påverka modellresultatet.
* Modellen kräver minst en minuts tal i ljudet.
* Modellen är utformad för att känna igen ett konversationskonversationellt tal (inte röstkommandon, igenkänning osv.).

## <a name="next-steps"></a>Nästa steg

* [Översikt](video-indexer-overview.md)
* [Identifiera och transkribera innehåll på flera språk automatiskt](multi-language-identification-transcription.md)
