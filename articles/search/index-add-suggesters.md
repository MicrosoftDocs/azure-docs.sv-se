---
title: Skapa en förslagsställare
titleSuffix: Azure Cognitive Search
description: Aktivera åtgärder för typ i förväg i Azure Kognitiv sökning genom att skapa förslag och utforma begär Anden som anropar Autoavsluta eller automatiska förslag på sökord.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a03ca4bcad9bb577db68e2728ff9dbebb5779a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626834"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Skapa en förslags ställare för att aktivera Autoavsluta och föreslagna resultat i en fråga

I Azure Kognitiv sökning aktive ras typeahead eller "Sök-som-du-Type" via en *förslags ställare*. En förslags lista innehåller en lista över fält som genomgår ytterligare tokenisering, vilket genererar prefixvärde för att stödja matchningar på delar av villkor. En förslags ställare som innehåller ett Orts fält med ett värde för "Seattle" har till exempel prefix för "Sea", "plats", "plats" och "seattl" för att stödja typeahead.

Matchningar på del villkor kan vara antingen en kompletterad fråga eller en föreslagen matchning. Samma förslags ställare stöder båda upplevelserna.

## <a name="typeahead-experiences-in-cognitive-search"></a>Typeahead-upplevelser i Kognitiv sökning

Typeahead kan vara *komplettera automatiskt*, som slutför indatamängden för en fullständig term fråga eller *förslag* som bjuder in genom att klicka till en viss matchning. Funktionen Komplettera automatiskt skapar en fråga. Förslag skapar ett matchande dokument.

