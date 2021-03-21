---
title: Stegvisa anriknings begrepp (för hands version)
titleSuffix: Azure Cognitive Search
description: Cachelagra mellanliggande innehåll och stegvisa ändringar från AI-pipeline i Azure Storage för att bevara investeringar i befintliga bearbetade dokument. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 2448609b1184c8e91947bffbd13cfea8e3fe5d52
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390869"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Stegvis anrikning och cachelagring i Azure Kognitiv sökning

> [!IMPORTANT] 
> Stegvis anrikning är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> [REST API för hands versioner](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande ingen portal eller .NET SDK-support.

*Stegvis anrikning* är en funktion som är riktad mot [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md). Den använder Azure Storage för att spara bearbetnings resultatet som släpps av en pipeline-pipeline för åter användning i framtida indexerare körs. När det är möjligt återanvänder indexeraren alla cachelagrade utdata som fortfarande är giltiga. 

Den stegvisa berikningen bevarar inte bara din monetära investering i bearbetningen (särskilt OCR-bearbetning och bild bearbetning), men det gör också ett mer effektivt system. 

Ett arbets flöde som använder stegvis cachelagring innehåller följande steg:

1. [Skapa eller identifiera ett Azure Storage-konto](../storage/common/storage-account-create.md) för lagring av cacheminnet.
1. [Aktivera stegvis berikning](search-howto-incremental-index.md) i indexeraren.
1. [Skapa en indexerare](/rest/api/searchservice/create-indexer) – plus en [färdigheter](/rest/api/searchservice/create-skillset) -för att anropa pipelinen. Under bearbetningen sparas stadier av berikning för varje dokument i Blob Storage för framtida bruk.
1. Testa koden och när du har gjort ändringarna använder du [Uppdatera färdigheter](/rest/api/searchservice/update-skillset) för att ändra en definition.
1. [Kör indexeraren](/rest/api/searchservice/run-indexer) för att anropa pipelinen, Hämta cachelagrade utdata för snabbare och mer kostnads effektiv bearbetning.

Mer information om steg och överväganden när du arbetar med en befintlig indexerare finns i [Konfigurera stegvis berikning](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Indexerare-cache

Stegvis anrikning lägger till en cache i pipelinen. Indexeraren cachelagrar resultaten från dokument sprickor plus utdata för varje kunskap för varje dokument. När en färdigheter uppdateras, körs bara de ändrade eller underordnade färdigheterna igen. De uppdaterade resultaten skrivs till cachen och dokumentet uppdateras i Sök indexet eller i kunskaps lagret.

Fysiskt lagras cacheminnet i en BLOB-behållare i ditt Azure Storage-konto. Cachen använder också Table Storage för en intern post av bearbetnings uppdateringar. Alla index i en Sök tjänst kan dela samma lagrings konto för indexeraren cache. Varje indexerare tilldelas en unik och oföränderlig cache-identifierare till den behållare som den använder.

## <a name="cache-configuration"></a>Cache-konfiguration

Du måste ange `cache` egenskapen på indexeraren för att starta får från en stegvis berikning. I följande exempel illustreras en indexerare med cachelagring aktiverat. Vissa delar av den här konfigurationen beskrivs i följande avsnitt. Mer information finns i [Konfigurera stegvis berikning](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Om du anger den här egenskapen för en befintlig indexerare måste du återställa och köra indexeraren igen, vilket leder till att alla dokument i data källan bearbetas igen. Det här steget är nödvändigt för att eliminera dokument som har berikats av tidigare versioner av färdigheter. 

## <a name="cache-management"></a>Hantering av cache

Livs längden för cachen hanteras av indexeraren. Om `cache` egenskapen på indexeraren har angetts till null eller om anslutnings strängen ändras, tas den befintliga cachen bort när nästa indexerare körs. Cachens livs cykel är också kopplad till indexerare livs cykel. Om en indexerare tas bort, tas även den tillhör ande cachen bort.

Även om den stegvisa berikningen är utformad för att identifiera och svara på ändringar utan någon åtgärd från din sida, finns det parametrar som du kan använda för att åsidosätta standard beteenden:

+ Prioritera nya dokument
+ Kringgå färdigheter-kontroller
+ Kringgå kontroller av data Källa
+ Framtvinga färdigheter-utvärdering

### <a name="prioritize-new-documents"></a>Prioritera nya dokument

Ange `enableReprocessing` egenskapen för att styra bearbetningen av inkommande dokument som redan visas i cacheminnet. När `true` (standard), kommer dokument som redan finns i cacheminnet att ombearbetas när du kör om indexeraren, förutsatt att din kunskaps uppdatering påverkar dokumentet. 

När de `false` befintliga dokumenten inte bearbetas igen, prioriteras nya, inkommande innehåll i befintligt innehåll. Du bör bara ställas in `enableReprocessing` `false` på temporär basis. För att säkerställa konsekvens över sökkorpus `enableReprocessing` bör du vara det `true` mesta av tiden, vilket säkerställer att alla dokument, både nya och befintliga, är giltiga enligt den aktuella färdigheter-definitionen.

### <a name="bypass-skillset-evaluation"></a>Kringgå färdigheter-utvärdering

Det går inte att ändra en färdigheter och ombearbeta den färdigheter. Vissa ändringar i en färdigheter bör dock inte resultera i ombearbetning (till exempel att distribuera en anpassad färdighet till en ny plats eller med en ny åtkomst nyckel). De flesta sannolika är att de är kring utrustnings ändringar som inte har någon verklig inverkan på själva färdigheter. 

Om du vet att en ändring i färdigheter verkligen är ytlig, bör du åsidosätta färdigheter-utvärderingen genom `disableCacheReprocessingChangeDetection` att ange parametern till `true` :

1. Anropa Update färdigheter och ändra färdigheter-definitionen.
1. Lägg till `disableCacheReprocessingChangeDetection=true` parametern i begäran.
1. Skicka ändringen.

Genom att ange den här parametern ser du till att endast uppdateringar av färdigheter-definitionen genomförs och ändringen utvärderas inte för effekter på den befintliga sökkorpus.

I följande exempel visas en uppdatering färdigheter-begäran med parametern:

```http
PUT https://[search service].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Kringgå verifierings kontroller för data Källa

De flesta ändringar av en data käll definition kommer att ogiltig förklara cachen. Men för scenarier där du vet att en ändring inte ska göra detta ska du inte göra en ändring i cachen, till exempel ändra en anslutnings sträng eller rotera nyckeln på lagrings kontot – Lägg till `ignoreResetRequirement` parametern i uppdateringen av data källan. Om du anger den här parametern så att `true` incheckning kan gå igenom, utan att utlösa ett återställnings villkor som skulle resultera i att alla objekt byggs om och fylls från från början.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Framtvinga färdigheter-utvärdering

Syftet med cachen är att undvika onödig bearbetning, men vi antar att du gör en ändring i en färdighet som indexeraren inte identifierar (till exempel ändra något i extern kod, till exempel en anpassad färdighet).

I det här fallet kan du använda [återställnings kunskaper](/rest/api/searchservice/preview-api/reset-skills) för att tvinga ombearbetning av en viss färdighet, inklusive eventuella efterföljande kunskaper som har ett beroende på den aktuella färdighetens utdata. Detta API accepterar en POST-begäran med en lista med kunskaper som ska ogiltig förklaras och markeras för ombearbetning. När du har återställt färdigheter kan du köra indexeraren för att anropa pipelinen.

### <a name="reset-documents"></a>Återställ dokument

[Om du återställer en indexerare](/rest/api/searchservice/reset-indexer) kommer alla dokument i Sök sökkorpus att ombearbetas. I scenarier där bara några få dokument behöver ombearbetas och data källan inte kan uppdateras använder du [Återställ dokument (för hands version)](/rest/api/searchservice/preview-api/reset-documents) för att tvinga ombearbetning av vissa dokument. När ett dokument återställs, ogiltig förklaras cachen för dokumentet och dokumentet bearbetas igen genom att läsa det från data källan. Mer information finns i [köra eller återställa indexerare, kunskaper och dokument](search-howto-run-reset-indexers.md).

## <a name="change-detection"></a>Ändringsidentifiering

När du aktiverar ett cacheminne utvärderar indexeraren ändringar i din pipeline-sammansättning för att avgöra vilket innehåll som kan återanvändas och vilka som behöver bearbetas igen. I det här avsnittet räknas ändringar som inte validerar cachen direkt, följt av ändringar som utlöser stegvis bearbetning. 

### <a name="changes-that-invalidate-the-cache"></a>Ändringar som invaliderar cachen

En ogiltig ändring är en där hela cachen inte längre är giltig. Ett exempel på en ogiltig ändring är en plats där data källan uppdateras. Här är en fullständig lista över ändringar som skulle göra din cache ogiltig:

* Ändra till typ av data Källa
* Ändra till data källans behållare
* Datakällans autentiseringsuppgifter
* Princip för ändrings identifiering av data Källa
* Ta bort identifierings princip för data källor
* Fält mappningar för indexerare
* Indexerings parametrar
    * Tolknings läge
    * Uteslutna fil namns tillägg
    * Indexerade fil namns tillägg
    * Indexera lagrings metadata endast för dokument med storleks storlek
    * Avgränsade text rubriker
    * Avgränsad text avgränsare
    * Dokument rot
    * Avbildnings åtgärd (ändringar i hur bilder extraheras)

### <a name="changes-that-trigger-incremental-processing"></a>Ändringar som utlöser stegvis bearbetning

Stegvis bearbetning utvärderar din färdigheter-definition och avgör vilka kunskaper som ska köras igen och selektivt uppdaterar de berörda delarna i dokument trädet. Här är en fullständig lista över ändringar som resulterar i stegvisa anrikning:

* Kompetensen i färdigheter har en annan typ. OData-typen för kunskapen uppdateras
* Kunskapsbaserade parametrar har uppdaterats, till exempel URL, standardinställningar eller andra parametrar
* Ändringar i kunskaps utmatningar, kunskapen returnerar ytterligare eller andra utdata
* Kunskaps uppdateringar som uppstår är olika föregångare, kunskaps kedjan har ändrats, dvs. kompetens inmatningar
* Eventuella indata från en överordnad kompetens, om en färdighet som tillhandahåller indata till den här kompetensen uppdateras
* Uppdateringar till platsen för kunskaps lager projektion, resultat i omprojektering av dokument
* Ändringar i kunskaps lagrets projektioner, resultat i omprojektering av dokument
* Mappningar av utdatakolumner som har ändrats på en indexerare resulterar i omprojektering av dokument till indexet

## <a name="api-reference"></a>API-referens

REST API-versionen `2020-06-30-Preview` ger stegvis berikning genom ytterligare egenskaper för indexerare. Färdighetsuppsättningar och data källor kan använda den allmänt tillgängliga versionen. Utöver referens dokumentationen finns mer information om hur du anropar API: erna i  [Konfigurera cachelagring för stegvis berikning](search-howto-incremental-index.md) .

+ [Skapa indexerare (API-version = 2020-06 -30 – för hands version)](/rest/api/searchservice/create-indexer) 

+ [Uppdatera indexerare (API-version = 2020-06 -30 – för hands version)](/rest/api/searchservice/update-indexer) 

+ [Uppdatera färdigheter (API-version = 2020-06-30)](/rest/api/searchservice/update-skillset) (ny URI-parameter på begäran)

+ [Återställa kunskaper (API-version = 2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ Databas indexerare (Azure SQL, Cosmos DB). Vissa indexerare hämtar data via frågor. För frågor som hämtar data stöder [uppdaterings data källan](/rest/api/searchservice/update-data-source) en ny parameter på en begäran- **ignoreResetRequirement**, som ska ställas in på `true` när din uppdaterings åtgärd inte ska göra en ogiltig verifiering av cachen. 

  Använd **ignoreResetRequirement** sparsamt eftersom det kan leda till oavsiktlig inkonsekvens i dina data som inte kommer att identifieras enkelt.

## <a name="next-steps"></a>Nästa steg

Stegvis anrikning är en kraftfull funktion som utökar ändrings spårningen till färdighetsuppsättningar och AI-anrikning. Stegvisa anrikning möjliggör åter användning av befintligt bearbetat innehåll när du itererar över färdigheter design.

I nästa steg ska du aktivera cachelagring på en befintlig indexerare eller lägga till ett cacheminne när du definierar en ny indexerare.

> [!div class="nextstepaction"]
> [Konfigurera cachelagring för stegvis anrikning](search-howto-incremental-index.md)