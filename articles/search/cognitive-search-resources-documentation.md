---
title: Dokumentations Länkar för AI-anrikning
titleSuffix: Azure Cognitive Search
description: En kommenterad lista över artiklar, självstudier, exempel och blogg inlägg som rör AI-arbetsbelastningar i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935355"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Dokumentations resurser för AI-anrikning i Azure Kognitiv sökning

AI-anrikning är ett tillägg till indexerare indexering som hittar latend information i icke-text källor och en icke-differentierad text, som omvandlar den till text sökbar innehåll i Azure Kognitiv sökning. 

För inbyggd bearbetning kallas de förtränade AI-modellerna i Cognitive Services internt för att utföra analyserna. Du kan också integrera anpassade modeller med hjälp av Azure Machine Learning, Azure Functions eller andra metoder.

Följande är en lista över dokumentation för AI-berikning.

## <a name="concepts"></a>Begrepp

+ [AI-anrikning](cognitive-search-concept-intro.md)
+ [Kompetensuppsättningar](cognitive-search-working-with-skillsets.md)
+ [Felsökningssessioner](cognitive-search-debug-session.md)
+ [Kunskapslager](knowledge-store-concept-intro.md)
+ [Projektioner](knowledge-store-projection-overview.md)
+ [Stegvis anrikning (åter användning av ett cachelagrat berikat dokument)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Hands om genom gångar

+ [Snabb start: skapa en kognitiv färdigheter i Azure Portal](cognitive-search-quickstart-blob.md)
+ [Självstudie: omfattande indexering med AI](cognitive-search-tutorial-blob.md)
+ [Självstudie: diagnostisera, reparera och genomför ändringar i din färdigheter med fel söknings sessioner](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Kunskapslager

+ [Snabb start: skapa ett kunskaps lager i Azure Portal](knowledge-store-create-portal.md)
+ [Skapa ett kunskaps lager med REST och Postman](knowledge-store-create-rest.md)
+ [Visa ett kunskaps lager med Storage Explorer](knowledge-store-view-storage-explorer.md)
+ [Anslut ett kunskaps lager med Power BI](knowledge-store-connect-power-bi.md)
+ [Projekt exempel (hur du kan forma och exportera-anrikninger)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Anpassade kunskaper (avancerat)

+ [Definiera ett anpassat kunskaps gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Exempel: skapa en anpassad kunskap med Azure Functions (och Entitetssökning i Bing API: er)](cognitive-search-create-custom-skill-example.md)
+ [Exempel: skapa en anpassad kunskap med hjälp av python](cognitive-search-custom-skill-python.md)
+ [Exempel: skapa en anpassad kompetens med hjälp av formulär igenkänning](cognitive-search-custom-skill-form.md) 
+ [Exempel: skapa en anpassad färdighet med Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Instruktions vägledning

+ [Bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md)
+ [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md)
+ [referens anteckningar i en färdigheter](cognitive-search-concept-annotations-syntax.md)
+ [Mappa fält till ett index](cognitive-search-output-field-mapping.md)
+ [Bearbeta och extrahera information från bilder](cognitive-search-concept-image-scenarios.md)
+ [Konfigurera cachelagring för stegvis anrikning](search-howto-incremental-index.md)
+ [Återskapa ett Azure Kognitiv sökning-index](search-howto-reindex.md)
+ [Designtips](cognitive-search-concept-troubleshooting.md)
+ [Vanliga fel och varningar](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Referens för färdigheter

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
  + [Microsoft. färdigheter. text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft. färdigheter. text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft. färdigheter. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. färdigheter. text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. färdigheter. text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft. färdigheter. text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft. färdigheter. text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. färdigheter. text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft. färdigheter. vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft. färdigheter. vision. OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. färdigheter. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. färdigheter. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Anpassade färdigheter
  + [Microsoft. färdigheter. Custom. AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft. färdigheter. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Inaktuella färdigheter](cognitive-search-skill-deprecated.md)
  + [Microsoft. färdigheter. text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API:er

+ [REST-API](/rest/api/searchservice/)
  + [Skapa färdigheter (API-version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Skapa indexerare (API-version = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Se även

+ [REST-API:er för Azure Cognitive Search](/rest/api/searchservice/)
+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Vad är Azure Cognitive Search?](search-what-is-azure-search.md)