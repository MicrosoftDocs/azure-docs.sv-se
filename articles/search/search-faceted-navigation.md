---
title: Lägg till en hierarki för en hierarki med en navigerings kategori
titleSuffix: Azure Cognitive Search
description: Lägg till fasett navigering för självriktad filtrering i Sök program som integreras med Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 05be5295ae5f8c73c916a21bba7dbc98ab0c5e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002801"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Implementera fasettisk navigering i Azure Kognitiv sökning

Fasettisk navigering är en filtrerings funktion som ger självriktad detalj nivå navigering i Sök program. Termen "fasettisk navigering" kan vara okänd, men du har förmodligen använt det tidigare. I följande exempel visas en fasettisk navigering som inte är mer än de kategorier som används för att filtrera resultat.

 ![Demo om Azure Kognitiv sökning-jobb portalen](media/search-faceted-navigation/azure-search-faceting-example.png "Demo om Azure Kognitiv sökning-jobb portalen")

Fasettisk navigering är en alternativ Start punkt att söka i. Det finns ett bekvämt alternativ för att skriva komplexa Sök uttryck. Ansikte kan hjälpa dig att hitta det du söker, samtidigt som du ser till att du inte får noll resultat. Som utvecklare kan du med hjälp av ansikts Visa de mest användbara Sök kriterierna för att navigera i Sök indexet. I återförsäljar-program är fasett-navigering ofta byggd över varumärken, avdelningar (barns skor), storlek, pris, popularitet och klassificering. 

Implementering av aspekt navigering skiljer sig mellan Sök teknik. I Azure Kognitiv sökning skapas fasettisk navigering vid tid med fält som du tidigare har attributat i schemat.

-   I de frågor som ditt program skapar måste en fråga skicka *fasett-frågeparametrar* för att hämta de tillgängliga aspekt filter värdena för dokument resultat uppsättningen.

-   För att rensa dokument resultat uppsättningen måste programmet också använda ett `$filter` uttryck.

I din program utveckling kan du skriva kod som konstruerar frågor utgör arbets delen. Många av de program beteenden som du förväntar dig från en aspektad navigering tillhandahålls av tjänsten, inklusive inbyggt stöd för att definiera intervall och för att räkna antalet aspekt resultat. Tjänsten innehåller också lämpliga-standardvärden som hjälper dig att undvika svårhanterligt navigerings strukturer. 

## <a name="sample-code-and-demo"></a>Exempel kod och demo
I den här artikeln används en jobb Sök portal som exempel. Exemplet implementeras som ett ASP.NET MVC-program.

