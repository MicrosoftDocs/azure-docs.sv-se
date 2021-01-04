---
title: Analys verktyg för språk-och text bearbetning
titleSuffix: Azure Cognitive Search
description: Tilldela analyserare till sökbara textfält i ett index om du vill ersätta standard standard-Lucene med anpassade, fördefinierade eller språkspecifika alternativ.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bbda4268ca00d1c12f851517e2b35add7fba7f9b
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694300"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analys verktyg för text bearbetning i Azure Kognitiv sökning

En *Analyzer* är en komponent i den [fullständiga texts öknings motorn](search-lucene-query-architecture.md) som ansvarar för bearbetning av text i frågesträngar och indexerade dokument. Text bearbetning (kallas även för en lexikal analys) är transformativa och ändrar en sträng genom åtgärder som dessa:

+ Ta bort icke-grundläggande ord (stoppord) och interpunktion
+ Dela upp fraser och avstavade ord i komponent delar
+ Gemener, versaler och gemener
+ Minska ord i primitiva rot formulär för lagrings effektivitet och så att matchningar kan hittas oavsett om de är på flera nivåer

Analysen gäller för `Edm.String` fält som marker ATS som "sökbara", vilket innebär full texts ökning. För fält med den här konfigurationen sker analysen under indexeringen när tokens skapas och sedan igen vid frågekörningen när frågor parsas och motorn söker efter matchande token. Det är mer troligt att en matchning inträffar när samma analys används för både indexering och frågor, men du kan ställa in analys för varje arbets belastning oberoende av varandra, beroende på dina behov.

Frågetyper som inte är full texts ökning, till exempel reguljära uttryck eller fuzzy-sökning, går inte genom analys fasen på fråge sidan. I stället skickar parsern dessa strängar direkt till sökmotorn med det mönster som du anger som bas för matchningen. Vanligt vis kräver dessa fråge formulär hela-sträng-token för att skapa mönster matchnings arbete. Om du vill hämta token för hela termer under indexering kan du behöva [Anpassade analyser](index-add-custom-analyzers.md). Mer information om när och varför sökord analyseras finns i [fullständig texts ökning i Azure kognitiv sökning](search-lucene-query-architecture.md).

Mer bakgrunds information om lexikal analys finns i följande videoklipp med en kort förklaring.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Standard analys  

I Azure Kognitiv sökning-frågor anropas en analys automatiskt i alla sträng fält som marker ATS som sökbara. 

