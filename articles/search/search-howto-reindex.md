---
title: Återskapa ett Sök index
titleSuffix: Azure Cognitive Search
description: Lägg till nya element, uppdatera befintliga element eller dokument eller ta bort föråldrade dokument i en fullständig återskapning eller partiell indexering för att uppdatera ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 47e9b80bb25b7ff14695cc67682265fe338ff76f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119109"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Återskapa ett index i Azure Kognitiv sökning

I den här artikeln förklaras hur du återaktiverar ett Azure Kognitiv sökning-index, under vilka omständigheter som återuppbyggnadar krävs och rekommendationer för att minimera effekten av återuppbyggnad av pågående fråge förfrågningar.

En *Rebuild* syftar på att släppa och återskapa de fysiska data strukturer som är associerade med ett index, inklusive alla fältbaserade inverterade index. I Azure Kognitiv sökning kan du inte släppa och återskapa enskilda fält. För att återskapa ett index måste alla fält lagrings enheter tas bort, återskapas baserat på ett befintligt eller uppdaterat index schema och sedan fyllas i igen med data som skickas till indexet eller hämtas från externa källor. 

Det är vanligt att bygga om index under utvecklingen när du har itererat över index design, men du kan också behöva återskapa ett index på produktions nivå för att hantera strukturella ändringar, till exempel lägga till komplexa typer eller lägga till fält i förslag.

## <a name="rebuild-versus-refresh"></a>"Återskapa" jämfört med "uppdatera"

Rebuild bör inte förväxlas med uppdatering av innehållet i ett index med nya, ändrade eller borttagna dokument. Att uppdatera en Sök-sökkorpus är nästan en del i varje Sökapp, med vissa scenarier som kräver uppdaterade uppdateringar (till exempel när en Sök sökkorpus behöver Visa inventerings ändringar i en app som är online Sales).

Så länge du inte ändrar indexets struktur kan du uppdatera ett index med samma teknik som du använde för att läsa in indexet från början:

* Anropa [Lägg till, uppdatera eller ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents) för att skicka ändringar till ett index i push-läge-indexering.

* För indexerare kan du [Schemalägga körning av indexerare](search-howto-schedule-indexers.md) och använda ändrings spårning eller tidsstämplar för att identifiera delta. Om uppdateringar måste reflekteras snabbare än vad en Scheduler kan hantera kan du i stället använda indexering i push-läge.

## <a name="rebuild-conditions"></a>Återskapa villkor

Släpp och återskapa ett index om något av följande villkor är uppfyllt. 

| Villkor | Description |
|-----------|-------------|
| Ändra en fält definition | Att ändra ett fält namn, data typ eller specifika [indexattribut](/rest/api/searchservice/create-index) (sökbar, Filterable, sorterbar, aspektbar) kräver en fullständig återuppbyggnad. |
| Tilldela ett fält till en analys | [Analys](search-analyzers.md) verktyg definieras i ett index och tilldelas sedan till fält. Du kan lägga till en ny analys definition till ett index när som helst, men du kan bara *tilldela* en analys när fältet har skapats. Detta gäller både för egenskaperna **Analyzer** och **indexAnalyzer** . Egenskapen **searchAnalyzer** är ett undantag (du kan tilldela den här egenskapen till ett befintligt fält). |
| Uppdatera eller ta bort en analys definition i ett index | Du kan inte ta bort eller ändra en befintlig analys konfiguration (Analyzer, tokenizer, token filter eller char filter) i indexet om du inte bygger om hela indexet. |
| Lägg till ett fält till en förslags ställare | Om det redan finns ett fält och du vill lägga till det i en [förslags](index-add-suggesters.md) konstruktion måste du återskapa indexet. |
| Ta bort ett fält | Om du vill ta bort alla spår för ett fält fysiskt måste du återskapa indexet. När det inte är praktiskt att skapa en omedelbar återskapning kan du ändra program koden för att inaktivera åtkomst till fältet "borttaget" eller använda [parametern $Select fråga](search-query-odata-select.md) för att välja vilka fält som ska visas i resultat uppsättningen. Fysiskt är fält definitionen och innehållet kvar i indexet tills nästa återuppbyggnad när du använder ett schema som utelämnar fältet i fråga. |
| Växlings nivåer | Om du behöver mer kapacitet finns det ingen uppgradering på plats i Azure Portal. En ny tjänst måste skapas och index måste byggas från grunden på den nya tjänsten. Du kan automatisera processen genom att använda exempel koden **index-Backup-Restore** i den här [Azure kognitiv sökning .net-exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-dotnet-samples). Den här appen säkerhetskopierar ditt index till en serie JSON-filer och återskapar sedan indexet i en Sök tjänst som du anger.|

## <a name="update-conditions"></a>Uppdaterings villkor

Många andra ändringar kan göras utan att det påverkar befintliga fysiska strukturer. Mer specifikt kräver *inte* följande ändringar att en index återskapas. För dessa ändringar kan du [Uppdatera en index definition](/rest/api/searchservice/update-index) med dina ändringar.

