---
title: Uppgradera till Azure Search .NET SDK version 3
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 3 från äldre versioner. Läs om vad som är nytt och vilka kod ändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e9f4602582a8c24f74d6603b732e7b91d97fa8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002699"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Uppgradera till Azure Search .NET SDK version 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Om du använder version 2,0 – för hands version eller äldre av [Azure Search .NET SDK](/dotnet/api/overview/azure/search)hjälper den här artikeln dig att uppgradera ditt program till att använda version 3.

En mer allmän genom gång av SDK inklusive exempel finns i [så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 3 av Azure Search .NET SDK innehåller vissa ändringar från tidigare versioner. Dessa är huvudsakligen mindre, så att ändringar i din kod bara kräver minimal ansträngning. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar din kod till att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 1.0.2 – Preview eller äldre bör du först uppgradera till version 1,1 och sedan uppgradera till version 3. Instruktioner finns i [Uppgradera till Azure Search .NET SDK version 1,1](search-dotnet-sdk-migration-version-1.md) .
>
> Din Azure Search tjänst instans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Vad är nytt i version 3
Version 3 av Azure Search .NET SDK är riktad mot den senaste allmänt tillgängliga versionen av Azure Search REST API, särskilt 2016-09-01. Detta gör det möjligt att använda många nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [Anpassade analysverktyg](index-add-custom-analyzers.md)
* Stöd för [azure Blob Storage](search-howto-indexing-azure-blob-storage.md) och [Azure Table Storage](search-howto-indexing-azure-tables.md) -indexeraren
* Indexerare-anpassning via [fält mappningar](search-indexer-field-mappings.md)
* ETags-stöd för att möjliggöra säker samtidig uppdatering av index definitioner, indexerare och data källor
* Stöd för att skapa index fält definitioner deklarativ genom att dekorera modell klassen och använda den nya `FieldBuilder` klassen.
* Stöd för .NET Core och .NET Portable Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Först uppdaterar du din NuGet-referens för `Microsoft.Azure.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. I så fall är det dags att sätta igång!

Om din version Miss lyckas bör du se ett build-fel som följande:

```output
Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)
```

Nästa steg är att åtgärda det här build-felet. Mer information om vad som orsakar felet och hur du kan åtgärda det finns i avsnittet om att [bryta ändringar i version 3](#ListOfChanges) .

Du kan se ytterligare build-varningar relaterade till föråldrade metoder eller egenskaper. Varningarna innehåller instruktioner om vad som ska användas i stället för den föråldrade funktionen. Om ditt program exempelvis använder `IndexingParameters.Base64EncodeKeys` egenskapen, ska du få en varning om att `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

När du har åtgärdat eventuella build-fel kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK beskrivs i [Vad är nytt i version 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Bryta ändringar i version 3
Det finns ett litet antal avbryter ändringar i version 3 som kan kräva kod ändringar förutom att återskapa ditt program.

### <a name="indexesgetclient-return-type"></a>Index. GetClient returnerar typ
`Indexes.GetClient`Metoden har en ny returtyp. Tidigare returnerades den `SearchIndexClient` , men detta ändrades till `ISearchIndexClient` i version 2,0 – för hands version och denna ändring överför till version 3. Detta är att stödja kunder som vill modellera `GetClient` metoden för enhets tester genom att returnera en modell implementering av `ISearchIndexClient` .

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, datatype och andra är inte längre implicit konverterbara till strängar
Det finns många typer i Azure Search .NET SDK som härleds från `ExtensibleEnum` . Tidigare var dessa typer alla implicit konverterbara till Type `string` . Men en bugg identifierades i `Object.Equals` implementeringen för dessa klasser och korrigerar den bugg som krävs för att inaktivera denna implicita konvertering. Explicit konvertering till `string` tillåts fortfarande.

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Du kan ändra den till detta för att åtgärda eventuella build-fel:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Borttagna föråldrade medlemmar

Du kan se build-fel relaterade till metoder eller egenskaper som marker ATS som föråldrade i version 2,0 – för hands version och sedan tas bort i version 3. Gör så här om du stöter på sådana fel:

- Om du använder den här konstruktorn: `ScoringParameter(string name, string value)` använder du den här i stället: `ScoringParameter(string name, IEnumerable<string> values)`
- Om du använder `ScoringParameter.Value` egenskapen använder du `ScoringParameter.Values` egenskapen eller `ToString` metoden i stället.
- Om du använder `SearchRequestOptions.RequestId` egenskapen använder du `ClientRequestId` egenskapen i stället.

### <a name="removed-preview-features"></a>Borttagna för hands versions funktioner

Om du uppgraderar från version 2,0 – för hands version till version 3, bör du tänka på att stöd för JSON-och CSV-parsning för BLOB-indexerare har tagits bort eftersom dessa funktioner fortfarande är i för hands version. Mer specifikt har följande metoder för `IndexingParametersExtensions` klassen tagits bort:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om programmet har ett hårt beroende av dessa funktioner kommer du inte att kunna uppgradera till version 3 av Azure Search .NET SDK. Du kan fortsätta att använda version 2,0 – för hands version. Tänk dock på att **vi inte rekommenderar att du använder för hands versioner av SDK: er i produktions program**. För hands versions funktionerna är endast för utvärdering och kan ändras.

## <a name="conclusion"></a>Slutsats
Om du behöver mer information om hur du använder Azure Search .NET SDK, se [.net How-to](search-howto-dotnet-sdk.md).

Vi välkomnar din feedback om SDK. Om du stöter på problem kan du be oss om hjälp om [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar en bugg kan du ange ett problem i [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Var noga med att ange din ärende rubrik med "[Azure Search]".

Tack för att du använder Azure Search!