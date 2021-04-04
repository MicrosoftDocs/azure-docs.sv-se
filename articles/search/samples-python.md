---
title: Python-exempel
titleSuffix: Azure Cognitive Search
description: Hitta Azure Kognitiv sökning demo python-kod exempel som använder Azure .NET SDK för python eller REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955130"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-kod exempel för Azure Kognitiv sökning

Lär dig mer om python-kod exemplen som demonstrerar funktionerna och arbets flödet för en Azure Kognitiv sökning-lösning. De här exemplen använder [**azure kognitiv sökning-klient biblioteket**](/python/api/overview/azure/search-documents-readme) för [**Azure SDK för python**](/azure/developer/python/), som du kan utforska genom följande länkar.

| Mål | Länk |
|--------|------|
| Hämtning av paket | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API-referens | [Azure – Sök-dokument](/python/api/azure-search-documents)  |
| API-testfall | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Källkod | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK-exempel

Kod exempel från Azure SDK Development Team demonstrerar API-användning. Du hittar dessa exempel i [**Azure-SDK-för-python/tree/master/SDK/search/Azure-Search-Documents/samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) på GitHub.

| Exempel | Beskrivning |
|---------|-------------|
| [Autentisera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Visar hur du konfigurerar en klient och autentiserar till tjänsten. | 
| [Index Create-Read-Update-Delete-åtgärder](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [Sök index](search-what-is-an-index.md). |
| [Indexerare skapa-läsa-uppdatera-ta bort-åtgärder](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Visar hur du skapar, uppdaterar, hämtar, listar, återställer och tar bort [indexerare](search-indexer-overview.md). |
| [Sök efter indexerare data källor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [Synonymer](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [synonym Maps](search-synonyms.md).  |
| [Läsa in dokument](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Visar hur du överför eller sammanfogar dokument till ett index i en [data import](search-what-is-data-import.md) åtgärd. |
| [Exempelfråga](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Visar hur du ställer in en [grundläggande fråga](search-query-overview.md). |
| [Filter fråga](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Visar hur du skapar ett [filter uttryck](search-filters.md). |
| [Fasett-fråga](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Visar hur du arbetar med [ansikte](search-filters-facets.md). |

## <a name="doc-samples"></a>Doc-exempel

Kod exempel från Kognitiv sökning team demonstrerar funktioner och arbets flöden. Många av dessa exempel refereras till självstudier, snabb starter och instruktions artiklar. Du kan hitta dessa exempel i [**Azure-samples/Azure-Search-python-samples**](https://github.com/Azure-Samples/azure-search-python-samples) på GitHub.

| Exempel | Artikel |
|---------|---------|
| [Start](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Källkod för [snabb start: skapa ett Sök index i python](search-get-started-python.md). Den här artikeln beskriver det grundläggande arbets flödet för att skapa, läsa in och skicka frågor till ett Sök index med hjälp av exempel data. |
| [självstudie – AI – berikning](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Källkod för [Självstudier: Använd python och AI för att generera sökbart innehåll från Azure-blobbar](cognitive-search-tutorial-blob-python.md). Den här artikeln visar hur du skapar en BLOB-indexerare med en kognitiv färdigheter, där färdigheter skapar och transformerar RAW-innehåll för att göra det sökbart eller använda. |
| [AzureML – anpassad – kompetens](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Käll kod till [exempel: skapa en anpassad kunskap med hjälp av python](cognitive-search-custom-skill-python.md). Den här artikeln visar indexerings-och färdigheter-integrering med djup inlärnings modeller i Azure Machine Learning. |

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=python&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.