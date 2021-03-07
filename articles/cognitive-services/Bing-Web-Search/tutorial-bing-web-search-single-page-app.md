---
title: 'Självstudier: Skapa ensideswebbapp – API för webbsökning i Bing'
titleSuffix: Azure Cognitive Services
description: Den här ensidesappen visar hur API för webbsökning i Bing kan användas för att hämta, analysera och visa relevanta sökresultat i en ensidesapp.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca542ac6c5184a0307a9293fada305a6c6356edd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434176"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Självstudier: Skapa en ensidesapp med hjälp av API för webbsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Den här ensidesappen visar hur du hämtar, analyserar och visar sökresultat från API för webbsökning i Bing. Självstudien använder formaterad HTML och CSS och fokuserar på JavaScript-koden. HTML-, CSS- och JS-filer finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) med snabbstartsinstruktioner.

Den här exempelappen kan:

> [!div class="checklist"]
> * Anropa API för webbsökning i Bing med sökalternativ
> * Visa webb, bild, nyheter och videoresultat
> * Sidnumrera resultat
> * Hantera prenumerationsnycklar
> * Hantera fel

För att använda den här appen krävs ett [Azure Cognitive Services-konto](../cognitive-services-apis-create-account.md) med API:er för Bing-sökresultat.

## <a name="prerequisites"></a>Förutsättningar

Här följer några saker som du kan behöva för att köra appen:

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.

* Node.js 8 eller senare

Det första steget är att klona lagringsplatsen med exempelappens källkod.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Kör sedan `npm install`. För den här självstudien är Express.js det enda beroendet.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>Appkomponenter

Exempelappen som vi bygger består av fyra delar:

* `bing-web-search.js` - Vår Express.js-app. Den hanterar begäran/svarslogik och routning.
* `public/index.html` - Stommen i vår app. Den definierar hur data ska visas för användaren.
* `public/css/styles.css` -Definierar sidans format, såsom teckensnitt, färger, textstorlek.
* `public/js/scripts.js` - Innehåller logik för att göra begäranden till API för webbsökning i Bing, hantera prenumerationsnycklar, hantera och parsa svar och visa resultat.

Den här självstudien fokuserar på `scripts.js` och den logik som krävs för att anropa API för webbsökning i Bing och hantera svaret.

## <a name="html-form"></a>HTML-formulär

`index.html` innehåller ett formulär som gör det möjligt för användare att söka efter och välja alternativ för sökning. Attributet `onsubmit` utlöses när formuläret skickas och anropar den `bingWebSearch()`-metod som definieras i `scripts.js`. Den tar tre argument:

* Sökfråga
* Valda alternativ
* Prenumerationsnyckel

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Frågealternativ

HTML-formuläret innehåller alternativ som mappar till frågeparametrar i [API för webbsökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters). Den här tabellen innehåller en detaljerad analys av hur användarna kan filtrera sökresultat med hjälp av exempelappen:

| Parameter | Beskrivning |
|-----------|-------------|
| `query` | Ett textfält för att ange en frågesträng. |
| `where` | En nedrullningsbar meny för att välja marknaden (plats och språk). |
| `what` | Kryssrutorna för att flytta upp specifika resultattyper. Uppgradera bilder ökar till exempel rangordningen av bilder i sökresultaten. |
| `when` | En nedrullningsbar meny som används för att begränsa sökresultaten till dagens datum, den här veckan eller den här månaden. |
| `safe` | En kryssruta för att aktivera Bing SafeSearch som filtrerar ut innehåll som är olämpligt för barn. |
| `count` | Dolt fält. Antal sökresultat som returneras för varje begäran. Ändra det här värdet om du vill visa färre eller fler resultat per sida. |
| `offset` | Dolt fält. Förskjutningen av det första sökresultatet i begäran, vilket används för växling. Den återställs till `0` för varje ny begäran. |

> [!NOTE]
> API för webbsökning i Bing erbjuder ytterligare frågeparametrar för att begränsa sökresultaten. I det här exemplet används bara några få. En fullständig lista över tillgängliga parametrar finns i [referensen API för webbsökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters).

Funktionen `bingSearchOptions()` konverterar dessa alternativ för att matcha det format som krävs av API för sökning i Bing.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`SafeSearch` kan anges till `strict`, `moderate` eller `off`, med `moderate` som standardinställningen för webbsökning i Bing. Det här formuläret använder en kryss ruta som har två tillstånd: `strict` eller `moderate` .

Om något av kryssrutorna **befordra** är markerade har parametern `answerCount` lagts till i frågan. `answerCount` krävs när du använder parametern `promote`. I det här kodfragmentet anges värdet till `9` för att returnera alla tillgängliga resultattyper.
> [!NOTE]
> Att uppgradera en resultattyp *garanterar* inte att den tas med i sökresultatet. I stället ökar befordran rangordningen för denna typ av resultat i förhållande till deras vanliga rangordning. För att begränsa sökningen till olika typer av resultat, använder du frågeparametern `responseFilter` eller anropar en mer specifik slutpunkt för bildsökning i Bing t.ex nyhetssökning i Bing.

