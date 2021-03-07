---
title: 'Självstudie: Enkelsidig webbapp med Entitetssökning i Bing'
titleSuffix: Azure Cognitive Services
description: I den här självstudien visas hur du använder API för entitetsökning i Bing i ett webb program med en enda sida.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: f2c15221268635ca1892a9292d5b0c208c13dd34
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426815"
---
# <a name="tutorial-single-page-web-app"></a>Självstudie: Enkelsidig webbapp

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med API:et för entitetsökning i Bing kan du söka efter information på webben om *entiteter* och *platser*. Du kan begära endera typ av resultat eller båda i en given fråga. Definitionerna av platser och entiteter visas nedan.

| Resultat | Description |
|-|-|
|Entiteter|Välkända personer, platser och saker som du hittar med hjälp av namn|
|Placerar|Restauranger, hotell och andra lokala företag som du hittar med hjälp av namn *eller* efter typ (italienska restauranger)|

I de här självstudierna skapar vi ett enkelsidigt program som använder API:et för entitetsökning i Bing för att visa sökresultat till höger på sidan. Programmet innehåller komponenterna HTML, CSS och JavaScript.

Med API:et kan du prioritera resultat efter plats. I en mobilapp kan du fråga enheten om dess egen plats. I webbappen kan du använda funktionen `getPosition()`. Det här anropet fungerar dock bara i säkra sammanhang och ger kanske inte en exakt plats. Dessutom vill användaren kanske söka efter entiteter nära en plats som inte är användarens egen plats.

Därför använder vår app Bing Maps-tjänsten för att hämta latitud och longitud från en användarangiven plats. Användaren kan sedan ange namnet på ett landmärke (”Space Needle”) eller en fullständig eller partiell adress (”New York City”) så ger Bing Maps API koordinaterna.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> JSON- och HTTP-huvudena längst ned på sidan visar JSON-svar och information om HTTP-begäran när de klickas. Den här informationen är användbar när du utforskar tjänsten.

I den här självstudieappen visas hur du:

> [!div class="checklist"]
> * Utföra ett API-anrop för entitetsökning i Bing i JavaScript
> * Utföra ett `locationQuery` API-anrop för entitetsökning i Bing i JavaScript
> * Skicka sökalternativ till API-anropen
> * Visa sökresultat
> * Hantera Bing-klient-ID och prenumerationsnyckeln för API:et
> * Hantera eventuella fel som kan uppstå

Självstudiekurssidan är helt självständigt. Den använder inte några externa ramverk, formatmallar eller ens bildfiler. Den använder endast JavaScript-språkfunktioner som stöds och fungerar med aktuella versioner av alla större webbläsare.

I den här självstudien diskuterar vi endast vissa delar av källkoden. Den fullständiga källkoden finns [på en separat sida](). Kopiera och klistra in den här koden i en textredigerare och spara den som `bing.html`.

> [!NOTE]
> Den här självstudien liknar i stora delar [självstudien om enkelsidiga app för Webbsökning i Bing](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), men den behandlar endast entitetssökresultat.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa med i själv studie kursen behöver du prenumerations nycklar för API: et för Bing-sökning och Bing Maps. 

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration:
  * <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Skapa en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
  * <a href="https://www.microsoft.com/maps/create-a-bing-maps-key.aspx"  title="Skapa en Visuellt innehåll resurs "  target="_blank"> skapa en Bing Maps-resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.

## <a name="app-components"></a>Appkomponenter

Som andra enkelsidiga webbappar innehåller det här självstudieprogrammet tre delar:

> [!div class="checklist"]
> * HTML – definierar struktur och innehåll på sidan
> * CSS – definierar utseendet på sidan
> * JavaScript – definierar beteendet för sidan.

Den här kursen täcker inte det mesta av HTML eller CSS i detalj eftersom de är förhållandevis enkla.

HTML-koden innehåller sökformuläret där användaren anger en fråga och väljer sökalternativ. Formuläret är kopplat till det JavaScript som i själva verket utför sökningen med hjälp av `<form>`-taggens attribut `onsubmit`:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

`onsubmit`-hanteraren returnerar `false`, som ser till att formuläret inte skickas till en server. JavaScript-koden utför den faktiska insamlingen av nödvändig information i formuläret och sökningen.

Sökningen görs i två faser. Om användaren har angett en platsbegränsning görs först en Bing Maps-fråga för att konvertera den till koordinater. Motringningen för den här frågan inleder sedan Entitetssökning i Bing-frågan.

HTML-koden innehåller också avdelningar (HTML `<div>`-taggar) där sökresultatet visas.