Följande skärm bild från [skapa din första app i C#](tutorial-csharp-type-ahead-and-suggestions.md) visar båda. Autoavsluta förväntar sig en möjlig term, och avslutar "TW" med "in". Förslag är små Sök resultat, där ett fält som ett hotell namn representerar ett matchande hotell Sök dokument från indexet. För förslag kan du Visa alla fält som innehåller beskrivande information.

![Visuell jämförelse av Autoavsluta och föreslagna frågor](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuell jämförelse av Autoavsluta och föreslagna frågor")

Du kan använda dessa funktioner separat eller tillsammans. Det finns ett index och en frågekomponent-komponent för att implementera dessa beteenden i Azure Kognitiv sökning. 

+ Lägg till en förslags pekare till en Sök index definition. Resten av den här artikeln fokuserar på att skapa en förslags ställare.

+ Anropa en förslags aktive rad fråga, i form av en förslags förfrågan eller en begäran om automatisk komplettering, med hjälp av något av [API: erna som anges nedan](#how-to-use-a-suggester).

Stöd för sökning efter typ har Aktiver ATS baserat på fältet per fält för sträng fält. Du kan implementera båda typeahead-beteenden inom samma Sök lösning om du vill ha en upplevelse som liknar den som anges i skärm bilden. Båda begär Anden mål *dokument* samlingen för ett särskilt index och svar returneras efter att en användare har angett minst en sträng med tre tecken.

## <a name="how-to-create-a-suggester"></a>Så här skapar du en förslags ställare

Om du vill skapa en förslags pekare, lägger du till en i en [index definition](/rest/api/searchservice/create-index). En förslags ställare tar ett namn och en samling fält över vilka typeahead-upplevelsen är aktive rad. Det bästa sättet att skapa en förslags ställare är när du även definierar det fält som ska använda det.

+ Använd endast sträng fält.

+ Om sträng fältet är en del av en komplex typ (t. ex. fältet stad i adressen) inkluderar du överordnad i fält Sök vägen: `"Address/City"` (rest och C# och python) eller `["Address"]["City"]` (Java Script).

+ Använd standard standard Lucene Analyzer ( `"analyzer": null` ) eller en [språk analys](index-add-language-analyzers.md) (till exempel `"analyzer": "en.Microsoft"` ) i fältet.

Om du försöker skapa en förslags ställare med redan existerande fält kommer API: et att tillåta det. Prefix skapas vid indexering, om del termer i två eller flera teckenkombinationer är token i hela termer. Eftersom befintliga fält redan har tokens, måste du återskapa indexet om du vill lägga till dem i en förslags ställare. Mer information finns i [så här återskapar du ett Azure kognitiv sökning-index](search-howto-reindex.md).

### <a name="choose-fields"></a>Välj fält

Även om en förslags ställare har flera egenskaper, är det i första hand en samling sträng fält för vilka du aktiverar en sökning efter typ. Det finns en förslags ställare för varje index, så att listan över förslag måste innehålla alla fält som bidrar med innehåll för både förslag och Autoavsluta.

Autoavsluta-förmåner från en större pool med fält att rita från eftersom det ytterligare innehållet har mer långsiktigt slut för ande.

Förslag ger å andra sidan bättre resultat när valet av fält är selektivt. Kom ihåg att förslaget är en proxy för ett Sök dokument, så att du vill att fält som bäst representerar ett enda resultat. Namn, titlar eller andra unika fält som skiljer sig mellan flera matchningar fungerar bäst. Om fälten består av upprepade värden, består förslagen av identiska resultat och en användare vet inte vilken av dem som ska klicka.

Lägg till alla fält som du behöver för Komplettera automatiskt, men Använd "$select", "$top", "$filter" och "searchFields" för att kontrol lera resultaten för förslag.

### <a name="choose-analyzers"></a>Välj analys verktyg

Ditt val av analys bestämmer hur fälten ska vara token och sedan föregås. För en avstavad sträng som "Sammanhangs känslig", används till exempel följande token för att använda en språk analys: "context", "känslig", "kontext känslig". Hade du använt standard Lucene Analyzer, den avstavade strängen finns inte. 

När du utvärderar analyser, bör du överväga att använda [analys text API](/rest/api/searchservice/test-analyzer) för att lära dig hur termer bearbetas. När du skapar ett index kan du prova olika analys verktyg i en sträng för att visa utdata för token.

Fält som använder [anpassade analyserare](index-add-custom-analyzers.md) eller [inbyggda analyser](index-add-custom-analyzers.md#built-in-analyzers) (med undantag för standard Lucene) är uttryckligen inte tillåtna för att förhindra dåliga resultat.

> [!NOTE]
> Om du behöver använda ett nyckelord eller ngram Analyzer för vissa fråge scenarier, bör du använda två separata fält för samma innehåll. Detta gör att ett av fälten kan ha en förslags ställare, medan det andra kan konfigureras med en anpassad Analyzer-konfiguration.

## <a name="create-using-the-portal"></a>Skapa med portalen

När du använder **Lägg till index** eller guiden **Importera data** för att skapa ett index, har du möjlighet att aktivera en förslags ställare:

1. I index definitionen anger du ett namn för förslags tillägget.

1. I varje fält definition för nya fält markerar du en kryss ruta i kolumnen förslag. En kryss ruta är endast tillgänglig i sträng fält. 

Som tidigare nämnts påverkar Analyzer valet tokenisering och prefastion. Överväg hela fält definitionen när du aktiverar förslag. 

## <a name="create-using-rest"></a>Skapa med REST

Lägg till förslag via [skapa index](/rest/api/searchservice/create-index) eller [uppdatera index](/rest/api/searchservice/update-index)i REST API. 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Skapa med .NET

I C# definierar du ett [SearchSuggester-objekt](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` är en samling på ett SearchIndex-objekt, men det kan bara ta ett objekt. Lägg till en förslags ställare i index definitionen.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Egenskaps referens

|Egenskap      |Beskrivning      |
|--------------|-----------------|
| name        | Anges i förslags definitionen, men kallas även för en Autoavsluta-eller förslags förfrågan. |
| sourceFields | Anges i förslags definitionen. Det är en lista över ett eller flera fält i indexet som är källan till innehållet för förslag. Fält måste vara av typen `Edm.String` och `Collection(Edm.String)` . Om en analys anges i fältet måste det vara en namngiven lexikalisk analys från [den här listan](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (inte en anpassad analys). </br></br>Vi rekommenderar att du bara anger de fält som lånar ut sig till ett förväntat och lämpligt svar, oavsett om det är en slutförd sträng i ett sökfält eller i en nedrullningsbar listruta. </br></br>Ett hotell namn är en bra kandidat eftersom det har precision. Utförliga fält som beskrivningar och kommentarer är för kompakta. På samma sätt är upprepade fält, till exempel kategorier och taggar, mindre effektiva. I exemplen inkluderar vi "Category" ändå för att demonstrera att du kan inkludera flera fält. |
| searchMode  | En REST-parameter, men även synlig i portalen. Den här parametern är inte tillgänglig i .NET SDK. Den anger den strategi som används för att söka efter kandidat fraser. Det enda läge som stöds för närvarande är `analyzingInfixMatching` , som för närvarande matchar i början av en term.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Använd en förslags ställare

En förslags ställare används i en fråga. När en förslags ställare har skapats anropar du en av följande API: er för en sökning efter typ:

+ [Förslag REST API](/rest/api/searchservice/suggestions)
+ [Autoavsluta-REST API](/rest/api/searchservice/autocomplete)
+ [SuggestAsync-metod](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync-metod](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

I ett sökprogram bör klient koden använda ett bibliotek som [JQUERY UI komplettera automatiskt](https://jqueryui.com/autocomplete/) för att samla in den partiella frågan och ange matchningen. Mer information om den här uppgiften finns i [lägga till komplettera automatiskt eller föreslagna resultat till klient koden](search-add-autocomplete-suggestions.md).

API-användningen illustreras i följande anrop till REST API för automatisk komplettering. Det finns två takeaways i det här exemplet. För det första, som med alla frågor, är åtgärden mot dokument samlingen för ett index och frågan innehåller en "Sök"-parameter, som i det här fallet innehåller den partiella frågan. Därefter måste du lägga till "suggesterName" i begäran. Om en förslags ställare inte har definierats i indexet kommer ett anrop till komplettera automatiskt eller förslag att Miss Missing.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Exempelkod

+ Genom att [lägga till en sökning på en webbplats (Java Script)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) används ett förslags paket med öppen källkod för ofullständig avslutning av termen i klient programmet.

+ [Skapa din första app i C# (Lektion 3 – Lägg till sökning efter typ) exempel på](tutorial-csharp-type-ahead-and-suggestions.md) frågor, Autoavsluta och fasett-navigering. Den här koden ger inbyggt stöd för typeahead i stället för att använda en widget.

## <a name="next-steps"></a>Nästa steg

Läs mer om begär Anden \ formulering.

> [!div class="nextstepaction"]
> [Lägg till komplettera automatiskt och förslag till klient kod](search-add-autocomplete-suggestions.md)