- Se och testa arbets demon online på [Azure kognitiv sökning Job Portal-demon](https://aka.ms/azjobsdemo).

- Ladda ned koden från [Azure-samples-lagrings platsen på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Kom igång
Om du inte har använt söknings utvecklingen på bästa sätt är det bästa sättet att tänka på den, och det visar möjligheterna för självriktad sökning. Det är en typ av detaljerad Sök upplevelse, baserat på fördefinierade filter, som används för att snabbt begränsa Sök resultaten genom att peka och klicka. 

### <a name="interaction-model"></a>Interaktions modell

Sök upplevelsen för den fasettiska navigeringen är iterativ, så vi börjar med att förstå det som en sekvens av frågor som Unfold som svar på användar åtgärder.

Start punkten är en program sida som tillhandahåller fasett-navigering, vanligt vis placerad på omkretsen. Fasett-navigering är ofta en träd struktur med kryss rutor för varje värde eller text som kan klickas. 

1. En fråga som skickas till Azure Kognitiv sökning anger den fasettiska navigerings strukturen via en eller flera aspekt parametrar. Frågan kan till exempel innehålla `facet=Rating` , kanske med ett `:values` eller `:sort` alternativ för att ytterligare förfina presentationen.
2. Presentations lagret återger en Sök sida som ger en fasett-navigering med hjälp av de fasetter som anges i begäran.
3. Om du har en aspektad navigerings struktur som innehåller klassificering klickar du på "4" för att visa att endast produkter med en klassificering på 4 eller högre ska visas. 
4. Som svar skickar programmet en fråga som innehåller `$filter=Rating ge 4` 
5. Presentations lagret uppdaterar sidan med en reducerad resultat uppsättning som innehåller bara de objekt som uppfyller de nya villkoren (i det här fallet produkter som klassats 4 och uppåt).

En aspekt är en frågeparameter, men den kan inte förväxla den med frågans ingångar. Den används aldrig som urvals villkor i en fråga. Tänk i stället på fasett-frågeparametrar som indata till navigerings strukturen som kommer tillbaka i svaret. För varje aspekt-frågeparameter som du anger, utvärderar Azure Kognitiv sökning hur många dokument som finns i de partiella resultaten för varje fasett-värde.

Observera `$filter` i steg 4. Filtret är en viktig aspekt av en aspektad navigering. Även om ansikts och filter är oberoende av API: et, behöver du båda för att leverera den upplevelse som du har tänkt. 

### <a name="app-design-pattern"></a>Design mönster för appen

I program kod är mönstret att använda fasett-frågeparametrar för att returnera den fasettiska navigerings strukturen tillsammans med fasett-resultat plus ett $filter-uttryck.  Filter uttrycket hanterar händelsen klickning på fasett svärdet. Vi tänker på `$filter` uttrycket som koden bakom den faktiska trimningen av Sök resultat som returneras till presentations lagret. Om du har fått en palett med färger klickar du på den röda färgen implementeras genom ett `$filter` uttryck som bara väljer de objekt som har en röd färg. 

### <a name="query-basics"></a>Grundläggande frågor

I Azure Kognitiv sökning anges en begäran via en eller flera frågeparametrar (se [Sökdokumenten](/rest/api/searchservice/Search-Documents) för en beskrivning av var och en). Ingen av frågeparametrar krävs, men du måste ha minst en för att en fråga ska vara giltig.

Precision, som tolkas som möjligheten att filtrera bort irrelevanta träffar, uppnås genom ett eller båda dessa uttryck:

-   **Sök =**  
    Värdet för den här parametern utgör Sök uttrycket. Det kan vara ett enda texts tycke eller ett komplext Sök uttryck som innehåller flera termer och operatorer. På-servern används ett Sök uttryck för full texts ökning, frågor i sökbara fält i indexet för matchande villkor, vilket returnerar resultat i rangordnings ordning. Om du anger `search` till null är frågekörningen över hela indexet (det vill säga `search=*` ). I det här fallet är andra element i frågan, till exempel en `$filter` eller bedömnings profil, de primära faktorer som påverkar vilka dokument som returneras `($filter` ) och i vilken ordning ( `scoringProfile` eller `$orderby` ).

-   **$filter =**  
    Ett filter är en kraftfull mekanism för att begränsa storleken på Sök resultat baserat på värdena för vissa dokumentattribut. A `$filter` utvärderas först, följt av aspekt logiken som genererar de tillgängliga värdena och motsvarande antal för varje värde

Komplexa Sök uttryck minskar frågans prestanda. Om möjligt kan du använda välkända filter uttryck för att öka precisionen och förbättra frågans prestanda.

För att bättre förstå hur ett filter lägger till mer precision, jämför ett komplext Sök uttryck till ett som innehåller ett filter uttryck:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Båda frågorna är giltiga, men den andra är överlägsen om du söker efter icke-Motels med parkering i Seattle.
-   Den första frågan är beroende av de ord som nämns eller som inte nämns i sträng fält som namn, beskrivning och andra fält som innehåller sökbara data.
-   Den andra frågan söker efter exakta matchningar på strukturerade data och är förmodligen mycket mer exakt.

Se till att varje användar åtgärd över en fasett-navigerings struktur åtföljs av en begränsning av Sök resultaten i program som innehåller fasett-navigering. Använd ett filter uttryck för att begränsa resultaten.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Bygg en aspekt av en webbapp
Du implementerar en aspektad navigering med Azure Kognitiv sökning i din program kod som skapar Sök förfrågan. Den fasettiska navigeringen är beroende av element i schemat som du definierade tidigare.

Fördefinierat i ditt sökindex är `Facetable [true|false]` attributet index, som anges på valda fält för att aktivera eller inaktivera användningen i en fasett-navigerings struktur. Det `"Facetable" = true` går inte att använda ett fält i aspekt navigering utan.

Presentations lagret i din kod ger användar upplevelsen. Den ska visa en lista över delarna i den fasetten navigeringen, till exempel etikett, värden, kryss rutor och antalet. Azure Kognitiv sökning REST API är plattforms oberoende, så Använd det språk och den plattform som du vill använda. Det viktiga är att inkludera GRÄNSSNITTs element som stöder stegvis uppdatering, med uppdaterat GRÄNSSNITTs tillstånd som varje ytterligare aspekt är markerat. 

Vid tidpunkten skapar din program kod en begäran som innehåller `facet=[string]` en parameter för begäran som anger att fältet ska fasetta av. En fråga kan ha flera facets, t. ex. `&facet=color&facet=category&facet=rating` , var och en avgränsad med ett et-tecken (&).

Program koden måste också skapa ett `$filter` uttryck för att hantera klicknings händelserna i en fasett-navigering. En `$filter` minska Sök resultatet genom att använda fasett-värdet som filter villkor.

Azure Kognitiv sökning returnerar Sök resultaten baserat på en eller flera villkor som du anger, tillsammans med uppdateringar av den fasettiska navigerings strukturen. I Azure Kognitiv sökning är den fasettiska navigeringen en konstruktion med en nivå med aspekt värden och antalet hur många resultat som finns för var och en av dem.

I följande avsnitt tar vi en närmare titt på hur du skapar varje del.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Bygg indexet
Fasettering har Aktiver ATS för fält-för-fält i indexet, via detta index-attribut: `"Facetable": true` .  
Alla fält typer som kan användas i en aspektad navigering är `Facetable` som standard. Sådana fält typer är `Edm.String` , `Edm.DateTimeOffset` och alla numeriska fält typer (i stort sett alla fält typer är fasettable förutom `Edm.GeographyPoint` , som inte kan användas i en aspektad navigering). 

När du skapar ett index är det en bra idé att inaktivera fasett för fält som aldrig ska användas som aspekt.  I synnerhet bör sträng fält för singleton-värden, t. ex. ett ID eller ett produkt namn, anges till `"Facetable": false` för att förhindra att deras oavsiktliga (och ineffektiva) användning används i en fasett-navigering. Genom att inaktivera fasetter där du inte behöver det kan du behålla storleken på det små indexet, och normalt förbättra prestanda.

Följande är en del av schemat för demonstrations exempel appen för jobb portalen, putsad av vissa attribut för att minska storleken:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Som du kan se i exempel schemat `Facetable` är inaktive rad för sträng fält som inte ska användas som FACET, till exempel ID-värden. Genom att inaktivera fasetter där du inte behöver det kan du behålla storleken på det små indexet, och normalt förbättra prestanda.

> [!TIP]
> Bästa praxis är att inkludera en fullständig uppsättning indexattribut för varje fält. Även om `Facetable` är aktiverat som standard för nästan alla fält, kan inställning av varje attribut hjälpa dig att tänka igenom konsekvenserna av varje schema beslut. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Kontrol lera data
Kvaliteten på dina data har en direkt inverkan på om den fasettiska navigerings strukturen materialiseras som du förväntar dig. Det påverkar också enklast konstruktion av filter för att minska resultat uppsättningen.

Om du vill aspekt av varumärke eller pris ska varje dokument innehålla värden för *BrandName* och *productPrice* som är giltiga, konsekventa och produktiva som ett filter alternativ.

Här är några påminnelser om vad du behöver för att skrubba:

* Fråga dig själv om det innehåller värden som är lämpliga som filter i självhänvisad sökning för varje fält som du vill aspekt av. Värdena bör vara korta, beskrivande och tillräckligt distinkta för att erbjuda ett tydligt val mellan konkurrerande alternativ.
* Fel stavning eller nästan matchande värden. Om du aspektar på färg, och fältvärdena innehåller orange och Ornage (en felstavad), kommer en aspekt som baseras på fältet färg att hämta båda.
* Blandad Case-text kan också wreak oreda i fasett-navigering, med orange och orange visas som två olika värden. 
* Enkla och plural-versioner av samma värde kan resultera i en separat aspekt för varje.

Som du kan föreställa dig är noggrannhet för förberedelse av data en viktig aspekt av effektiv navigering.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Skapa användargränssnittet
Att arbeta från presentations lagret kan hjälpa dig att återställa krav som kan ha missats, och förstå vilka funktioner som är viktiga för Sök upplevelsen.

Vid aspekt navigering visar din webb-eller program sida den fasett-navigerings struktur som identifierar användarindata på sidan och infogar de ändrade elementen. 

För webb program används AJAX ofta i presentations lagret eftersom det gör att du kan uppdatera stegvisa ändringar. Du kan också använda ASP.NET MVC eller någon annan visualiserings plattform som kan ansluta till en Azure Kognitiv sökning-tjänst via HTTP. Det exempel program som refereras till i den här artikeln – **Azure-kognitiv sökning jobb Portal demonstration** – sker som ett ASP.NET MVC-program.

I exemplet är en aspektad navigering inbyggd på sidan Sök resultat. Följande exempel, som tas från `index.cshtml` filen i exempel programmet, visar den statiska HTML-strukturen för visning av fasett-navigering på sidan Sök resultat. Listan över Faces skapas eller återskapas dynamiskt när du skickar en sökterm eller väljer eller avmarkerar en aspekt.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Följande kodfragment från `index.cshtml` sidan bygger dynamiskt HTML-koden för att visa den första aspekten, affärs titeln. Samma funktioner skapar HTML-koden dynamiskt för de andra fasetterna. Varje aspekt har en etikett och ett antal som visar antalet objekt som hittades för det fasett-resultatet.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Kom ihåg att lägga till en mekanism för att ta bort ansikte när du utformar Sök Resultat sidan. Om du lägger till kryss rutor kan du enkelt se hur du rensar filtren. För andra layouter kan du behöva ett navigerings mönster eller en annan kreativ metod. I exempel programmet jobbs öknings Portal kan du till exempel klicka på `[X]` efter en vald aspekt för att ta bort aspekten.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Bygg frågan
Koden som du skriver för att skapa frågor ska ange alla delar av en giltig fråga, inklusive Sök uttryck, ansikts, filter, bedömnings profiler – allt som används för att formulera en begäran. I det här avsnittet ska vi utforska var ansikte passar in i en fråga och hur filter används med FACET för att leverera en reducerad resultat uppsättning.

Observera att ansikte är integrala i det här exempel programmet. Sök funktionen i demo versionen av jobb portalen är utformad runt navigering och filter. Den framträdande placeringen av den fasettiska navigeringen på sidan visar dess betydelse. 

Ett exempel är ofta en bra plats att börja på. Följande exempel, som tas från `JobsSearch.cs` filen, skapar en begäran som skapar aspekt navigering baserat på affärs rubrik, plats, bokförings typ och lägsta lön. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

En aspekt fråga-parameter har angetts till ett fält och, beroende på datatyp, kan utföras ytterligare med en kommaavgränsad lista som innehåller `count:<integer>` , `sort:<>` , `interval:<integer>` och `values:<list>` . En värde lista stöds för numeriska data vid inställning av intervall. Se [Sök i dokument (Azure KOGNITIV sökning API)](/rest/api/searchservice/Search-Documents) för användnings information.

Tillsammans med FACET, bör begäran som formuleras av ditt program också skapa filter för att begränsa uppsättningen av kandidat dokument baserat på ett aspekt värdes val. För en cykel butik ger den fasettiska navigeringen LED trådar till frågor som *vilka färger, tillverkare och typer av cyklar är tillgängliga?*. Filtrera svar på frågor som *vilka exakta cyklar är röda, Mountain Bikes, i det här pris intervallet?*. När du klickar på "röd" för att ange att endast röda produkter ska visas, kommer nästa fråga som programmet skickar att innehålla `$filter=Color eq 'Red'` .

Följande kodfragment från `JobsSearch.cs` sidan lägger till den valda affärs titeln i filtret om du väljer ett värde från affärs rubrik aspekten.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tips och regelverk

### <a name="indexing-tips"></a>Indexerings tips
**Förbättra index effektiviteten om du inte använder en sökruta**

Om ditt program använder fasettt navigering exklusivt (dvs. ingen sökruta) kan du markera fältet som `searchable=false` , `facetable=true` för att skapa ett mer kompakt index. Indexering sker dessutom bara på hela aspekt värden, utan ord brytning eller indexering av komponent delarna i ett värde med flera ord.

**Ange vilka fält som kan användas som ansikts**

Kom ihåg att schema för indexet avgör vilka fält som är tillgängliga för användning som aspekt. Förutsatt att ett fält är fasettable anger frågan vilka fält som ska fasetta. Det fält som du fasetterar innehåller värdena som visas under etiketten. 

De värden som visas under varje etikett hämtas från indexet. Om t. ex. aspekt fältet är *färg*, är de tillgängliga värdena för ytterligare filtrering värdena för fältet – röd, svart och så vidare.

För numeriska värden och DateTime-värden kan du uttryckligen ange värden i aspekt fältet (till exempel `facet=Rating,values:1|2|3|4|5` ). En värde lista är tillåten för dessa fält typer för att förenkla separering av fasett-resultat i sammanhängande intervall (antingen intervall baserat på numeriska värden eller tids perioder). 

**Som standard kan du bara ha en nivå av fasett-navigering** 

Det finns inget direkt stöd för att kapsla facets i en hierarki. Som standard har fasett-navigering i Azure Kognitiv sökning bara stöd för en nivå med filter. Det finns dock lösningar. Du kan koda en hierarkisk aspekt struktur i en `Collection(Edm.String)` med en start punkt per hierarki. Att implementera den här lösningen ligger utanför omfånget för den här artikeln. 

### <a name="querying-tips"></a>Tips om frågor
**Validera fält**

Om du skapar listan över ansikte dynamiskt baserat på indata från ej betrodda användare, verifierar du att namnen på de fasettiska fälten är giltiga. Eller så kan du undanta namnen när du skapar URL: er med hjälp av antingen `Uri.EscapeDataString()` i .net eller motsvarande på valfri plattform.

### <a name="filtering-tips"></a>Filtrerings tips
**Öka Sök precisionen med filter**

Använda filter. Om du bara använder Sök uttryck enskilt kan det hända att ett dokument returneras som inte har det exakta fasett-värdet i något av dess fält.

**Öka Sök prestanda med filter**

Filtren begränsar uppsättningen av kandidat dokument för sökning och undantar dem från rangordning. Om du har en stor uppsättning dokument ger du ofta bättre prestanda om du använder en selektiv aspekts ökning.
  
**Filtrera enbart de fasettiska fälten**

I fasetter kan du vanligt vis bara inkludera dokument som har fasett svärdet i ett angivet (fasett) fält, inte överallt i alla sökbara fält. Genom att lägga till ett filter kan du förstärka mål fältet genom att dirigera tjänsten till att bara söka i det fasettiska fältet för ett matchande värde.

**Trimma fasett-resultat med fler filter**

Fasett-resultat är dokument som finns i Sök resultaten som matchar en fasett-term. I följande exempel i Sök Resultat för *molnbaserad data behandling* har 254-objekt även *intern specifikation* som en innehålls typ. Objekt är inte alltid ömsesidigt uteslutande. Om ett objekt uppfyller kriterierna för båda filtren räknas det i vart och ett. Den här dupliceringen är möjlig vid aspektering av `Collection(Edm.String)` fält som ofta används för att implementera dokument taggning.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

I allmänhet rekommenderar vi att du lägger till fler filter för att ge användarna fler alternativ för att begränsa sökningen, om du upptäcker att fasett-resultatet är konsekvent för stort.

### <a name="tips-about-result-count"></a>Tips om resultat antal

**Begränsa antalet objekt i aspekt navigeringen**

För varje fasettt fält i navigerings trädet finns det en standard gräns på 10 värden. Detta standardvärde är meningsfullt för navigerings strukturer eftersom den behåller värde listan till en hanterbar storlek. Du kan åsidosätta standardvärdet genom att tilldela ett värde till Count.

* `&facet=city,count:5` anger att endast de fem första städerna som finns i de översta rankade resultaten returneras som ett fasett-resultat. Överväg en exempel fråga med Sök termen "flyg plats" och 32 matchningar. Om frågan anger `&facet=city,count:5` är det bara de första fem unika städerna med de flesta dokument i Sök resultaten som ingår i fasett-resultatet.

Observera skillnaden mellan fasett-resultat och Sök resultat. Sök resultat är alla dokument som matchar frågan. Fasett-resultat är matchningarna för varje fasett-värde. I exemplet innehåller Sök resultaten Orts namn som inte finns i aspekt klassificerings listan (5 i vårt exempel). Resultat som filtreras ut genom den fasettiska navigeringen blir synliga när du tar bort FACET eller väljer andra ansikte förutom stad. 

> [!NOTE]
> `count`Det kan vara förvirrande att diskutera när det finns fler än en typ. Följande tabell innehåller en kort sammanfattning av hur termen används i Azure Kognitiv sökning API, exempel kod och dokumentation. 

* `@colorFacet.count`<br/>
  I presentations kod bör du se en Count-parameter på aspekten, som används för att visa antalet fasett-resultat. I aspekt resultat anger Count antalet dokument som matchar på fasett-termen eller intervallet.
* `&facet=City,count:12`<br/>
  I en aspekt fråga kan du ange Count till ett värde.  Standardvärdet är 10, men du kan ange det högre eller lägre. Inställningen `count:12` hämtar de översta 12 träffarna i aspekt resultatet efter antal dokument.
* "`@odata.count`"<br/>
  I svaret på frågan anger det här värdet antalet matchande objekt i Sök resultatet. I genomsnitt är det större än summan av alla fasett-resultat kombinerat, på grund av förekomst av objekt som matchar Sök termen, men som inte har något värde för fasett-värde.

**Hämta antal i aspekt resultat**

När du lägger till ett filter i en fasett-fråga kanske du vill behålla fasett-instruktionen (till exempel `facet=Rating&$filter=Rating ge 4` ). Tekniskt, aspekt = klassificering behövs inte, men om du behåller den returneras antalet aspekt värden för klassificering 4 och högre. Om du till exempel klickar på "4" och frågan innehåller ett filter som är större än eller lika med "4" returneras antalet för varje klassificering som är 4 och högre.  

**Se till att du får korrekta fasett-antal**

Under vissa omständigheter kanske du upptäcker att antalet fasetter inte matchar resultat uppsättningarna (se [aspekten navigering i Azure kognitiv sökning (Microsoft Q&en fråge sida)](/answers/topics/azure-cognitive-search.html)).

Fasett-antalet kan vara felaktigt på grund av horisontell partitionering-arkitekturen. Varje sökindex har flera Shards och varje Shard rapporterar de översta N fasetterna efter antal dokument, som sedan kombineras till ett enda resultat. Om vissa Shards har många matchande värden, medan andra har färre, kan det hända att vissa fasett-värden saknas eller under räkning i resultaten.

Även om detta beteende kan ändras när som helst, om du stöter på det här beteendet idag kan du kringgå det genom att artificiellt ange antalet: \<number> till ett stort antal för att framtvinga fullständig rapportering från varje Shard. Om värdet för Count: är större än eller lika med antalet unika värden i fältet, garanterar du korrekta resultat. Men när antalet dokument är högt är det en prestanda försämring, så Använd det här alternativet sparsamt.

### <a name="user-interface-tips"></a>Användar gränssnitts tips
**Lägg till etiketter för varje fält i aspekt navigering**

Etiketter definieras vanligt vis i HTML eller formulär ( `index.cshtml` i exempel programmet). Det finns inget API i Azure Kognitiv sökning för fasett-navigerings etiketter eller andra metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrera baserat på ett intervall
Fasettering över intervall med värden är ett vanligt Sök program krav. Intervall stöds för numeriska data och DateTime-värden. Du kan läsa mer om varje metod i [Sök dokument (Azure KOGNITIV sökning API)](/rest/api/searchservice/Search-Documents).

Azure Kognitiv sökning fören klar intervall konstruktion genom att tillhandahålla två metoder för att beräkna ett intervall. För båda metoderna skapar Azure Kognitiv sökning lämpliga intervall med de indata som du har angett. Om du till exempel anger intervall värden på 10 | 20 | 30 skapar det automatiskt intervall på 0-10, 10-20, 20-30. Programmet kan eventuellt ta bort alla intervall som är tomma. 

**Metod 1: Använd parametern Interval**  
Om du vill ange pris facets i $10-steg, anger du: `&facet=price,interval:10`

**Metod 2: Använd en värde lista**  
För numeriska data kan du använda en värde lista.  Ta hänsyn till aspekt intervallet för ett `listPrice` fält, återges på följande sätt:

  ![Lista med exempel värden](media/search-faceted-navigation/Facet-5-Prices.PNG "Lista med exempel värden")

Om du vill ange ett fasett-intervall som det som finns i föregående skärm bild, använder du en värde lista:

> `facet=listPrice,values:10|25|100|500|1000|2500`

Varje intervall skapas med 0 som start punkt, ett värde från listan som en slut punkt och rensas sedan det föregående intervallet för att skapa diskreta intervall. Azure Kognitiv sökning gör dessa saker som en del av den fasettiska navigeringen. Du behöver inte skriva kod för att strukturera varje intervall.

### <a name="build-a-filter-for-a-range"></a>Bygga ett filter för ett intervall
Om du vill filtrera dokument baserat på ett intervall som du väljer kan du `"ge"` använda `"lt"` operatorerna och i ett uttryck i två delar som definierar Intervallets slut punkter. Om du till exempel väljer intervallet 10-25 för ett fält blir `listPrice` filtret `$filter=listPrice ge 10 and listPrice lt 25` . I exempel koden använder filter uttrycket **priceFrom** -och **priceTo** -parametrar för att ange slut punkterna. 

  ![Fråga efter ett värde intervall](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Fråga efter ett värde intervall")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrera baserat på avstånd
Det är vanligt att se filter som hjälper dig att välja en butik, restaurang eller destination baserat på dess närhet till din aktuella plats. Den här typen av filter kan se ut som fasett-navigering, men det är bara ett filter. Vi nämner det här för de som specifikt letar efter implementerings råd för det specifika design problemet.

Det finns två geospatiala funktioner i Azure Kognitiv sökning, **geo. Distance** och **geo. snitt**.

* Funktionen **geo. Distance** returnerar avståndet i kilo meter mellan två punkter. En punkt är ett fält och andra är en konstant som skickas som en del av filtret. 
* Funktionen **geo. intersects** returnerar true om en viss punkt ligger inom en viss polygon. Punkten är ett fält och polygonen anges som en konstant lista över koordinater som har överförts som en del av filtret.

Du hittar filter exempel i [OData Expression-syntax (Azure kognitiv sökning)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Prova demonstrationen
Azure Kognitiv sökning Job Portal-demon innehåller exemplen som refereras i den här artikeln.

-   Se och testa arbets demon online på [Azure kognitiv sökning Job Portal-demon](https://aka.ms/azjobsdemo).

-   Ladda ned koden från [Azure-samples-lagrings platsen på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

När du arbetar med Sök resultat kan du titta på URL: en för ändringar i frågans konstruktion. Det här programmet sker för att lägga till FACET i URI: n när du väljer vart och ett.

1. Om du vill använda kart funktionen i demonstrations appen hämtar du en Bing Maps-nyckel från [Bing Maps dev Center](https://www.bingmapsportal.com/). Klistra in det över den befintliga nyckeln på `index.cshtml` sidan. `BingApiKey`Inställningen i `Web.config` filen används inte. 

2. Kör appen. Ta den valfria rund turen eller Stäng dialog rutan.
   
3. Ange en sökterm, till exempel "analytikert" och klicka på Sök ikonen. Frågan körs snabbt.
   
   En fasett navigerings struktur returneras också med Sök resultaten. På sidan Sök Resultat innehåller den fasettiska navigerings strukturen antal för varje fasett-resultat. Inga fasetter har marker ATS, så alla matchande resultat returneras.
   
   ![Sök resultat innan du väljer Faces](media/search-faceted-navigation/faceted-search-before-facets.png "Sök resultat innan du väljer Faces")

4. Klicka på en affärs rubrik, plats eller lägsta lön. FACET var null i den inledande sökningen, men när de tar med värden, trimmas Sök resultaten för objekt som inte längre matchar.
   
   ![Sök Resultat efter att du har valt ansikts](media/search-faceted-navigation/faceted-search-after-facets.png "Sök Resultat efter att du har valt ansikts")

5. Om du vill ta bort den fasetta frågan så att du kan prova olika fråge beteenden, klickar du på `[X]` när de markerade fasetterna ska ta bort fasetterna.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Läs mer
Titta på [Azure kognitiv sökning djupgående](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Vid 45:25 finns det en demonstration om hur du implementerar ansikte.

För mer information om design principer för fasett-navigering rekommenderar vi följande länkar:

* [Design mönster: Fasettisk navigering](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Klient dels problem vid implementering av fasett-sökning – del 1](https://articles.uie.com/faceted_search2/)