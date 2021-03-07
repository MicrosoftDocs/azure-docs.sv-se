---
title: 'Självstudie: skapa en webbapp på en sida med hjälp av API för nyhetssökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här självstudien för att skapa ett enkelsidigt program som kan skicka sökfrågor till API:et för nyhetssökning i Bing och visa resultaten på webbsidan.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: cb9e68efd27deb3bf66d3c286c0cd7a128d8bf59
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430589"
---
# <a name="tutorial-create-a-single-page-web-app"></a>Självstudie: skapa en webbapp med en sida

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med API för nyhetssökning i Bing kan du söka på webben och få resultat av nyhetstyp som är relevanta för en sökfråga. I den här självstudien skapar vi ett enkelsidigt program som använder API för nyhetssökning i Bing för att visa sökresultat på sidan. Programmet innehåller komponenterna HTML, CSS och JavaScript. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html).

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> JSON- och HTTP-huvudena längst ned på sidan när du klickar visar JSON-svar och information om HTTP-begäran. Den här informationen kan vara användbar när du utforskar tjänsten.

I den här självstudieappen visas hur du:
> [!div class="checklist"]
> * Anropar API för nyhetssökning i Bing med JavaScript
> * Skickar sökalternativ till API:et för nyhetssökning i Bing
> * Visar nyhetssökningsresultat från fyra kategorier: valfri typ, företag, hälsa eller politik från tidsintervall på 24 timmar, den senaste veckan, en månad eller all tillgänglig tid
> * bläddrar igenom sökresultat
> * hanterar Bing-klient-ID och prenumerationsnyckeln för API:et
> * hanterar fel som kan uppstå.

Självstudiesidan är helt självständigt. Den använder inte några externa ramverk, formatmallar eller bildfiler. Den använder endast JavaScript-språkfunktioner som stöds och fungerar med aktuella versioner av alla större webbläsare.


## <a name="prerequisites"></a>Förutsättningar

Om du vill följa med i själv studie kursen behöver du prenumerations nycklar för Bing-sökning-API: et. Om du inte har dessa måste du skapa dem:

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.

## <a name="app-components"></a>Appkomponenter
Som andra enkelsidiga webbappar innehåller den här självstudiekursprogrammet tre delar:

> [!div class="checklist"]
> * HTML – definierar struktur och innehåll på sidan
> * CSS – definierar utseendet på sidan
> * JavaScript – definierar beteendet för sidan.

Större delen av HTML och CSS är konventionell, så det tas inte upp i självstudien. HTML-koden innehåller sökformuläret där användaren anger en fråga och väljer sökalternativ. Formuläret är kopplat till JavaScript som i själva verket utför en sökning med hjälp av attributet `onsubmit` för `<form>`-taggen:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
`onsubmit`-hanteraren returnerar `false`, som ser till att formuläret inte skickas till en server. JavaScript-koden samlar in nödvändig information i formuläret och sökningen.

HTML-koden innehåller också avdelningar (HTML `<div>`-taggar) där sökresultatet visas.

## <a name="managing-subscription-key"></a>Hantera prenumerationsnyckel

För att undvika att lägga till prenumerationsnyckeln för API:et för Bing-sökning i koden använder vi webbläsarens beständiga lagring för att lagra nyckeln. Innan nyckeln lagras efterfrågar vi användarens nyckel. Om nyckeln senare avvisas av API:et ogiltigförklarar vi den lagrade nyckeln så att användaren tillfrågas igen.

