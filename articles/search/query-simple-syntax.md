---
title: Enkel frågesyntax
titleSuffix: Azure Cognitive Search
description: Referens för enkel frågesyntax som används för fullständiga texts öknings frågor i Azure Kognitiv sökning.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516579"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Enkel frågesyntax i Azure Kognitiv sökning

Azure Kognitiv sökning implementerar två Lucene-baserade frågespråk: [enkel Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) och [Lucene Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Den enkla parsern är mer flexibel och kommer att försöka tolka en begäran även om den inte är helt sammansatt. På grund av den här flexibiliteten är det standardinställningen för frågor i Azure Kognitiv sökning.

Den enkla syntaxen används för frågeuttryck som skickas i **`search`** parametern för en [sökning av sökdokument (REST API)](/rest/api/searchservice/search-documents) , ska inte förväxlas med [OData-syntaxen](query-odata-filter-orderby-syntax.md) som används för [**`$filter`**](search-filters.md) och- [**`$orderby`**](search-query-odata-orderby.md) uttryck i samma begäran. OData-parametrar har olika syntax och regler för att konstruera frågor, undantags strängar och så vidare.

Även om den enkla parsern är baserad på den enkla klassen i [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , så utesluter implementeringen i kognitiv sökning fuzzy search. Om du behöver [fuzzy search](search-query-fuzzy.md)bör du ta hänsyn till den alternativa [fullständiga Lucene-frågesyntaxen](query-lucene-syntax.md) i stället.

## <a name="example-simple-syntax"></a>Exempel (enkel syntax)

Även om **`queryType`** anges nedan, är det standard och kan utelämnas om du inte återställer från en annan typ. Följande exempel är en sökning över oberoende villkor, med ett krav på att alla matchande dokument inkluderar "pool".

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

**`searchMode`** Parametern är relevant i det här exemplet. När booleska operatorer finns i frågan bör du vanligt vis ange att `searchMode=all` *alla* villkor matchas. Annars kan du använda standardvärdet `searchMode=any` som prioriterar åter användning av precision.

Fler exempel finns i [exempel på enkla frågesyntax](search-query-simple-examples.md). Mer information om fråge förfrågningen och parametrarna finns i [Sök efter dokument (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="keyword-search-on-terms-and-phrases"></a>Nyckelords sökning på termer och fraser

Strängar som skickas till **`search`** parametern kan innehålla termer eller fraser i språk som stöds, booleska operatorer, prioritets operatorer, jokertecken eller inledande tecken för "börjar med" frågor, escape-tecken och URL-kodade tecken. **`search`** Parametern är valfri. Ospecificerad, Sök ( `search=*` eller `search=" "` ) returnerar de översta 50 dokumenten i godtycklig (ungraded) ordning.

+ En *terms ökning* är en fråga med en eller flera villkor, där någon av villkoren betraktas som en matchning.

+ En *fras sökning* är en exakt fras som omges av citat tecken `" "` . Till exempel, medan ```Roach Motel``` (utan citat tecken) söker efter dokument som innehåller ```Roach``` och/eller ```Motel``` var som helst i vilken ordning som helst ```"Roach Motel"``` (med citat tecken) kommer bara att matcha dokument som innehåller hela frasen och i den ordningen (lexikalisk analys gäller fortfarande). 

  Beroende på din Sök klient kan du behöva undanta citat tecknen i en fras sökning. I Postman i en POST-begäran skulle exempelvis en Frass ökning på `"Roach Motel"` i begär ande texten anges som `"\"Roach Motel\""` .

Som standard genomgår alla termer eller fraser som har skickats i **`search`** parametern lexikala analyser. Se till att du förstår tokenisering-beteendet för den analys som du använder. När frågeresultat är oväntad kan orsaken ofta spåras till hur termerna ska vara token vid tidpunkten för frågan.

All text med en eller flera villkor betraktas som en giltig start punkt för frågekörningen. Azure Kognitiv sökning matchar dokument som innehåller några eller alla villkor, inklusive eventuella variationer som påträffas under analys av texten.

Så enkelt som det här ljudet finns det en aspekt av frågekörning i Azure Kognitiv sökning som *kan* producera oväntade resultat, vilket ökar i stället för att minska Sök resultaten när fler villkor och operatorer läggs till i Indatasträngen. Huruvida den här utökningen inträffar beror på inkludering av en NOT-Operator, kombinerat med en **`searchMode`** parameter inställning som avgör hur inte tolkas i termer av och eller eller beteenden. Mer information finns i operatorn NOT under [booleska operatorer](#boolean-operators).

## <a name="boolean-operators"></a>Booleska operatorer

Du kan bädda in booleska operatorer i en frågesträng för att förbättra precisionen för en matchning. I den enkla syntaxen är booleska operatorer teckenbaserade. Text operatorer, till exempel ordet och, stöds inte.

| Tecken | Exempel | Användning |
|----------- |--------|-------|
| `+` | `pool + ocean` | En-och-åtgärd. Till exempel `pool + ocean` föreskriver att ett dokument måste innehålla båda termerna.|
| `|` | `pool | ocean` | En eller-åtgärd hittar en matchning när någon av villkoren hittas. I exemplet returnerar frågesyntaxen matchning av dokument som innehåller antingen `pool` eller `ocean` eller båda. Eftersom eller är standard operatorn, kan du också lämna ut den, till exempel `pool ocean` motsvarande  `pool | ocean` .|
| `-` | `pool – ocean` | En åtgärd som inte returnerar matchar i dokument som undantar termen. <br/><br/>Om du vill få det förväntade beteendet för ett NOT-uttryck kan du överväga att ställa in **`searchMode=all`** på begäran. I annat fall får **`searchMode=any`** du matchningar på `pool` , plus matchningar för alla dokument som inte innehåller `ocean` , vilket kan vara mycket av dokumenten. **`searchMode`** Parametern på en förfrågan styr om en term med operatorn not ANDed eller Ored med andra termer i frågan (förutsatt att det inte finns någon `+` eller `|` operator på de andra villkoren). Användningen **`searchMode=all`** ökar precisionen för frågor genom att inkludera färre resultat och tolkas som standard som "och inte". <br/><br/>När du bestämmer dig för en **`searchMode`** inställning bör du tänka på användar interaktions mönstren för frågor i olika program. Användare som söker efter information är mer sannolika att inkludera en operatör i en fråga, i stället för e-handelsplatser som har fler inbyggda navigerings strukturer. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Prefix frågor

För "börjar med"-frågor lägger du till en suffix-operator ( `*` ) som plats hållare för resten av en term. En prefix fråga måste börja med minst ett alfanumeriskt tecken innan du kan lägga till suffix-operatorn.

| Tecken | Exempel | Användning |
|----------- |--------|-------|
| `*` | `lingui*` kommer att matcha på "språklig" eller "Linguini" | Asterisken ( `*` ) representerar ett eller flera tecken med godtycklig längd, vilket ignorerar Skift läge.  |

I likhet med filter söker en prefix-fråga efter en exakt matchning. Det finns därför ingen relevans Poäng (alla resultat får ett Sök resultat på 1,0). Tänk på att prefix frågor kan vara långsamma, särskilt om indexet är stort och prefixet består av ett litet antal tecken. En alternativ metod, till exempel Edge n-gram tokenisering, kan utföras snabbare.

Enkel syntax stöder bara prefix matchning. Använd [fullständig Lucene-syntax för sökning efter jokertecken](query-lucene-syntax.md#bkmk_wildcard)för att matcha suffix eller infix mot slutet eller mitten av en term.

## <a name="escaping-search-operators"></a>Hoppar över Sök operatorer  

I den enkla syntaxen innehåller Sök operatorer följande tecken: `+ | " ( ) ' \`  

Om något av dessa tecken ingår i en token i indexet kan du kringgå det genom att prefixet med ett enda omvänt snedstreck ( `\` ) i frågan. Anta till exempel att du har använt en anpassad analys för hela termen tokenisering och att indexet innehåller strängen "lyxen + hotell". Om du vill få en exakt matchning för denna token infogar du ett escape-tecken: `search=luxury\+hotel` .

För att göra det enklare för vanliga fall finns det två undantag till den här regeln där inga undantag krävs:  

+ Operatorn NOT `-` behöver bara föregås om det är det första tecken efter ett blank steg. Om `-` visas i mitten (t. ex. i `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ) kan du hoppa över undantag.

+ Suffixets operator `*` måste bara föregås om det är det sista före ett blank steg. Om `*` visas i mitten (t. ex. i `4*4=16` ) behövs inga undantags tecken.

> [!NOTE]  
> Som standard tar standard Analyzer bort och avbryter ord för bindestreck, blank steg, et-tecken och andra tecken under [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis). Om du behöver specialtecken som ska finnas kvar i frågesträngen kan du behöva en analys som bevarar dem i indexet. Vissa alternativ är Microsofts [språk analys](index-add-language-analyzers.md)verktyg, som bevarar avstavade ord eller en anpassad analys för mer komplexa mönster. Mer information finns i [del termer, mönster och specialtecken](search-query-partial-matching.md).

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Koda osäkra och reserverade tecken i URL: er

Se till att alla osäkra och reserverade tecken är kodade i en URL. Till exempel är ' # ' ett osäkert värde eftersom det är ett fragment/ankare-ID i en URL. Specialtecknet måste vara kodad till `%23` om den används i en URL. "&" och "=" är exempel på reserverade tecken när de begränsar parametrar och anger värden i Azure Kognitiv sökning. Mer information finns i [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

Osäkra tecken är ``" ` < > # % { } | \ ^ ~ [ ]`` . Reserverade tecken är `; / ? : @ = + &` .

## <a name="special-characters"></a>Specialtecken

I vissa fall kanske du vill söka efter ett specialtecken, t. ex. en "❤"-eller "" "-tecken. I sådana fall ser du till att den analys som du använder inte filtrerar ut dessa tecken. Standard Analyzer kringgår många specialtecken, förutom dem från indexet.

Analys verktyg som kommer att Tokenize specialtecken innehåller analys funktionen "blank steg", som tar hänsyn till eventuella tecken sekvenser åtskilda med blank steg som tokens (så att strängen "❤" betraktas som en token). Dessutom skulle en språk analys som Microsoft English Analyzer ("en. Microsoft") ta "€"-strängen som en token. Du kan [testa en analys](/rest/api/searchservice/test-analyzer) för att se vilka tokens som genereras för en specifik fråga.

När du använder Unicode-tecken ser du till att symboler är korrekt avbrotts i fråge-URL: en (till exempel för "❤" använder Escape-sekvensen `%E2%9D%A4+` ). Postman gör översättningen automatiskt.  

## <a name="precedence-grouping"></a>Prioritet (gruppering)

Du kan använda parenteser för att skapa under frågor, inklusive operatorer inom den parentetiska instruktionen. Söker till exempel `motel+(wifi|luxury)` efter dokument som innehåller termen "Motel" och antingen "WiFi" eller "lyxen" (eller båda).

## <a name="query-size-limits"></a>Storleks gränser för fråga

Om programmet genererar Sök frågor program mässigt rekommenderar vi att du utformar det på ett sådant sätt att det inte genererar frågor om obegränsad storlek. 

+ Längden på URL: en får inte överstiga 8 KB.

+ För POST (och alla andra begär Anden), där bröd texten i begäran inkluderar `search` och andra parametrar, till exempel `filter` och `orderby` , är den maximala storleken 16 MB, där det maximala antalet satser i `search` (uttryck åtskiljda med och, och så vidare) är 1024. Det finns också en gräns på ungefär 32 KB på storleken på en enskild term i en fråga. Mer information finns i [begränsningar för API-begäranden](search-limits-quotas-capacity.md#api-request-limits).

## <a name="next-steps"></a>Nästa steg

Om du ska konstruera frågor program mässigt kan du läsa igenom [full texts ökningen i Azure kognitiv sökning](search-lucene-query-architecture.md) för att förstå stegen i frågans bearbetning och konsekvenserna av text analys.

Du kan också läsa följande artiklar för att lära dig mer om att ställa frågor:

+ [Fråga exempel för enkel sökning](search-query-simple-examples.md)
+ [Fråga exempel för fullständig Lucene-sökning](search-query-lucene-examples.md)
+ [REST API för dokumentsökning](/rest/api/searchservice/Search-Documents)
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [Uttryck för filter och välj (OData)](query-odata-filter-orderby-syntax.md)