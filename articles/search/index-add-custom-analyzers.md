---
title: Lägg till anpassade analys verktyg i sträng fält
titleSuffix: Azure Cognitive Search
description: Konfigurera text-tokenizers och tecken filter för att utföra text analyser på strängar vid indexering och frågor.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 831e57a68c79c245b96baec0fc3d062c4c9112c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604448"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Lägga till anpassade analys verktyg i sträng fält i ett Azure Kognitiv sökning-index

En *anpassad analys* är en kombination av tokenizer, ett eller flera token-filter och ett eller flera teckenuppsättningar som du definierar i Sök indexet och sedan referensen för fält definitioner som kräver anpassad analys. Tokenizer ansvarar för att dela upp text i tokens och token-filter för att ändra tokens som genereras av tokenizer. Med tecken filter förbereder du inmatad text innan den bearbetas av tokenizer. 

Med en anpassad analys kan du styra processen med att konvertera text till index bara och sökbara token genom att låta dig välja vilka typer av analys eller filtrering som ska anropas, samt i vilken ordning de förekommer. Om du vill använda en inbyggd analys med anpassade alternativ, till exempel att ändra maxTokenLength på standard, skulle du skapa en anpassad analys med ett användardefinierat namn för att ange dessa alternativ.

Situationer där anpassade analys verktyg kan vara användbara:

- Använd tecken filter för att ta bort HTML-kod innan text inmatning är token, eller Ersätt vissa tecken eller symboler.

- Fonetisk sökning. Lägg till ett fonetiskt filter om du vill aktivera sökning baserat på hur ett ord låter, inte hur det är stavat.  

- Inaktivera lexikalisk analys. Använd nyckelords analys för att skapa sökbara fält som inte analyseras.  

- Sökning efter snabb prefix/suffix. Lägg till token för kant-N-gram för att indexera prefix för ord för att aktivera matchning med snabb prefix. Kombinera det med det omvända token-filtret för att matcha suffix.  

- Anpassad tokenisering. Använd t. ex. blank steg tokenizer för att dela upp meningar i token med blank steg som avgränsare  

- ASCII-vikning. Lägg till standard-ASCII-filtret för att normalisera dia kritiska tecken som ö eller ê i Sök termer.  

Om du vill skapa en anpassad analys anger du den i avsnittet "analyserare" i ett index i design läge och refererar sedan till den i sökbara EDM. String-fält med hjälp av egenskapen "Analyzer" eller "indexAnalyzer" och "searchAnalyzer"-paret.

> [!NOTE]  
> Anpassade analys verktyg som du skapar visas inte i Azure Portal. Det enda sättet att lägga till en anpassad analys är genom kod som definierar ett index. 

## <a name="create-a-custom-analyzer"></a>Skapa ett anpassat analysverktyg

En analys definition innehåller ett namn, en typ, ett eller flera Character filter, högst ett tokenizer och ett eller flera token filter för bearbetning efter tokenisering. Character filter används före tokenisering. Token filter och Character filter används från vänster till höger.

- Namn i en anpassad analys måste vara unika och får inte vara samma som någon av de inbyggda analys verktyg, tokenizers, token filter eller tecken filter. Det får bara innehålla bokstäver, siffror, blank steg, bindestreck eller under streck, får bara börja och sluta med alfanumeriska tecken och är begränsat till 128 tecken. 

- Typen måste vara #Microsoft. Azure. search. CustomAnalyzer.