Vi definierar funktionerna `storeValue` och `retrieveValue` med antingen objektet `localStorage` (inte i alla webbläsare) eller en cookie. Funktionen `getSubscriptionKey()` använder dessa funktioner för att lagra och hämta användarens nyckel. Du kan använda den globala slut punkten nedan eller den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```
HTML-taggen `<form>``onsubmit` anropar `bingWebSearch`-funktionen för att returnera sökresultat. `bingWebSearch` använder `getSubscriptionKey()` för att autentisera varje fråga. Som du ser i den föregående definitionen ber `getSubscriptionKey` användaren om nyckeln om nyckeln inte har registrerats. Nyckeln lagras sedan för fortlöpande användning av programmet.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Välja sökalternativ
Följande bild visar frågetextrutan och alternativ som definierar en sökning efter nyheter om skolfinansiering.

![Alternativ för nyhetssökning i Bing](media/news-search-categories.png)

HTML-formuläret innehåller element med följande namn:

|Element|Beskrivning|
|-|-|
| `where` | En listruta för att välja marknad (plats och språk) som används för sökningen. |
| `query` | Textfältet för att ange sökvillkor. |
| `category` | Kryssrutorna för att främja olika typer av resultat. Om du till exempel främjar hälsa, höjs rangordningen för hälsonyheter. |
| `when` | Listruta för att valfritt begränsa sökningen till den senaste dagen, veckan eller månaden. |
| `safe` | En kryssruta som anger om du vill använda Bing SafeSearch-funktionen för att filtrera bort ”vuxeninnehåll”. |
| `count` | Dolt fält. Antal sökresultat som returneras för varje begäran. Ändra om du vill visa färre eller fler resultat per sida. |
| `offset`|  Dolt fält. Förskjutningen av det första sökresultatet i begäran. Används för växling. Den återställs till `0` vid en ny begäran. |

> [!NOTE]
> Webbsökning i Bing erbjuder andra frågeparametrar. Vi använder bara några av dem.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Till exempel kan parametern `SafeSearch` i ett faktiskt API-anrop vara `strict`, `moderate`, eller `off`, med `moderate` som standardvärde. Vårt formulär använder en kryssruta som bara har två tillstånd. JavaScript-koden konverterar den här inställningen till antingen `strict` eller `off` (`moderate` används inte).

## <a name="performing-the-request"></a>Utföra förfrågan
Beroende på frågan, alternativsträngen och API-nyckeln använder funktionen `BingNewsSearch` ett `XMLHttpRequest`-objekt för att göra begäran till Bing-slutpunkten för nyhetssökning.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```
När HTTP-begäran har slutförts anropar JavaScript `load`-händelsehanteraren, funktionen `handleBingResponse()`, för att hantera en lyckad HTTP GET-begäran till API:et. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "News") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> En lyckad HTTP-begäran betyder *inte* nödvändigtvis att själva sökningen lyckades. Om ett fel uppstår i sökåtgärden returnerar API:et för nyhetssökning i Bing en icke-200-HTTP-statuskod och inkluderar felinformation i JSON-svaret. Om begäran var begränsad returnerar API:et ett tomt svar.

En stor del av koden i de båda föregående funktionerna är dedikerade för felhantering. Fel kan inträffa i följande steg:

|Fas|Potentiella fel|Hanterat av|
|-|-|-|
|Skapa objekt för JavaScript-begäran|Ogiltig URL|`try`/`catch` blockera|
|Skapa begäran|Nätverksfel, avbrutna anslutningar|Händelsehanterare för `error` och `abort`|
|Genomföra sökningen|Ogiltig begäran, ogiltig JSON, hastighetsbegränsningar|tester i händelsehanterare för `load`|

Fel hanteras genom att anropa `renderErrorMessage()` med all känd information om felet. Om svaret klarar alla feltester anropar vi `renderSearchResults()` för att visa sökresultatet på sidan.

## <a name="displaying-search-results"></a>Visa sökresultat
Den huvudsakliga funktionen för att visa sökresultatet är `renderSearchResults()`. Den här funktionen tar den JSON som returnerades av tjänsten för nyhetssökning i Bing och återger nyhetsresultatet och relaterade sökningar, om det finns några.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
De viktigaste resultaten från sökningen returneras som `value`-objektet på den översta nivån i JSON-svaret. Vi skickar dem till vår funktion `renderResults()`, som itererar dem och anropar en separat funktion för att rendera varje objekt i HTML-format. Den resulterande HTML-filen returneras till `renderSearchResults()`, där den infogas i `results`-divisionen på sidan.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
API:et för nyhetssökning i Bing returnerar upp till fyra olika typer av relaterade resultat i sitt respektive toppnivåobjekt. De är:

|Relation|Description|
|-|-|
|`pivotSuggestions`|Frågor som ersätter ett pivotord i den ursprungliga sökningen med ett annat. Om du till exempel söker efter ”röda blommor” kan ett pivotord vara ”röda”, och ett pivotförslag kan vara ”gula blommor”.|
|`queryExpansions`|Frågor som begränsar den ursprungliga sökningen genom att lägga till fler termer. Om du exempelvis söker efter ”Microsoft Surface” kan en frågeexpansion vara ”Microsoft Surface Pro”.|
|`relatedSearches`|Frågor som också har angetts av andra användare som registrerade den ursprungliga sökningen. Om du till exempel söker efter ”Mount Rainier” kan en relaterad sökning vara ”Mt. Saint Helens.”|
|`similarTerms`|Frågor vars innebörd liknar den ursprungliga sökningen. Om du exempelvis söker efter ”skolor” kan en liknande term vara ”utbildning”.|