## <a name="managing-subscription-keys"></a>Hantera prenumerationsnycklar

> [!NOTE]
> Den här appen kräver prenumerationsnycklar för både API:et för Bing-sökning och API för Bing Maps.

För att undvika att lägga till prenumerationsnycklarna för API:et för Bing Search och API:et för Bing Maps i koden använder vi webbläsarens beständiga lagring för att lagra dem. Om någon av nycklarna inte har lagrats frågar vi efter den lagrar den för senare användning. Om nyckeln senare avvisas av API:et ogiltigförklarar vi den lagrade nyckeln så att användaren tillfrågas om den vid nästa sökning.

Vi definierar funktionerna `storeValue` och `retrieveValue` som använder antingen objektet `localStorage` (om webbläsaren stöder det) eller en cookie. Vår `getSubscriptionKey()`-funktion använder dessa funktioner för att lagra och hämta användarens nyckel. Du kan använda den globala slut punkten nedan eller den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

HTML-taggen `<body>` innehåller ett `onload`-attribut som anropar `getSearchSubscriptionKey()` och `getMapsSubscriptionKey()` när sidan har lästs in. Dessa anrop frågar omedelbart användaren om dess nycklar om nycklarna inte har angetts ännu.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Välja sökalternativ

![[Formulär för Entitetssökning i Bing]](media/entity-search-spa-form.png)

HTML-formuläret innehåller följande kontroller:

| Kontroll | Description |
|-|-|
|`where`|En listruta för att välja marknad (plats och språk) som används för sökningen.|
|`query`|Textfältet för att ange sökvillkor.|
|`safe`|En kryssruta som anger huruvida SafeSearch är aktiverat (begränsar ”vuxna” resultat)|
|`what`|En meny för att välja att söka efter entiteter, platser eller båda.|
|`mapquery`|Det textfältet där användaren kan ange en fullständig eller partiell adress, ett landmärken osv. för att hjälpa Entitetssökning i Bing att returnera mer relevanta resultat.|

> [!NOTE]
> Platsresultat är för närvarande endast tillgängliga i USA. Menyerna `where` och `what` innehåller kod för att framtvinga den här begränsningen. Om du väljer en icke-amerikansk marknad medan Platser har valts i menyn `what` ändras `what` till Vad som helst. Om du väljer Platser medan en icke-amerikansk marknad har valts i menyn `where` ändras `where` till USA.

Vår JavaScript-funktion `bingSearchOptions()` konverterar dessa fält till en partiell frågesträng för API:et för Bing-sökning.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Till exempel kan SafeSearch-funktionen vara `strict`, `moderate` eller `off`, där `moderate` är standardvärdet. Vårt formulär använder dock en kryssruta som bara har två tillstånd. JavaScript-koden konverterar den här inställningen till antingen `strict` eller `off` (vi använder inte `moderate`).

Fältet `mapquery` hanteras inte i `bingSearchOptions()` eftersom det används för Bing Maps-platsfrågan, inte för Entitetssökning i Bing.

## <a name="obtaining-a-location"></a>Hämta en plats