- "charFilters" kan vara ett eller flera filter från [Character filter](#CharFilter), bearbetas före tokenisering, i den ordning som anges. Vissa Character-filter har alternativ, som kan anges i ett "charFilter"-avsnitt. Character filter är valfria.

- "tokenizer" är exakt ett [tokenizer](#tokenizers). Ett värde måste anges. Om du behöver fler än en tokenizer kan du skapa flera anpassade analys verktyg och tilldela dem till fält för fält i index schemat.

- "tokenFilters" kan vara ett eller flera filter från [token filter](#TokenFilters), bearbetas efter tokenisering, i angiven ordning. För token filter som har alternativ lägger du till avsnittet "tokenFilter" för att ange konfigurationen. Token filter är valfria.

Analys verktyg får inte skapa token som är längre än 300 tecken, eller så fungerar inte indexeringen. Om du vill trimma lång token eller exkludera dem använder du **TruncateTokenFilter** och **LengthTokenFilter** . Se [**token filters**](#TokenFilters) för referens.

```json
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

I en index definition kan du placera det här avsnittet var som helst i bröd texten i en Create index-begäran, men vanligt vis hamnar det i slutet:  

```json
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Analys definitionen är en del av det större indexet. Definitioner för char filter, tokenizers och token filter läggs bara till i indexet om du anger anpassade alternativ. Om du vill använda ett befintligt filter eller tokenizer som det är, anger du det efter namn i analys definitionen. Mer information finns i [skapa index (rest)](/rest/api/searchservice/create-index). Fler exempel finns i [lägga till analyser i Azure kognitiv sökning](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Testa anpassade analys verktyg

Du kan använda [test analys (rest)](/rest/api/searchservice/test-analyzer) för att se hur en analys delar en text i tokens.

**Förfrågan**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Response**

```http
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Uppdatera anpassade analys verktyg

När en analys, ett tokenizer, ett token-filter eller ett Character-filter har definierats, kan det inte ändras. Nya kan bara läggas till i ett befintligt index om `allowIndexDowntime` flaggan har angetts till sant i index uppdaterings förfrågan:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Den här åtgärden tar ditt index offline under minst några sekunder, vilket gör att dina indexerings-och fråge begär Anden misslyckades. Prestanda-och skrivnings tillgänglighet för indexet kan vara försämrade i flera minuter efter att indexet har uppdaterats, eller längre för mycket stora index, men dessa effekter är temporära och kommer att lösa sig på egen hand.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Inbyggda analyser

Om du vill använda en inbyggd analys med anpassade alternativ kan du skapa en anpassad analys funktion genom att ange dessa alternativ. För att du ska kunna använda en inbyggd analys i befintligt skick behöver du däremot bara referera till [det efter namn](search-analyzers.md#how-to-specify-analyzers) i fält definitionen.

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Beskrivning och alternativ**|  
|-----------------|-------------------------------|---------------------------|  
|[följt](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typen gäller endast när alternativen är tillgängliga) |Behandlar hela innehållet i ett fält som en enda token. Detta är användbart för data som post nummer, ID: n och vissa produkt namn.|  
|[ofta](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Flexibelt separerar text till termer via ett reguljärt uttrycks mönster. </br></br>**Alternativ** </br></br>gemener (typ: bool) – anger om termerna är små. Standardvärdet är true. </br></br>[mönster](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: sträng) – ett mönster för reguljära uttryck för att matcha token-avgränsare. Standardvärdet är `\W+` , som matchar tecken som inte är ord. </br></br>[flaggor](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: sträng) – reguljära uttrycks flaggor. Standardvärdet är en tom sträng. Tillåtna värden: CANON_EQ, CASE_INSENSITIVE, kommentarer, DOTALL, LITERAL, Multiline, UNICODE_CASE, UNIX_LINES </br></br>stoppord (typ: sträng mat ris) – en lista över stoppord. Standardvärdet är en tom lista.|  
|[gång](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typen gäller endast när alternativen är tillgängliga) |Delar upp text på icke-bokstäver och konverterar dem till gemener. |  
|[standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(Kallas även standard. Lucene)|StandardAnalyzer|Standard Lucene Analyzer, som består av standard tokenizer, gemena filter och stopp filter. </br></br>**Alternativ** </br></br>maxTokenLength (typ: int) – maximal längd på token. Standardvärdet är 255. Token som är längre än den maximala längden delas. Maximal längd på token som kan användas är 300 tecken. </br></br>stoppord (typ: sträng mat ris) – en lista över stoppord. Standardvärdet är en tom lista.|  
|standardasciifolding. Lucene|(typen gäller endast när alternativen är tillgängliga) |Standard Analyzer med ASCII-filter. |  
|[Stanna](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Delar upp text på icke-bokstäver, använder filter för gemener och stoppord-token. </br></br>**Alternativ** </br></br>stoppord (typ: sträng mat ris) – en lista över stoppord. Standardvärdet är en fördefinierad lista för engelska. |  
|[blank steg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typen gäller endast när alternativen är tillgängliga) |En analys som använder blank steg tokenizer. Tokens som är längre än 255 tecken delas.|  

 <sup>1</sup> analys typer har alltid prefixet "#Microsoft. Azure. search", så att "PatternAnalyzer" faktiskt anges som "#Microsoft. Azure. search. PatternAnalyzer". Vi har tagit bort prefixet för det kortfattat, men prefixet krävs i koden.

Analyzer_type anges endast för analyser som kan anpassas. Om det inte finns några alternativ, vilket är fallet med nyckelords analys, finns det ingen associerad #Microsoft. Azure. search-typ.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Character filter

I tabellen nedan är de tangent filter som implementeras med Apache Lucene länkade till Lucene API-dokumentationen.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Ett char-filter som försöker utesluta HTML-konstruktioner.|  
|[mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Ett tecken filter som använder mappningar som definierats med alternativet mappningar. Matchning är girig (den längsta mönster matchningen vid en viss punkt vinner). Ersättning tillåts vara en tom sträng.  </br></br>**Alternativ**  </br></br> mappningar (typ: sträng mat ris) – en lista över mappningar i följande format: "a =>b" (alla förekomster av tecknen "a" ersätts med tecken "b"). Krävs.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Ett char-filter som ersätter tecken i Indatasträngen. Det använder ett reguljärt uttryck för att identifiera tecken sekvenser som ska bevaras och ett ersättnings mönster för att identifiera tecken som ska ersättas. Till exempel inmatad text = "AA BB AA BB", Pattern = "(AA) \\ \s + (BB)" ersättning = "$ 1 # $2", result = "AA # BB AA # BB".  </br></br>**Alternativ**  </br></br>mönster (typ: sträng) – obligatoriskt.  </br></br>ersättning (typ: sträng) – krävs.|  

 <sup>1</sup> char filter Types är alltid fasta i koden med "#Microsoft. Azure. search", så att "MappingCharFilter" faktiskt anges som "#Microsoft. Azure. search. MappingCharFilter. Vi har tagit bort prefixet för att minska tabellens bredd, men kom ihåg att ta med det i koden. Observera att char_filter_type endast anges för filter som kan anpassas. Om det inte finns några alternativ, vilket är fallet med html_strip, finns det ingen kopplad #Microsoft. Azure. search-typ.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Tokenizers

En tokenizer delar upp kontinuerlig text i en sekvens med tokens, som att dela upp en mening i ord. I tabellen nedan är de tokenizers som implementeras med Apache Lucene länkade till Lucene API-dokumentationen.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|------------------|-------------------------------|---------------------------|  
|[klassiskt](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Grammatikbaserade tokenizer som lämpar sig för att bearbeta de flesta dokument på europeisk språk.  </br></br>**Alternativ**  </br></br>maxTokenLength (typ: int) – maximal längd på token. Standard: 255, Max: 300. Token som är längre än den maximala längden delas.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes inmatat från en kant till n g av den eller de angivna storlekarna.  </br></br> **Alternativ**  </br></br>minGram (typ: int)-standard: 1, Max: 300.  </br></br>maxGram (typ: int)-standard: 2, Max: 300. Måste vara större än minGram.  </br></br>tokenChars (typ: sträng mat ris) – tecken klasser som ska behållas i tokens. Tillåtna värden: </br>"Letter", "siffror", "blank steg", "skiljetecken", "symbol". Standardvärdet är en tom matris – behåller alla tecken. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Genererar hela indatamängden som en token.  </br></br>**Alternativ**  </br></br>maxTokenLength (typ: int) – maximal längd på token. Standard: 256, Max: 300. Token som är längre än den maximala längden delas.|  
|[versal](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typen gäller endast när alternativen är tillgängliga)  |Delar upp text på icke-bokstäver. Tokens som är längre än 255 tecken delas.|  
|[versal](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typen gäller endast när alternativen är tillgängliga)  |Delar upp text på icke-bokstäver och konverterar dem till gemener. Tokens som är längre än 255 tecken delas.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Delar upp text med språkspecifika regler.  </br></br>**Alternativ**  </br></br>maxTokenLength (typ: int) – maximal token-längd, standard: 255, Max: 300. Token som är längre än den maximala längden delas. Tokens som är längre än 300 tecken delas upp i tokens med längden 300 och sedan delas var och en av dessa token samman baserat på maxTokenLength-uppsättningen.  </br></br>isSearchTokenizer (typ: bool) – ange värdet sant om det används som Sök-tokenizer, ange värdet FALSE om det används som index tokenizer. </br></br>språk (typ: sträng) – språk som ska användas, standard "engelska". Tillåtna värden är: </br>"Bangla", "bulgariska", "katalanska", "chineseSimplified", "chineseTraditional", "kroatiska", "tjeckiska", "danska", "nederländska", "engelska", "franska", "tyska", "grekiska", "hindi", "isländska", "indonesiska", "italienska", "Japanese", "koreanska", "malajiska", "Malayalam", "Marathi", "norwegianBokmaal", "polska", "portugisiska", "portugueseBrazilian", "punjabi", "rumänska", "ryska", "serbianCyrillic", "serbianLatin", "slovensk", "spanska", "svenska", "Tamil", "Telugu", "Thai", "ukrainsk", "urdu", "vietnamesisk" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Delar upp text med språkspecifika regler och minskar ord till deras bas formulär </br></br>**Alternativ** </br></br>maxTokenLength (typ: int) – maximal token-längd, standard: 255, Max: 300. Token som är längre än den maximala längden delas. Tokens som är längre än 300 tecken delas upp i tokens med längden 300 och sedan delas var och en av dessa token samman baserat på maxTokenLength-uppsättningen. </br></br> isSearchTokenizer (typ: bool) – ange värdet sant om det används som Sök-tokenizer, ange värdet FALSE om det används som index tokenizer. </br></br>språk (typ: sträng) – språk som ska användas, standard "engelska". Tillåtna värden är: </br>"arabiska", "Bangla", "bulgariska", "katalanska", "kroatiska", "tjeckiska", "danska", "nederländska", "engelska", "estniska", "finska", "franska", "tyska", "grekiska", "Gujarati", "hebreiska", "hindi", "isländska", "isländsk", "indonesiska", "italienska", "Kannada", "litauiska", "malajiska", "Malayalam", "Marathi", "norwegianBokmaal", "polska", "portugisiska", "portugueseBrazilian", "punjabi", "rumänska", "ryska", "serbianCyrillic", "serbianLatin", "slovakiska", "slovensk", "spanska", "svenska", "Tamil", "Telugu", "turkiska", "ukrainsk", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes in i n-gram av de angivna storlekarna. </br></br>**Alternativ** </br></br>minGram (typ: int)-standard: 1, Max: 300. </br></br>maxGram (typ: int)-standard: 2, Max: 300. Måste vara större än minGram. </br></br>tokenChars (typ: sträng mat ris) – tecken klasser som ska behållas i tokens. Tillåtna värden: "Letter", "siffror", "blank steg", "skiljetecken", "symbol". Standardvärdet är en tom matris – behåller alla tecken. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer för sökvägar till hierarkier. **Alternativ** </br></br>avgränsare (typ: sträng)-standard:/. </br></br>ersättnings (typ: sträng) – om den anges ersätter avgränsnings tecken. Standardvärdet för avgränsare. </br></br>maxTokenLength (typ: int) – maximal längd på token. Standard: 300, Max: 300. Sökvägar som är längre än maxTokenLength ignoreras. </br></br>omvänd (typ: bool)-om värdet är true genererar token i omvänd ordning. Standard: falskt. </br></br>hoppa över (typ: bool) – inledande token som ska hoppas över. Standardvärdet är 0.|  
|[ofta](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|I den här tokenizer används regex-mönster matchning för att skapa distinkta tokens. </br></br>**Alternativ** </br></br> [mönster](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (typ: sträng) – mönster för reguljära uttryck för att matcha token-avgränsare. Standardvärdet är `\W+` , som matchar tecken som inte är ord. </br></br>[flaggor](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: sträng) – reguljära uttrycks flaggor. Standardvärdet är en tom sträng. Tillåtna värden: CANON_EQ, CASE_INSENSITIVE, kommentarer, DOTALL, LITERAL, Multiline, UNICODE_CASE, UNIX_LINES </br></br>grupp (typ: int) – vilken grupp som ska extraheras till token. Standardvärdet är-1 (Split).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Delar upp text efter [Unicodes regler för text segmentering](https://unicode.org/reports/tr29/). </br></br>**Alternativ** </br></br>maxTokenLength (typ: int) – maximal längd på token. Standard: 255, Max: 300. Token som är längre än den maximala längden delas.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes-URL: er och e-postmeddelanden som en token. </br></br>**Alternativ** </br></br> maxTokenLength (typ: int) – maximal längd på token. Standard: 255, Max: 300. Token som är längre än den maximala längden delas.|  
|[blank steg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typen gäller endast när alternativen är tillgängliga) |Delar upp text i blank steg. Tokens som är längre än 255 tecken delas.|  

 <sup>1</sup> Tokenizer-typer har alltid prefixet "#Microsoft. Azure. search", så att "ClassicTokenizer" faktiskt anges som "#Microsoft. Azure. search. ClassicTokenizer". Vi har tagit bort prefixet för att minska tabellens bredd, men kom ihåg att ta med det i koden. Observera att tokenizer_type endast anges för tokenizers som kan anpassas. Om det inte finns några alternativ, vilket är fallet med bokstaven tokenizer, finns det ingen associerad #Microsoft. Azure. search-typ.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Token-filter

Ett token-filter används för att filtrera ut eller ändra de tokens som genereras av en tokenizer. Du kan till exempel ange ett gement filter som konverterar alla tecken till gemener. Du kan ha flera token-filter i en anpassad analys. Token-filter körs i den ordning som de visas i listan. 

I tabellen nedan är de token-filter som implementeras med Apache Lucene länkade till Lucene API-dokumentationen.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Ett token filter som tillämpar den arabiska normaliserings regeln för att normalisera Orthography.|  
|[apostrof](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Tar bort alla tecken efter en apostrof (inklusive själva apostrofen). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Konverterar alfabetiska, numeriska och symboliska Unicode-tecken som inte finns i de första 127 ASCII-tecknen (det "grundläggande latinska" Unicode-blocket) till deras ASCII-motsvarigheter, om ett sådant finns.<br /><br /> **Alternativ**<br /><br /> preserveOriginal (typ: bool) – om värdet är true behålls den ursprungliga token. Standardvärdet är False.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Utgör en mängd CJK-termer som genereras från StandardTokenizer.<br /><br /> **Alternativ**<br /><br /> ignoreScripts (typ: sträng mat ris) – skript som ska ignoreras. Tillåtna värden är: "han", "hiragana", "katakana", "hangul". Standardvärdet är en tom lista.<br /><br /> outputUnigrams (typ: bool) – Ange som sant om du alltid vill mata in både unigrams och två g. Standardvärdet är False.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Normaliserar CJK-breddens skillnader. Vik ASCII-varianter med full bredd i motsvarande grundläggande latinska och halv bredds katakana-varianter till motsvarande kana. |  
|[klassiskt](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Tar bort den engelska possessives och punkter från akronymer. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Konstruera en mängd för vanliga termer vid indexering. Enkla villkor är fortfarande indexerade, med bibehållet som har övergått.<br /><br /> **Alternativ**<br /><br /> commonWords (typ: sträng mat ris) – en uppsättning vanliga ord. Standardvärdet är en tom lista. Krävs.<br /><br /> ignoreCase (typ: bool) – om värdet är True är matchning Skift läges okänsligt. Standardvärdet är False.<br /><br /> queryMode (typ: bool) – genererar och genererar sedan vanliga ord och enstaka termer följt av ett vanligt ord. Standardvärdet är False.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Delar upp sammansatta ord som finns i många Germanic-språk.<br /><br /> **Alternativ**<br /><br /> Ord lista (typ: sträng mat ris) – listan över ord som ska matchas mot. Standardvärdet är en tom lista. Krävs.<br /><br /> minWordSize (typ: int) – endast ord som är längre än vad som bearbetas. Standardvärdet är 5.<br /><br /> minSubwordSize (typ: int) – endast ord som är längre än det här är ett undantag. Standardvärdet är 2.<br /><br /> maxSubwordSize (typ: int) – endast under ord som är kortare än det här är. Standardvärdet är 15.<br /><br /> onlyLongestMatch (typ: bool) – Lägg bara till det längsta matchnings under ord som ska matas ut. Standardvärdet är False.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Genererar n-g av de angivna storlekarna från början eller bak sidan av en indataport.<br /><br /> **Alternativ**<br /><br /> minGram (typ: int)-standard: 1, Max: 300.<br /><br /> maxGram (typ: int)-standard: 2, högsta 300. Måste vara större än minGram.<br /><br /> sida (typ: sträng) – anger vilken sida i indatamängden som n-g ska genereras från. Tillåtna värden: "fram", "tillbaka" |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Tar bort elisions. Till exempel konverteras "L'Avion" (planet) till "Avion" (planet).<br /><br /> **Alternativ**<br /><br /> artiklar (typ: sträng mat ris) – en uppsättning artiklar som ska tas bort. Standardvärdet är en tom lista. Om det inte finns någon lista med artiklar, tas alla franska artiklar bort som standard.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Normaliserar tyska tecken enligt heuristiken för [German2 Snowball-algoritmen](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Normaliserar text i hindi för att ta bort vissa skillnader i stavnings variationer. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliserar Unicode-representationen av text på indiska språk.
|[föra](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Ett token filter som endast bevarar tokens med text som finns i en angiven lista med ord.<br /><br /> **Alternativ**<br /><br /> keepWords (typ: sträng mat ris) – en lista med ord som ska behållas. Standardvärdet är en tom lista. Krävs.<br /><br /> keepWordsCase (typ: bool) – om värdet är true, gemener först. Standardvärdet är False.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Markerar termer som nyckelord.<br /><br /> **Alternativ**<br /><br /> Nyckelord (typ: sträng mat ris) – en lista med ord som ska markeras som nyckelord. Standardvärdet är en tom lista. Krävs.<br /><br /> ignoreCase (typ: bool) – om värdet är true, gemener först. Standardvärdet är False.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Avger varje inkommande token två gånger som nyckelord och en gång som icke-nyckelord. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Ett kstem filter med höga prestanda för engelska. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Tar bort ord som är för långa eller för korta.<br /><br /> **Alternativ**<br /><br /> min (typ: int) – minsta antal. Standard: 0, Max: 300.<br /><br /> Max (typ: int) – maximalt antal. Standard: 300, Max: 300.|  
|[gräns](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft. Azure. search. LimitTokenFilter|Begränsar antalet tokens vid indexering.<br /><br /> **Alternativ**<br /><br /> maxTokenCount (typ: int) – högsta antal tokens som ska skapas. Standard är 1.<br /><br /> consumeAllTokens (typ: bool) – om alla tokens från indatamängden måste förbrukas även om maxTokenCount har nåtts. Standardvärdet är False.|  
|[versal](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Normaliserar token-text till gemener. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Genererar n-g av de tilldelade storlekarna.<br /><br /> **Alternativ**<br /><br /> minGram (typ: int)-standard: 1, Max: 300.<br /><br /> maxGram (typ: int)-standard: 2, högsta 300. Måste vara större än minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Använder Java-regex för att generera flera tokens, en för varje infångnings grupp i ett eller flera mönster.<br /><br /> **Alternativ**<br /><br /> mönster (typ: sträng mat ris) – en lista över mönster som ska matchas mot varje token. Krävs.<br /><br /> preserveOriginal (typ: bool) – ange till true för att returnera den ursprungliga token även om något av mönstren matchar, default: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Ett token filter som använder ett mönster för varje token i data strömmen och som ersätter matchnings förekomster med den angivna ersättnings strängen.<br /><br /> **Alternativ**<br /><br /> mönster (typ: sträng) – obligatoriskt.<br /><br /> ersättning (typ: sträng) – krävs.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typen gäller endast när alternativen är tillgängliga) |Använder normalisering för persiska. |  
|[tals](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Skapa token för fonetiska matchningar.<br /><br /> **Alternativ**<br /><br /> kodare (typ: sträng)-fonetisk kodare som ska användas. Tillåtna värden är: "metaphone", "doubleMetaphone", "SOUNDEX", "refinedSoundex", "caverphone1", "caverphone2", "Cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Standard: "metaphone". Standardvärdet är metaphone.<br /><br /> Mer information finns i [Encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) .<br /><br /> Ersätt (typ: bool) – sant om kodade tokens ska ersätta ursprungliga tokens, falskt om de ska läggas till som synonymer. Standardvärdet är true.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typen gäller endast när alternativen är tillgängliga)  |Transformerar token-dataströmmen enligt [Porter-algoritmen](https://tartarus.org/~martin/PorterStemmer/). |  
|[Återför](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Kastar om token-strängen. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Normaliserar användningen av utbytbara Scandinavian tecken. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Vik Scandinavian tecken åÅäæÄÆ->a-och öÖøØ->o. Den kan också skilja sig mot användningen av dubbla vokaler AA, AE, Ao, OE och säkerhets, och bara lämna det första. |  
|[shingle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Skapar kombinationer av tokens som en enda token.<br /><br /> **Alternativ**<br /><br /> maxShingleSize (typ: int) – standardvärdet är 2.<br /><br /> minShingleSize (typ: int) – standardvärdet är 2.<br /><br /> outputUnigrams (typ: bool) – om värdet är true, innehåller utdataström för indata (unigrams) och shingles. Standardvärdet är true.<br /><br /> outputUnigramsIfNoShingles (typ: bool) – om det här värdet är sant åsidosätts beteendet för outputUnigrams = = false för de tider då inga shingles är tillgängliga. Standardvärdet är False.<br /><br /> tokenSeparator (typ: sträng) – strängen som ska användas vid koppling av intilliggande token för att skapa en Shingle. Standardvärdet är "".<br /><br /> filterToken (typ: sträng) – strängen som ska infogas för varje position där det inte finns någon token. Standardvärdet är "_".|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball token-filter.<br /><br /> **Alternativ**<br /><br /> språk (typ: sträng) – tillåtna värden är: "armeniska", "baskiska", "katalanska", "danska", "holländska", "engelska", "finska", "franska", "tyska", "german2", "ungerska", "italienska", "KP", "Lovins", "norska", "Porter", "portugisiska", "rumänska", "ryska", "spanska", "svenska", "turkiska"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliserar Unicode-representationen av sorani-text.<br /><br /> **Alternativ**<br /><br /> Inga.|  
|verbböjning|StemmerTokenFilter|Språkspecifikt filter för igenkänning av språk.<br /><br /> **Alternativ**<br /><br /> språk (typ: sträng) – tillåtna värden är: <br /> -   [Arabisk](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [armeniska](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [baskiska](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [brasilianska](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />– "bulgariska"<br />-   [katalanska](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [Tjeckiska](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [snurrevadar](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [nederländska](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [amerikanska](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [finska](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   [franska](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"Galiciska"<br />- "minimalGalician"<br />-   [italienska](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   [gemen](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-"hindi"<br />-   [skrift](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [indonesiska](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   [irländsk](https://snowballstem.org/otherapps/oregan/)<br />-   [italienska](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [lettisk](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [norsk](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [portugisiska](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [rumänsk](https://snowballstem.org/otherapps/romanian/)<br />-   [ryska](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [Svenska](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [Svenska](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   [turkiska](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Eventuella ord listor som omfattas – har marker ATS som nyckelord, vilket förhindrar att kedjan visas. Måste placeras före eventuella filterande filter.<br /><br /> **Alternativ**<br /><br /> regler (typ: sträng mat ris) – stötande regler i följande format "Word => stock", till exempel "körning => kör". Standardvärdet är en tom lista.  Krävs.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Tar bort stoppord från en token-dataström. Som standard använder filtret en fördefinierad stopp ord lista för engelska.<br /><br /> **Alternativ**<br /><br /> stoppord (typ: sträng mat ris) – en lista över stoppord. Kan inte anges om en stopwordsList har angetts.<br /><br /> stopwordsList (typ: sträng) – en fördefinierad lista över stoppord. Kan inte anges om stoppord har angetts. Tillåtna värden är: "arabiska", "armeniska", "baskiska", "brasiliansk", "bulgariska", "katalanska", "Tjeckien", "danska", "nederländska", "svenska", "finska", "svenska", "Galiciska", "tyska", "grekiska", "hindi", "ungerska", "indonesiska", "irländska", "italienska", "lettiska", "norska", "persiska", "portugisiska", "rumänska", "ryska", "sorani", "spanska", "svenska", "thailändska", "turkisk", standard: "engelska". Kan inte anges om stoppord har angetts. <br /><br /> ignoreCase (typ: bool) – om värdet är True är alla ord lägre bokstäver först. Standardvärdet är False.<br /><br /> removeTrailing (typ: bool) – om värdet är true, ignorera den senaste Sök termen om det är ett stopp ord. Standardvärdet är true.
|[liktydigt](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Matchar enstaka eller flera ord synonymer i en token-dataström.<br /><br /> **Alternativ**<br /><br /> Synonymer (typ: sträng mat ris) – obligatoriskt. Lista över synonymer i något av följande två format:<br /><br /> – otrolig, unbelievable, Fabulous => otrolig – alla termer till vänster om => symbol ersätts med alla termer på höger sida.<br /><br /> – otrolig, unbelievable, Fabulous, otrolig – en kommaavgränsad lista med likvärdiga ord. Ange alternativet expandera för att ändra hur listan tolkas.<br /><br /> ignoreCase (typ: bool) – Case-vikning inmatad för matchning. Standardvärdet är False.<br /><br /> Expandera (typ: bool) – om värdet är True är alla ord i listan över synonymer (IF => notation inte används) mappar till varandra. <br />Följande lista: otrolig, unbelievable, Fabulous, fantastisk motsvarar: otrolig, unbelievable, Fabulous, fantastisk => otrolig, unbelievable, Fabulous, fantastisk<br /><br />– Om det här värdet är falskt är följande lista: otrolig, unbelievable, Fabulous, fantastisk motsvarar: otrolig, unbelievable, Fabulous, otrolig => otrolig.|  
|[reducera](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Trimmar inledande och avslutande blank steg från tokens. |  
|[trunkera](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Trunkerar villkoren till en viss längd.<br /><br /> **Alternativ**<br /><br /> längd (typ: int)-standard: 300, Max: 300. Krävs.|  
|[unikt](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtrerar ut token med samma text som föregående token.<br /><br /> **Alternativ**<br /><br /> onlyOnSamePosition (typ: bool) – om det är inställt tar du bort dubbletter på samma plats. Standardvärdet är true.|  
|[vers](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typen gäller endast när alternativen är tillgängliga)  |Normaliserar token-text till versaler. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Delar upp ord i under ord och utför valfria omvandlingar i under ord grupper.<br /><br /> **Alternativ**<br /><br /> generateWordParts (typ: bool) – gör att delar av ord skapas, till exempel "AzureSearch" blir "Azure" search ". Standardvärdet är true.<br /><br /> generateNumberParts (typ: bool) – orsakar tal under ord som ska genereras. Standardvärdet är true.<br /><br /> catenateWords (typ: bool) – orsakar maximalt antal körningar av Word-delar som ska catenated, till exempel "Azure-Search" blir "AzureSearch". Standardvärdet är False.<br /><br /> catenateNumbers (typ: bool) – orsakar maximal körning av antal delar som ska catenated, till exempel "1-2" blir "12". Standardvärdet är False.<br /><br /> catenateAll (typ: bool) – gör så att alla del ord delar kan catenated, t. ex. "Azure-Search-1" blir "AzureSearch1". Standardvärdet är False.<br /><br /> splitOnCaseChange (typ: bool)-om värdet är true, delas orden på caseChange, till exempel "AzureSearch" blir "Azure" search ". Standardvärdet är true.<br /><br /> preserveOriginal – gör att de ursprungliga orden bevaras och läggs till i ord listan. Standardvärdet är False.<br /><br /> splitOnNumerics (typ: bool) – om värdet är true blir delningar av siffror, till exempel "Azure1Search" blir "Azure" "search". Standardvärdet är true.<br /><br /> stemEnglishPossessive (typ: bool) – gör så att efterföljande "rader" tas bort för varje under ord. Standardvärdet är true.<br /><br /> protectedWords (typ: sträng mat ris)-token som ska skyddas från avgränsas. Standardvärdet är en tom lista.|  

 <sup>1</sup> token filter types har alltid prefixet "#Microsoft. Azure. search", så att "ArabicNormalizationTokenFilter" faktiskt anges som "#Microsoft. Azure. search. ArabicNormalizationTokenFilter".  Vi har tagit bort prefixet för att minska tabellens bredd, men kom ihåg att ta med det i koden.  

## <a name="see-also"></a>Se även

- [REST-API: er för Azure Kognitiv sökning](/rest/api/searchservice/)
- [Analys verktyg i Azure Kognitiv sökning (exempel)](search-analyzers.md#examples)
- [Skapa index (REST)](/rest/api/searchservice/create-index)