Frågeparametrarna `textDecoration` och `textFormat` är hårdkodade i skriptet och gör så att söktermen är i fetstilt i sökresultatet. Dessa parametrar behövs inte.

## <a name="manage-subscription-keys"></a>Hantera prenumerationsnycklar

För att undvika hårdkodning av prenumerationsnyckeln för API:er för sökning i Bing använder den här exempelappen beständig lagring i en webbläsare för att lagra prenumerationsnyckeln. Om ingen prenumerationsnyckel lagras, uppmanas användaren att ange en. Om prenumerationsnyckeln har avvisats av API:et, ombeds användaren att ange en prenumerationsnyckel på nytt.

Funktionen `getSubscriptionKey()` använder funktionerna `storeValue` och `retrieveValue` för att lagra och hämta en användares prenumerationsnyckel. Dessa funktioner använder objektet `localStorage`, om det stöds, eller cookies.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Som vi såg tidigare, när formuläret skickas utlöses `onsubmit` och anropar `bingWebSearch`. Den här funktionen initierar och skickar en begäran. `getSubscriptionKey` anropas vid varje överföring för att autentisera begäran.

## <a name="call-bing-web-search"></a>Anropa webbsökning i Bing

Baserat på frågan, alternativsträngen och prenumerationsnyckeln, skapar funktionen `BingWebSearch` ett `XMLHttpRequest`-objekt för att anropa slutpunkten för webbsökning i Bing.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Efter en lyckad begäran, utlöses `load`-händelsehanteraren och anropar funktionen `handleBingResponse`. `handleBingResponse` parsar resultatobjektet, visar resultaten och innehåller fellogik för misslyckade förfrågningar.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> En lyckad HTTP-begäran betyder *inte* att själva sökningen lyckades. Om ett fel uppstår i sökåtgärden returnerar API för webbsökning i Bing en icke-200-HTTP-statuskod och inkluderar felinformation i JSON-svaret. Om begäran var begränsad returnerar API:et ett tomt svar.

En stor del av koden i de båda föregående funktionerna är dedikerade för felhantering. Fel kan inträffa i följande steg:

| Fas | Potentiella fel | Hanterat av |
|-------|--------------------|------------|
| Skapa objektbegäran | Ogiltig URL | `try` / `catch` blockera |
| Skapa begäran | Nätverksfel, avbrutna anslutningar | Händelsehanterare för `error` och `abort` |
| Genomföra sökningen | Ogiltig begäran, ogiltig JSON, hastighetsbegränsningar | tests i `load` händelsehanterare |

Fel hanteras genom att anropa `renderErrorMessage()`. Om svaret klarar samtliga av dessa feltest, anropas `renderSearchResults()` för att visa sökresultaten.

## <a name="display-search-results"></a>Visa sökresultat

Det finns [användar- och visningskrav](./use-display-requirements.md) för resultaten som returnerades av API för webbsökning i Bing. Eftersom ett svar kan innehålla olika resultattyper, räcker det inte att gå igenom den översta `WebPages`-samlingen. I stället använder exempelappen `RankingResponse` för att sortera resultaten till specifikationen.

> [!NOTE]
> Om du bara vill ha en resultattyp, använder du frågeparametern `responseFilter` eller överväger att använda en av de andra slutpunkterna för sökning i Bing, till exempel bildsökning i Bing.

Varje svar har ett `RankingResponse`-objekt som kan innehålla upp till tre samlingar: `pole`, `mainline` och `sidebar`. `pole`, om det finns, är det mest relevanta sökresultatet och måste visas på en framträdande plats. `mainline` innehåller de flesta av sökresultaten och visas omedelbart efter `pole`. `sidebar` innehåller extra sökresultat. Om möjligt ska de här resultaten visas i sidopanelen. Om skärmens gränser gör det opraktiskt med en sidopanel, visas de här resultaten efter `mainline`-resultaten.

Varje `RankingResponse` innehåller en `RankingItem`-matris som anger hur resultat måste ordnas. Vårt exempel använder parametrarna `answerType` och `resultIndex` för att identifiera resultatet.

> [!NOTE]
> Det finns andra sätt att identifiera och rangordna resultat. Mer information finns i [Använda rangordning för att visa resultat](rank-results.md).

Låt oss ta en titt på koden:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

Funktionen `renderResultsItems()` går igenom objekten i varje `RankingResponse`-samling, mappar varje rangordningsresultat till ett sökresultat med hjälp av värdena `answerType` och `resultIndex` och anropar lämplig renderingsfunktion för att generera HTML. Om `resultIndex` inte har angetts för ett objekt, går `renderResultsItems()` igenom alla resultat av denna typ och anropar renderingsfunktionen för varje objekt. Resulterande HTML matas in i aktuellt `<div>`-element i `index.html`.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>Granska renderingsfunktioner

I vår exempelapp innehåller `searchItemRenderers`-objektet funktioner som genererar HTML för varje typ av sökresultat.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> Exempelappen har renderare för webbsidor, nyheter, bilder, videoklipp och relaterade sökningar. Ditt program behöver renderare för alla typer av resultat det kan få, vilket kan omfatta beräkningar, stavningsförslag, entiteter, tidszoner och definitioner.