+ Lägg till ett nytt fält
+ Ange det **hämtnings** bara attributet för ett befintligt fält
+ Ange ett **searchAnalyzer** för ett befintligt fält
+ Lägga till en ny analys definition i ett index
+ Lägga till, uppdatera eller ta bort bedömnings profiler
+ Lägga till, uppdatera eller ta bort CORS-inställningar
+ Lägg till, uppdatera eller ta bort synonymMaps

När du lägger till ett nytt fält får befintliga indexerade dokument ett null-värde för det nya fältet. Vid en framtida data uppdatering ersätter värden från externa källdata de null-värden som läggs till av Azure Kognitiv sökning. Mer information om hur du uppdaterar index innehåll finns i [lägga till, uppdatera eller ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Återskapa ett index

Under utvecklingen ändras index schemat ofta. Du kan planera för det genom att skapa index som kan tas bort, återskapas och läsas in snabbt med en liten representativ data uppsättning.

För program som redan finns i produktion rekommenderar vi att du skapar ett nytt index som kör sida vid sida ett befintligt index för att undvika frågor om avbrott. Din program kod tillhandahåller omdirigering till det nya indexet.

Indexeringen körs inte i bakgrunden och tjänsten kommer att balansera ytterligare indexering mot pågående frågor. Under indexeringen kan du [övervaka fråge förfrågningar](search-monitor-queries.md) i portalen för att se till att frågor slutförs inom rimlig tid.

1. Avgör om en återuppbyggnad krävs. Om du bara lägger till fält eller ändrar någon del av indexet som inte är relaterat till fält, kan du enkelt [Uppdatera definitionen](/rest/api/searchservice/update-index) utan att ta bort, återskapa och helt ladda om den.

1. [Hämta en index definition](/rest/api/searchservice/get-index) om du behöver den för framtida bruk.

1. [Släpp det befintliga indexet](/rest/api/searchservice/delete-index), förutsatt att du inte kör nya och gamla index sida vid sida. 

   Alla frågor som är riktade mot indexet släpps direkt. Kom ihåg att om du tar bort ett index går det inte att ångra, förstöra fysiskt lagrings utrymme för fält samlingen och andra konstruktioner. Pausa för att tänka på konsekvenserna innan du släpper det. 

1. [Skapa ett uppdaterat index](/rest/api/searchservice/create-index), där bröd texten i begäran inkluderar ändrade eller ändrade fält definitioner.

1. [Läs in indexet med dokument](/rest/api/searchservice/addupdate-or-delete-documents) från en extern källa.

När du skapar ett index allokeras det fysiska lagrings utrymmet för varje fält i index schemat, med ett inverterat index som skapats för varje sökbart fält. Fält som inte är sökbara kan användas i filter eller uttryck, men inte har inverterade index och är inte full text eller fuzzy sökbar. Vid en index återskapning tas dessa inverterade index bort och återskapas baserat på det index schema som du anger.

När du läser in indexet fylls varje fälts inverterade index med alla unika, token ord från varje dokument, med en karta till motsvarande dokument-ID. Om du till exempel indexerar en hotell data uppsättning kan ett inverterat index som skapats för ett stads fält innehålla villkor för Seattle, Göteborg och så vidare. Dokument som innehåller Seattle eller Göteborg i fältet stad har sitt dokument-ID som anges tillsammans med termen. På alla åtgärder för att [lägga till, uppdatera eller ta bort](/rest/api/searchservice/addupdate-or-delete-documents) uppdateras villkoren och dokument-ID-listan.

> [!NOTE]
> Om du har stränga SLA-krav kan du överväga att tillhandahålla en ny tjänst särskilt för detta arbete, med utveckling och indexering som inträffar i fullständig isolering från ett produktions index. En separat tjänst körs på sin egen maskin vara, vilket eliminerar risken för resurs konkurrens. När utvecklingen är klar skulle du antingen lämna det nya indexet på plats, omdirigera frågor till den nya slut punkten och indexet, eller så kan du köra slutförd kod för att publicera ett reviderat index på den ursprungliga Azure Kognitiv sökning-tjänsten. Det finns för närvarande ingen mekanism för att flytta ett klart att använda-index till en annan tjänst.

## <a name="check-for-updates"></a>Sök efter uppdateringar

Du kan börja fråga ett index så snart det första dokumentet har lästs in. Om du känner till ett dokuments ID returnerar [Sök dokumentet REST API](/rest/api/searchservice/lookup-document) det aktuella dokumentet. För bredare testning bör du vänta tills indexet har lästs in och sedan använda frågor för att kontrol lera den kontext som du förväntar dig att se.

Du kan använda [Sök Utforskaren](search-explorer.md) eller ett webbtest-verktyg som [Postman](search-get-started-rest.md) eller [Visual Studio Code](search-get-started-vs-code.md) för att söka efter uppdaterat innehåll.

Om du har lagt till eller bytt namn på ett fält använder du [$Select](search-query-odata-select.md) för att returnera fältet: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexera stora data mängder i skala](search-howto-large-index.md)
+ [Indexering i portalen](search-import-data-portal.md)
+ [Azure SQL Database indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Kognitiv sökning](search-security-overview.md)