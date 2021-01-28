---
title: Java-exempel
titleSuffix: Azure Cognitive Search
description: Hitta exempel på Java-kod exempel för Azure Kognitiv sökning demo som använder Azure .NET SDK för Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955045"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Exempel på Java-kod för Azure Kognitiv sökning

Lär dig mer om Java-kod exemplen som demonstrerar funktionerna och arbets flödet för en Azure Kognitiv sökning-lösning. De här exemplen använder [**azure kognitiv sökning-klient biblioteket**](/java/api/overview/azure/search-documents-readme) för [**Azure SDK för Java**](/azure/developer/java/sdk), som du kan utforska genom följande länkar.

| Mål | Länk |
|--------|------|
| Hämtning av paket | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API-referens | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API-testfall | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Källkod | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK-exempel

Kod exempel från Azure SDK Development Team demonstrerar API-användning. Du hittar dessa exempel i [**Azure/Azure-SDK-för-Java/tree/master/SDK/search/Azure-Search-Documents/src/samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) på GitHub.

| Exempel | Description |
|---------|-------------|
| [Skapa Sök index](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Visar hur du skapar [Sök index](search-what-is-an-index.md). |
| [Skapa synonym](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Visar hur du skapar [synonym Maps](search-synonyms.md).  |
| [Skapa Sök Indexer](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Visar hur du skapar [indexerare](search-indexer-overview.md). |
| [Skapa sökning indexerare data Källa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Visar hur du skapar indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [Skapa färdigheter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Visar hur du skapar [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) som är kopplade till indexerare och som utför AI-baserad anrikning vid indexering. |
| [Läsa in dokument](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Visar hur du överför eller sammanfogar dokument till ett index i en [data import](search-what-is-data-import.md) åtgärd. |
| [Frågesyntax](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Visar hur du ställer in en [grundläggande fråga](search-query-overview.md). |

## <a name="doc-samples"></a>Doc-exempel

Kod exempel från Kognitiv sökning team demonstrerar funktioner och arbets flöden. Många av dessa exempel refereras till självstudier, snabb starter och instruktions artiklar. Du kan hitta dessa exempel i [**Azure-samples/Azure-Search-Java-samples**](https://github.com/Azure-Samples/azure-search-java-samples) på GitHub.

| Exempel | Artikel | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Källkod för [snabb start: skapa ett sökindex i Java och rest](search-get-started-java.md). Det här exemplet har inte uppdaterats för Java SDK. Den anropar REST-API: erna. |

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=java&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="other-samples"></a>Andra exempel

Följande exempel publiceras också av Kognitiv sökning-teamet, men det finns inga referenser till dokumentationen. Tillhör ande README-filer innehåller användnings instruktioner.

| Exempel | Description |
|---------|-------------|
| [Sök-Java-kom igång](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Använder klient biblioteket Java SDK för att skapa, läsa in och fråga ett sökindex. Det här exemplet är för fristående. |
| [Sök-Java-indexerare – demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Visar en Azure Cosmos DB-indexerare i Java. Det här exemplet har inte uppdaterats för Java SDK. Den anropar REST-API: erna.|