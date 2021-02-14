---
title: Köra eller återställa indexerare
titleSuffix: Azure Cognitive Search
description: Återställ en indexerare, flera kunskaper eller enskilda dokument för att uppdatera hela eller delar av och index eller kunskaps lager.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 06f9d336bb04aa26cea4ebcdae1cfd045e1c2a1b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361255"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Köra eller återställa indexerare, kunskaper eller dokument

Körning av indexerare kan ske när du först skapar [indexeraren](search-indexer-overview.md), när du kör en indexerare på begäran eller när du ställer in en indexerare enligt ett schema. Efter den första körningen håller en indexerare reda på vilka Sök dokument har indexerats genom ett internt "högt vatten märke". Markören exponeras aldrig i API: et, men internt indexeraren vet var indexeringen stoppas, så att den kan hämtas där den slutade vid nästa körning.

Du kan ta bort det övre vatten märket genom att återställa indexeraren om du vill bearbeta den från grunden. Reset-API: er är tillgängliga vid minskning av nivåer i Object-hierarkin:

+ Hela Sök sökkorpus (Använd [Återställ indexerare](#reset-indexers))
+ Ett enskilt dokument eller en lista med dokument (Använd [Återställ dokument – för hands version](#reset-docs))
+ En speciell kunskap eller berikning i ett dokument (Använd [återställnings kunskaper – för hands version](#reset-skills))

Återställnings-API: erna används för att uppdatera cachelagrat innehåll (tillämpligt i [AI-anriknings](cognitive-search-concept-intro.md) scenarier) eller för att ta bort det höga vatten märket och återskapa indexet.

Återställning, följt av kör, kan bearbeta befintliga dokument och nya dokument, men tar inte bort överblivna sökdokument i Sök indexet som skapades på tidigare körningar. Mer information om borttagning finns i [lägga till, uppdatera eller ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Köra indexerare

[Skapa indexerare](/rest/api/searchservice/create-indexer) skapar och kör indexeraren om du inte skapar den i inaktiverat tillstånd ("inaktive rad": true). Den första körningen tar lite längre tid eftersom dess objekt skapas också.

[Kör indexeraren](/rest/api/searchservice/run-indexer) identifierar och bearbetar bara det som krävs för att synkronisera Sök indexet med data källan. Blob Storage har inbyggd ändrings identifiering. Andra data källor, till exempel Azure SQL eller Cosmos DB, måste konfigureras för ändrings identifiering innan indexeraren kan läsa bara de nya och uppdaterade raderna.

Du kan köra en indexerare med någon av följande metoder:

+ Azure Portal med kommandot **Kör** på sidan Indexer
+ [Köra indexerare (REST)](/rest/api/searchservice/run-indexer)
+ [RunIndexers-metoden](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) i Azure .NET SDK (eller med motsvarande RunIndexer-metod i en annan SDK)

Körning av indexerare omfattas av följande gränser:

+ Det maximala antalet indexerare jobb är 1 per replik som inte samtidiga jobb.

  Om körning av indexerare redan har kapacitet visas följande meddelande: "Det gick inte att köra indexeraren" <indexerare-Name> ", fel:" en annan Indexer-anrop pågår för närvarande. samtidiga anrop tillåts inte. "

+ Maximal kör tid är 2 timmar om du använder en färdigheter och 24 timmar utan. 

  Du kan sträcka ut bearbetningen genom att placera indexeraren enligt ett schema. Den kostnads fria nivån har lägre körnings tids gränser. En fullständig lista finns i [gränser för indexerare](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Återställa en indexerare

Att återställa en indexerare är all kompass. I Sök indexet markeras alla sökdokument som ursprungligen fylldes av indexeraren för fullständig bearbetning. Alla nya dokument som hittas den underliggande källan kommer att läggas till i indexet som sökdokument. Om indexeraren har kon figurer ATS för att använda en färdigheter och [cachelagring](search-howto-incremental-index.md), körs färdigheter igen och cachen uppdateras.

Du kan återställa en indexerare med någon av dessa metoder, följt av en indexerare som körs med hjälp av en av de metoder som beskrivs ovan.

+ Azure Portal med kommandot **Reset** på sidan Indexer
+ [Återställ indexerare (REST)](/rest/api/searchservice/reset-indexer)
+ [ResetIndexers-metoden](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) i Azure .NET SDK (eller med motsvarande RunIndexer-metod i en annan SDK)

En återställnings flagga rensas när körningen är klar. All logik för vanlig ändrings identifiering som är avgörande för din data källa kommer att återupptas vid nästa körning, hämta alla nya eller uppdaterade värden i resten av data uppsättningen.

> [!NOTE]
> En återställnings förfrågan bestämmer vad som ombearbetas (indexerare, kunskap eller dokument), men påverkar inte i övrigt indexerings körnings beteende. Om indexeraren har kör tids parametrar, fält mappningar, cachelagring, batch-alternativ och så vidare, gäller dessa inställningar när du kör en indexerare när du har återställt den.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Återställ kunskaper (för hands version)

> [!IMPORTANT] 
> [Återställning av kunskaper](/rest/api/searchservice/preview-api/reset-skills) finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

För indexerare som har färdighetsuppsättningar kan du återställa vissa kunskaper för att framtvinga bearbetning av den kunskapen och eventuella efterföljande kunskaper som är beroende av dess utdata. [Cachelagrade anrikninger](search-howto-incremental-index.md) uppdateras också. Om du återställer kunskaper inaktive ras de cachelagrade kunskaps resultaten, vilket är användbart när en ny version av en färdighet distribueras och du vill att indexeraren kör om den kunskapen för alla dokument. 

[Återställning av färdigheter](/rest/api/searchservice/preview-api/reset-skills) är tillgängligt via REST **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Du kan ange enskilda kunskaper, som du ser i exemplet ovan, men om någon av dessa färdigheter kräver utdata från ej listade kunskaper (#2 via #4) körs inte listade kunskaper om inte cachen kan tillhandahålla nödvändig information. För att detta ska vara sant får inte cachelagrade berikade kunskaper för färdigheter #2 via #4 vara beroende av #1 (listade för återställning).

Om inga kunskaper har angetts körs hela färdigheter och om cachelagring har Aktiver ATS kommer cachen också att uppdateras.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Återställ dokument (förhands granskning)

> [!IMPORTANT] 
> [Återställ dokument](/rest/api/searchservice/preview-api/reset-documents) finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[API: et för återställda dokument](https://docs.microsoft.com/rest/api/searchservice/preview-api/reset-documents) accepterar en lista med dokument nycklar så att du kan uppdatera vissa dokument. Om det här alternativet anges blir återställnings parametrarna den enda Determinant av vad som bearbetas, oavsett andra ändringar i underliggande data. Om till exempel 20 blobbar har lagts till eller uppdaterats sedan den senaste indexeraren kördes, men du bara återställer ett dokument, så kommer bara det ett dokument att bearbetas.

På per dokument-basis uppdateras alla fält i Sök dokumentet med värden från data källan. Du kan inte välja och välja vilka fält som ska uppdateras. 

Om dokumentet har berikats via en färdigheter och har cachelagrade data, anropas färdigheter bara för de angivna dokumenten och cachen uppdateras för de ombearbeta dokumenten.

När du testar detta API för första gången hjälper följande API: er dig att validera och testa beteenden:

+ [Hämta indexerings status](/rest/api/searchservice/get-indexer-status) med API `2020-06-30-Preview` -version för att kontrol lera återställnings status och körnings status. Du kan hitta information om återställnings förfrågan i slutet av status svaret.
+ [Återställ dokument](/rest/api/searchservice/preview-api/reset-documents) med API `2020-06-30-Preview` -version för att ange vilka dokument som ska bearbetas.
+ [Kör indexeraren](/rest/api/searchservice/run-indexer) för att köra indexeraren (alla API-versioner).
+ [Sök i dokument](/rest/api/searchservice/search-documents) för att söka efter uppdaterade värden och returnera dokument nycklar om du är osäker på värdet. Använd `"select": "<field names>"` om du vill begränsa vilka fält som ska visas i svaret.

### <a name="formulate-and-send-the-reset-request"></a>Formulera och skicka återställnings förfrågan

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

De dokument nycklar som anges i begäran är värden från Sök indexet, som kan skilja sig från motsvarande fält i data källan. Om du är osäker på värdet för nyckeln skickar du [en fråga](search-query-create.md) för att returnera värdet. Du kan använda `select` för att bara returnera dokument nyckel fältet.

För blobbar som är parsade i flera Sök dokument (till exempel om du använde [jsonLines eller jsonArrays](search-howto-index-json-blobs.md), eller [delimitedText](search-howto-index-csv-blobs.md)) som ett tolknings läge, genereras dokument nyckeln av indexeraren och kan vara okänd för dig. I det här fallet kommer en fråga för dokument nyckeln att instrumentell i att ange rätt värde.

Om du anropar API: et flera gånger med olika nycklar läggs de nya nycklarna till i listan över återställda dokument nycklar. Om du anropar API **`overwrite`** : et med parametern inställt på Sant skrivs den aktuella listan över dokument nycklar som ska återställas med begärans nytto last:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Kontrol lera återställnings status

Om du vill kontrol lera status för en återställning och se vilka dokument nycklar köade för bearbetningen använder du [Hämta indexerings status](/rest/api/searchservice/get-indexer-status) med **`api-version=06-30-2020-Preview`** . För hands versions-API: et returnerar **`currentState`** avsnittet, som du hittar i slutet av status svaret get Indexer.

"Mode" används för att **`indexingAllDocs`** återställa färdigheter (eftersom potentiellt alla dokument påverkas, för de fält som är ifyllda via AI-anrikning).

För att återställa dokument är läget inställt på **`indexingResetDocs`** . Indexeraren behåller denna status tills alla dokument nycklar som anges i återställnings dokument anropet har bearbetats och inga andra indexerings jobb körs medan åtgärden fortskrider. Att söka efter alla dokument i listan med dokument nycklar kräver att varje dokument måste befinna sig och matchas på nyckeln, och det kan ta en stund om data uppsättningen är stor. Om en BLOB-behållare innehåller hundratals blobbar och de dokument som du vill återställa är i slutet, hittar inte indexeraren matchande blobbar förrän alla andra har marker ATS först.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

När dokumenten har bearbetats återgår indexeraren till **`indexingAllDocs`** läget och bearbetar alla nya eller uppdaterade dokument vid nästa körning.

## <a name="next-steps"></a>Nästa steg

Reset API: er används för att informera omfånget för nästa indexerare-körning. Vid faktisk bearbetning måste du anropa en index körning på begäran eller tillåta att ett schemalagt jobb slutför arbetet. När körningen är färdig återgår indexeraren till normal bearbetning, oavsett om det är ett schema eller en bearbetning på begäran.

När du har återställt och kör om indexerings jobb kan du övervaka status från Sök tjänsten eller Hämta detaljerad information via diagnostikloggning.

+ [Indexerings åtgärder (REST)](/rest/api/searchservice/indexer-operations)
+ [Övervaka status för Sök indexering](search-howto-monitor-indexers.md)
+ [Samla in och analysera loggdata](search-monitor-logs.md)
+ [Schemalägga en indexerare](search-howto-schedule-indexers.md)