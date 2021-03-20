---
title: Uppgradera till Azure Search .NET SDK version 9
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 9 från äldre versioner. Läs om vad som är nytt och vilka kod ändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7726877efcb72caf5bb4c92e2e1c65e5df68f270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002716"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Uppgradera till Azure Search .NET SDK version 9

Om du använder version 7,0 – för hands version eller äldre av [Azure Search .NET SDK](/dotnet/api/overview/azure/search)hjälper den här artikeln dig att uppgradera ditt program till att använda version 9.

> [!NOTE]
> Om du vill använda version 8,0 – för hands version för att utvärdera funktioner som inte är allmänt tillgängliga än, kan du också följa anvisningarna i den här artikeln för att uppgradera till 8,0 – för hands version från tidigare versioner.

En mer allmän genom gång av SDK inklusive exempel finns i [så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 9 av Azure Search .NET SDK innehåller många ändringar från tidigare versioner. Några av dessa är avbryter ändringar, men de bör bara kräva relativt mindre ändringar i koden. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar din kod till att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 4,0-Preview eller äldre bör du först uppgradera till version 5 och sedan uppgradera till version 9. Instruktioner finns i [Uppgradera till Azure Search .NET SDK version 5](search-dotnet-sdk-migration-version-5.md) .
>
> Din Azure Search tjänst instans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Vad är nytt i version 9
Version 9 av Azure Search .NET SDK riktar sig till 2019-05-06-versionen av Azure Search REST API med följande funktioner:

* [AI-anrikning](cognitive-search-concept-intro.md) är möjligheten att extrahera text från bilder, blobbar och andra ostrukturerade data källor – innehåll som gör det mer sökbart i ett Azure Search index.
* Med stöd för [komplexa typer](search-howto-complex-data-types.md) kan du modellera nästan vilken KAPSLAd JSON-struktur som helst i ett Azure Search index.
* [Funktionen Komplettera automatiskt](search-autocomplete-tutorial.md) innehåller ett alternativ till **föreslå** API för att implementera sökning efter typ av funktion. Autoavsluta "avslutar" ordet eller frasen som en användare skriver för närvarande.
* [JsonLines tolknings läge](search-howto-index-json-blobs.md), en del av Azure Blob-indexering, skapar ett Sök dokument per JSON-entitet som skiljs åt av en ny rad.

### <a name="new-preview-features-in-version-80-preview"></a>Nya för hands versions funktioner i version 8,0 – för hands version
Version 8,0 – för hands version av Azure Search .NET SDK-mål API-version 2017-11-11 – för hands version. Den här versionen innehåller alla funktioner i version 9, plus:

* [Kundhanterade krypterings nycklar](search-security-manage-encryption-keys.md) för service Side Encryption-at-rest är en ny förhands gransknings funktion. Förutom den inbyggda krypterings-vid-rest som hanteras av Microsoft kan du använda ett extra lager av kryptering där du är den enda ägaren av nycklarna.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Först uppdaterar du din NuGet-referens för `Microsoft.Azure.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. I så fall är det dags att sätta igång!

Om det inte går att bygga upp måste du åtgärda varje build-fel. Mer information om hur du löser de olika möjliga build-problemen finns i avsnittet om att lösa [ändringar i version 9](#ListOfChanges) .

Du kan se ytterligare build-varningar relaterade till föråldrade metoder eller egenskaper. Varningarna innehåller instruktioner om vad som ska användas i stället för den föråldrade funktionen. Om ditt program exempelvis använder `DataSourceType.DocumentDb` egenskapen, ska du få en varning om att "den här medlemmen är föråldrad. Använd CosmosDb i stället.

När du har åtgärdat eventuella build-fel eller varningar kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK beskrivs i [Vad är nytt i version 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Bryta ändringar i version 9

Det finns flera större ändringar i version 9 som kan kräva kod ändringar förutom att återskapa ditt program.

> [!NOTE]
> Listan över ändringar nedan är inte fullständig. Vissa ändringar kommer troligen inte att resultera i build-fel, men är tekniskt avbrotts kraft eftersom de bryter mot binär kompatibilitet med sammansättningar som är beroende av tidigare versioner av Azure Search .NET SDK-sammansättningar. Sådana ändringar visas inte nedan. Återskapa ditt program när du uppgraderar till version 9 för att undvika eventuella binära kompatibilitetsproblem.

### <a name="immutable-properties"></a>Oföränderliga egenskaper

De offentliga egenskaperna för flera modell klasser är nu oföränderliga. Om du behöver skapa anpassade instanser av dessa klasser för testning kan du använda de nya konstruktorerna för parametrar:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Ändra till fält

`Field`Klassen har ändrats nu och kan även representera komplexa fält.

Följande `bool` egenskaper är nu null-bara:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Detta beror på att dessa egenskaper måste göras `null` i händelse av komplexa fält. Om du har kod som läser dessa egenskaper måste det förberedas för att hantera `null` . Observera att alla andra egenskaper för `Field` alltid har varit och fortfarande kan vara null, och vissa av dem kommer också att vara `null` i det fallet komplexa fält – särskilt följande:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Parameter lös konstruktor `Field` har gjorts `internal` . Från och med nu `Field` måste alla ha ett explicit namn och en datatyp vid tidpunkten för konstruktion.

### <a name="simplified-batch-and-results-types"></a>Förenklad batch-och resultat typer

I version 7,0 – för hands versionen och tidigare var de olika klasserna som inkapslade grupper av dokument strukturerade i parallella klass hierarkier:

  -  `DocumentSearchResult` och `DocumentSearchResult<T>` ärvts från `DocumentSearchResultBase`
  -  `DocumentSuggestResult` och `DocumentSuggestResult<T>` ärvts från `DocumentSuggestResultBase`
  -  `IndexAction` och `IndexAction<T>` ärvts från `IndexActionBase`
  -  `IndexBatch` och `IndexBatch<T>` ärvts från `IndexBatchBase`
  -  `SearchResult` och `SearchResult<T>` ärvts från `SearchResultBase`
  -  `SuggestResult` och `SuggestResult<T>` ärvts från `SuggestResultBase`

De härledda typerna utan en generisk typ parameter är avsedda att användas i scenarier med dynamiskt typ och förmodad användning av `Document` typen.

Från och med version 8,0 – för hands version har bas klasserna och icke-generiska härledda klasser tagits bort. För dynamiskt skrivna scenarier kan du använda `IndexBatch<Document>` , `DocumentSearchResult<Document>` och så vidare.
 
### <a name="removed-extensibleenum"></a>Tog bort ExtensibleEnum

`ExtensibleEnum`Bask Lassen har tagits bort. Alla klasser som härleds från det är nu strukturer, till `AnalyzerName` exempel, `DataType` och `DataSourceType` . Deras `Create` metoder har också tagits bort. Du kan bara ta bort anrop till `Create` eftersom dessa typer är implicit konverterbara från strängar. Om detta resulterar i kompileringsfel kan du explicit anropa konverterings operatorn via databyte till disambiguate-typer. Du kan till exempel ändra kod så här:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

till denna:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Egenskaper som hålls valfria värden av dessa typer skrivs nu explicit som null, så de fortsätter att vara valfria.

### <a name="removed-facetresults-and-hithighlights"></a>Tog bort FacetResults och HitHighlights

`FacetResults` `HitHighlights` Klasserna och har tagits bort. Fasett-resultatet skrivs nu som `IDictionary<string, IList<FacetResult>>` och träffen visas som `IDictionary<string, IList<string>>` . Ett snabbt sätt att lösa build-fel som introducerats av den här ändringen är att lägga till `using` alias överst i varje fil som använder de borttagna typerna. Exempel:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Ändra till SynonymMap 

`SynonymMap`Konstruktorn har inte längre någon `enum` parameter för `SynonymMapFormat` . Den här uppräkningen hade bara ett värde och var därför redundant. Om du ser build-fel som ett resultat av detta tar du bara bort referenser till `SynonymMapFormat` parametern.

### <a name="miscellaneous-model-class-changes"></a>Diverse ändringar i modell klass

`AutocompleteMode`Egenskapen för `AutocompleteParameters` kan inte längre vara null. Om du har kod som tilldelar den här egenskapen till `null` , kan du helt enkelt ta bort den och egenskapen kommer automatiskt att initieras till standardvärdet.

Ordningen på parametrarna till `IndexAction` konstruktorn har ändrats nu när konstruktorn genereras automatiskt. I stället för att använda konstruktorn rekommenderar vi att du använder fabriks metoderna `IndexAction.Upload` , `IndexAction.Merge` och så vidare.

### <a name="removed-preview-features"></a>Borttagna för hands versions funktioner

Om du uppgraderar från version 8,0 – för hands version till version 9 bör du vara medveten om att kryptering med Kundhanterade nycklar har tagits bort eftersom den här funktionen fortfarande är i för hands version. Mer specifikt, `EncryptionKey` egenskaperna för `Index` och `SynonymMap` har tagits bort.

Om programmet har ett hårt beroende av den här funktionen kommer du inte att kunna uppgradera till version 9 av Azure Search .NET SDK. Du kan fortsätta att använda version 8,0 – för hands version. Tänk dock på att **vi inte rekommenderar att du använder för hands versioner av SDK: er i produktions program**. För hands versions funktionerna är endast för utvärdering och kan ändras.

> [!NOTE]
> Om du har skapat krypterade index eller synonym Maps med version 8,0 – för hands version av SDK, kan du fortfarande använda dem och ändra deras definitioner med hjälp av version 9 av SDK utan att påverka krypterings statusen negativt. Version 9 av SDK: n kommer inte `encryptionKey` att skicka egenskapen till REST API och därför kommer REST API inte att ändra resursens krypterings status. 

### <a name="behavioral-change-in-data-retrieval"></a>Beteende ändring i data hämtning

Om du använder "dynamiskt skrivna" `Search` , `Suggest` eller `Get` API: er som returnerar instanser av typ `Document` , bör du vara medveten om att de nu DEserialiserar tomma JSON-matriser till `object[]` i stället för `string[]` .

## <a name="conclusion"></a>Slutsats
Om du behöver mer information om hur du använder Azure Search .NET SDK, se [.net How-to](search-howto-dotnet-sdk.md).

Vi välkomnar din feedback om SDK. Om du stöter på problem kan du be oss om hjälp om [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar en bugg kan du ange ett problem i [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Var noga med att ange din ärende rubrik med "[Azure Search]".

Tack för att du använder Azure Search!