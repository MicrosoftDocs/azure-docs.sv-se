---
title: Lucene-frågesyntaxen
titleSuffix: Azure Cognitive Search
description: Referens för fullständig Lucene-frågesyntax som används i Azure Kognitiv sökning för jokertecken, fuzzy search, RegEx och andra avancerade fråge konstruktioner.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 0dbf418d0a673dd0799f0f638e454c484f837fd7
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516602"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Lucene-frågesyntax i Azure Kognitiv sökning

När du skapar frågor kan du välja metoden [Lucene Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) för specialiserade fråge formulär: jokertecken, fuzzy search, närhets sökning, reguljära uttryck. En stor del av den här syntaxen för en Lucene-fråga [implementeras intakt i Azure kognitiv sökning](search-lucene-query-architecture.md), med undantag för *intervalls ökningar* som är konstruerade genom **`$filter`** uttryck. 

Den fullständiga Lucene-syntaxen används för frågeuttryck som skickas i **`search`** parametern för en [söknings dokument (REST API)](/rest/api/searchservice/search-documents) -begäran, och ska inte förväxlas med [OData-syntaxen](query-odata-filter-orderby-syntax.md) som används för [**`$filter`**](search-filters.md) och- [**`$orderby`**](search-query-odata-orderby.md) uttryck i samma begäran. OData-parametrar har olika syntax och regler för att konstruera frågor, undantags strängar och så vidare.

## <a name="example-full-syntax"></a>Exempel (fullständig syntax)

Ange **`queryType`** parametern för att ange fullständig Lucene. I följande exempel anropas sökning och term ökning i fält. Den här frågan söker efter hotell där kategori fältet innehåller termen "budget". Alla dokument som innehåller frasen "nyligen renovated" rangordnas högre upp till följd av termen förstärknings värde (3).  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

**`searchMode`** Parametern är relevant i det här exemplet. När operatörer finns i frågan bör du vanligt vis ange att `searchMode=all` *alla* villkor matchas.  

