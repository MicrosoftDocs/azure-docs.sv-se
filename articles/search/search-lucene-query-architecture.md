---
title: Full text fråga och index motor arkitektur (Lucene)
titleSuffix: Azure Cognitive Search
description: Undersöker process bearbetnings-och dokument hämtnings begrepp i Lucene för full texts ökning, som rör Azure Kognitiv sökning.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002756"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Full texts ökning i Azure Kognitiv sökning

Den här artikeln är för utvecklare som behöver en djupare förståelse för hur Lucene full texts ökning fungerar i Azure Kognitiv sökning. För text frågor ger Azure Cognitive Search sömlöst förväntade resultat i de flesta fall, men ibland kan du få ett resultat som ser lite konstigt ut. I dessa fall är det bra att känna till de fyra stegen i Lucene-frågekörning (frågeparsning, lexikal analys, dokumentmatchning och poängsättning) när du vill identifiera vissa ändringar av frågeparametrar eller indexkonfiguration som ger önskat resultat. 

> [!Note] 
> Azure Kognitiv sökning använder Lucene för full texts ökning, men Lucene-integrering är inte uttömmande. Vi selektivt exponerar och utökar funktionerna i Lucene för att aktivera scenarier som är viktiga för Azure Kognitiv sökning. 

## <a name="architecture-overview-and-diagram"></a>Arkitektur översikt och diagram

Bearbetning av en fullständig text Sök fråga börjar med att parsa frågetexten för att extrahera Sök villkor. Sökmotorn använder ett index för att hämta dokument med matchande villkor. Enskilda sökord är ibland brutna och rekonstituerade i nya formulär för att omvandla ett bredare netto över vad som kan anses vara en möjlig matchning. En resultat uppsättning sorteras sedan efter resultat som tilldelas varje enskilt matchnings dokument. De som visas överst i listan rangordnas returneras till det anropande programmet.

Frågekörningen har fyra steg: 

1. Fråga parsing 
2. Lexikal analys 
3. Dokument hämtning 
4. Resultat 

Diagrammet nedan visar de komponenter som används för att bearbeta en Sök förfrågan. 

 ![Lucene Query Architecture diagram i Azure Kognitiv sökning][1]


| Nyckelkomponenter | Funktions Beskrivning | 
|----------------|------------------------|
|**Fråga parser** | Avgränsa sökorden från fråga operatörer och skapa frågans struktur (ett frågeuttryck) som ska skickas till sökmotorn. |
|**Analysverktyg** | Genomför lexikal analys av sökord. Den här processen kan innebära omvandling, borttagning eller expandering av sökord. |
|**Index** | En effektiv data struktur som används för att lagra och organisera sökbara termer som extraheras från indexerade dokument. |
|**Sökmotor** | Hämtar och resultat av matchande dokument baserat på innehållet i det inverterade indexet. |

## <a name="anatomy-of-a-search-request"></a>Anatomy of a Search Request

En Sök förfrågan är en fullständig specifikation av vad som ska returneras i en resultat uppsättning. I enklaste form är det en tom fråga utan några kriterier av något slag. Ett mer realistiskt exempel inkluderar parametrar, flera sökord, som kan omfatta vissa fält, med eventuellt ett filter uttryck och ordnings regler.  

Följande exempel är en Sök förfrågan som du kan skicka till Azure Kognitiv sökning med hjälp av [REST API](/rest/api/searchservice/search-documents).  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

I den här förfrågan gör sökmotorn följande:

1. Filtrerar bort dokument där priset är minst $60 och mindre än $300.
2. Kör frågan. I det här exemplet består Sök frågan av fraser och villkor: `"Spacious, air-condition* +\"Ocean view\""` (användarna brukar inte ange interpunktion, utan även i exemplet kan vi förklara hur analys verktyget hanterar det). I den här frågan genomsöker sökmotorn beskrivningen och rubrik fälten som anges i `searchFields` för dokument som innehåller "Oceanien", och dessutom på termen "Spacious" eller på villkor som börjar med prefixet "Air-condition". `searchMode`Parametern används för att matcha på valfri term (standard) eller alla, för fall där en term inte uttryckligen krävs ( `+` ).
3. Beställer den resulterande uppsättningen hotell genom närhet till en angiven geografisk plats och sedan tillbaka till det anropande programmet. 

Merparten av den här artikeln är om bearbetning av *Sök frågan*: `"Spacious, air-condition* +\"Ocean view\""` . Filtrering och sortering är utanför omfånget. Mer information finns i [referens dokumentationen för Sök-API](/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Steg 1: fråga parsing 

Som anges är frågesträngen den första raden i begäran: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

Frågans parser delar upp operatorer (till exempel `*` och `+` i exemplet) från Sök villkor och dekonstruerar Sök frågan i under *frågor* av en typ som stöds: 

+ *term fråga* för fristående villkor (t. ex. Spacious)
+ *fras fråga* för citerade villkor (t. ex. havs Visa)
+ *prefix fråga* för villkor följt av en prefix operator `*` (t. ex. Air-villkor)

En fullständig lista över frågetyper som stöds finns i [Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) -frågesyntax

Operatorer som är associerade med en under fråga avgör om frågan "måste vara" eller "ska vara" nöjd i ordningen för att ett dokument ska anses vara en matchning. Till exempel `+"Ocean view"` är "måste" på grund av `+` operatorn. 

Frågans parser omstrukturerar under frågorna till ett *frågeuttryck* (en intern struktur som representerar frågan) som den går vidare till i sökmotorn. I det första steget av frågans parsning ser hierarkiträdet ut så här.  

 ![Boolesk fråga searchmode alla][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Parsar som stöds: enkel och fullständig Lucene 

 Azure Kognitiv sökning visar två olika frågespråk, `simple` (standard) och `full` . Genom att ange `queryType` parametern med din sökbegäran, anger du det Query-tolkare som du väljer så att det vet hur du kan tolka operatorer och syntax. Det [enkla frågespråket](/rest/api/searchservice/simple-query-syntax-in-azure-search) är intuitivt och robust, vilket ofta är lämpligt att tolka användarindata som-är utan bearbetning på klient sidan. Det stöder sökoperatörer som är bekanta med Webbs öknings motorer. Det [fullständiga Lucene-frågespråket](/rest/api/searchservice/lucene-query-syntax-in-azure-search), som du får genom att ställa in `queryType=full` , utökar standard språket för enkla frågor genom att lägga till stöd för fler operatorer och frågetyper som jokertecken, fuzzy, regex och fält omfattningar frågor. Ett reguljärt uttryck som skickas i en enkel frågesyntax tolkas exempelvis som en frågesträng och inte ett uttryck. I exempel förfrågan i den här artikeln används hela Lucene-frågespråket.

### <a name="impact-of-searchmode-on-the-parser"></a>Effekt av searchMode på parsern 

En annan Sök begär ande parameter som påverkar parsningen är `searchMode` parametern. Den styr standard operatorn för booleska frågor: any (standard) eller alla.  

När `searchMode=any` , som är standardvärdet, är avstånds avgränsaren mellan Spacious och Air-condition eller ( `||` ), vilket gör exempel frågan text som motsvarar: 

```
Spacious,||air-condition*+"Ocean view" 
```

Explicita operatorer, som `+` i `+"Ocean view"` , är tvetydiga i boolesk fråge konstruktion (termen *måste* matcha). Mindre uppenbart är hur man tolkar återstående villkor: Spacious och Air-villkor. Ska sökmotorn hitta matchningar i vyn havs- *och* Spacious- *och* Air-villkor? Eller om den ska kunna hitta *en* till havs-vy plus någon av de återstående villkoren? 

Som standard ( `searchMode=any` ) förutsätter sökmotorn den bredare tolkningen. Något av fälten *måste* matchas, reflekterande "eller" semantik. Det första frågeuttrycket som illustrerades tidigare, med de två "borde"-åtgärderna, visar standardvärdet.  

Anta att vi nu har angett `searchMode=all` . I det här fallet tolkas området som en "och"-åtgärd. Var och en av de återstående villkoren måste båda finnas i dokumentet för att kvalificera sig som en matchning. Den resulterande exempel frågan tolkas enligt följande: 

```
+Spacious,+air-condition*+"Ocean view"
```

Ett ändrat frågeuttryck för den här frågan skulle vara följande, där ett matchande dokument är skärnings punkten för alla tre under frågor: 

 ![Boolesk fråga searchmode alla][3]

> [!Note] 
> Att välja `searchMode=any` över `searchMode=all` är ett beslut som vi har kommit till genom att köra representativa frågor. Användare som troligen kommer att inkludera operatorer (vanligt vid sökning i dokument Arkiv) kan hitta resultat mer intuitivt om `searchMode=all` informerar booleska frågans konstruktioner. Mer information om mellanuppspelning mellan `searchMode` och-operatorer finns i [enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Steg 2: lexikal analys 

Lexikala analyserare bearbetar *term frågor* och *fras frågor* efter att taggträdet är strukturerat. En analys accepterar de text inmatningar som anges för den av parsern, bearbetar texten och skickar sedan tillbaka token-termer som ska införlivas i frågeuttrycket. 

Den vanligaste typen av lexikal analys är *språklig analys* som omvandlar sökord baserat på regler som är specifika för ett specifikt språk: 

* Minska en frågeterm till rot formen av ett ord 
* Ta bort icke-nödvändiga ord (stoppord, t. ex. "The" eller "och" på engelska) 
* Dela upp ett sammansatt ord i komponent delar 
* Gement versal i ett versal ord 

Alla dessa åtgärder tenderar att radera skillnader mellan text indata från användaren och de termer som lagras i indexet. Sådana åtgärder går utöver text bearbetningen och kräver djupgående kunskap om själva språket. För att lägga till det här lagret av språklig medvetenhet stöder Azure Kognitiv sökning en lång lista med [språk analys](/rest/api/searchservice/language-support) verktyg från både Lucene och Microsoft.

> [!Note]
> Analys kraven kan vara från minimal till att utveckla beroende på ditt scenario. Du kan kontrol lera komplexiteten för en lexikal analys genom att välja en av de fördefinierade analyserna eller genom att skapa en egen [anpassad analys](/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analyser är begränsade till sökbara fält och anges som en del av en fält definition. På så sätt kan du variera lexikal analys baserat på varje fält. Ospecificerad används *standard* Lucene Analyzer.

I vårt exempel har det första frågeuttrycket, "Spacious", med ett versaler och ett kommatecken att frågan tolkar som en del av frågeintervallet (ett kommatecken betraktas inte som en frågespråk).  

När standard analys processen bearbetar termen kommer den att vara i gemener och "Spacious" och ta bort kommatecknet. Det ändrade frågeuttrycket ser ut så här: 

 ![Boolesk fråga med analyserade villkor][4]

### <a name="testing-analyzer-behaviors"></a>Testa Analyzer-beteenden 

Beteendet för en analys kan testas med hjälp av [analys-API: et](/rest/api/searchservice/test-analyzer). Ange den text som du vill analysera för att se vilka termer som visas i Analyzer. Om du till exempel vill se hur standard analys processen bearbetar texten "Air-condition" kan du utfärda följande begäran:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

Standard analysen delar in inmatad text i följande två tokens, och kommenterar dem med attribut som start-och slut förskjutningar (används för träff markering) och deras placering (används för fras matchning):

```json
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Undantag till lexikalisk analys 

Lexikalisk analys gäller endast för frågetyper som kräver fullständiga villkor – antingen en term fråga eller en fras fråga. Den gäller inte för frågetyper med ofullständiga villkor – prefix fråga, jokertecken, regex fråga – eller till en Fuzzy-fråga. Dessa frågetyper, inklusive prefixs frågan med termen `air-condition*` i vårt exempel, läggs direkt till i frågans träd, vilket kringgår analys steget. Den enda omvandlingen som utförs på sökorden för dessa typer är lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Steg 3: dokument hämtning 

Dokument hämtning syftar på att hitta dokument med matchande villkor i indexet. Det här steget tolkas bäst genom ett exempel. Vi börjar med ett hotell index med följande enkla schema: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Anta ytterligare att det här indexet innehåller följande fyra dokument: 

```json
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
```

**Så här indexeras termer**

För att förstå hämtningen är det bra att känna till några grundläggande information om indexering. Lagrings enheten är ett inverterat index, ett för varje sökbart fält. I ett inverterat index är en sorterad lista över alla termer från alla dokument. Varje term mappar till listan över dokument där det förekommer, som i exemplet nedan.

Om du vill skapa villkoren i ett inverterat index utför sökmotorn en lexikal analys över innehållet i dokumenten, på samma sätt som vid bearbetning av frågor:

1. *Text inmatningar* skickas till en analys, en lägre bokstäver, bort skiljetecken och så vidare, beroende på analys konfigurationen. 
2. *Tokens* är resultatet av lexikal analys.
3. *Villkoren* läggs till i indexet.

Det är vanligt, men krävs inte, för att använda samma analyser för söknings-och indexerings åtgärder så att villkoren i frågan ser mer likadana ut som termer i indexet.

> [!Note]
> Med Azure Kognitiv sökning kan du ange olika analys verktyg för indexering och sökning via ytterligare `indexAnalyzer` `searchAnalyzer` parametrar och fält parametrar. Om inget anges används den analys uppsättning som har `analyzer` egenskapen för både indexering och sökning.  

**Inverterat index för exempel dokument**

I vårt exempel, för fältet **title** ser det inverterade indexet ut så här:

| Period | Dokument lista |
|------|---------------|
| atman | 1 |
| bollar | 2 |
| Personalen | 1, 3 |
| havet | 4  |
| playa | 3 |
| utväg | 3 |
| retreat | 4 |

I fältet Rubrik visas bara *hotell* i två dokument: 1, 3.

I fältet **Beskrivning** ser indexet ut så här:

| Period | Dokument lista |
|------|---------------|
| löst | 3
| och | 4
| bollar | 1
| villkor | 3
| nöjd | 3
| avstånd | 1
| Julön | 2
| Kaua ʻ i | 2
| finns | 2
| north | 2
| havet | 1, 2, 3
| av | 2
| på |2
| tyst | 4
| kylrum  | 1, 3
| secluded | 4
| Shore | 2
| spacious | 1
| Det | 1, 2
| på | 1
| visa | 1, 2, 3
| vandra | 1
| med | 3


**Matcha sökord mot indexerade villkor**

Med de inverterade indexen ovan kan vi gå tillbaka till exempel frågan och se hur matchande dokument hittas för vår exempel fråga. Kom ihåg att det slutgiltiga trädet ser ut så här: 

 ![Boolesk fråga med analyserade villkor][4]

Under frågekörningen körs enskilda frågor mot sökbara fält oberoende av varandra. 

+ TermQuery, "Spacious", matchar dokument 1 (hotell Atman). 

+ PrefixQuery, "Air-condition *", matchar inte några dokument. 

  Detta är ett beteende som ibland samär utvecklare. Även om termen Air-conditiont finns i dokumentet delas det upp i två termer av standard analys verktyget. Kom ihåg att prefixfrågor, som innehåller ofullständiga termer, inte analyseras. Därför visas villkor med prefix "Air-condition" i det inverterade indexet och inte hittas.

+ PhraseQuery, "sjösikten", slår upp termerna "oceanen" och "View" och kontrollerar hur nära villkoren i det ursprungliga dokumentet är. Dokument 1, 2 och 3 matchar den här frågan i fältet Beskrivning. Meddelande om dokument 4 har termen oceanen i rubriken, men betraktas inte som en matchning, eftersom vi letar efter frasen "sjöliggande vy" i stället för enskilda ord. 

> [!Note]
> En Sök fråga körs oberoende av alla sökbara fält i Azures Kognitiv sökning index om du inte begränsar de fält som har angetts med `searchFields` parametern, enligt beskrivningen i exempel Sök förfrågan. Dokument som matchar i något av de valda fälten returneras. 

I hela frågan är dokumenten som matchar 1, 2, 3, för frågan i fråga. 

## <a name="stage-4-scoring"></a>Steg 4: Poäng  

Varje dokument i en Sök Resultat uppsättning tilldelas en relevans poäng. Funktionen för relevans poängen är att rangordna högre dokument som bäst svarar på en användar fråga som uttrycks av Sök frågan. Poängen beräknas utifrån statistiska egenskaper för villkor som matchar. I en bedömnings formels kärna är [TF/IDF (term frekvens – invertering av dokument frekvens)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). I frågor som innehåller ovanliga och gemensamma villkor befordrar TF/IDF resultat som innehåller den sällsynta termen. Till exempel, i ett hypotetiskt index med alla Wikipedia-artiklar, från dokument som matchade frågan *till VD*, betraktas dokument som matchar på *VD* mer relevanta än dokument som *matchar.*


### <a name="scoring-example"></a>Bedömnings exempel

Återkalla de tre dokument som matchar vår exempel fråga:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
```

Dokument 1 matchade frågan bäst eftersom både termen *Spacious* och den obligatoriska frasen i *vyn* i fältet Beskrivning visas. De två följande dokumenten matchar bara frasen i frasen i *vyn*. Det kan vara överraskande att relevans-poängen för dokument 2 och 3 är olika trots att de matchade frågan på samma sätt. Det beror på att bedömnings formeln har fler komponenter än bara TF/IDF. I det här fallet har dokument 3 tilldelats ett något högre poäng eftersom beskrivningen är kortare. Lär dig mer om [Lucene: s praktiska bedömnings formel](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) för att förstå hur fält längd och andra faktorer kan påverka relevans poängen.

Vissa frågetyper (jokertecken, prefix, regex) bidrar alltid till en konstant poäng till det övergripande dokument resultatet. Detta gör att matchningar som upptäckts via frågans expansion tas med i resultatet, men utan att det påverkar rangordningen. 

Ett exempel illustrerar varför den här saken. Sökningar i jokertecken, inklusive prefix, är tvetydiga efter definition eftersom indatatypen är en delvis sträng med potentiella matchningar på ett mycket stort antal olika villkor (Tänk på att "guidad visning *" med matchningar som finns på "visningar", "Tourettes" och "Tourmaline"). Med hänsyn till arten av dessa resultat går det inte att på ett rimligt sätt härleda vilka termer som är mer värdefulla än andra. Av den anledningen ignorerar vi term frekvenser när Poäng resultat visas i frågor av typen jokertecken, prefix och regex. I en sökbegäran med flera delar som innehåller delar av och fullständiga villkor, ingår resultat från den delvis indatan med en konstant Poäng för att undvika förskjutning mot potentiellt oväntade matchningar.

### <a name="score-tuning"></a>Poäng justering

Det finns två sätt att justera relevans poängen i Azure Kognitiv sökning:

1. **Bedömnings profiler** marknadsför dokument i den rangordnade listan över resultat baserat på en uppsättning regler. I vårt exempel kan vi överväga dokument som matchades i rubrik fältet mer relevanta än dokument som matchar i fältet Beskrivning. Om vårt index har ett pris fält för varje hotell skulle vi dessutom kunna befordra dokument med lägre pris. Läs mer om hur du [lägger till bedömnings profiler i ett sökindex.](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Term ökning** (endast tillgängligt i fullständig Lucene-frågesyntax) tillhandahåller en förstärknings operator `^` som kan tillämpas på alla delar av frågans träd. I vårt exempel, i stället för att söka efter prefixet *Air-condition* \* , kan en söka efter antingen den exakta termen *Air-villkor* eller prefix, men dokument som matchar den exakta termen rangordnas högre genom att tillämpa Boost på term frågan: * Air-villkor ^ 2 | | luft-villkor * *. Läs mer om [term ökning](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Poängsättning i ett distribuerat index

Alla index i Azure Kognitiv sökning delas upp automatiskt i flera Shards, så att vi snabbt kan distribuera indexet mellan flera noder under tjänstens skala upp eller ned. När en Sök förfrågan utfärdas utfärdas den för varje Shard oberoende. Resultaten från varje Shard slås sedan samman och ordnas efter Poäng (om ingen annan sortering definieras). Det är viktigt att veta att bedömnings funktionen viktar frågeterm-frekvensen mot den inverterade dokument frekvensen i alla dokument i Shard, inte över alla Shards!

Det innebär att en relevans Poäng *kan* vara olika för identiska dokument om de finns på olika Shards. Sådana skillnader tenderar att försvinna när antalet dokument i indexet växer på grund av ytterligare jämn fördelning. Det går inte att anta vilka Shard som ett givet dokument kommer att placeras. Förutsatt att en dokument nyckel inte ändras, kommer den dock alltid att tilldelas samma Shard.

I allmänhet är dokument poängen inte det bästa attributet för att beställa dokument om order stabiliteten är viktig. Om du till exempel har två dokument med identiska poäng, finns det ingen garanti för att det visas först i efterföljande körningar av samma fråga. Dokument poängen bör bara ge en allmän uppfattning om dokumentets relevans i förhållande till andra dokument i resultat uppsättningen.

## <a name="conclusion"></a>Slutsats

Det framgångs rik Internet sökmotorer har fått förväntningar för full texts ökning över privata data. För nästan alla typer av Sök upplevelser förväntar vi dig nu motorn för att förstå vår avsikt, även om termerna är felstavade eller ofullständiga. Vi kan till och med vänta på matchningar baserat på närmast likvärdiga villkor eller synonymer som vi aldrig har angett.

Från en teknisk synpunkt är full texts ökning mycket komplex, vilket kräver avancerad språklig analys och en systematisk metod för bearbetning på sätt som destillerar, expanderar och transformerar sökord för att leverera ett relevant resultat. Med tanke på de kompliceradeste riskerna finns det många faktorer som kan påverka resultatet av en fråga. Av den anledningen kan man investera tiden för att förstå Mechanics i full texts ökning och få konkreta förmåner vid försök att arbeta genom oväntade resultat.  

I den här artikeln utforskas fullständig texts ökning i samband med Azure-Kognitiv sökning. Vi hoppas att det ger dig tillräckligt med bakgrund för att identifiera potentiella orsaker och lösningar för att lösa vanliga frågor. 

## <a name="next-steps"></a>Nästa steg

+ Bygg exempel indexet, prova olika frågor och granska resultaten. Instruktioner finns i [bygga och fråga ett index i portalen](search-get-started-portal.md#query-index).

+ Försök med ytterligare frågesyntax från avsnittet [Sök efter dokument](/rest/api/searchservice/search-documents#bkmk_examples) exempel eller från [enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) i Sök Utforskaren i portalen.

+ Granska [bedömnings profiler](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) om du vill justera rangordningen i Sök programmet.

+ Lär dig hur du använder [språkspecifika lexikala analyser](/rest/api/searchservice/language-support).

+ [Konfigurera anpassade analys](/rest/api/searchservice/custom-analyzers-in-azure-search) verktyg för minimal bearbetning eller specialiserad bearbetning på specifika fält.

## <a name="see-also"></a>Se även

[REST API för dokumentsökning](/rest/api/searchservice/search-documents) 

[Enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Fullständig Lucene-frågesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Hantera sökresultat](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png