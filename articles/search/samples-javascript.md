---
title: JavaScript-exempel
titleSuffix: Azure Cognitive Search
description: Hitta exempel på JavaScript-kod exempel för Azure Kognitiv sökning demo som använder Azure .NET SDK för Java Script.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 85a4d6390087100d8d9521f6ac20dbace3a711eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955949"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>JavaScript-kod exempel för Azure Kognitiv sökning

Lär dig mer om JavaScript-kod exemplen som demonstrerar funktionerna och arbets flödet för en Azure Kognitiv sökning-lösning. De här exemplen använder [**azures kognitiv sökning klient bibliotek**](/javascript/api/overview/azure/search-documents-readme) för [**Azure SDK för Java Script**](/azure/developer/javascript/), som du kan utforska genom följande länkar.

| Mål | Länk |
|--------|------|
| Hämtning av paket | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API-referens | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API-testfall | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Källkod | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK-exempel

Kod exempel från Azure SDK Development Team demonstrerar API-användning. Du hittar dessa exempel i [**Azure-SDK-för-JS/tree/master/SDK/search/search-Documents/samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) på GitHub.

### <a name="javascript-sdk-samples"></a>JavaScript SDK-exempel

| Exempel | Beskrivning |
|---------|-------------|
| [index](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [Sök index](search-what-is-an-index.md). Den här exempel kategorin innehåller också ett exempel på tjänst statistik. |
| [dataSourceConnections (för indexerare)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [indexerare](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Visar hur du skapar, uppdaterar, hämtar, listar, återställer och tar bort [indexerare](search-indexer-overview.md).|
| [Färdigheter](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) som är kopplade till indexerare och som utför AI-baserad anrikning under indexering. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [synonym Maps](search-synonyms.md).  |

### <a name="typescript-samples"></a>TypeScript-exempel

| Exempel | Beskrivning |
|---------|-------------|
| [index](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [Sök index](search-what-is-an-index.md). Den här exempel kategorin innehåller också ett exempel på tjänst statistik. |
| [dataSourceConnections (för indexerare)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [indexerare](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Visar hur du skapar, uppdaterar, hämtar, listar, återställer och tar bort [indexerare](search-indexer-overview.md).|
| [Färdigheter](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) som är kopplade till indexerare och som utför AI-baserad anrikning under indexering. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [synonym Maps](search-synonyms.md).  |

## <a name="doc-samples"></a>Doc-exempel

Kod exempel från Kognitiv sökning team demonstrerar funktioner och arbets flöden. Många av dessa exempel refereras till självstudier, snabb starter och instruktions artiklar. Du hittar dessa exempel i [**Azure-samples/Azure-Search-JavaScript-samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) på GitHub.

| Exempel | Artikel |
|---------|---------|
| [Start](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Källkod för [snabb start: skapa ett Sök index i Java Script](search-get-started-javascript.md). Den här artikeln beskriver det grundläggande arbets flödet för att skapa, läsa in och skicka frågor till ett Sök index med hjälp av exempel data. |

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=javascript&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="other-samples"></a>Andra exempel

Följande exempel publiceras också av Kognitiv sökning-teamet, men det finns inga referenser till dokumentationen. Tillhör ande README-filer innehåller användnings instruktioner.

| Exempel | Beskrivning |
|---------|-------------|
| [Azure-Search-reakta-Template](https://github.com/dereklegenzoff/azure-search-react-template) | Reagera på mall för Azure Kognitiv sökning (github.com) |