---
title: Lägg till Autoavsluta i en sökruta
titleSuffix: Azure Cognitive Search
description: Aktivera frågor som är av typ typ i Azure Kognitiv sökning genom att skapa förslag och utforma begär Anden som kompletterar en sökruta med färdiga villkor eller fraser. Du kan också returnera föreslagna matchningar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: a46c7f35e30b2dda7b4800ed553447cef5bb5d33
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609363"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Lägg till komplettera automatiskt och förslag till klient program med Azure Kognitiv sökning

Sökning efter typ är en vanlig teknik för att förbättra produktiviteten för frågor. I Azure Kognitiv sökning stöds den här upplevelsen genom *Autoavsluta*, som avslutar en term eller fras baserat på inaktuella inleveranser (som slutförs med "Micro" med "Microsoft"). En andra användar upplevelse är *förslag* eller en kort lista med matchande dokument (som returnerar bok titlar med ett ID så att du kan länka till en informations sida om den boken). Både Autoavsluta och förslag är predikat på en matchning i indexet. Tjänsten erbjuder inte frågor som returnerar noll resultat.

Om du vill implementera dessa upplevelser i Azure Kognitiv sökning behöver du:

+ En *förslags* definition som är inbäddad i index schemat.
+ En *fråga* som anger [Autoavsluta](/rest/api/searchservice/autocomplete) -eller [Suggestions](/rest/api/searchservice/suggestions) -API: et för begäran.
+ En *gränssnitts kontroll* som hanterar interaktioner från sökning till typ i klient programmet. Vi rekommenderar att du använder ett befintligt JavaScript-bibliotek för detta ändamål.

I Azure Kognitiv sökning hämtas kompletterade frågor och föreslagna resultat från Sök indexet från valda fält som du har registrerat med en förslags ställare. En förslags ställare är en del av indexet och anger vilka fält som ska tillhandahålla innehåll som antingen fyller i en fråga, föreslår ett resultat eller båda. När indexet skapas och läses in skapas en förslags data struktur internt för att lagra prefix som används för att matcha delar av frågor. För förslag bör du välja lämpliga fält som är unika eller som minst inte upprepas. Mer information finns i [skapa en förslags ställare](index-add-suggesters.md).

Resten av den här artikeln fokuserar på frågor och klient kod. Den använder Java Script och C# för att illustrera viktiga punkter. REST API exempel används för att kortfattat presentera varje åtgärd. Länkar till slut punkts kod exempel finns i [Nästa steg](#next-steps).

## <a name="set-up-a-request"></a>Konfigurera en begäran

Element i en begäran inkluderar en av Sök-som-typ-API: erna, en partiell fråga och en förslags ställare. Följande skript visar komponenter i en begäran med hjälp av Autoavsluta-REST API som exempel.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

"SuggesterName" ger dig de förslags bara fält som används för att slutföra villkor eller förslag. För förslag i synnerhet bör fält listan bestå av de som erbjuder tydliga val mellan matchande resultat. På en plats som säljer dator spel kan fältet vara spelets rubrik.

Parametern "search" innehåller den partiella frågan där tecken matas in i förfrågan via jQuery-kontrollen för automatisk komplettering. I exemplet ovan är "minecraf" en statisk illustration av vad kontrollen kan ha gått till.

API: erna tillämpar inte minimi kraven på längd på den partiella frågan. Det kan vara så litet som ett enda steg. JQuery komplettera automatiskt innehåller dock en minimilängd. Minst två eller tre tecken är typiska.

Matchningar är i början av en term var som helst i Indatasträngen. Med "Quick Jansson Fox" kommer både Autoavsluta och förslag att matchas på vissa versioner av "The", "Quick", "brun" eller "Fox", men inte i delar av infix-termer som "raden" eller "ox". Dessutom ställer varje matchning över omfånget för underordnade expansionar. En partiell fråga om "Quick br" kommer att matchas på "Quick Jansson" eller "Quick bröd", men varken "brun" eller "bröd" kan vara detsamma om inte "Quick" föregår dem.

