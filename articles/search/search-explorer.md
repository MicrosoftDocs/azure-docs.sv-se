---
title: Verktyget search Explorer-fråga i Azure Portal
titleSuffix: Azure Cognitive Search
description: I den här Azure Portal snabb starten använder du Sök Utforskaren för att lära dig frågesyntaxen, testa frågeuttryck eller granska ett Sök dokument. Sök Utforskaren frågar efter index i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: e9607a71ed6b045ac704c43bf4ea54c9f181bbf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179783"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Snabb start: Använd Sök Utforskaren för att köra frågor i portalen

**Sök Utforskaren** är ett inbyggt frågespråk som används för att köra frågor mot ett Sök index i Azure kognitiv sökning. Det här verktyget gör det enkelt att lära sig frågesyntaxen, testa ett fråge-eller filter uttryck eller bekräfta data uppdatering genom att kontrol lera om nytt innehåll finns i indexet.

I den här snabb starten används ett befintligt index för att demonstrera Sök Utforskaren. Begär Anden formuleras med hjälp av [sök REST API](/rest/api/searchservice/search-documents), med svar som returneras som UTFÖRLIGa JSON-dokument.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande krav på plats:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ *Realestate-US-Sample-indexet* används för den här snabb starten. Använd [snabb start: skapa ett index](search-import-data-portal.md) för att skapa indexet med hjälp av standardvärden. En inbyggd exempel data Källa från Microsoft (**realestate-US-Sample**) tillhandahåller data.

## <a name="start-search-explorer"></a>Starta Sök Utforskaren

1. I [Azure Portal](https://portal.azure.com)öppnar du sidan Sök översikt från instrument panelen eller [söker efter din tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Öppna Sök Utforskaren från kommando fältet:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Kommandot Sök Utforskaren i portalen" border="true":::

    Eller Använd den inbäddade **Sök Utforskaren** -fliken i ett öppet index:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Fliken Sök Utforskaren" border="true":::

## <a name="unspecified-query"></a>Ospecificerad fråga

För en första titt på innehåll kör du en tom sökning genom att klicka på **Sök** utan att ange några villkor. En tom sökning är användbar som första fråga eftersom den returnerar hela dokument så att du kan granska dokument kompositionen. I en tom sökning finns det ingen Sök rang och dokument returneras i valfri ordning ( `"@search.score": 1` för alla dokument). Som standard returneras 50-dokument i en Sök förfrågan.

Motsvarande syntax för en tom sökning är `*` eller `search=*` .
   
   ```http
   search=*
   ```

   **Resultat**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Icke-kvalificerat eller tomt fråge exempel" border="true":::

## <a name="free-text-search"></a>Texts ökning utan text

Kostnads fria former, med eller utan operatörer, är användbara för att simulera användardefinierade frågor som skickas från en anpassad app till Azure Kognitiv sökning. Endast de fält som är attribut som **sökbara** i index definitionen genomsöks efter matchningar. 

Observera att när du anger Sök villkor, t. ex. sökord eller uttryck, kommer Sök rankningen att spelas upp. I följande exempel visas en texts ökning i fritext.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultat**

   Du kan använda Ctrl-F för att söka i resultat för specifika ränte villkor.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Exempel på fritext fråga" border="true":::

## <a name="count-of-matching-documents"></a>Antal matchande dokument 

Lägg till **$Count = sant** om du vill hämta antalet matchningar som finns i ett index. I en tom sökning är Count det totala antalet dokument i indexet. I en kvalificerad sökning är det antalet dokument som matchar frågans inmatade frågor. Kom ihåg att tjänsten returnerar de översta 50-matchningarna som standard, så du kan ha fler matchningar i indexet än vad som ingår i resultaten.

   ```http
   $count=true
   ```

   **Resultat**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Antal matchande dokument i index" border="true":::

## <a name="limit-fields-in-search-results"></a>Begränsa fält i Sök Resultat

Lägg till [**$Select**](search-query-odata-select.md) för att begränsa resultaten till de uttryckligen namngivna fälten för mer läsbara utdata i **Sök Utforskaren**. Om du vill behålla Sök strängen och **$Count = True**, prefix argument med **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultat**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Begränsa fält i Sök Resultat" border="true":::

## <a name="return-next-batch-of-results"></a>Returnera nästa resultat grupp

Azure Kognitiv sökning returnerar de översta 50-matchningarna baserat på Sök rankningen. Om du vill hämta nästa uppsättning matchande dokument, lägger du till **$Top = 100, &$Skip = 50** för att öka resultat uppsättningen till 100-dokument (standard är 50, maximum är 1000) och hoppar över de första 50-dokumenten. Du kan kontrol lera dokument nyckeln (listingID) för att identifiera ett dokument. 

Kom ihåg att du måste ange Sök villkor, till exempel en frågeterm eller ett uttryck för att få rankade resultat. Observera att Sök Resultat minskar djupare som du når i Sök resultaten.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultat**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Returnera nästa batch med Sök Resultat" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filter uttryck (större än, mindre än, lika med)

Använd parametern [**$filter**](search-query-odata-filter.md) när du vill ange exakta villkor i stället för texts ökning utan text. Fältet måste ha attributet **Filterable** i indexet. Det här exemplet söker efter sovrum som är större än 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultat**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Filtrera efter kriterier" border="true":::

## <a name="order-by-expressions"></a>Ordning efter uttryck

Lägg till [**$OrderBy**](search-query-odata-orderby.md) för att sortera resultat efter ett annat fält förutom Sök resultat. Fältet måste ha attributet **sorterat** i indexet. Ett exempel uttryck som du kan använda för att testa det här är:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultat**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Ändra sorteringsordning" border="true":::

Både **$filter** -och **$OrderBy** -uttryck är OData-konstruktioner. Mer information finns i [OData-filtersyntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Lärdomar

I den här snabb starten använde du **Sök Utforskaren** för att fråga ett index med hjälp av REST API.

+ Resultat returneras som utförliga JSON-dokument så att du kan se dokument konstruktion och innehåll, i sin helhet. Parametern **$Select** i ett frågeuttryck kan begränsa vilka fält som returneras.

+ Dokument består av alla fält som har marker ATS som **hämtnings** bara i indexet. Om du vill visa indexattribut i portalen klickar du på *realestate-US-Sample* i listan **index** på sidan Sök översikt.

+ Kostnads fria frågor, som liknar vad du kan ange i en kommersiell webbläsare, är användbara för att testa en slut användar upplevelse. Om du till exempel antar det inbyggda realestate exempel indexet, kan du ange "Seattle lägenheter Lake Washington" och sedan använda Ctrl-F för att hitta termer inom Sök resultaten. 

+ Fråga-och filter uttryck är ledade i en syntax som implementeras av Azure Kognitiv sökning. Standardvärdet är en [enkel syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search), men du kan också använda [fullständig Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) för mer kraftfulla frågor. [Filter uttryck](/rest/api/searchservice/odata-expression-syntax-for-azure-search) är en OData-syntax.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om frågeuttryck och syntax använder du Postman eller ett motsvarande verktyg för att skapa frågeuttryck som utnyttjar fler delar av API: et. [Sök REST API](/rest/api/searchservice/search-documents) är särskilt användbart för utbildning och utforskning.

> [!div class="nextstepaction"]
> [Skapa en grundläggande fråga i Postman](search-get-started-rest.md)