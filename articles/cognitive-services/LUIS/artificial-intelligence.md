---
title: Artificiell intelligens (AI)
description: LUIS använder artificiell intelligens (AI) för att tillhandahålla språk förståelse för dina data baserat på det schema som du har definierat.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021379"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Artificiell intelligens i Language Understanding (LUIS)

LUIS använder artificiell intelligens (AI) för att tillhandahålla naturliga språk förståelse (NLU) för dina data baserat på det schema som du har definierat.

## <a name="natural-language-processing-nlp"></a>Bearbetning av naturligt språk (NLP)

Naturlig Language Understanding (NLU) är ett speciellt underavsnitt av NLP (naturlig Language Processing).

Bearbetning av naturligt språk är ett bredare koncept som hanterar alla former av bearbetning av text data, detta omfattar bland annat:

* Tokenisering
* Del av tal märkning (POS)
* Segmentering
* Morfologisk analys
* Semantisk likhet
* Samtal
* Översättning

## <a name="natural-language-processing-in-luis"></a>Bearbetning av naturligt språk i LUIS

Bearbetning av naturligt språk är tillgängligt för LUIS-appen på följande sätt:
* [Natur språks förståelse](#natural-language-processing-nlp) (Luis)
* Konfigurerbara NLP-aspekter i LUIS:
    * [Tokenisering](luis-language-support.md#tokenization)
    * [API-inställningar](luis-reference-application-settings.md) för den här inställningen för dia kritiska tecken, interpunktion och ord former
* Efter bearbetning av frågan uttryck som tillhandahålls av andra [Cognitive Services](../what-are-cognitive-services.md) , till exempel:
    * [Översättning](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>Natur språks förståelse (NLU)

NLU är möjligheten att _omvandla_ en språklig instruktion till en åter givning som gör det möjligt för dig att förstå dina användare naturligt. Naturligt språk att förstå är ett mycket utmanande problem och definieras som ett _AI-hårt_ problem.

LUIS är avsett att fokusera på avsikt och extraktion, men detta inkluderar att kunna identifiera:
* Vad användaren vill
* Vad de pratar om.

LUIS har lite eller ingen kunskap om de bredare _NLP_ -aspekterna, till exempel semantisk likhet, utan explicit identifiering i exempel. Till exempel är följande tokens (ord) tre olika saker tills de används i liknande kontexter i de exempel som tillhandahålls:

* handlar
* köpa
* åter

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [utvecklings livs cykeln](luis-concept-app-iteration.md) för en Luis-app