### <a name="apis-for-search-as-you-type"></a>API: er för sökning efter typ

Följ dessa länkar för referens sidorna REST och .NET SDK:

+ [Förslag REST API](/rest/api/searchservice/suggestions) 
+ [Autoavsluta-REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestAsync-metod](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync-metod](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Strukturera ett svar

Svar för Autoavsluta och förslag är vad du kan förväntat dig för mönstret: [Autoavsluta](/rest/api/searchservice/autocomplete#response) returnerar en lista med villkor, [förslag](/rest/api/searchservice/suggestions#response) returnerar villkor plus ett dokument-ID så att du kan hämta dokumentet (Använd sökverktygets API för att hämta det [aktuella dokumentet för](/rest/api/searchservice/lookup-document) en informations sida).

Svaren är formade för parametrarna på begäran:

+ För automatisk komplettering ställer du in [autocompleteMode](/rest/api/searchservice/autocomplete#query-parameters) för att avgöra om text komplettering sker på en eller två villkor. 

+ Ange [$Select](/rest/api/searchservice/suggestionse#query-parameters) för att returnera fält som innehåller unika eller särskiljande värden, t. ex. namn och beskrivning, för förslag. Undvik fält som innehåller dubblettvärden (till exempel en kategori eller stad).

Följande ytterligare parametrar gäller för både Autoavsluta och förslag, men är kanske mer nödvändiga för förslag, särskilt när en förslags ställare innehåller flera fält.

| Parameter | Användning |
|-----------|-------|
| searchFields | Begränsa frågan till vissa fält. |
| $filter | Använd matchnings villkor i resultat uppsättningen ( `$filter=Category eq 'ActionAdventure'` ). |
| $top | Begränsa resultatet till ett angivet tal ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Lägg till användar interaktions kod

Att automatiskt fylla en frågeterm eller släppa en lista över matchande länkar kräver användar interaktions kod, vanligt vis Java Script, som kan använda förfrågningar från externa källor, t. ex. Autoavsluta-eller förslags frågor mot ett Azure Search kognitivt index.

Även om du kan skriva den här koden internt är det mycket enklare att använda funktioner från befintliga JavaScript-bibliotek. Den här artikeln nämner två, en för förslag och en annan för automatisk komplettering. 

+ [Autocomplete-widgeten (JQUERY UI)](https://jqueryui.com/autocomplete/) visas i förslags kodfragmentet. Du kan skapa en sökruta och sedan referera till den i en JavaScript-funktion som använder widgeten Autoavsluta. Egenskaperna i widgeten anger källan (en Autoavsluta-eller Suggestions-funktion), minimal längd på angivna tecken innan åtgärden tas och placering.

+ [XDSoft-plugin-programmet för automatisk komplettering](https://xdsoft.net/jqplugins/autocomplete/) visas i kodfragmentet för automatisk komplettering.

Vi använder dessa bibliotek för att bygga sökrutan som stöder både förslag och Autoavsluta. Indata som samlas in i sökrutan är kopplade till förslag och åtgärder för automatisk komplettering.

## <a name="suggestions"></a>Förslag

Det här avsnittet vägleder dig genom en implementering av föreslagna resultat som börjar med Sök Rute definitionen. Den visar också hur och skript som anropar det första biblioteket för Java Script-komplettering som refereras i den här artikeln.

### <a name="create-a-search-box"></a>Skapa en sökruta

Om du antar [JQUERY UI-biblioteket](https://jqueryui.com/autocomplete/) och ett MVC-projekt i C# kan du definiera sökrutan med hjälp av Java Script i filen **index. cshtml** . Biblioteket lägger till interaktionen sökning efter typ i sökrutan genom att göra asynkrona anrop till MVC-kontrollanten för att hämta förslag.

I **index. cshtml** under mappen \Views\Home kan en linje för att skapa en sökruta vara följande:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Det här exemplet är en enkel text ruta med en klass för formatering, ett ID som ska refereras till av Java Script och platshållartext.  

I samma fil bäddar du in Java Script som refererar till sökrutan. Följande funktion anropar det rekommenderade API: t, som begär föreslagna matchnings dokument baserat på indata från delvis term:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

I `source` beskrivs funktionen Komplettera automatiskt i JQUERY UI där du kan hämta listan över objekt som ska visas under sökrutan. Eftersom projektet är ett MVC-projekt anropas funktionen **föreslå** i **HomeController. cs** som innehåller logiken för att returnera fråge förslag. Den här funktionen skickar också några parametrar för att kontrol lera högdagrar, fuzzy Matching och term. JavaScript-API:et för automatisk komplettering lägger till term-parametern.

Det `minLength: 3` säkerställer att rekommendationerna endast visas när det finns minst tre tecken i sökrutan.

### <a name="enable-fuzzy-matching"></a>Aktivera fuzzy-matchning

Med fuzzy-sökningar kan du få resultat för nära matchningar även om användaren stavar fel på ett ord i sökrutan. Redigera avståndet är 1, vilket innebär att det kan finnas en maximal avvikelse på ett av tecknen mellan användarindata och en matchning. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Aktivera markering

Markeringen använder tecken formatet för de tecken i resultatet som motsvarar indatatypen. Om t. ex. den partiella indatamängden är "Micro" visas resultatet **som** mikrosoft, **Micro**-omfattning och så vidare. Markering baseras på parametrarna HighlightPreTag och HighlightPostTag, definierade infogade med förslags funktionen.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Funktionen föreslå

Om du använder C# och ett MVC-program är **HomeController. cs** under katalogen controllers där du kan skapa en klass för föreslagna resultat. I .NET baseras en förslags funktion på [metoden SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync). Mer information om .NET SDK finns i [så här använder du Azure kognitiv sökning från ett .NET-program](search-howto-dotnet-sdk.md).

`InitSearch`Metoden skapar en autentiserad HTTP-index-klient till Azure kognitiv sökning-tjänsten. Egenskaperna för [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) -klassen avgör vilka fält som genomsöks och returneras i resultaten, antalet matchningar och om fuzzy Matching används. 

För automatisk komplettering är en partiell matchning begränsad till ett redigerings avstånd (ett utelämnat eller felplacerat). Observera att fuzzy-matchning i frågor för Komplettera automatiskt kan ibland ge oväntade resultat beroende på index storlek och hur det är shardade. Mer information finns i avsnittet om [partition-och horisontell partitionering-koncept](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

Funktionen SuggestAsync tar två parametrar som avgör om träff markeringar returneras eller fuzzy-matchning används utöver Sök term ingången. Upp till åtta matchningar kan inkluderas i föreslagna resultat. Metoden skapar ett [SuggestOptions-objekt](/dotnet/api/azure.search.documents.suggestoptions), som sedan skickas till föreslå API. Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

## <a name="autocomplete"></a>Komplettera automatiskt

Hittills har Sök-UX-koden centrerats på förslag. Nästa kodblock visar komplettera automatiskt med funktionen XDSoft jQuery UI komplettera automatiskt och skickar en begäran om automatisk komplettering av Azure-Kognitiv sökning. Som med förslag, i ett C#-program, går kod som stöder användar interaktionen i **index. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funktionen Komplettera automatiskt

Autoavsluta baseras på AutocompleteAsync- [metoden](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Som med förslag kan det här kod blocket placeras i filen **HomeController. cs** .

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

Funktionen Autoavsluta tar search term-indatamängden. Metoden skapar ett [AutoCompleteParameters-objekt](/rest/api/searchservice/autocomplete). Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

## <a name="next-steps"></a>Nästa steg

Följ dessa länkar för slut punkt till slut punkts instruktioner eller kod som demonstrerar både sökning efter typ-upplevelser. Exemplet demonstrerar hybrid implementeringar av förslag och Autoavsluta tillsammans.

+ [Självstudie: skapa din första app i C# (Lektion 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C#-kod exempel: Azure-Search-dotNet-samples/Create-First-app/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead)