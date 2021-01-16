---
title: Synonymer för frågans expansion över ett Sök index
titleSuffix: Azure Cognitive Search
description: Skapa en synonym karta för att expandera omfånget för en Sök fråga på ett Azure Kognitiv sökning-index. Omfattningen utökas för att inkludera motsvarande termer som du anger i en lista.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251632"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonymer i Azure Kognitiv sökning

Med synonym Maps kan du associera motsvarande villkor och expandera omfånget för en fråga utan att användaren behöver ange termen. Exempel: anta att "hund", "Canine" och "Puppy" är synonymer, kommer en fråga på "Canine" att matcha ett dokument som innehåller "hund".

## <a name="create-synonyms"></a>Skapa synonymer

En synonym karta är en till gång som kan skapas en gång och användas av många index. [Tjänst nivån](search-limits-quotas-capacity.md#synonym-limits) avgör hur många synonymer som du kan skapa, från tre synonym kartor för kostnads fria och grundläggande nivåer, upp till 20 för standard nivåerna. 

Du kan skapa flera synonymer för olika språk, till exempel engelska och franska versioner, eller lexikon om ditt innehåll innehåller teknisk eller dold terminologi. Även om du kan skapa flera synonymer i din Sök tjänst kan ett fält bara använda en av dem.

En synonym mappning består av namn, format och regler som fungerar som synonym mappnings poster. Det enda format som stöds är `solr` och `solr` formatet bestämmer regel konstruktionen.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Om du vill skapa en synonym mappning använder du [mappningen skapa synonym (REST API)](/rest/api/searchservice/create-synonym-map) eller Azure SDK. För C#-utvecklare rekommenderar vi att du börjar med att [lägga till synonymer i Azure kognitiv sökning med C#](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Definiera regler

Mappnings reglerna följer synonym filter specifikationen med öppen källkod för Apache Solr, som beskrivs i det här dokumentet: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). `solr` Formatet stöder två typer av regler:

+ likvärdighet (där termer är lika med ersättningar i frågan)

+ explicita mappningar (där villkoren mappas till en explicit term före frågas)

Varje regel måste avgränsas med det nya rad specialtecknet ( `\n` ). Du kan definiera upp till 5 000 regler per synonym mappning i en kostnads fri tjänst och 20 000 regler per karta på andra nivåer. Varje regel kan ha upp till 20 utökningar (eller objekt i en regel). Mer information finns i [synonym gränser](search-limits-quotas-capacity.md#synonym-limits).

Fråga parser kommer att sänka versaler eller versaler, men om du vill bevara specialtecken i strängen, till exempel ett kommatecken eller ett bindestreck, lägger du till lämpliga escape-tecken när du skapar synonym kartan.

### <a name="equivalency-rules"></a>Regler för likvärdighet

Regler för likvärdiga villkor är kommaavgränsade i samma regel. I det första exemplet expanderas en fråga `USA` till `USA` eller `"United States"` eller `"United States of America"` . Observera att om du vill matcha på en fras måste själva frågan vara en offert-omgiven fras fråga.

I fall visar en fråga för att `dog` frågan ska även innehålla `puppy` och `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Explicit mappning

Regler för en explicit mappning betecknas med en pil `=>` . När det här alternativet anges kommer en term ordning i en Sök fråga som matchar den vänstra sidan `=>` att ersättas med alternativen till höger vid tidpunkten för frågan.

I det här fallet kommer en fråga för `Washington` `Wash.` eller `WA` att skrivas om som `WA` och frågemotor söker bara efter matchningar på villkoret `WA` . Explicit mappning gäller endast i den angivna riktningen och skriver inte om frågan `WA` till `Washington` i det här fallet.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Hoppar över specialtecken

Synonymer analyseras under frågans bearbetning. Om du behöver definiera synonymer som innehåller kommatecken eller andra specialtecken kan du kringgå dem med ett omvänt snedstreck, som i det här exemplet:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Eftersom omvänt snedstreck är ett specialtecken i andra språk som JSON och C#, behöver du förmodligen dubbla escape-tecken. Till exempel skulle JSON som skickas till REST API för ovanstående synonym karta se ut så här:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Ladda upp och hantera synonym Maps

Som tidigare nämnts kan du skapa eller uppdatera en synonym mappning utan att avbryta frågor och indexera arbets belastningar. En synonym karta är ett fristående objekt (t. ex. index eller data källor), och så länge inget fält använder den, kommer uppdateringar inte att göra att indexeringen eller frågorna inte fungerar. Men när du har lagt till en synonym mappning till en fält definition, och om du sedan tar bort en synonym mappning, kommer alla frågor som innehåller fälten i fråga att Miss Miss 404-fel.

Att skapa, uppdatera och ta bort en synonym mappning är alltid en hel dokument åtgärd, vilket innebär att du inte kan uppdatera eller ta bort delar av synonym mappningen stegvis. Uppdatering av även en enskild regel kräver en ombelastning.

## <a name="assign-synonyms-to-fields"></a>Tilldela synonymer till fält

När du har överfört en synonym karta kan du aktivera synonymerna för fält av typen `Edm.String` eller `Collection(Edm.String)` , i fält med `"searchable":true` . Som anges kan en fält definition endast använda en synonym karta.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Fråga i motsvarande eller mappade fält

Att lägga till synonymer ger inte nya krav på fråge konstruktion. Du kan utfärda term-och fras frågor precis som du gjorde innan du lade till synonymer. Den enda skillnaden är att om en frågeterm finns i synonym kartan, expanderar eller skriver du om termen eller frasen, beroende på vilken regel som används.

## <a name="how-synonyms-are-used-during-query-execution"></a>Hur synonymer används vid frågekörningen

Synonymer är en metod för att utöka en fråga som kompletterar innehållet i ett index med motsvarande villkor, men endast för fält som har en synonym tilldelning. Om en fråga som omfattas av en fråga *utesluter* ett synonymt-aktiverat fält visas inte matchningar från synonym kartan.

Synonymer är underställda samma text analys som det associerade fältet för synonyma, aktiverade fält. Om ett fält till exempel analyseras med hjälp av standard-Lucene Analyzer, kommer synonym villkoren också att omfattas av standard-Lucene Analyzer vid tidpunkten för frågan. Om du vill bevara interpunktion, t. ex. punkter eller bindestreck, på synonym termen, använder du en innehålls-bevarande analys i fältet.

Internt skriver funktionen synonymer om den ursprungliga frågan med synonymer med operatorn OR. Av den anledningen kan träff markeringar och bedömnings profiler behandla den ursprungliga termen och synonymer som likvärdiga.

Synonymer gäller endast för formulär med fritext frågor och stöds inte för filter, ansikts, komplettera automatiskt eller förslag. Autoavsluta och förslag baseras bara på den ursprungliga termen. synonym matchningar visas inte i svaret.

Synonym expansionar gäller inte för sökord med jokertecken. termerna prefix, fuzzy och regex expanderas inte.

Om du behöver göra en enskild fråga som använder synonym expansion och jokertecken, regex eller fuzzy-sökningar kan du kombinera frågorna med hjälp av eller-syntaxen. Om du t. ex. vill kombinera synonymer med jokertecken för enkel frågesyntax skulle termen vara `<query> | <query>*` .

Om du har ett befintligt index i en utvecklings miljö (icke-produktion) kan du experimentera med en liten ord lista för att se hur tillägget av synonymer ändrar Sök upplevelsen, inklusive påverkan på bedömnings profiler, träff markering och förslag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en synonym karta (REST API)](/rest/api/searchservice/create-synonym-map)