Som standard använder Azure Kognitiv sökning [Apache Lucene standard Analyzer (standard Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)som delar upp text i element enligt reglerna för [Unicode-text segment](https://unicode.org/reports/tr29/) . Dessutom konverterar standard analys alla tecken till gemener. Både indexerade dokument och Sök termer går igenom analysen vid indexering och bearbetning av frågor.  

Du kan åsidosätta standardvärdet för fält-för-fält. Alternativa analys verktyg kan vara [språk analys](index-add-language-analyzers.md) för språklig bearbetning, en [anpassad analys](index-add-custom-analyzers.md)eller en fördefinierad analys från [listan över tillgängliga analyser](index-add-custom-analyzers.md#AnalyzerTable).

## <a name="types-of-analyzers"></a>Typer av analys verktyg

I följande lista beskrivs vilka analys verktyg som är tillgängliga i Azure Kognitiv sökning.

| Kategori | Beskrivning |
|----------|-------------|
| [Standard Lucene Analyzer](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standard. Ingen specifikation eller konfiguration krävs. Den här generella analysen fungerar bra för många språk och scenarier.|
| Fördefinierade analys verktyg | Erbjuds som en färdig produkt som är avsedd att användas i befintligt skick. <br/>Det finns två typer: specialiserade och språk. Vad gör dem "fördefinierade" är att du refererar till dem efter namn, utan konfiguration eller anpassning. <br/><br/>[Specialiserade oberoende-analyser (Language-)](index-add-custom-analyzers.md#AnalyzerTable) används när text inmatningar kräver specialiserad bearbetning eller minimal bearbetning. Icke-språkdefinierade analys verktyg omfattar **Asciifolding**, **nyckelord**, **mönster**, **enkel**, **stopp**, **blank steg**.<br/><br/>[Språk analys](index-add-language-analyzers.md) verktyg används när du behöver omfattande språk stöd för enskilda språk. Azure Kognitiv sökning stöder 35 Lucene-språkanalyser och 50 Microsoft Natural Language Processing-analyser. |
|[Anpassade analysverktyg](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refererar till en användardefinierad konfiguration av en kombination av befintliga element, som består av ett tokenizer (obligatoriskt) och valfria filter (Char eller token).|

Några fördefinierade analyser, till exempel **mönster** eller **stopp**, stöder en begränsad uppsättning konfigurations alternativ. Om du vill ange de här alternativen skapar du en anpassad analys som består av den fördefinierade analysen och ett av de alternativa alternativen som dokumenteras i den [fördefinierade analys referensen](index-add-custom-analyzers.md#AnalyzerTable). Som med valfri anpassad konfiguration kan du ange en ny konfiguration med ett namn, till exempel *myPatternAnalyzer* , för att skilja den från Lucene Pattern Analyzer.

## <a name="how-to-specify-analyzers"></a>Ange analys verktyg

Att ställa in en analys är valfritt. Som en allmän regel kan du prova att använda standard standard-Lucene Analyzer först för att se hur det fungerar. Om frågor inte kan returnera de förväntade resultaten är det ofta rätt lösning att växla till en annan analys.

1. När du skapar en fält definition i [indexet](/rest/api/searchservice/create-index)ställer du in egenskapen  **Analyzer** på något av följande: en [fördefinierad analys](index-add-custom-analyzers.md#AnalyzerTable) , till exempel, `keyword` en [språk analys](index-add-language-analyzers.md) , till exempel `en.microsoft` eller en anpassad analys (definieras i samma index schema).  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   Om du använder en [språk analys](index-add-language-analyzers.md)måste du använda **Analyzer** -egenskapen för att ange den. **SearchAnalyzer** -och **indexAnalyzer** -egenskaperna har inte stöd för språk analys verktyg.

1. Du kan också ställa in **indexAnalyzer** och **searchAnalyzer** för att variera analys funktionen för varje arbets belastning. Dessa egenskaper ställs in tillsammans och ersätter egenskapen **Analyzer** , som måste vara null. Du kan använda olika analys verktyg för förberedelse av data och hämtning om någon av dessa aktiviteter kräver en särskild omvandling som inte behövs av den andra.

   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "whitespace"
    },
   ```

1. För anpassade analys verktyg skapar du en post i avsnittet **[analyserare]** i indexet och tilldelar sedan den anpassade analysen till fält definitionen enligt något av de föregående två stegen. Mer information finns i [skapa index](/rest/api/searchservice/create-index) och även [lägga till anpassade analys](index-add-custom-analyzers.md)verktyg.

## <a name="when-to-add-analyzers"></a>När du ska lägga till analyser

Den bästa tiden för att lägga till och tilldela analyserare är under aktiv utveckling, när du släpper och återskapar index är rutinmässig.

Eftersom analys verktyg används för att Tokenize villkor, bör du tilldela en analys när fältet har skapats. I själva verket är det inte tillåtet att tilldela **Analyzer** -eller **indexAnalyzer** till ett fält som redan har skapats fysiskt (men du kan ändra egenskapen **searchAnalyzer** när som helst utan att påverka indexet).

Om du vill ändra Analyzer för ett befintligt fält måste du [återskapa indexet helt](search-howto-reindex.md) (du kan inte återskapa enskilda fält). För index i produktion kan du skjuta upp en ny version genom att skapa ett nytt fält med den nya Analyzer-tilldelningen och börja använda den i stället för den gamla. Använd [Uppdatera index](/rest/api/searchservice/update-index) för att lägga till det nya fältet och [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) för att fylla det. Som en del av den planerade indexerings servicen kan du senare rensa indexet för att ta bort föråldrade fält.

För att lägga till ett nytt fält i ett befintligt index, anropa [uppdaterings index](/rest/api/searchservice/update-index) för att lägga till fältet och [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) för att fylla det.

För att lägga till en anpassad analys i ett befintligt index, skicka flaggan **allowIndexDowntime** i [Update index](/rest/api/searchservice/update-index) om du vill undvika det här felet:

*"Index uppdatering tillåts inte eftersom det skulle orsaka drift stopp. För att lägga till nya analys verktyg, tokenizers, token-filter eller Character-filter i ett befintligt index ställer du in frågeparametern "allowIndexDowntime" till "true" i begäran om index uppdatering. Observera att den här åtgärden kommer att spara indexet offline under minst några sekunder, vilket gör att dina indexerings-och fråge begär Anden Miss söker. Prestanda-och skrivnings tillgänglighet för indexet kan vara försämrade i flera minuter efter att indexet har uppdaterats, eller längre för mycket stora index. "*

## <a name="recommendations-for-working-with-analyzers"></a>Rekommendationer för att arbeta med analyser

Det här avsnittet innehåller råd om hur du arbetar med analyser.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>En analys för Läs-och skriv åtgärder om du inte har särskilda krav

Med Azure Kognitiv sökning kan du ange olika analys verktyg för indexering och sökning via ytterligare **indexAnalyzer** och **searchAnalyzer** fält egenskaper. Om inget anges används analys uppsättningen med **Analyzer** -egenskapen för både indexering och sökning. Om **Analyzer** inte anges används standard standard-Lucene Analyzer.

En allmän regel är att använda samma analys för både indexering och frågor, om inte särskilda krav anger något annat. Se till att testa noggrant. När text bearbetning skiljer sig vid sökning och indexerings tiden, kan du köra risken för matchning mellan sökord och indexerade villkor när konfigurationerna för sökning och indexerings analys inte är justerade.

### <a name="test-during-active-development"></a>Testa under aktiv utveckling

Att åsidosätta standard Analyzer kräver en index återuppbyggnad. Om möjligt bör du bestämma vilka analys verktyg som ska användas under en aktiv utveckling innan du rullar ett index i produktionen.

### <a name="inspect-tokenized-terms"></a>Inspektera token-villkor

Om en sökning inte kan returnera förväntade resultat, är det mest sannolika scenariet token avvikelser mellan term inmatningar i frågan och token-termer i indexet. Om tokens inte är desamma matchar inte materialisera. Om du vill kontrol lera tokenizer-utdata rekommenderar vi att du använder [analysera API](/rest/api/searchservice/test-analyzer) som ett verktyg för undersökning. Svaret består av tokens, som genereras av en speciell analys.

<a name="examples"></a>

## <a name="rest-examples"></a>REST-exempel

I exemplen nedan visas analys definitioner för några viktiga scenarier.

+ [Exempel på anpassade Analyzer](#Custom-analyzer-example)
+ [Tilldela analyserare till ett fält exempel](#Per-field-analyzer-assignment-example)
+ [Mixa analyser för indexering och sökning](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exempel på språk analys](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exempel på anpassade Analyzer

Det här exemplet illustrerar en analys definition med anpassade alternativ. Anpassade alternativ för char filter, tokenizers och token-filter anges separat som namngivna konstruktioner och refereras sedan till i analys definitionen. Fördefinierade element används som de är och refereras bara till efter namn.

Genom det här exemplet:

* Analys verktyg är en egenskap för fält klassen för ett sökbart fält.
* En anpassad analys är en del av en index definition. Det kan vara lätt att anpassa (till exempel anpassa ett enda alternativ i ett filter) eller anpassas på flera platser.
* I det här fallet är den anpassade analysen "my_analyzer", som i sin tur använder en anpassad standard-tokenizer "my_standard_tokenizer" och två token-filter: gement och anpassat asciifolding filter "my_asciifolding".
* Den definierar också två anpassade tecken filter "map_dash" och "remove_whitespace". Den första ersätter alla streck med under streck medan den andra tar bort alla blank steg. Blank stegen måste vara UTF-8-kodade i mappnings reglerna. Char-filtren tillämpas före tokenisering och kommer att påverka de resulterande token (standard tokenizer pauser på streck och blank steg, men inte på under streck).

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Exempel på analys av analys per fält

Standard Analyzer är standard. Anta att du vill ersätta standardvärdet med en annan fördefinierad analys, till exempel Pattern Analyzer. Om du inte anger anpassade alternativ behöver du bara ange den efter namn i fält definitionen.

Elementet "Analyzer" åsidosätter standard analys baserat på fält-för-fält. Det finns ingen global åsidosättning. I det här exemplet `text1` använder Pattern Analyzer och `text2` , som inte anger någon analys, standardvärdet.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mixa analyser för indexerings-och Sök åtgärder

API: erna inkluderar ytterligare index-attribut för att ange olika analyser för indexering och sökning. Attributen **searchAnalyzer** och **indexAnalyzer** måste anges som ett par, vilket ersätter attributet Single **Analyzer** .


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Exempel på språk analys

Fält som innehåller strängar på olika språk kan använda en språk analys, medan andra fält behåller standardvärdet (eller använder någon annan fördefinierad eller anpassad analys). Om du använder en språk analys måste det användas för både indexerings-och Sök åtgärder. Fält som använder en språk analys kan inte ha olika analys verktyg för indexering och sökning.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>C#-exempel

Om du använder kod exempel för .NET SDK kan du lägga till dessa exempel för att använda eller konfigurera analyser.

+ [Tilldela en inbyggd analys](#Assign-a-language-analyzer)
+ [Konfigurera en analys](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Tilldela en språk analys

En analys som används i befintligt skick, utan konfiguration, anges i en fält definition. Det finns inget krav på att skapa en post i avsnittet **[analyserare]** i indexet. 

Språk analys verktyg används i befintligt skick. Om du vill använda dem anropar du [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer)och anger [LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) -typ som ger en text analys som stöds i Azure kognitiv sökning.

Anpassade analys verktyg anges på samma sätt i fält definitionen, men för att det ska fungera måste du ange analys i index definitionen, enligt beskrivningen i nästa avsnitt.

```csharp
    public partial class Hotel
    {
       . . . 
        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(AnalyzerName = "url-analyze")]
        public string Url { get; set; }
      . . .
    }
```

<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definiera en anpassad analys

När du behöver anpassning eller konfiguration måste du lägga till en Analyzer-konstruktion i ett index. När du har definierat det kan du lägga till den som fält definition som visas i föregående exempel.

Skapa ett [CustomAnalyzer](/dotnet/api/azure.search.documents.indexes.models.customanalyzer) -objekt. En anpassad analys är en användardefinierad kombination av ett känt tokenizer, noll eller flera token-filter, och inget eller flera tecken filter namn:

+ [CustomAnalyzer.Tokenizer](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer)
+ [CustomAnalyzer.TokenFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenfilters)
+ [CustomAnalyzer.CharFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.charfilters)

I följande exempel skapas en anpassad analys med namnet "URL-analys" som använder [uax_url_email tokenizer](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer) och [filtret med gemena token](/dotnet/api/microsoft.azure.search.models.tokenfiltername.lowercase).

```csharp
private static void CreateIndex(string indexName, SearchIndexClient adminClient)
{
   FieldBuilder fieldBuilder = new FieldBuilder();
   var searchFields = fieldBuilder.Build(typeof(Hotel));

   var analyzer = new CustomAnalyzer("url-analyze", "uax_url_email")
   {
         TokenFilters = { TokenFilterName.Lowercase }
   };

   var definition = new SearchIndex(indexName, searchFields);

   definition.Analyzers.Add(analyzer);

   adminClient.CreateOrUpdateIndex(definition);
}
```

Fler exempel finns i [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

## <a name="next-steps"></a>Nästa steg

+ Läs vår omfattande förklaring av [hur full texts ökning fungerar i Azure kognitiv sökning](search-lucene-query-architecture.md). I den här artikeln används exempel för att förklara vilka beteenden som kan vara till hjälp för den aktuella ytan.

+ Försök med ytterligare frågesyntax från avsnittet [Sök efter dokument](/rest/api/searchservice/search-documents#bkmk_examples) exempel eller från [enkel frågesyntax](query-simple-syntax.md) i Sök Utforskaren i portalen.

+ Lär dig hur du använder [språkspecifika lexikala analyser](index-add-language-analyzers.md).

+ [Konfigurera anpassade analys](index-add-custom-analyzers.md) verktyg för minimal bearbetning eller specialiserad bearbetning på enskilda fält.

## <a name="see-also"></a>Se även

 [REST API för dokumentsökning](/rest/api/searchservice/search-documents) 

 [Enkel frågesyntax](query-simple-syntax.md) 

 [Fullständig Lucene-frågesyntax](query-lucene-syntax.md) 
 
 [Hantera sökresultat](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png