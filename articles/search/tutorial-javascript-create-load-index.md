---
title: 'JavaScript-självstudie: Lägg till sökning i Web Apps'
titleSuffix: Azure Cognitive Search
description: Skapa index och importera CSV-data till Sök index med Java Script med NPM SDK @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726981"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2 – Skapa och Läs in Sök index med Java Script

Fortsätt att bygga din Sök-aktiverade webbplats genom att:
* Skapa en Sök resurs med VS Code-tillägget
* Skapa ett nytt index och importera data med Java Script med hjälp av exempel skriptet och Azure SDK [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Skapa en Azure Search resurs 

Skapa en ny Sök resurs med [Azure kognitiv sökning](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) -tillägget för Visual Studio Code.

1. Öppna [aktivitets fältet](https://code.visualstudio.com/docs/getstarted/userinterface)i Visual Studio Code och välj Azure-ikonen. 

1. I sido fältet **högerklickar du på din Azure-prenumeration** under avsnittet `Azure: Cognitive Search` och väljer **Skapa ny Sök tjänst**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="I sido fältet högerklickar du på din Azure-prenumeration under * * Azure: Kognitiv sökning * * område och väljer * * Skapa ny Sök tjänst * *.":::

1. Följ anvisningarna för att ange följande information:

    |Prompt|Ange|
    |--|--|
    |Ange ett globalt unikt namn för den nya Search Service.|**Kom ihåg det här namnet**. Resurs namnet blir en del av resurs slut punkten.|
    |Välj en resurs grupp för nya resurser|Använd den resurs grupp som du skapade för den här självstudien.|
    |Välj SKU för din Sök tjänst.|Välj **kostnads fri** för den här självstudien. Du kan inte ändra en pris nivå för SKU när tjänsten har skapats.|
    |Välj en plats för nya resurser.|Välj en region nära dig.|

1. När du har slutfört prompterna skapas den nya Sök resursen. 

## <a name="get-your-search-resource-admin-key"></a>Hämta din administratörs nyckel för Sök resurs

Hämta din Sök resurs administratörs nyckel med Visual Studio Code-tillägget. 

1. I Visual Studio Code högerklickar du på din Sök resurs i sido fältet och väljer **Kopiera administratörs nyckel**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="I sido fältet högerklickar du på din Sök resurs och väljer * * Kopiera administratörs nyckel * *.":::

1. Behåll den här administratörs nyckeln. du måste använda den i [ett senare avsnitt](#prepare-the-bulk-import-script-for-search). 

## <a name="prepare-the-bulk-import-script-for-search"></a>Förbered Mass import skriptet för sökning

Skriptet använder Azure SDK för Kognitiv sökning:

* [NPM-paket @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Referensdokumentation](/javascript/api/overview/azure/search-documents-readme)

1. I Visual Studio Code öppnar du `bulk_insert_books.js` filen i under katalogen,  `search-web/bulk-insert` ersätter följande variabler med dina egna värden för att autentisera med Azure Search SDK:

    * DIN-SEARCH – RESURS NAMN
    * DIN-SEARCH-ADMIN-NYCKEL

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Öppna en integrerad Terminal i Visual Studio för projekt katalogens under katalog, `search-web/bulk-insert` och kör följande kommando för att installera beroendena. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Kör Mass import skriptet för sökning

1. Fortsätt att använda den integrerade terminalen i Visual Studio för projekt katalogens under katalog, `search-web/bulk-insert` för att köra följande bash-kommando för att köra `bulk_insert_books.js` skriptet:

    ```javascript
    npm start
    ```

1. När koden körs visar konsolen förloppet. 
1. När uppladdningen är klar är den sista instruktionen som skrevs ut till-konsolen "klar".

## <a name="review-the-new-search-index"></a>Granska det nya Sök indexet

När överföringen är klar är Sök indexet redo att användas. Granska det nya indexet.

1. Öppna Azure Kognitiv sökning-tillägget i Visual Studio Code och välj din Sök resurs.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="Öppna Azure Kognitiv sökning-tillägget i Visual Studio Code och öppna din Sök resurs.":::

1. Expandera index, sedan dokument och välj sedan `good-books` ett dokument för att se alla användarspecifika data.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Expandera index, sedan &quot;felfri-böcker&quot; och välj sedan ett dokument.":::

## <a name="copy-your-search-resource-name"></a>Kopiera Sök resurs namnet

Anteckna **Sök resurs namnet**. Du behöver detta för att ansluta Azure Function-appen till din Sök resurs. 

> [!CAUTION]
> Du kanske frestas att använda din Sök administratörs nyckel i Azure-funktionen, som inte följer principen om minsta behörighet. Azure-funktionen kommer att använda fråg-tangenten för att uppfylla minsta behörighet. 

## <a name="next-steps"></a>Nästa steg

[Distribuera din statiska webbapp](tutorial-javascript-deploy-static-web-app.md)