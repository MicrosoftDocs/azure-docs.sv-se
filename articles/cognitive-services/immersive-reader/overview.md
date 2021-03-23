---
title: Vad är den fördjupade läsaren?
titleSuffix: Azure Cognitive Services
description: Avancerad läsare är ett verktyg som är utformat för att hjälpa personer med inlärnings skillnader eller hjälpa nya läsare och språk kunskaper med att läsa förståelse.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: läsare, språk information, Visa bilder, förbättra läsning, läsa innehåll, Översätt
ms.openlocfilehash: 1911d3ce62434f1dc24966798e9ace259ae072bc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801578"
---
# <a name="what-is-immersive-reader"></a>Vad är Avancerad läsare?

[Avancerad läsare](https://www.onenote.com/learningtools) är ett särskilt utformat verktyg som implementerar beprövade tekniker för att förbättra Läs förståelse för nya läsare, språkutbildningar och personer med inlärnings skillnader som Dyslexia. Med klient biblioteket för avancerad läsare kan du använda samma teknik som används i Microsoft Word och Microsoft en anteckning för att förbättra dina webb program. 

Den här dokumentationen innehåller följande typer av artiklar:  

* **[Snabb starter](quickstarts/client-libraries.md)** hjälper dig att komma igång med instruktioner för att göra förfrågningar till tjänsten.
* **[Instruktions guider](how-to-create-immersive-reader.md)** innehåller instruktioner för att använda tjänsten på mer exakta eller anpassade sätt.

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>Använd avancerad läsare för att förbättra läsnings hjälpmedel 

Avancerad läsare är utformad för att göra läsningen enklare och mer tillgänglig för alla. Låt oss ta en titt på några av de fördjupade läsarens kärn funktioner.

### <a name="isolate-content-for-improved-readability"></a>Isolera innehåll för förbättrad läsbarhet

Avancerad läsare isolerar innehåll för att förbättra läsbarheten. 

  ![Isolera innehåll för förbättrad läsbarhet med avancerad läsare](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Visa bilder för vanliga ord

För vanliga villkor visas en bild i den fördjupade läsaren.

  ![Bild ord lista med fördjupad läsare](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>Markera delar av tal

Avancerad läsare kan användas för att lära sig förstå delar av tal och grammatik genom att markera verb, substantiv, prosubstantivs med mera.

  ![Visa delar av tal med fördjupad läsare](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Läs innehåll högt

Tal syntesen (eller text till tal) är bakade i tjänsten för avancerad läsare, vilket gör att läsarna kan välja text som ska läsas högt. 

  ![Läs text högt med avancerad läsare](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Översätt innehåll i real tid

Avancerad läsare kan översätta text till flera språk i real tid. Detta är användbart för att förbättra förståelsen för läsarna att lära sig ett nytt språk.

  ![Översätt text med fördjupad läsare](./media/translation.png)

### <a name="split-words-into-syllables"></a>Dela upp ord i stavelser

Med avancerad läsare kan du dela upp ord i stavelser för att förbättra läsbarheten eller för att få ut nya ord.

  ![Dela upp ord i stavelser med avancerad läsare](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hur fungerar den fördjupade läsaren?

Avancerad läsare är ett fristående webb program. När det anropas med klient biblioteket för fördjupad läsare visas överst i det befintliga webb programmet i en `iframe` . När ditt WEP-program anropar tjänsten för avancerad läsning anger du det innehåll som ska visas i läsaren. Klient biblioteket för avancerad läsare hanterar skapandet och formateringen av `iframe` och kommunikationen med Server dels tjänsten för avancerad läsare. Tjänsten för avancerad läsare bearbetar innehållet för delar av tal, text till tal, översättning med mera.

## <a name="get-started-with-immersive-reader"></a>Kom igång med avancerad läsare

Klient biblioteket för avancerad läsare är tillgängligt i C#, Java Script, Java (Android), Kotlin (Android) och SWIFT (iOS). Kom igång med:

* [Snabb start: Använd klient biblioteket för avancerad läsare](quickstarts/client-libraries.md)
