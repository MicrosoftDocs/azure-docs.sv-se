---
title: Gränser – QnA Maker
description: QnA Maker har metagränser för delar av kunskapsbasen och tjänsten. Det är viktigt att hålla kunskapsbasen inom dessa gränser för att testa och publicera.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: ad498575b029f918538909a9b5b2d52c71c1389c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816376"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker och gränser för kunskapsbas

QnA Maker som anges nedan är en [kombination av Azure Cognitive Search prisnivågränser](../../search/search-limits-quotas-capacity.md) och [de QnA Maker prisnivågränserna.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) Du behöver känna till båda uppsättningarna med gränser för att förstå hur många kunskapsbaser du kan skapa per resurs och hur stor varje kunskapsbas kan växa.

## <a name="knowledge-bases"></a>Kunskapsbaser

Det maximala antalet kunskapsbaser baseras på Azure Cognitive Search [nivåbegränsningar.](../../search/search-limits-quotas-capacity.md)

|**Azure Cognitive Search nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximalt antal publicerade kunskapsbaser som tillåts|2|14|49|199|199|2,999|

 Om din nivå till exempel har 15 tillåtna index kan du publicera 14 kunskapsbaser (1 index per publicerad kunskapsbas). Det 15:e `testkb` indexet, , används för alla kunskapsbaser för redigering och testning.

## <a name="extraction-limits"></a>Extraheringsgränser

### <a name="file-naming-constraints"></a>Begränsningar för namngivning av filer

Filnamn får inte innehålla följande tecken:

|Använd inte tecken|
|--|
|Enkelt citattecken `'`|
|Dubbla citattecken `"`|

### <a name="maximum-file-size"></a>Maximal filstorlek

|Format|Maximal filstorlek (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximalt antal filer

Det maximala antalet filer som kan extraheras och maximal filstorlek baseras på QnA Maker **[prisnivåbegränsningar.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**

> [!NOTE]
> QnA Maker (förhandsversion) är en kostnadsfri tjänst utan begränsningar för antalet källor som kan läggas till. Dataflödet är för närvarande begränsat till 10 transaktioner per sekund för både hanterings-API:er och förutsägelse-API:er.

### <a name="maximum-number-of-deep-links-from-url"></a>Maximalt antal djuplänkar från URL

Det maximala antalet djuplänkar som kan crawlas för extrahering av QnA:er från en URL-sida är **20**.

## <a name="metadata-limits"></a>Metadatabegränsningar

Metadata visas som ett textbaserat key:value-par, till exempel `product:windows 10` . Den lagras och jämförs i gemener.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Efter Azure Cognitive Search prisnivå

Maximalt antal metadatafält per kunskapsbas baseras på dina **[Azure Cognitive Search på nivån](../../search/search-limits-quotas-capacity.md)**.

|**Azure Cognitive Search nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximalt antal metadatafält per QnA Maker tjänst (över alla KB)|1 000|100*|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Efter namn och värde

Längden och godkända tecken för metadatanamn och -värde visas i följande tabell.

|Objekt|Tillåtna tecken|Mönstermatchning för Regex|Maximalt antal tecken|
|--|--|--|--|
|Namn (nyckel)|Tillåter<br>alfanumeriskt (bokstäver och siffror)<br>`_` (understreck)<br> Får inte innehålla blanksteg.|`^[a-zA-Z0-9_]+$`|100|
|Värde|Tillåter allt utom<br>`:` (kolon)<br>`|` (lodrätt rör)<br>Endast ett värde tillåts.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Innehållsbegränsningar för kunskapsbas
Övergripande begränsningar för innehållet i kunskapsbasen:
* Svarstextlängd: 25 000 tecken
* Frågetextens längd: 1 000 tecken
* Längden på metadatanyckeltext: 100 tecken
* Längden på metadatavärdetext: 500 tecken
* Tecken som stöds för metadatanamn: Alfabet, siffror och `_`
* Tecken som stöds för metadatavärde: Alla utom `:` och `|`
* Filnamnets längd: 200
* Filformat som stöds: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Maximalt antal alternativa frågor: 300
* Maximalt antal par med frågor och svar: Beror på Azure Cognitive Search **[valda](../../search/search-limits-quotas-capacity.md#document-limits)** nivån. Ett par med frågor och svar mappar till ett dokument Azure Cognitive Search index.
* URL-/HTML-sida: 1 miljon tecken

## <a name="create-knowledge-base-call-limits"></a>Skapa anropsgränser för kunskapsbas:
Dessa representerar gränserna för varje åtgärd för att skapa kunskapsbas. det vill säga genom att *klicka på Skapa kunskapsbas* eller anropa CreateKnowledgeBase-API:et.
* Rekommenderat maximalt antal alternativa frågor per svar: 300
* Maximalt antal URL:er: 10
* Maximalt antal filer: 10
* Maximalt antal QnA:er som tillåts per anrop: 1 000

## <a name="update-knowledge-base-call-limits"></a>Uppdatera anropsgränser för kunskapsbas
Dessa representerar gränserna för varje uppdateringsåtgärd. det vill säga, klicka *på Spara och träna* eller anropa UpdateKnowledgeBase-API:et.
* Längden på varje källnamn: 300
* Rekommenderat maximalt antal alternativa frågor som lagts till eller tagits bort: 300
* Maximalt antal metadatafält som lagts till eller tagits bort: 10
* Maximalt antal URL:er som kan uppdateras: 5
* Maximalt antal QnA:er som tillåts per anrop: 1 000

## <a name="add-unstructured-file-limits"></a>Lägga till ostrukturerade filgränser

> [!NOTE]
> * Om du behöver använda större filer än vad gränsen tillåter kan du dela upp filen i mindre filer innan du skickar dem till API:et. 

Dessa representerar gränserna när ostrukturerade filer används för att skapa *KB eller* anropa CreateKnowledgeBase-API:et:
* Fillängd: Vi extraherar de första 32 000 tecknen
* Maximalt 3 svar per fil.

## <a name="prebuilt-question-answering-limits"></a>Fördefinierade gränser för frågesvar

> [!NOTE]
> * Om du behöver använda större dokument än vad gränsen tillåter kan du dela upp texten i mindre segment av text innan du skickar dem till API:et. 
> * Ett dokument är en sträng med texttecken.  

Dessa representerar gränserna när fördefinierade API:er används för att generera *svar eller* anropa API:et GenerateAnswer:
* Antal dokument: 5
* Maximal storlek för ett enskilt dokument: 5 120 tecken
* Maximalt 3 svar per dokument.

## <a name="next-steps"></a>Nästa steg

Lär dig när och hur du [ändrar tjänstprisnivåer.](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)
