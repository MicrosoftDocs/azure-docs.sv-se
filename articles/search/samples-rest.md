---
title: REST-exempel
titleSuffix: Azure Cognitive Search
description: 'Hitta kod exempel för Azure Kognitiv sökning demo som använder REST-API: er för sökning eller hantering.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98956586"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>REST Code-exempel för Azure Kognitiv sökning

Lär dig mer om REST API exempel som demonstrerar funktionerna och arbets flödet för en Azure Kognitiv sökning-lösning. Dessa exempel använder [**Sök REST-API: er**](/rest/api/searchservice).

REST är det slutliga programmerings gränssnittet för Azure Kognitiv sökning och alla åtgärder som kan anropas program mässigt är tillgängliga först i vila och sedan i SDK: er. De flesta exempel i dokumentationen utnyttjar REST-API: erna för att demonstrera eller förklara viktiga begrepp.

REST-exempel utvecklas och testas vanligt vis på Postman, men du kan använda vilken klient som helst som stöder HTTP-anrop:

+ Starta med [snabb start: skapa ett Azure kognitiv sökning-index med hjälp av REST-API: er](search-get-started-rest.md) för att utforma http-anrop.
+ Testa [Visual Studio Code-tillägget för Azure kognitiv sökning](search-get-started-vs-code.md), för närvarande i för hands version, om du arbetar i Visual Studio Code.

## <a name="doc-samples"></a>Doc-exempel

Kod exempel från Kognitiv sökning team demonstrerar funktioner och arbets flöden. Många av dessa exempel refereras till självstudier, snabb starter och instruktions artiklar. Du kan hitta dessa exempel i [**Azure-samples/Azure-Search-Postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples) på GitHub.

| Exempel | Artikel |
|---------|---------|
| [Snabbstart](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Källkod för [snabb start: skapa ett Sök index med hjälp av REST API: er](search-get-started-rest.md). Den här artikeln beskriver det grundläggande arbets flödet för att skapa, läsa in och skicka frågor till ett Sök index med hjälp av exempel data. |
| [Självstudie](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Källkod för [Självstudier: Använd rest och AI för att generera sökbart innehåll från Azure-blobbar](cognitive-search-tutorial-blob.md). Den här artikeln visar hur du skapar en färdigheter som itererar över Azure-blobbar för att extrahera information och härleda struktur.|
| [Felsök – sessioner](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Källkod för [Självstudier: diagnostisera, reparera och genomför ändringar i din färdigheter](cognitive-search-tutorial-debug-sessions.md). Den här artikeln visar hur du använder en färdigheter-felsökningssession i Azure Portal. REST används för att skapa de objekt som används under fel sökning.|
| [anpassade analyser](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Källkod för [Självstudier: skapa en anpassad analys för telefonnummer](tutorial-create-custom-analyzer.md). Den här artikeln förklarar hur du använder analyser för att bevara mönster och specialtecken i sökbart innehåll.|
| [kunskap – butik](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Käll koden för att [skapa ett kunskaps lager med hjälp av rest och Postman](knowledge-store-create-rest.md). Den här artikeln förklarar de steg som krävs för att fylla i ett kunskaps lager som används för kunskaps utvinnings arbets flöden |
| [projektioner](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Käll koden för [att forma och exportera-anrikninger](knowledge-store-projections-examples.md). Den här artikeln förklarar hur du anger fysiska data strukturer i ett kunskaps lager.|
| [index-krypterade-blobbar](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Käll kod för [hur du indexerar krypterade blobbar med BLOB-indexerare och färdighetsuppsättningar](search-howto-index-encrypted-blobs.md). Den här artikeln visar hur du indexerar dokument i Azure Blob Storage som tidigare har krypterats med Azure Key Vault. |

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="other-samples"></a>Andra exempel

Följande exempel publiceras också av Kognitiv sökning-teamet, men det finns inga referenser till dokumentationen. Tillhör ande README-filer innehåller användnings instruktioner.

| Exempel | Beskrivning |
|---------|-------------|
| [Fråga – exempel](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Postman-samlingar som demonstrerar de olika fråge teknikerna, inklusive fuzzy search, RegEx och jokertecken, komplettera automatiskt och så vidare. |