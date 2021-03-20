---
title: Uppgradera till Azure Search .NET SDK version 5
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 5 från äldre versioner. Läs om vad som är nytt och vilka kod ändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7f0e994cd41fc55824cf347a51ccfed25589a310
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91539545"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Uppgradera till Azure Search .NET SDK version 5

Om du använder version 4,0 – för hands version eller äldre av [.NET SDK](/dotnet/api/overview/azure/search)hjälper den här artikeln dig att uppgradera ditt program till att använda version 5.

En mer allmän genom gång av SDK inklusive exempel finns i [så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 5 av Azure Search .NET SDK innehåller vissa ändringar från tidigare versioner. Dessa är huvudsakligen mindre, så att ändringar i din kod bara kräver minimal ansträngning. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar din kod till att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 2,0-Preview eller äldre, bör du uppgradera till version 3 först och sedan uppgradera till version 5. Instruktioner finns i [Uppgradera till Azure Search .NET SDK version 3](search-dotnet-sdk-migration.md) .
>
> Din Azure Search tjänst instans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Vad är nytt i version 5
Version 5 av Azure Search .NET SDK riktar sig till den senaste allmänt tillgängliga versionen av Azure Search REST API, särskilt 2017-11-11. Detta gör det möjligt att använda nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [Synonymer](search-synonyms.md).
* Du kan nu program mässigt komma åt varningar i körnings historiken för Indexer (se `Warning` egenskapen för `IndexerExecutionResult` i [.net-referensen](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult) för mer information).
* Stöd för .NET Core 2.
* Den nya paket strukturen stöder bara de delar av SDK: n som du behöver (se [bryta ändringar i version 5](#ListOfChanges) för mer information).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Först uppdaterar du din NuGet-referens för `Microsoft.Azure.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. I så fall är det dags att sätta igång!

Om din version Miss lyckas bör du se ett build-fel som följande:

```output
The name 'SuggesterSearchMode' does not exist in the current context
```

Nästa steg är att åtgärda det här build-felet. Mer information om vad som orsakar felet och hur du kan åtgärda det finns i avsnittet om att [bryta ändringar i version 5](#ListOfChanges) .

Observera att på grund av ändringar i packningen av Azure Search .NET SDK måste du återskapa ditt program för att kunna använda version 5. De här ändringarna beskrivs i de större [ändringarna i version 5](#ListOfChanges).

Du kan se ytterligare build-varningar relaterade till föråldrade metoder eller egenskaper. Varningarna innehåller instruktioner om vad som ska användas i stället för den föråldrade funktionen. Om ditt program exempelvis använder `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metoden, ska du få en varning om att "det här beteendet är aktiverat som standard, så att anropa den här metoden är inte längre nödvändig".

När du har åtgärdat eventuella build-fel eller varningar kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK beskrivs i [Vad är nytt i version 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Bryta ändringar i version 5

### <a name="new-package-structure"></a>Ny paket struktur

Den mest betydande brytande ändringen i version 5 är att `Microsoft.Azure.Search` sammansättningen och dess innehåll har delats in i fyra separata sammansättningar som nu är distribuerade som fyra separata NuGet-paket:

 - `Microsoft.Azure.Search`: Det här är ett meta-paket som innehåller alla andra Azure Search-paket som beroenden. Om du uppgraderar från en tidigare version av SDK behöver du bara uppgradera det här paketet och skapa en ny version för att kunna börja använda den nya versionen.
 - `Microsoft.Azure.Search.Data`: Använd det här paketet om du utvecklar ett .NET-program med hjälp av Azure Search och du bara behöver fråga eller uppdatera dokument i dina index. Om du också behöver skapa eller uppdatera index, synonym mappningar eller andra resurser på tjänst nivå använder du `Microsoft.Azure.Search` paketet i stället.
 - `Microsoft.Azure.Search.Service`: Använd det här paketet om du utvecklar automatisering i .NET för att hantera Azure Search index, synonym kartor, indexerare, data källor eller andra resurser på tjänst nivå. Om du bara behöver fråga eller uppdatera dokument i dina index använder du `Microsoft.Azure.Search.Data` paketet i stället. Om du behöver alla funktioner i Azure Search använder du `Microsoft.Azure.Search` paketet i stället.
 - `Microsoft.Azure.Search.Common`: Vanliga typer som krävs av Azure Search .NET-bibliotek. Du behöver inte använda det här paketet direkt i ditt program. Den är endast avsedd att användas som ett beroende.
 
Den här ändringen är tekniskt avhuggen eftersom många typer har flyttats mellan sammansättningar. Det är därför nödvändigt att bygga om ditt program för att kunna uppgradera till version 5 av SDK: n.

Det finns ett litet antal andra ändringar i version 5 som kan kräva kod ändringar förutom att återskapa ditt program.

### <a name="change-to-suggesters"></a>Ändra till förslag 

`Suggester`Konstruktorn har inte längre någon `enum` parameter för `SuggesterSearchMode` . Den här uppräkningen hade bara ett värde och var därför redundant. Om du ser build-fel som ett resultat av detta tar du bara bort referenser till `SuggesterSearchMode` parametern.

### <a name="removed-obsolete-members"></a>Borttagna föråldrade medlemmar

Du kan se build-fel som rör metoder eller egenskaper som marker ATS som föråldrade i tidigare versioner och sedan tagits bort i version 5. Gör så här om du stöter på sådana fel:

- Om du använde `IndexingParametersExtensions.IndexStorageMetadataOnly` metoden använder du `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` i stället.
- Om du använde `IndexingParametersExtensions.SkipContent` metoden använder du `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` i stället.

### <a name="removed-preview-features"></a>Borttagna för hands versions funktioner

Om du uppgraderar från version 4,0 – för hands version till version 5 bör du vara medveten om att JSON-matrisen och stöd för CSV-parsning för BLOB-indexerare har tagits bort eftersom dessa funktioner fortfarande är i för hands version. Mer specifikt har följande metoder för `IndexingParametersExtensions` klassen tagits bort:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om programmet har ett hårt beroende av dessa funktioner kommer du inte att kunna uppgradera till version 5 av Azure Search .NET SDK. Du kan fortsätta att använda version 4,0 – för hands version. Tänk dock på att **vi inte rekommenderar att du använder för hands versioner av SDK: er i produktions program**. För hands versions funktionerna är endast för utvärdering och kan ändras.

## <a name="conclusion"></a>Slutsats
Om du behöver mer information om hur du använder Azure Search .NET SDK, se [.net How-to](search-howto-dotnet-sdk.md).

Vi välkomnar din feedback om SDK. Om du stöter på problem kan du be oss om hjälp om [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar en bugg kan du ange ett problem i [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Var noga med att ange din ärende rubrik med "[Azure Search]".

Tack för att du använder Azure Search!