Fler exempel finns i [exempel på Lucene](search-query-lucene-examples.md)-frågesyntax. Mer information om fråge förfrågningen och parametrarna finns i [Sök efter dokument (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Grundläggande syntax  

Följande grundläggande syntax gäller för alla frågor som använder Lucene-syntaxen.  

### <a name="operator-evaluation-in-context"></a>Utvärdering av operator i kontext

Placering avgör om en symbol tolkas som en operator eller bara ett tecken i en sträng.

I till exempel fullständig syntax för Lucene används tilde (~) för både fuzzy search och närhets sökning. Vid placering efter en citerad fras, ~ aktiverar närhets sökning. När den placeras i slutet av en term, så anropar ~ Sök funktionen.

Under en period, till exempel "Business ~ analytiker", utvärderas inte det som en operator. I detta fall antar vi att frågan är en term-eller fras fråga. [full texts ökning](search-lucene-query-architecture.md) med en [lexikalisk analys](search-lucene-query-architecture.md#stage-2-lexical-analysis) tar bort den ~ och bryter termen "Business ~ analytiker" i två: Business eller analytiker.

Exemplet ovan är tilde (~), men samma princip gäller för alla operatorer.

### <a name="escaping-special-characters"></a>Hoppar över specialtecken

Om du vill använda någon av Sök operatorerna som en del av Sök texten, kan du undanta det genom att prefixet med ett enda omvänt snedstreck ( `\` ). Om du till exempel söker i jokertecken `https://` , där `://` är en del av frågesträngen, anger du `search=https\:\/\/*` . På samma sätt kan ett escapeat telefon mönster se ut så här `\+1 \(800\) 642\-7676` .

Specialtecken som kräver undantags tecken innehåller följande:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Även om undantag bevarar token tillsammans, kan [lexikalisk analys](search-lucene-query-architecture.md#stage-2-lexical-analysis) under indexeringen medföra att de utsätts för sig. Standard-Lucene-analysatorn kommer till exempel att ta bort ord för bindestreck, blank steg och andra tecken. Om du behöver specialtecken i frågesträngen kan du behöva en analys som bevarar dem i indexet. Vissa alternativ är Microsofts [språk analys](index-add-language-analyzers.md)verktyg, som bevarar avstavade ord eller en anpassad analys för mer komplexa mönster. Mer information finns i [del termer, mönster och specialtecken](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Koda osäkra och reserverade tecken i URL: er

Kontrol lera att alla osäkra och reserverade tecken är kodade i en URL. Till exempel är ' # ' ett osäkert värde eftersom det är ett fragment/ankare-ID i en URL. Specialtecknet måste vara kodad till `%23` om den används i en URL. "&" och "=" är exempel på reserverade tecken när de begränsar parametrar och anger värden i Azure Kognitiv sökning. Mer information finns i [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

Osäkra tecken är ``" ` < > # % { } | \ ^ ~ [ ]`` . Reserverade tecken är `; / ? : @ = + &` .

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Booleska operatorer

Du kan bädda in booleska operatorer i en frågesträng för att förbättra precisionen för en matchning. Den fullständiga syntaxen stöder text operatörer förutom tecken operatorer. Ange alltid text booleska operatorer (och, eller, inte) med versaler.

|Textoperator | Tecken | Exempel | Användning |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | Anger villkor som en matchning måste innehålla. I exemplet söker frågemotor efter dokument som innehåller både `wifi` och `luxury` . Plus tecknet ( `+` ) används för obligatoriska villkor. Till exempel `+wifi +luxury` föreskriver att båda termerna måste visas någonstans i fältet i ett enda dokument.|
| ELLER | `|` | `wifi | luxury` | Söker efter en matchning när någon av villkoren hittas. I exemplet returnerar frågesyntaxen matchning av dokument som innehåller antingen `wifi` eller `luxury` eller båda. Eftersom eller är standard operatorn, kan du också lämna ut den, till exempel `wifi luxury` motsvarande  `wifi | luxury` .|
| NOT | `!`, `-` | `wifi –luxury` | Returnerar matchningar för dokument som undantar termen. Söker till exempel `wifi –luxury` efter dokument som har `wifi` villkoret men inte `luxury` . <br/><br/>`searchMode`Parametern på en förfrågan styr om en term med operatorn not ANDed eller Ored med andra termer i frågan (förutsatt att det inte finns någon `+` eller `|` operator på de andra villkoren). Giltiga värden är `any` eller `all` .  <br/><br/>`searchMode=any` ökar åter kallelsen av frågor genom att inkludera fler resultat och `-` tolkas som standard som "eller inte". Kommer till exempel `wifi -luxury` att matcha dokument som innehåller den `wifi` eller de som inte innehåller någon term `luxury` .  <br/><br/>`searchMode=all` ökar precisionen för frågor genom att inkludera färre resultat och som standard tolkas som "och inte". Kommer till exempel `wifi -luxury` att matcha dokument som innehåller termen `wifi` och som inte innehåller termen "lyxen". Det här är utan tvekan ett mer intuitivt beteende för `-` operatorn. Därför bör du överväga att använda `searchMode=all` i stället för `searchMode=any` om du vill optimera sökningarna efter precision i stället för att återkalla, *och* användarna använder ofta `-` operatorn i sökningar.<br/><br/>När du bestämmer dig för en `searchMode` inställning bör du tänka på användar interaktions mönstren för frågor i olika program. Användare som söker efter information är mer sannolika att inkludera en operatör i en fråga, i stället för e-handelsplatser som har fler inbyggda navigerings strukturer. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Sökning efter fält

Du kan definiera en fält Sök åtgärd med `fieldName:searchExpression` syntaxen, där Sök uttrycket kan vara ett enstaka ord eller en fras, eller ett mer komplext uttryck inom parentes, eventuellt med booleska operatorer. Några exempel är följande:  

- Genre: inte historik för Jazz  

- artister:("" Maria Davis "" John Coltrane ")

Se till att placera flera strängar inom citat tecken om du vill att båda strängarna ska utvärderas som en enda entitet, i det här fallet söker du efter två distinkta artister i `artists` fältet.  

Fältet som anges i `fieldName:searchExpression` måste vara ett `searchable` fält.  Se [skapa index](/rest/api/searchservice/create-index) för information om hur index-attribut används i fält definitioner.  

> [!NOTE]
> När du använder ett fält med sökuttryck behöver du inte använda `searchFields` parametern eftersom varje fält som är angivet i uttrycket har ett explicit angivet fält namn. Du kan dock fortfarande använda `searchFields` parametern om du vill köra en fråga där vissa delar är begränsade till ett visst fält och resten kan gälla för flera fält. Frågan skulle till exempel `search=genre:jazz NOT history&searchFields=description` matcha `jazz` enbart till `genre` fältet, men den matchar `NOT history` `description` fältet. Fält namnet som har angetts i `fieldName:searchExpression` har alltid företräde framför `searchFields` parametern, vilket är anledningen till att vi inte behöver inkludera `genre` i parametern i det här exemplet `searchFields` .

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Fuzzy-sökning

En Fuzzy-sökning hittar matchningar i termer som har en liknande konstruktion, vilket utökar en period upp till högst 50 villkor som uppfyller avstånds kriterierna på två eller färre. Mer information finns i [fuzzy search](search-query-fuzzy.md).

Om du vill göra en Fuzzy-sökning använder du Tilde-symbolen "~" i slutet av ett enstaka ord med en valfri parameter, ett tal mellan 0 och 2 (standard) som anger redigerings avståndet. Till exempel "blå ~" eller "blå ~ 1" returnerar "blått", "blått" och "lim".

En Fuzzy-sökning kan bara tillämpas på termer, inte fraser, men du kan lägga till Tilde till varje term individuellt i ett namn eller en fras i flera delar. Exempel: "Unviersty ~ ~" Wshington ~ "skulle matcha" University of Washington ".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Närhets sökning

Närhets sökningar används för att hitta termer som ligger nära varandra i ett dokument. Infoga en tilde ~-symbol i slutet av en fras följt av antalet ord som skapar närhets kanten. Hittar till exempel `"hotel airport"~5` villkoren "hotell" och "flyg plats" inom 5 ord för varandra i ett dokument.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Term förstärkning

Term förstärkning syftar på att rangordna ett dokument högre om det innehåller den ökade perioden, i förhållande till dokument som inte innehåller termen. Detta skiljer sig från bedömnings profiler i dessa bedömnings profiler och ökar vissa fält i stället för specifika villkor.  

I följande exempel kan du illustrera skillnaderna. Anta att det finns en bedömnings profil som höjer matchningar i ett visst fält, t. ex. *Genre* i  [musicstoreindex-exemplet](index-add-scoring-profiles.md#bkmk_ex). Termen förstärkning kan användas för att ytterligare öka vissa Sök villkor som är större än andra. Höjer till exempel `rock^2 electronic` dokument som innehåller Sök villkoren i fältet Genre högre än andra sökbara fält i indexet. Dessutom rangordnas dokument som innehåller Sök termen *rock* högre än den andra Sök termen *elektroniskt* som ett resultat av termen förstärknings värde (2).  

 Om du vill förstärka en term använder du cirkumflex, "^", symbol med en förstärknings faktor (ett tal) i slutet av den period som du söker. Du kan också öka fraser. Ju högre förstärknings faktor, desto mer relevant är termen i förhållande till andra Sök villkor. Som standard är förstärknings faktorn 1. Förstärknings faktorn måste vara positiv, men den kan vara mindre än 1 (till exempel 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Sökning efter reguljära uttryck  
 En sökning efter reguljära uttryck hittar en matchning baserat på mönster som är giltiga under Apache Lucene, enligt beskrivningen i [klassen RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html). I Azure Kognitiv sökning omges ett reguljärt uttryck mellan snedstreck `/` .

 Om du till exempel vill hitta dokument som innehåller "Motel" eller "hotell" anger du `/[mh]otel/` . Sökningar i reguljära uttryck matchas mot enstaka ord.

Vissa verktyg och språk har ytterligare krav på escape-tecken. För JSON undantas strängar som innehåller ett snedstreck med ett omvänt snedstreck: "microsoft.com/azure/" blir `search=/.*microsoft.com\/azure\/.*/` där `search=/.* <string-placeholder>.*/` anger det reguljära uttrycket och `microsoft.com\/azure\/` är strängen med ett escapeat snedstreck.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Sökning med jokertecken

Du kan använda allmänt identifierad syntax för jokertecken för flera ( `*` ) eller enkla ( `?` )-Character-sökningar. Ett frågeuttryck till exempel `search=alpha*` returnerar "alfanumerisk" eller "alfabetisk". Observera att funktionen Lucene Query parser stöder användningen av dessa symboler med en enda term och inte en fras.

Fullständig Lucene-syntax stöder prefix, infix och suffix matchning. Men om allt du behöver är prefix matchning, kan du använda den enkla syntaxen (prefix matchning stöds i båda).

Matchning av suffix, där `*` eller `?` föregår strängen (som i `search=/.*numeric./` ) eller infix-matchning kräver fullständig Lucene-syntax, samt utgående snedstrecks avgränsare för reguljära uttryck `/` . Du kan inte använda * eller? symbol som det första tecknet i en term, eller inom en term, utan `/` . 

> [!NOTE]  
> Som regel är mönster matchning långsam, så du kanske vill utforska alternativa metoder, till exempel Edge n-gram tokenisering som skapar tokens för sekvenser av tecken på en term. Indexet är större, men frågor kan köras snabbare, beroende på mönster konstruktion och längden på de strängar som du indexerar.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Effekt av en analys av jokertecken för frågor

Vid frågans parsning skickas frågor som formuleras som prefix, suffix, jokertecken eller reguljära uttryck som de är i frågans träd, vilket kringgår [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis). Matchningar hittas bara om indexet innehåller strängarna i formatet som frågan anger. I de flesta fall behöver du en analys under indexeringen som bevarar sträng integritet så att partiell term och mönster matchning lyckas. Mer information finns i [partiell terms ökning i Azure kognitiv sökning-frågor](search-query-partial-matching.md).

Tänk dig en situation där du kanske vill att Sök frågan "avsluta *" ska returnera resultat som innehåller villkor som "avsluta", "avsluta" och "avsluta".

Om du använder en. Lucene-analys (English Lucene) skulle den tillämpa aggressiva anspråks igenkänning av varje term. Till exempel avslutas "Terminate", "Termination", "Terminate" på token "termi" i indexet. På den andra sidan analyseras inte termer i frågor som använder jokertecken eller en Fuzzy-sökning alls., så det skulle inte finnas några resultat som skulle matcha frågan "avsluta *".

På den andra sidan är Microsofts analys verktyg (i det här fallet en. Microsoft-analys) en lite mer avancerad och Använd lemmatisering i stället för att det ingår. Det innebär att alla genererade tokens ska vara giltiga engelska ord. Till exempel är ' Terminate ', ' Terminate ' och ' terminion ' kvar i indexet och det är ett bättre alternativ för scenarier som är beroende av många jokertecken och en suddig sökning.

## <a name="scoring-wildcard-and-regex-queries"></a>Poängsättnings-och regex-frågor

Azure Kognitiv sökning använder frekvens-baserad poängsättning ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) för text frågor. För jokertecken och regexfrågor där termernas omfattning kan vara breda, ignoreras dock frekvens faktorn för att förhindra att rankningen prioriteras mot matchningar från rarer villkor. Alla matchningar behandlas lika för jokertecken och regex-sökningar.

## <a name="special-characters"></a>Specialtecken

I vissa fall kanske du vill söka efter ett specialtecken, t. ex. en "❤"-eller "" "-tecken. I sådana fall ser du till att den analys som du använder inte filtrerar ut dessa tecken. Standard Analyzer kringgår många specialtecken, förutom dem från indexet.

Analys verktyg som kommer att Tokenize specialtecken innehåller analys funktionen "blank steg", som tar hänsyn till eventuella tecken sekvenser åtskilda med blank steg som tokens (så att strängen "❤" betraktas som en token). Dessutom skulle en språk analys som Microsoft English Analyzer ("en. Microsoft") ta "€"-strängen som en token. Du kan [testa en analys](/rest/api/searchservice/test-analyzer) för att se vilka tokens som genereras för en specifik fråga.

När du använder Unicode-tecken ser du till att symboler är korrekt avbrotts i fråge-URL: en (till exempel för "❤" använder Escape-sekvensen `%E2%9D%A4+` ). Postman gör översättningen automatiskt.  

## <a name="precedence-grouping"></a>Prioritet (gruppering)

Du kan använda parenteser för att skapa under frågor, inklusive operatorer inom den parentetiska instruktionen. Söker till exempel `motel+(wifi|luxury)` efter dokument som innehåller termen "Motel" och antingen "WiFi" eller "lyxen" (eller båda).

Fält grupperingen liknar varandra men omfångerar grupperingen till ett enda fält. Söker till exempel `hotelAmenities:(gym+(wifi|pool))` fältet "hotelAmenities" för "gymmet" och "WiFi", eller "gymmet" och "pool".  

## <a name="query-size-limits"></a>Storleks gränser för fråga

Det finns en gräns för hur många frågor du kan skicka till Azure Kognitiv sökning. Mer specifikt kan du ha högst 1024-satser (uttryck avgränsade med och, eller, och så vidare). Det finns också en gräns på ungefär 32 KB på storleken på en enskild term i en fråga. Om programmet genererar Sök frågor program mässigt rekommenderar vi att du utformar det på ett sådant sätt att det inte genererar frågor om obegränsad storlek.  

## <a name="see-also"></a>Se även

+ [Fråga exempel för enkel sökning](search-query-simple-examples.md)
+ [Fråga exempel för fullständig Lucene-sökning](search-query-lucene-examples.md)
+ [Sök dokument](/rest/api/searchservice/Search-Documents)
+ [OData-uttrycks syntax för filter och sortering](query-odata-filter-orderby-syntax.md)   
+ [Enkel frågesyntax i Azure Kognitiv sökning](query-simple-syntax.md)