Som tidigare visats i `renderSearchResults()` renderar vi endast `relatedItems`-förslag och placerar de resulterande länkarna i sidans sidopanel.

## <a name="rendering-result-items"></a>Rendering av resultatobjekt

I JavaScript-koden innehåller `searchItemRenderers`-objektet *renderare:* funktioner som genererar HTML för varje typ av sökresultat.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
En funktion för rendering kan acceptera följande parametrar:

|Parameter|Beskrivning|
|-|-|
|`item`| JavaScript-objekt som innehåller objektets egenskaper, som dess webbadress och en beskrivning.|
|`index`| Index för resultatobjektet i en samling.|
|`count`| Antal objekt i sökresultatets objektsamling.|

Parametrarna `index` och `count` kan användas till att numrera resultat, för att generera särskilda HTML-filer för början eller slutet av en samling, för att infoga radbrytningar efter ett visst antal objekt och så vidare. Om en renderare inte behöver den här funktionen behöver den inte godkänna dessa två parametrar.

`news`Åter givningen visas i följande JavaScript-utdrag:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
Funktionen för nyhetsrendering:
> [!div class="checklist"]
> * Skapar en styckestagg och tilldelar den till klassen `news` och skickar den till html-matrisen.
> * Beräknar miniatyrbildstorlek (bredden är högst 60 bildpunkter, höjden beräknas proportionellt).
> * Skapar HTML `<img>`-taggen för att visa miniatyrbilden. 
> * Skapar HTML `<a>`-taggar som länkar till bilden och den sida som innehåller den.
> * Skapar beskrivning som visar information om bilden och den plats som den finns på.

Storlek på miniatyrbilderna används i både `<img>`-taggen och fälten `h` och `w` i miniatyrbildens webbadress. Sedan levererar [Bings miniatyrbildtjänst](../bing-web-search/resize-and-crop-thumbnails.md) en miniatyrbild med exakt den storleken.

## <a name="persisting-client-id"></a>Bestående klient-ID
Svar från API:er för Bing Search kan innehålla ett `X-MSEdge-ClientID`-huvud som ska skickas tillbaka till API:et med efterföljande förfrågningar. Om flera API:er för Bing-sökning används ska samma klient-ID användas för dem om möjligt.

När `X-MSEdge-ClientID`-huvudet tillhandahålls kan Bing-API:er associera alla sökningar för en användare, vilket innebär två viktiga fördelar.

Först hjälper Bing-sökmotorn till med att tillämpa tidigare kontexter på sökningarna för att hitta resultat som bättre tillfredsställer användaren. Om en användare tidigare har sökt efter termer som exempelvis relaterar till segling kan en senare sökning efter ”knopar” returnera information om knopar som används vid segling.

Därefter väljer Bing slumpmässigt ut användare som ska prova nya funktioner innan de blir allmänt tillgängliga. Om samma klient-ID tillhandahålls med varje begäran säkerställer det att användare som ser funktionen alltid ser den. Utan klient-ID kan användaren se en funktion som sedan försvinner, till synes slumpmässigt, i sökresultatet.

Säkerhetsprinciper för webbläsaren (CORS) kan hindra att `X-MSEdge-ClientID`-huvudet visas för JavaScript. Den här begränsningen uppstår när söksvaret har ett annat ursprung än sidan som begärt det. I en produktionsmiljö bör du hantera den här principen genom att lägga upp ett serverskript som gör API-anrop på samma domän som webbsidan. Eftersom skriptet har samma ursprung som webbsidan är sedan `X-MSEdge-ClientID`-huvudet tillgängligt för JavaScript.

> [!NOTE]
> Du bör utföra begäran på serversidan i ett produktionsklart webbprogram. I annat fall måste API-nyckeln för Bing-sökning inkluderas i webbsidan där den är tillgänglig för alla som visar källan. Du debiteras för all användning under din API-prenumerationsnyckel, även begäranden som görs av obehöriga personer, så det är viktigt att inte exponera nyckeln.

I utvecklingssyfte kan du begära API för webbsökning i Bing via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers` huvud som tillåter svarshuvuden och gör dem tillgängliga för Java Script.

Det är enkelt att installera en CORS-proxy för att tillåta att självstudien får åtkomst till klientens ID-huvud. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Sedan kör du följande kommando i ett kommandofönster:

```console
npm install -g cors-proxy-server
```

Ändra sedan Webbsökning i Bing-slutpunkten i HTML-filen till: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Slutligen startar du CORS-proxyn med följande kommando:

```console
cors-proxy-server
```

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [API-referens för Bing-nyhetssökning](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)