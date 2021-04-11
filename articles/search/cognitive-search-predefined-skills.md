---
title: Inbyggd text-och bild bearbetning under indexering
titleSuffix: Azure Cognitive Search
description: Data extrahering, naturligt språk, kognitiva bild bearbetnings kunskaper Lägg till semantik och struktur till rå data i en Azure Kognitiv sökning pipeline.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bde82a0ad50c2172e70337ad202b91af0a62c530
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547823"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Inbyggda kognitiva färdigheter för text-och bild bearbetning under indexering (Azure Kognitiv sökning)

I den här artikeln får du lära dig om kognitiva färdigheter som medföljer Azure Kognitiv sökning som du kan inkludera i en färdigheter för att extrahera innehåll och struktur. En *kognitiv kunskap* är en modul eller åtgärd som transformerar innehåll på något sätt. Ofta är det en komponent som extraherar data eller härleder struktur, och därmed förstärker vår förståelse av indata. Nästan alltid är utdatan text-baserad. En *färdigheter* är en samling kunskaper som definierar anriknings pipelinen. 

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).
>
> Med funktionen för [förhands granskning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) kan du tillhandahålla ett cacheminne som gör det möjligt för indexeraren att vara mer effektivt när du bara kör de kognitiva färdigheter som behövs om du ändrar din färdigheter i framtiden, vilket sparar tid och pengar.


## <a name="built-in-skills"></a>Inbyggda färdigheter

Flera färdigheter är flexibla i vad de använder eller producerar. I allmänhet baseras de flesta kunskaper på förtränade modeller, vilket innebär att du inte kan träna modellen med dina egna tränings data. I följande tabell räknas och beskrivs de kunskaper som tillhandahålls av Microsoft. 

| Kvalifikation | Beskrivning |
|-------|-------------|
|[Microsoft. färdigheter. text. CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Söker efter text från en anpassad, användardefinierad lista med ord och fraser.|
| [Microsoft. färdigheter. text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | Den här kunskapen använder en förtränad modell för att identifiera viktiga fraser baserade på placering av termer, språk regler, närhet till andra villkor och hur ovanlig termen är i käll data. |
| [Microsoft. färdigheter. text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Den här kunskapen använder en förtränad modell för att identifiera vilket språk som används (ett språk-ID per dokument). När flera språk används i samma text segment, är utdata LCID för det mest använda språket.|
| [Microsoft. färdigheter. text. MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliderar text från en samling fält till ett enda fält.  |
| [Microsoft. färdigheter. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Den här kunskapen använder en förtränad modell för att upprätta entiteter för en fast uppsättning kategorier: personer, plats, organisation, e-post, URL: er, DateTime-fält. |
| [Microsoft. färdigheter. text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Den här kunskapen använder en förtränad modell för att extrahera personlig information från en specifik text. Kompetensen ger också olika alternativ för att maskera de identifierade personliga informations enheterna i texten.  |
| [Microsoft. färdigheter. text. SentimentSkill](cognitive-search-skill-sentiment.md)  | Den här kunskapen använder en förtränad modell för att räkna upp positiva eller negativa sentiment på en post efter post. Poängen är mellan 0 och 1. Neutrala Poäng inträffar för både null-fall när sentiment inte kan identifieras och för text som betraktas som neutral.  |
| [Microsoft. färdigheter. text. SplitSkill](cognitive-search-skill-textsplit.md) | Delar upp text i sidor så att du kan utöka eller utöka innehållet stegvis. |
| [Microsoft. färdigheter. text. TranslationSkill](cognitive-search-skill-text-translation.md) | Den här kunskapen använder en förtränad modell för att översätta inmatad text till en rad olika språk för normalisering eller lokalisering av användnings fall. |
| [Microsoft. färdigheter. vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Den här kunskapen använder en algoritm för avbildnings avkänning för att identifiera innehållet i en bild och generera en text beskrivning. |
| [Microsoft. färdigheter. vision. OcrSkill](cognitive-search-skill-ocr.md) | Optisk typsnitts igenkänning. |
| [Microsoft. färdigheter. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Tillåter filtrering, tilldelar ett standardvärde och sammanfogar data baserat på ett villkor.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extraherar innehåll från en fil inom en pipeline för anrikning. |
| [Microsoft. färdigheter. util. ShaperSkill](cognitive-search-skill-shaper.md) | Mappar utdata till en komplex typ (en data typ med flera delar, som kan användas för ett fullständigt namn, en rad med flera rader eller en kombination av efter namn och personligt ID.) |
| [Microsoft. färdigheter. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md) | Tillåter utökning av en AI-rikare pipeline genom att göra ett HTTP-anrop till ett anpassat webb-API |
| [Microsoft. färdigheter. Custom. AmlSkill](cognitive-search-aml-skill.md) | Tillåter utökning av en AI-rikare pipeline med en Azure Machine Learning modell |


Information om hur du skapar en [anpassad färdighet](cognitive-search-custom-skill-web-api.md)finns i [så här definierar du ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md) och [exempel: skapa en anpassad färdighet för AI-berikning](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Se även

+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Definition av anpassat kompetens gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Självstudie: omfattande indexering med AI](cognitive-search-tutorial-blob.md)
