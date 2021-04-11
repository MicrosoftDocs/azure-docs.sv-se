---
title: 'JavaScript-självstudie: Sök efter integrations höjd punkter'
titleSuffix: Azure Cognitive Search
description: Förstå Sök frågor för Java Script SDK som används på den Sök aktiverade webbplatsen
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726940"
---
# <a name="4---search-integration-highlights"></a>4 – Sök efter integrations höjd punkter

I föregående lektioner lade du till sökning i en statisk webbapp. I den här lektionen beskrivs de grundläggande stegen som upprättar integrering. Om du letar efter ett lathund-blad om hur du integrerar sökning i JavaScript-appen, förklarar den här artikeln vad du behöver veta.

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

Function-appen använder Azure SDK för Kognitiv sökning:

* NPM [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Referens dokumentation: [klient bibliotek](/javascript/api/overview/azure/search-documents-readme)

Function-appen autentiseras via SDK: n till den molnbaserade Kognitiv sökning API med hjälp av resurs namn, resurs nyckel och index namn. Hemligheterna lagras i de statiska inställningarna för webbapp och hämtas till funktionen som miljövariabler. 

## <a name="configure-secrets-in-a-configuration-file"></a>Konfigurera hemligheter i en konfigurations fil

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Azure Function: Sök i katalogen

`Search` [API: et](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) tar ett sökord och söker i dokumenten i Sök indexet och returnerar en lista över matchningar. 

Routning för Sök-API finns i [function.jsför](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) bindningar.

Azure-funktionen hämtar information om Sök konfigurationen och uppfyller frågan.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Klient: Sök från katalogen

Anropa Azure-funktionen i den reagera klienten med följande kod. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure Function: förslag från katalogen

`Suggest` [API: et](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) tar en Sök term medan användaren skriver och föreslår Sök termer som bok titlar och författare i dokumenten i Sök indexet, och returnerar en liten lista med matchningar. 

Sök förslagen, `sg` , definieras i [schema filen](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) som används vid Mass uppladdning.

Routning för föreslå API finns i [function.jspå](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) bindningar.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Klient: förslag från katalogen

Den Föreslåde funktionen API anropas i den reagera appen vid `\src\components\SearchBar\SearchBar.js` komponent initiering:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure Function: Hämta ett speciellt dokument 

`Lookup` [API: et](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) tar ett ID och returnerar objektet Document från Sök indexet. 

Routning för söknings-API: t finns i [function.jspå](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) bindningar.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Klient: Hämta ett speciellt dokument 

Den här funktionen API anropas i den reagera appen vid `\src\pages\Details\Detail.js` komponent initiering:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Nästa steg

* [Indexera Azure SQL-data](search-indexer-tutorial.md)
