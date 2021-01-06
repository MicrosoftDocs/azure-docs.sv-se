---
title: Lägga till språk analys verktyg i sträng fält
titleSuffix: Azure Cognitive Search
description: Flerspråkig lexikal analys för icke-engelska frågor och index i Azure Kognitiv sökning.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 555709776c88dd3003e400bbcefe2ec1cfa0f4af
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934177"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Lägga till språk analys verktyg i sträng fält i ett Azure Kognitiv sökning-index

En *språk analys* är en specifik typ av [text analys](search-analyzers.md) som utför en lexikal analys med hjälp av språk reglerna för mål språket. Alla sökbara fält har en **analys** egenskap. Om ditt innehåll består av översatta strängar, t. ex. separata fält för engelsk och kinesisk text, kan du ange språk analys verktyg för varje fält för att få åtkomst till de omfattande språk funktionerna för dessa analyser.

## <a name="when-to-use-a-language-analyzer"></a>När du ska använda en språk analys

Du bör tänka på en språk analys när medvetenheten om ord-eller menings strukturen lägger till värde i text tolkning. Ett vanligt exempel är associationen av oregelbundna verb ("placera" och "framställt) eller plural Substantiv (" möss "och" Mouse "). Utan språklig medvetenhet parsas dessa strängar enbart på fysiska egenskaper, vilket inte kan fånga anslutningen. Eftersom stora text segment är mer sannolika för det här innehållet, är fält som består av beskrivningar, granskningar eller sammanfattningar lämpliga kandidater för en språk analys.

Du bör också överväga språk analyser när innehållet består av icke-västerländska språk strängar. [Standard analys](search-analyzers.md#default-analyzer) verktyget är språk-oberoende, men begreppet att använda blank steg och specialtecken (bindestreck och snedstreck) för att separera strängar är mer tillämpbart på västerländska språk än de som inte är västerländska. 

Till exempel, på kinesiska, japanska, koreanska (CJK) och andra asiatiska språk, är ett blank steg inte nödvändigt vis en ord avgränsare. Överväg följande japanska sträng. Eftersom det inte finns några blank steg skulle en oberoende analys förmodligen analysera hela strängen som en token, när strängen i själva verket är en fras.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

I exemplet ovan måste en lyckad fråga innehålla hela token eller en partiell token med ett jokertecken, vilket resulterar i en naturlig och begränsad Sök upplevelse.

En bättre upplevelse är att söka efter enskilda ord: 明るい (ljus), 私たちの (vår), 銀河系 (galax). Att använda en av de japanska analys verktyg som är tillgängliga i Kognitiv sökning är svårare att låsa upp det här beteendet eftersom dessa analyser är bättre utrustade vid delning av text segmentet till meningsfulla ord på mål språket.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Jämföra Lucene och Microsofts analys verktyg

Azure Kognitiv sökning stöder 35-språkanalyser som backas upp av Lucene, och 50 språk analys verktyg som backas upp av den tillverkarspecifika Microsoft-tekniken för naturliga språk som används i Office och Bing.

Vissa utvecklare kan föredra den mer välkända, enkla lösningen med öppen källkod för Lucene. Lucene-språkanalyser är snabbare, men Microsofts analys verktyg har avancerade funktioner, till exempel lemmatisering, ord desammansatt (på språk som tyska, danska, holländska, svenska, norska, estniska, finish, ungerska, slovakiska) och entitets igenkänning (URL: er, e-post, datum, siffror). Om möjligt bör du köra jämförelser av både Microsoft-och Lucene-analyserna för att avgöra vilken som passar bäst. 

Indexering med Microsoft-analyser är i genomsnitt två till tre gånger långsammare än deras Lucene-motsvarigheter, beroende på språket. Sök prestanda ska inte påverkas nämnvärt för genomsnittlig storleks frågor. 

### <a name="english-analyzers"></a>Engelska analys verktyg

Standard analys verktyget är standard Lucene, som fungerar bra för engelska, men kanske inte som Lucene, engelska Analyzer eller Microsofts engelska analys. 
 
+ Den engelska analysen i Lucene utökar standard analys verktyget. Den tar bort possessives (efterföljande) från ord, tillämpar det som följer av algoritmen för Porter och tar bort engelska stoppord.  

+ Microsofts engelska Analyzer utför lemmatisering i stället för att det finns. Det innebär att den kan hantera inflected och oregelbundna ord former mycket bättre, vilket resulterar i mer relevanta Sök Resultat 

## <a name="configuring-analyzers"></a>Konfigurera analys verktyg

Språk analys verktyg används i befintligt skick. För varje fält i index definitionen kan du ställa in **Analyzer** -egenskapen till ett analys namn som anger den språk-och språkstapel (Microsoft eller Lucene). Samma analys verktyg kommer att användas vid indexering och sökning efter fältet. Du kan till exempel ha separata fält för de engelska, franska och spanska hotell beskrivningar som finns sida vid sida i samma index.

> [!NOTE]
> Det går inte att använda en annan språk analys vid indexerings tiden än vid frågans tid för ett fält. Den funktionen är reserverad för [anpassade analys](index-add-custom-analyzers.md)verktyg. Av den anledningen, om du försöker ange **searchAnalyzer** -eller **indexAnalyzer** -egenskaperna till namnet på en språk analys, returnerar REST API ett fel svar. Du måste använda **Analyzer** -egenskapen i stället.

Använd Frågeparametern **searchFields** för att ange vilket språkspecifika fält som ska genomsökas i dina frågor. Du kan granska exempel på frågor som innehåller analys egenskapen i [Sök dokument](/rest/api/searchservice/search-documents). 

Mer information om index egenskaper finns i [skapa index &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/create-index). Mer information om analyser i Azure Kognitiv sökning finns i [analyserare i azure kognitiv sökning](./search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Språk analys lista 
 Nedan visas en lista över språk som stöds tillsammans med Lucene och Microsoft Analyzer-namn.  

| Språk | Microsoft Analyzer-namn | Namn på Lucene Analyzer |
|--|--|--|
| Arabiska | ar. Microsoft | ar. Lucene |
| Armeniska |  | hy. Lucene |  |
| Bangla | BN. Microsoft |  |  |
| Baskiska |  | EU. Lucene |  |
| Bulgariska | BG. Microsoft | BG. Lucene |  |
| Katalanska | ca. Microsoft | ca. Lucene |  |
| Kinesiska, förenklad | zh-hans. Microsoft | zh-hans. Lucene |  |
| Kinesiska, traditionell | zh-Hant. Microsoft | zh-Hant. Lucene |  |
| Kroatiska | HR. Microsoft |  |  |
| Tjeckiska | CS. Microsoft | CS. Lucene |  |
| Danska | da. Microsoft | da. Lucene |  |
| Nederländska | nl. Microsoft | nl. Lucene |  |
| Engelska | en. Microsoft | en. Lucene |  |
| Estniska | et. Microsoft |  |  |
| Finska | Fi. Microsoft | Fi. Lucene |  |
| Franska | fr. Microsoft | fr. Lucene |  |
| Galiciska |  | GL. Lucene |  |
| Tyska | de. Microsoft | de. Lucene |  |
| Grekiska | El. Microsoft | El. Lucene |  |
| Gujarati | gu. Microsoft |  |  |
| Hebreiska | he. Microsoft |  |  |
| Hindi | Hej. Microsoft | Hej. Lucene |  |
| Ungerska | HU. Microsoft | HU. Lucene |  |
| Isländska | är. Microsoft |  |  |
| Indonesiska (Bahasa) | ID. Microsoft | ID. Lucene |  |
| Iriska |  | ga. Lucene |  |
| Italienska | den. Microsoft | IT. Lucene |  |
| Japanska | Ja. Microsoft | Ja. Lucene |  |
| Kannada | KN. Microsoft |  |  |
| Koreanska | Ko. Microsoft | Ko. Lucene |  |
| Lettiska | LV. Microsoft | LV. Lucene |  |
| Litauiska | lt. Microsoft |  |  |
| Malayalam | ml. Microsoft |  |  |
| Malajiska (latinsk) | MS. Microsoft |  |  |
| Marathi | Mr. Microsoft |  |  |
| Norska | Obs! Microsoft | Nej. Lucene |  |
| Persiska |  | FA. Lucene |  |
| Polska | pl. Microsoft | pl. Lucene |  |
| Portugisiska (Brasilien) | pt-br. Microsoft | pt-br. Lucene |  |
| Portugisiska (Portugal) | pt-pt. Microsoft | pt-pt. Lucene |  |
| Punjabi | PA. Microsoft |  |  |
| Rumänska | ro. Microsoft | ro. Lucene |  |
| Ryska | ru. Microsoft | ru. Lucene |  |
| Serbiska (kyrillisk) | SR-kyrilliska. Microsoft |  |  |
| Serbiska (latinsk) (Serbien) | SR-Latin. Microsoft |  |  |
| Slovakiska | sk. Microsoft |  |  |
| Slovenska | SL. Microsoft |  |  |
| Spanska | ES. Microsoft | ES. Lucene |  |
| Svenska | sa. Microsoft | sa. Lucene |  |
| Tamilska | ta. Microsoft |  |  |
| Telugu | te. Microsoft |  |  |
| Thailändska | Microsoft | th. Lucene |  |
| Turkiska | TR. Microsoft | TR. Lucene |  |
| Ukrainska | Storbritannien. Microsoft |  |  |
| Urdu | ditt. Microsoft |  |  |
| Vietnamesiska | Vi. Microsoft |  |  |

 Alla analyser med namn som är kommenterade med **Lucene** drivs av [Apache Lucenes språk analys verktyg](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Se även  

+ [Skapa index &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/create-index)  

+ [LexicalAnalyzerName-klass](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)