Några av funktionerna för rendering accepterar endast parametern `item`. Andra accepterar extraparametrar som kan användas för att återge objekt på olika sätt beroende på kontext. Ett renderingsprogram som inte använder den här informationen behöver inte godkänna dessa parametrar.

Kontextargumenten är:

| Parameter  | Beskrivning |
|------------|-------------|
| `section` | Resultatavsnittet (`pole`, `mainline`, eller `sidebar`) i vilket objektet visas. |
| `index`<br>`count` | Tillgängligt när `RankingResponse`-objektet anger att alla resultat i en viss samling ska visas; `undefined` annars. Index för objektet i en samling och det totala antalet objekt i samlingen. Du kan använda den här informationen för att numrera resultaten för att generera olika HTML för det första eller sista resultatet och så vidare. |

I exempelappen, använder både renderare `images` och `relatedSearches` kontextargumenten för att anpassa genererad HTML. Låt oss ta en närmare titt på renderare `images`:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

Bildåtergivare:

* Beräknar storleken på miniatyrbilderna (bredd varierar, medan höjd är högst 60 bildpunkter).
* Infogar den HTML som föregår bildresultatet baserat på kontext.
* Skapar en HTML `<a>`-tagg som länkar till den sida som innehåller bilden.
* Skapar HTML `<img>`-taggen för att visa miniatyrbilden.

Bildåtergivning använder variablerna `section` och `index` för att visa resultat på olika sätt beroende på var de förekommer. En radbrytning (`<br>`-tagg) infogas mellan bildresultat i sidopanelen, så att sidopanelen visar en kolumn med bilder. I andra avsnitt, föregås det första bildresultatet `(index === 0)` av en `<p>`-tagg.

Storlek på miniatyrbilderna används i både `<img>`-taggen och fälten `h` och `w` i miniatyrbildens webbadress. Attribut `title` och `alt` (en textbeskrivning av bilden) skapas från bildens namn och värdnamnet i URL:en.

Här är ett exempel på hur bilder visas i exempelappen:

![[Bing bildresultat]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Spara klient-ID:t

Svar från API:er för sökning i Bing kan innehålla ett `X-MSEdge-ClientID`-huvud som ska skickas tillbaka till API:et med varje efterföljande begäran. Om mer än ett av API:erna för sökning i Bing används av din app, kontrollerar du att samma klient-ID har skickats med varje begäran över tjänster.

Genom att tillhandahålla `X-MSEdge-ClientID`-huvudet kan Bing-API:er associera en användares sökningar. Först hjälper Bing-sökmotorn till med att tillämpa den senaste kontext på sökningarna för att hitta resultat som bättre tillfredsställer begäran. Om en användare tidigare har sökt efter termer som exempelvis relaterar till segling kan en senare sökning efter ”knopar” returnera information om knopar som används vid segling. Därefter väljer Bing slumpmässigt ut användare som ska prova nya funktioner innan de blir allmänt tillgängliga. Genom att tillhandahålla samma klient-ID med varje begäran säkerställs att användare som har valts för att se en funktion alltid ser den. Utan klient-ID kan användaren se en funktion som sedan försvinner, till synes slumpmässigt, i sökresultatet.

Webbläsarens säkerhetsprinciper, till exempel resursdelning för korsande ursprung (CORS), kan hindra exempelappen från att komma åt rubriken `X-MSEdge-ClientID`. Den här begränsningen uppstår när söksvaret har ett annat ursprung än sidan som begärt det. I en produktionsmiljö bör du hantera den här principen genom att lägga upp ett serverskript som gör API-anrop på samma domän som webbsidan. Eftersom skriptet har samma ursprung som webbsidan är sedan `X-MSEdge-ClientID`-huvudet tillgängligt för JavaScript.

> [!NOTE]
> Du bör utföra begäran på serversidan i ett produktionsklart webbprogram ändå. I annat fall måste API-prenumerationsnyckeln för Bing-sökning inkluderas i webbsidan där den är tillgänglig för alla som visar källan. Du debiteras för all användning under din API-prenumerationsnyckel, även begäranden som görs av obehöriga personer, så det är viktigt att inte exponera nyckeln.

För utvecklingssyften kan du göra en begäran via en proxyserver för CORS. Svaret från den här typen av proxy har ett `Access-Control-Expose-Headers` huvud som filtrerar svarshuvuden och gör dem tillgängliga för Java Script.

Det är enkelt att installera en CORS-proxy för att tillåta att exempelappen får åtkomst till klientens ID-huvud. Kör följande kommando:

```console
npm install -g cors-proxy-server
```

Ändra slutpunkten för webbsökning i Bing i `script.js` till:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Starta CORS-proxyn med det här kommandot:

```console
cors-proxy-server
```

Lämna kommandofönstret öppet medan du använder exempelappen. Om du stänger fönstret stoppas proxyn. I avsnittet utbyggbara HTTP-huvuden under sökresultaten bör sidhuvudet `X-MSEdge-ClientID` visas. Kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för API för webbsökning i Bing v7](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)