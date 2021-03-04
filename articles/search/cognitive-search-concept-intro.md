---
title: Koncept för AI-anrikning
titleSuffix: Azure Cognitive Search
description: Innehålls extrahering, naturlig språk bearbetning (NLP) och bild bearbetning används för att skapa sökbart innehåll i Azure Kognitiv sökning index med både fördefinierade kognitiva kunskaper och anpassade AI-algoritmer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: references_regions
ms.openlocfilehash: 22d7c1bbe03d8b3c0e3b6026c9bac039f0651548
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037259"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>AI-anrikning i Azure Kognitiv sökning

AI-anrikning är ett tillägg till [indexerare](search-indexer-overview.md) som kan användas för att extrahera text från bilder, blobbar och andra ostrukturerade data källor. Anrikning och extraktion gör innehållet mer sökbart i objekt i indexerare, antingen ett [sökindex](search-what-is-an-index.md) eller ett [kunskaps lager](knowledge-store-concept-intro.md). 

Extraktion och berikning implementeras med *kognitiva kunskaper* som är kopplade till den indexerade pipelinen. Du kan använda inbyggda kunskaper från Microsoft eller bädda in extern bearbetning i en [*anpassad färdighet*](cognitive-search-create-custom-skill-example.md) som du skapar. Exempel på en anpassad färdighet kan vara en anpassad enhets-eller dokument klassificerare som riktar sig till en speciell domän, till exempel ekonomi, vetenskapliga publikationer eller medicin.

Inbyggda kunskaper ingår i följande kategorier: 

+ Kunskaper om **bearbetning av naturligt språk** inkluderar [enhets igenkänning](cognitive-search-skill-entity-recognition.md), [språk identifiering](cognitive-search-skill-language-detection.md), [extrahering av nyckel fraser](cognitive-search-skill-keyphrases.md), text manipulation, [sentiment identifiering](cognitive-search-skill-sentiment.md)och [identifiering av PII](cognitive-search-skill-pii-detection.md). Med dessa kunskaper mappas ostrukturerad text som sökbara och filter bara fält i ett index.

+ **Bild bearbetnings** kunskaper omfattar [optisk tecken läsning (OCR)](cognitive-search-skill-ocr.md) och identifiering av [visuella funktioner](cognitive-search-skill-image-analysis.md), till exempel ansikts igenkänning, bild tolkning, bild igenkänning (berömda-användare och landmärken) eller attribut som bild orientering. Dessa kunskaper skapar text representationer av bild innehåll, vilket gör det sökbart med hjälp av fråge funktionerna i Azure Kognitiv sökning.

![Pipeline-diagram för anrikning](./media/cognitive-search-intro/cogsearch-architecture.png "Översikt över anriknings pipeline")

Inbyggda kunskaper i Azure Kognitiv sökning baseras på förtränade maskin inlärnings modeller i API:er för Cognitive Services: [visuellt innehåll](../cognitive-services/computer-vision/index.yml) och [textanalys](../cognitive-services/text-analytics/overview.md). Du kan koppla en Cognitive Services-resurs om du vill utnyttja dessa resurser under innehålls bearbetning.

Naturligt språk och bild bearbetning används under fasen för data inmatning, med resultat som blir en del av ett dokuments sammansättning i ett sökbart index i Azure Kognitiv sökning. Data visas som en Azure-datauppsättning och flyttas sedan till en indexerings pipeline med hjälp av de [inbyggda kunskaper](cognitive-search-predefined-skills.md) du behöver.  

## <a name="feature-availability"></a>Funktionstillgänglighet

AI-berikning är tillgänglig i regioner där Azure Cognitive Services också är tillgängliga.  Du kan kontrol lera aktuell tillgänglighet för AI-berikning på sidan [tillgängliga Azure-produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=search) .  AI-anrikning är tillgängligt i alla regioner som stöds utom:

+ Australien, sydöstra
+ Kina, norra 2
+ Östra Norge
+ Tyskland, västra centrala

Om Sök tjänsten finns i någon av dessa regioner kan du inte skapa och använda färdighetsuppsättningar, men alla andra Sök tjänst funktioner är tillgängliga och stöds fullt ut.

## <a name="when-to-use-ai-enrichment"></a>När AI-anrikning ska användas

Du bör överväga att använda inbyggda kognitiva kunskaper om ditt råa innehåll är ostrukturerad text, bild innehåll eller innehåll som behöver språk identifiering och översättning. Genom att använda AI via inbyggda kognitiva kunskaper kan du låsa upp det här innehållet och öka dess värde och verktyg i dina Sök-och data vetenskaps appar. 

Dessutom kan du överväga att lägga till en anpassad färdighet om du har en öppen källkod, tredje part eller första parts kod som du vill integrera i pipelinen. Klassificerings modeller som identifierar viktigaste delarna egenskaper för olika dokument typer ingår i den här kategorin, men paket som lägger till värdet i innehållet kan användas.