Bing Maps-API: et erbjuder en [ `locationQuery` metod](//msdn.microsoft.com/library/ff701711.aspx)som vi använder för att hitta latitud och longitud för den plats som användaren anger. Dessa koordinater skickas till API:et för entitetsökning i Bing med användarens begäran. Sökresultaten prioritera entiteter och platser som är nära den angivna platsen.

Vi kan inte komma åt API:et för Bing Maps med hjälp av en vanlig `XMLHttpRequest`-fråga i en webbapp eftersom tjänsten inte har stöd för frågor med olika ursprung. Som tur är stöder den JSONP (”P” står för ”padded”, dvs. utfylld). Ett JSONP-svar är ett vanligt JSON-svar som är omslutet i ett funktionsanrop. Begäran görs genom att en `<script>`-tagg infogas i dokumentet. (Inläsning av skript omfattas inte av säkerhetsprinciper för webbläsare.)

Funktionen `bingMapsLocate()` skapar och infogar `<script>`-taggen för frågan. `jsonp=bingMapsCallback`-segmentet av frågesträngen anger namnet på den funktion som ska anropas med svaret.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Om API:et för Bing Maps inte svarar anropas aldrig `bingMapsCallBack()`-funktionen. Normalt innebär det att `bingEntitySearch()` inte anropas och att entitetssökresultatet inte visas. För att undvika det här scenariot anger `bingMapsLocate()` även en timer för att anropa `bingEntitySearch()` efter fem sekunder. Det finns logik i motringningsfunktionen för att undvika att entitetssökningen utförs två gånger.

När frågan är klar anropas `bingMapsCallback()`-funktionen enligt begäran.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Utöver latitud och longitud kräver Entitetssökning i Bing-frågan en *radie* som anger precisionen hos platsinformationen. Vi beräknar radien med hjälp av *avgränsningsfältet* som anges i Bing Maps-svaret. Avgränsningsfältet är en rektangel som omger hela platsen. Om användaren till exempel anger `NYC` innehåller resultatet ungefärliga centrala koordinater för New York City och ett avgränsningsfält som omfattar staden. 

Först beräknar vi avstånden från de primära koordinaterna till var och en av fyra hörnen i avgränsningsfältet med hjälp av funktionen `haversineDistance()` (visas inte). Vi använder det största av dessa fyra avstånd som radien. Den minsta radien är en kilometer. Det här värdet används även som standard om inget avgränsningsfält anges i svaret.

Nu när vi har hämtat koordinaterna och radien anropar vi `bingEntitySearch()` för att utföra den faktiska sökningen.

## <a name="performing-the-search"></a>Genomföra sökningen

Baserat på frågan, en plats, en alternativsträng och API-nyckeln utför `BingEntitySearch()`-funktionen Entitetssökning i Bing-begäran.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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

När HTTP-begäran har slutförts anropar JavaScript vår `load`-händelsehanterare, funktionen `handleBingResponse()`, för att hantera en lyckad HTTP GET-begäran till API:et. 

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> En lyckad HTTP-begäran betyder *inte* nödvändigtvis att själva sökningen lyckades. Om ett fel uppstår i sökåtgärden returnerar API:et för entitetsökning i Bing en icke-200-HTTP-statuskod och inkluderar felinformation i JSON-svaret. Om begäran var begränsad returnerar API:et ett tomt svar.

En stor del av koden i de båda föregående funktionerna är dedikerade för felhantering. Fel kan inträffa i följande steg:

|Fas|Potentiella fel|Hanterat av|
|-|-|-|
|Skapa objekt för JavaScript-begäran|Ogiltig URL|`try`/`catch` blockera|
|Skapa begäran|Nätverksfel, avbrutna anslutningar|Händelsehanterare för `error` och `abort`|
|Genomföra sökningen|Ogiltig begäran, ogiltig JSON, hastighetsbegränsningar|tester i händelsehanterare för `load`|

Fel hanteras genom att anropa `renderErrorMessage()` med all känd information om felet. Om svaret klarar alla feltester anropar vi `renderSearchResults()` för att visa sökresultatet på sidan.

## <a name="displaying-search-results"></a>Visa sökresultat

API:et för entitetsökning i Bing [kräver att du visar resultat i en angiven ordning](../bing-web-search/use-display-requirements.md). Eftersom API: et kan returnera två olika typer av svar, är det inte tillräckligt att iterera genom den översta nivån `Entities` eller `Places` samlingen i JSON-svaret och visa dessa resultat. (Om du bara vill ha en typ av resultat använder du `responseFilter`-frågeparametern.)

I stället använder vi `rankingResponse`-samlingen i sökresultatet för att sortera resultaten för visning. Det här objektet refererar till objekt i samlingen `Entitiess` och/eller `Places`.

`rankingResponse` kan innehålla upp till tre samlingar för sökresultat som anges som `pole`, `mainline` och `sidebar`. 

Om `pole` finns är den det mest relevanta sökresultatet och bör visas på en framträdande plats. `mainline` refererar till den största delen av sökresultaten. Huvudresultat bör visas omedelbart efter `pole` (eller först om `pole` inte finns). 

Slutligen refererar `sidebar` till extra sökresultat. De kan visas i en faktisk sidopanel eller helt enkelt efter huvudresultaten. För självstudieappen har vi valt det senare.

Varje objekt i en `rankingResponse`-samling refererar till de faktiska sökresultatobjekten på två olika men likvärdiga sätt.

| Objekt | Beskrivning |
|-|-|
|`id`|`id` ser ut som en URL men ska inte användas för länkar. `id`-typen av ett rankningsresultat matchar `id` för antingen en ett sökresultatobjekt i en svarssamling *eller* en hel svarssamling (t.ex. `Entities`).
|`answerType`<br>`resultIndex`|`answerType` refererar till den svarssamling på toppnivå som innehåller resultatet (till exempel `Entities`). `resultIndex` refererar till resultatets index i den samlingen. Om `resultIndex` är utelämnas refererar rankningsresultatet till hela samlingen.

> [!NOTE]
> Mer information om den här delen av söksvaret finns i [Rangordna resultat](rank-results.md).

Du kan använda den metod för att hitta det refererade sökresultatobjekt som bäst passar ditt program. I självstudiekoden använder vi `answerType` och `resultIndex` för att hitta varje sökresultat.

Slutligen är det dags att titta på funktionen `renderSearchResults()`. Den här funktionen itererar över tre `rankingResponse`-samlingar som representerar de tre avsnitten i sökresultaten. För varje avsnitt anropar vi `renderResultsItems()` för att rendera resultaten för det avsnittet.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Rendering av resultatobjekt

I JavaScript-koden finns objektet `searchItemRenderers`, som innehåller *renderare:* funktioner som genererar HTML för varje typ av sökresultat.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

En funktion för rendering kan acceptera följande parametrar:

| Parameter | Beskrivning |
|-|-|
|`item`|JavaScript-objekt som innehåller objektets egenskaper, som dess webbadress och en beskrivning.|
|`index`|Index för resultatobjektet i en samling.|
|`count`|Antal objekt i sökresultatets objektsamling.|

Parametrarna `index` och `count` kan användas till att numrera resultat, för att generera särskilda HTML-filer för början eller slutet av en samling, för att infoga radbrytningar efter ett visst antal objekt och så vidare. Om en renderare inte behöver den här funktionen behöver den inte godkänna dessa två parametrar. I själva verket använder vi dem inte i renderarna för självstudieappen.

Låt oss ta en närmare titt på renderare `entities`:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Entitetsrenderarfunktionen:

> [!div class="checklist"]
> * Skapar `<img>`-HTML-taggen för att visa miniatyrbilden, om sådan finns. 
> * Skapar en HTML `<a>`-tagg som länkar till den sida som innehåller bilden.
> * Skapar beskrivning som visar information om bilden och den plats som den finns på.
> * Inkluderar entitetens klassificering med hjälp av visningstips, om sådana finns.
> * Inkluderar en länk till en Bing-sökning för att få mer information om entiteten.
> * Visar eventuell licensierings- eller attributionsinformation som krävs av datakällor.

## <a name="persisting-client-id"></a>Bestående klient-ID

Svar från API:er för Bing Search kan innehålla ett `X-MSEdge-ClientID`-huvud som ska skickas tillbaka till API:et med efterföljande förfrågningar. Om flera API:er för Bing-sökning används ska samma klient-ID användas för dem om möjligt.

Genom att tillhandahålla `X-MSEdge-ClientID` sidhuvudet kan Bing-API: er associera alla användares sökningar, som har två viktiga fördelar.

Först hjälper Bing-sökmotorn till med att tillämpa tidigare kontexter på sökningarna för att hitta resultat som bättre tillfredsställer användaren. Om en användare tidigare har sökt efter termer som exempelvis relaterar till segling kan en senare sökning efter ”hamnar” returnera information om platser där segelbåtar kan förtöjas.

Därefter väljer Bing slumpmässigt ut användare som ska prova nya funktioner innan de blir allmänt tillgängliga. Genom att tillhandahålla samma klient-ID med varje begäran säkerställs att användare som har valts för att se en funktion alltid ser den. Utan klient-ID kan användaren se en funktion som sedan försvinner, till synes slumpmässigt, i sökresultatet.

Säkerhetsprinciper för webbläsaren (CORS) kan hindra att `X-MSEdge-ClientID`-huvudet visas för JavaScript. Den här begränsningen uppstår när söksvaret har ett annat ursprung än sidan som begärt det. I en produktionsmiljö bör du hantera den här principen genom att lägga upp ett serverskript som gör API-anrop på samma domän som webbsidan. Eftersom skriptet har samma ursprung som webbsidan är sedan `X-MSEdge-ClientID`-huvudet tillgängligt för JavaScript.

> [!NOTE]
> Du bör utföra begäran på serversidan i ett produktionsklart webbprogram ändå. I annat fall måste API-nyckeln för Bing-sökning inkluderas i webbsidan där den är tillgänglig för alla som visar källan. Du debiteras för all användning under din API-prenumerationsnyckel, även begäranden som görs av obehöriga personer, så det är viktigt att inte exponera nyckeln.

I utvecklingssyfte kan du begära API för webbsökning i Bing via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers` huvud som tillåter listor med svars rubriker och gör dem tillgängliga för Java Script.

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
> [Referens för API för entitetsökning i Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Dokumentation till API för Bing Maps](//msdn.microsoft.com/library/dd877180.aspx)