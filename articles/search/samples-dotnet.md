---
title: .NET-exempel
titleSuffix: Azure Cognitive Search
description: Hitta Azure Kognitiv sökning demo C#-kod exempel som använder .NET-klient bibliotek.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: cbb84a4934eed4d258cf07772753315785f98019
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218170"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Kod exempel för .NET (C#) för Azure Kognitiv sökning

Lär dig mer om C#-kod exemplen som demonstrerar funktionerna och arbets flödet för en Azure Kognitiv sökning-lösning. De här exemplen använder [**azure kognitiv sökning-klient biblioteket**](/dotnet/api/overview/azure/search) för [**Azure SDK för .net**](/dotnet/azure/), som du kan utforska genom följande länkar.

| Mål | Länk |
|--------|------|
| Hämtning av paket | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API-referens | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API-testfall | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Källkod | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK-exempel

Kod exempel från Azure SDK Development Team demonstrerar API-användning. Du hittar dessa exempel i [**Azure/Azure-SDK-för-net/tree/master/SDK/search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) on GitHub.

| Exempel | Description |
|---------|-------------|
| ["Hello World", synkront](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Visar hur du skapar en klient, autentiserar och hanterar fel med hjälp av synkrona metoder.|
| ["Hello World", asynkront](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Visar hur du skapar en klient, autentiserar och hanterar fel med hjälp av asynkrona metoder.  |
| [Åtgärder på tjänst nivå](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Visar hur du skapar index, indexerare, data källor, färdighetsuppsättningar och synonym Maps. Det här exemplet visar också hur du hämtar tjänst statistik och hur du frågar ett index.  |
| [Index åtgärder](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Visar hur du utför en åtgärd på ett befintligt index, i det här fallet får du ett antal dokument som lagras i indexet.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Visar en metod för att arbeta med data typer som inte stöds.  |
| [Indexera dokument (push-modell)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | "Push" modell indexering, där du skickar en JSON-nyttolast till ett index för en tjänst.   |
| [Exempel på krypterings nyckel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Visar hur du använder en kundhanterad krypterings nyckel för att lägga till ett extra skydds lager med känsligt innehåll.  |

## <a name="doc-samples"></a>Doc-exempel

Kod exempel från Kognitiv sökning team demonstrerar funktioner och arbets flöden. Många av dessa exempel refereras till självstudier, snabb starter och instruktions artiklar. Du kan hitta dessa exempel i [**Azure-samples/Azure-Search-dotNet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) och i [**Azure-samples/search-dotNet-kom igång**](https://github.com/Azure-Samples/search-dotnet-getting-started/) på GitHub.

| Exempel | Artikel  |
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Källkod för [snabb start: skapa ett sökindex ](search-get-started-dotnet.md). Den här artikeln beskriver det grundläggande arbets flödet för att skapa, läsa in och skicka frågor till ett Sök index med hjälp av exempel data. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Käll koden för [att använda .net-klient biblioteket](search-howto-dotnet-sdk.md). Den här artikeln går igenom det grundläggande arbets flödet, men i mer detalj och diskussion om API-användning.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Käll kod till [exempel: Lägg till synonymer i C#](search-synonyms-tutorial-sdk.md). Synonym listor används för frågans expansion, vilket ger matchnings bara termer som är externa i ett index. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Källkod för [Självstudier: indexera Azure SQL-data med .NET SDK](search-indexer-tutorial.md). Den här artikeln visar hur du konfigurerar en Azure SQL-indexerare som har ett schema, fält mappningar och parametrar.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Käll kod för [hur du konfigurerar Kundhanterade nycklar för data kryptering](search-security-manage-encryption-keys.md). |
| [Skapa din första app i C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Källkod för [Självstudier: skapa din första Sökapp](tutorial-csharp-create-first-app.md). De flesta exempel är konsol program, det här MVC-exemplet använder en webb sida för att visa exempel på hotell indexet, som demonstrerar grundläggande sökning, sid brytning, Autoavsluta och föreslagna frågor, ansikts och filter. |
| [flera data källor](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Källkod för [Självstudier: index från flera data källor](tutorial-multiple-data-sources.md). |
|  [optimera data indexering](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Källkod för [Självstudier: optimera indexering med push-API](tutorial-optimize-indexing-push-api.md).  |
| [självstudie – AI – berikning](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Källkod för [Självstudier: AI-genererat sökbart innehåll från Azure-blobbar med .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=csharp&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="other-samples"></a>Andra exempel

Följande exempel publiceras också av Kognitiv sökning-teamet, men det finns inga referenser till dokumentationen. Tillhör ande README-filer innehåller användnings instruktioner.

| Exempel | Description |
|---------|-------------|
| [Azure-Search – Power-färdigheter](https://github.com/Azure-Samples/azure-search-power-skills)  | Källkod för förbrukade anpassade kunskaper som du kan använda i dina vunna lösningar.  |
| [Lösningsaccelerator för kunskapsutvinning](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Innehåller mallar, stödfiler och analys rapporter som hjälper dig att prototypa en lösning för kunskaps utvinning från slut punkt till slut punkt.  |
| [Covid-19 Sök i app-lagringsplatsen](https://github.com/liamca/covid19search) | Käll kods lagrings plats för den Kognitiv sökning baserade [Covid-19 Sökappen](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Läs mer om [JFK-lösningen](https://www.microsoft.com/ai/ai-lab-jfk-files). |
| [Sök + QnA Maker Accelerator](https://github.com/Azure-Samples/search-qna-maker-accelerator) | En [lösning](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381) som kombinerar kraften hos Sök-och QNA Maker. Se live [demo-webbplatsen](https://aka.ms/qnaWithAzureSearchDemo). |