### <a name="more-about-built-in-skills"></a>Mer om inbyggda kunskaper

En [färdigheter](cognitive-search-defining-skillset.md) som samlas in med inbyggda kunskaper passar bra för följande program scenarier:

+ Skannade dokument (JPEG) som du vill göra full text sökbar. Du kan bifoga en OCR-färdighet (optisk tecken läsning) för att identifiera, extrahera och mata in text från JPEG-filer.

+ PDF-filer med kombinerad bild och text. Text i PDF-filer kan extraheras under indexering utan användning av anriknings steg, men tillägg av bilder och naturlig språk bearbetning kan ofta ge ett bättre resultat än en standard indexering.

+ Flerspråkigt innehåll som du vill använda för språk identifiering och eventuellt text översättning.

+ Ostrukturerade eller delvis strukturerade dokument med innehåll som har betydelse eller kontext som är dolt i det större dokumentet. 

  Blobbar innehåller ofta en stor innehålls förteckning som är förpackad i ett enda "fält". Genom att koppla färdigheter för avbildning och naturlig språk bearbetning till en indexerare kan du skapa ny information som är extant i rå data, men inte på annat sätt placeras som distinkta fält. Vissa inbyggda kognitiva kunskaper som kan hjälpa dig med att extrahera nyckel fraser, sentiment analys och enhets igenkänning (personer, organisationer och platser).

  Dessutom kan inbyggda kunskaper också användas för att omstrukturera innehåll genom text delnings-, sammanfognings-och form åtgärder.

### <a name="more-about-custom-skills"></a>Mer om anpassade kunskaper

