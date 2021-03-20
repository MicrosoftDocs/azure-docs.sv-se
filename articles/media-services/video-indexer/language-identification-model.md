---
title: Använd Video Indexer för att automatiskt identifiera talade språk – Azure
titleSuffix: Azure Media Services
description: Den här artikeln beskriver hur den Video Indexer språk identifierings modellen används för att automatiskt identifiera det talade språket i en video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81687128"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifiera det talade språket automatiskt med modellen för språkidentifiering

Video Indexer stöder automatisk språk identifiering (lock), vilket är en process för att automatiskt identifiera det talade språk innehållet från ljud och skicka medie filen som ska skrivas in i det dominerande identifierade språket. 

För närvarande stöder locket: engelska, spanska, franska, tyska, italienska, mandariner kinesiska, japanska, ryska och portugisiska (Brasiliansk). 

Läs avsnittet [rikt linjer och begränsningar](#guidelines-and-limitations) nedan.

## <a name="choosing-auto-language-identification-on-indexing"></a>Välja automatisk språk identifiering vid indexering

När du ska indexera [eller indexera om en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) med hjälp av API: et väljer du `auto detect` alternativet i `sourceLanguage` parametern.

När du använder portalen går du till dina **konto videor** på Start sidan för [video Indexer](https://www.videoindexer.ai/) och hovrar över namnet på videon som du vill indexera om. Klicka på knappen index igen i det högra hörnet. I dialog rutan **Indexera om video** väljer du *Automatisk identifiering* från List rutan för **video källans språk** .

![identifiera automatiskt](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Modell-utdata

Video Indexer beskrivar videon enligt det mest sannolika språket om förtroendet för det språket är `> 0.6` . Om språket inte kan identifieras med förtroende förutsätter det att det talade språket är engelska. 

Modellens dominerande språk är tillgängligt i insikts-JSON som `sourceLanguage` attribut (under root/video/Insights). Ett motsvarande förtroende poäng är också tillgängligt under `sourceLanguageConfidence` attributet.

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

## <a name="guidelines-and-limitations"></a>Rikt linjer och begränsningar

* Automatisk språk identifiering (lock) stöder följande språk: 

    Engelska, spanska, franska, tyska, italienska, mandariner chines, japanska, ryska och portugisiska (Brasiliansk).
* Även om Video Indexer stöder arabiska (modern standard och Levantine), hindi och koreanska, stöds inte dessa språk i locket.
* Om ljudet innehåller andra språk än de som stöds ovan, är resultatet oväntat.
* Om Video Indexer inte kan identifiera språket med hög nog tillförlitlighet ( `>0.6` ) är reserv språket engelska.
* Det finns inget aktuellt stöd för filen med ljud från blandade språk. Om ljudet innehåller blandade språk är resultatet oväntat. 
* Ljud med låg kvalitet kan påverka modell resultatet.
* Modellen kräver minst en minut med tal i ljudet.
* Modellen är utformad för att identifiera ett spontant samtals tal (inte röst kommandon, loggar osv.).

## <a name="next-steps"></a>Nästa steg

* [Översikt](video-indexer-overview.md)
* [Identifiera och transkribera innehåll på flera språk automatiskt](multi-language-identification-transcription.md)