Anpassade kunskaper kan stödja mer komplexa scenarier, till exempel igenkänning av formulär eller anpassad identifiering av entiteter med hjälp av en modell som du anger och omsluter i det [anpassade webb gränssnittet för kompetens](cognitive-search-custom-skill-interface.md). Flera exempel på anpassade kunskaper är [formulär igenkänning](../cognitive-services/form-recognizer/overview.md), integrering av [API för entitetsökning i Bing](./cognitive-search-create-custom-skill-example.md)och [anpassad enhets igenkänning](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="steps-in-an-enrichment-pipeline"></a>Steg i en pipeline för berikning <a name="enrichment-steps"></a>

En pipeline för anrikning baseras på [*indexerare*](search-indexer-overview.md). Indexerare fyller ett index baserat på fält-till-fält-mappningar mellan indexet och data källan för dokument sprickor. Färdigheter, som nu är kopplade till indexerare, utsnappa och utöka dokument enligt de färdigheter som du definierar. När du har indexerat kan du komma åt innehåll via Sök begär Anden via alla [frågetyper som stöds av Azure kognitiv sökning](search-query-overview.md).  Om du inte har använt indexerare i det här avsnittet vägleder vi dig genom stegen.

### <a name="step-1-connection-and-document-cracking-phase"></a>Steg 1: anslutnings-och dokument sprickors fas

I början av pipelinen har du ostrukturerad text eller innehåll som inte är text (till exempel bilder, skannade dokument eller JPEG-filer). Data måste finnas i en Azure Data Storage-tjänst som kan nås av en indexerare. Indexerare kan "knäcka" käll dokument för att extrahera text från källdata. Dokument sprickor är processen att extrahera eller skapa text innehåll från icke-text källor under indexering.

![Dokument sprickors fas](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokument sprickor")

 Källor som stöds är Azure Blob Storage, Azure Table Storage, Azure SQL Database och Azure Cosmos DB. Textbaserat innehåll kan extraheras från följande filtyper: PDF-filer, Word-, PowerPoint-och CSV-filer. En fullständig lista finns i [format som stöds](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Indexeringen tar tid så att den börjar med en liten, representativ data uppsättning och sedan skapar den stegvis i takt med att din lösning är vuxen.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Steg 2: kognitiva kunskaper och anriknings fas

Berikning utförs med *kognitiva kunskaper* som utför atomiska åtgärder. När du till exempel har knäckat en PDF-fil kan du använda entitets igenkänning, språk identifiering eller extrahering av nyckel fraser för att skapa nya fält i indexet som inte är tillgängliga i källan. Den samling med kunskaper som används i din pipeline kallas helt en *färdigheter*.  

![Anriknings fas](./media/cognitive-search-intro/enrichment-phase-blowup.png "anriknings fas")

En färdigheter baseras på [inbyggda kognitiva färdigheter](cognitive-search-predefined-skills.md) eller [anpassade kunskaper](cognitive-search-create-custom-skill-example.md) som du tillhandahåller och ansluter till färdigheter. En färdigheter kan vara minimal eller mycket komplicerad och bestämmer inte bara typ av bearbetning, utan även ordningen på åtgärder. En färdigheter plus de fält mappningar som definierats som en del av en indexerare anger helt en pipeline för anrikning. Mer information om hur du hämtar alla dessa delar finns i [definiera en färdigheter](cognitive-search-defining-skillset.md).

Internt genererar pipelinen en samling av förrikade dokument. Du kan bestämma vilka delar av de berikade dokumenten som ska mappas till index bara fält i ditt sökindex. Om du till exempel har använt extrahering av nyckel fraser och entitets igenkännings kunskaper blir de nya fälten en del av det förrikade dokumentet och kan mappas till fält i indexet. Se [anteckningar](cognitive-search-concept-annotations-syntax.md) om du vill veta mer om indata/utdata-formulär.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Lägg till ett knowledgeStore-element för att spara dina berikare

[Search REST API-version = 2020-06-30](/rest/api/searchservice/) utökar färdighetsuppsättningar med en `knowledgeStore` definition som tillhandahåller en Azure Storage-anslutning och projektioner som beskriver hur de är lagrade. Detta är förutom ditt index. I en standard-AI-pipeline är berikade dokument övergående, som endast används vid indexering och sedan ignoreras. Med kunskaps lager bevaras de dokument som är omfattande. Mer information finns i [Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Steg 3: Sök efter index och fråge baserad åtkomst

När bearbetningen är färdig har du ett sökindex som består av omfattande dokument, fullständigt text – sökbar i Azure Kognitiv sökning. Att [fråga indexet](search-query-overview.md) är hur utvecklare och användare får åtkomst till det innehåll som genereras av pipelinen. 

![Index med sökikon](./media/cognitive-search-intro/search-phase-blowup.png "Index med sökikon")

Indexet är som alla andra som du kan skapa för Azure Kognitiv sökning: du kan komplettera med anpassade analys verktyg, anropa oskarpa Sök frågor, lägga till filtrerad sökning eller experimentera med bedömnings profiler för att ändra form på Sök resultaten.

Index genereras från ett index schema som definierar fält, attribut och andra konstruktioner som är kopplade till ett särskilt index, till exempel bedömnings profiler och synonym mappningar. När ett index har definierats och fyllts i kan du indexera stegvis för att hämta nya och uppdaterade käll dokument. Vissa ändringar kräver en fullständig återuppbyggnad. Du bör använda en liten data uppsättning tills schema designen är stabil. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

**Check lista: ett typiskt arbets flöde**

1. Delmängd dina Azure-källdata i ett representativt exempel. Indexeringen tar tid så att den börjar med en liten, representativ data uppsättning och sedan skapar den stegvis i takt med att din lösning är vuxen.

1. Skapa ett [data käll objekt](/rest/api/searchservice/create-data-source) i Azure kognitiv sökning för att tillhandahålla en anslutnings sträng för data hämtning.

1. Skapa en [färdigheter](/rest/api/searchservice/create-skillset) med hjälp av anriknings steg.

1. Definiera [index schemat](/rest/api/searchservice/create-index). *Fält* samlingen innehåller fält från källdata. Du bör också utvärderar ytterligare fält för att innehålla genererade värden för innehåll som skapas under anrikning.

1. Definiera [indexeraren](/rest/api/searchservice/create-indexer) som refererar till data källan, färdigheter och indexet.

1. I indexeraren lägger du till *outputFieldMappings*. Det här avsnittet mappar utdata från färdigheter (i steg 3) till indata-fälten i index schemat (i steg 4).

1. Skicka en begäran om att *skapa indexerare* som du nyss skapade (en post-begäran med en indexerare-definition i begär ande texten) för att uttrycka indexeraren i Azure kognitiv sökning. I det här steget ska du köra indexeraren, anropa pipelinen.

1. Kör frågor för att utvärdera resultat och ändra kod för att uppdatera färdighetsuppsättningar, schema eller Indexer-konfiguration.

1. [Återställ indexeraren](search-howto-reindex.md) innan du skapar pipelinen igen.

## <a name="next-steps"></a>Nästa steg

+ [Dokumentations Länkar för AI-anrikning](cognitive-search-resources-documentation.md)
+ [Exempel: skapa en anpassad färdighet för AI-anrikning (C#)](cognitive-search-create-custom-skill-example.md)
+ [Snabb start: prova AI-anrikning i en portal genom gång](cognitive-search-quickstart-blob.md)
+ [Självstudie: Lär dig mer om API: er för AI-anrikning](cognitive-search-tutorial-blob.md)
+ [Knowledge Store](knowledge-store-concept-intro.md)
+ [Skapa ett kunskaps lager i REST](knowledge-store-create-rest.md)
+ [Fel söknings tips](cognitive-search-concept-troubleshooting.md)
