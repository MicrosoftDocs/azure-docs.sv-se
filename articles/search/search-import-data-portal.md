---
title: Importera data till ett Sök index med hjälp av Azure Portal
titleSuffix: Azure Cognitive Search
description: Lär dig hur du använder guiden Importera data i Azure Portal för att crawla Azure-data från Cosmos DB, Blob Storage, Table Storage, SQL Database, SQL-hanterad instans och SQL Server på virtuella Azure-datorer.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935219"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Guiden Importera data för Azure Kognitiv sökning

Azure Portal tillhandahåller guiden **Importera data** på Azure kognitiv sökning-instrumentpanelen för prototyper och inläsning av ett index. Den här artikeln beskriver fördelar och begränsningar i att använda guiden, indata och utdata samt viss användnings information. Praktisk vägledning om hur du går igenom guiden med hjälp av inbyggda exempel data finns i [skapa ett Azure kognitiv sökning-index med hjälp av snabb start för Azure Portal](search-get-started-portal.md) .

Åtgärder som den här guiden utför är:

1-Anslut till en Azure-datakälla som stöds.

2 – Skapa ett index schema som härleds genom data insamlings data.

3 – du kan även lägga till AI-berikare för att extrahera eller generera innehåll och struktur.

4 – kör guiden för att skapa objekt, importera data, ange ett schema och andra konfigurations alternativ.

Guiden skapar ett antal objekt som sparas i din Sök tjänst, som du kan använda för att komma åt program mässigt eller i andra verktyg.

## <a name="advantages-and-limitations"></a>Fördelar och begränsningar

Innan du skriver någon kod kan du använda guiden för att testa prototyper och koncept bevis. Guiden ansluter till externa data källor, samplar data för att skapa ett ursprungligt index och importerar sedan data som JSON-dokument till ett index på Azure Kognitiv sökning. 

Sampling är den process genom vilken ett index schema härleds och har vissa begränsningar. När data källan skapas, väljer guiden ett exempel på dokument för att bestämma vilka kolumner som är en del av data källan. Alla filer är inte lästa eftersom det kan ta flera timmar för mycket stora data källor. Ett urval av dokument, käll-metadata, till exempel fält namn eller typ, används för att skapa en fält samling i ett index schema. Beroende på data komplexiteten kan du behöva redigera det inledande schemat för precision, eller utöka det så att det blir klart. Du kan göra dina ändringar infogade på index definitions sidan.

Som helhet är fördelarna med att använda guiden tydliga: så länge kraven är uppfyllda kan du prototypa ett fråge bara index på några minuter. En del av de komplexa indexeringarna, till exempel att tillhandahålla data som JSON-dokument, hanteras av guiden.

Kända begränsningar sammanfattas på följande sätt:

+ Guiden stöder inte iteration eller åter användning. Varje steg i guiden skapar en ny index-, färdigheter-och indexerings konfiguration. Endast data källor kan vara beständiga och återanvändas i guiden. Om du vill redigera eller förfina andra objekt måste du använda REST-API: erna eller .NET SDK för att hämta och ändra strukturerna.

+ Käll innehållet måste finnas i en Azure-datakälla som stöds.

+ Sampling är över en delmängd av käll data. För stora data källor är det möjligt att guiden saknar fält. Du kan behöva utöka schemat eller rätta till härledda data typer om samplingen är otillräcklig.

+ AI-berikning, som visas i portalen, är begränsad till några inbyggda kunskaper. 

+ Ett [kunskaps lager](knowledge-store-concept-intro.md)som kan skapas i guiden är begränsat till några få standardprojektioner. Om du vill spara berikade dokument som skapats med guiden levereras BLOB-behållaren och-tabellerna med standard namn och struktur.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Data källans indata

Guiden **Importera data** ansluter till en extern data källa med hjälp av den interna logiken som tillhandahålls av Azure kognitiv sökning indexerare, som är utrustade för att testa källan, läsa metadata, knäcka dokument för att läsa innehåll och struktur och serialisera innehåll som JSON för efterföljande import till Azure kognitiv sökning.

Du kan bara importera från en enskild tabell, databas visning eller motsvarande data struktur, men strukturen kan innehålla hierarkiska eller kapslade under strukturer. Mer information finns i [så här modellerar du komplexa typer](search-howto-complex-data-types.md).

Du bör skapa den här enskilda tabellen eller vyn innan du kör guiden och den måste innehålla innehåll. Av uppenbara skäl är det inte klokt att köra guiden **Importera data** på en tom data källa.

|  Urval | Beskrivning |
| ---------- | ----------- |
| **Befintlig datakälla** |Om du redan har definierat indexerare i Sök tjänsten kan du ha en befintlig definition av data källan som du kan återanvända. I Azure Kognitiv sökning används data käll objekt endast av indexerare. Du kan skapa ett data käll objekt program mässigt eller via guiden **Importera data** och återanvända dem efter behov.|
| **Exempel**| Azure Kognitiv sökning innehåller två inbyggda exempel data källor som används i självstudier och snabb starter: en fastighets SQL-databas och en hotell databas som finns på Cosmos DB. En genom gång baserat på hotell exemplet finns i [skapa ett index i](search-get-started-portal.md) snabb starten för Azure Portal. |
| [**Azure SQL Database-eller SQL-hanterad instans**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Tjänstens namn, autentiseringsuppgifterna för en databasanvändare med läsbehörighet och ett databasnamn kan anges på sidan eller via en ADO.NET-anslutningssträng. Välj alternativet för anslutningssträngar till att visa eller anpassa egenskaperna. <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver.|
| **SQL Server på virtuella Azure-datorer** |Ange ett fullständigt tjänst namn, användar-ID och lösen ord samt databas som anslutnings sträng. Om du vill använda den här datakällan måste du tidigare ha installerat ett certifikat i det lokala arkiv som krypterar anslutningen. Instruktioner finns i [SQL VM-anslutning till Azure kognitiv sökning](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Kraven innefattar konto, databas och samling. Alla dokument i samlingen kommer att ingå i indexet. Du kan definiera en fråga för att förenkla eller filtrera rad uppsättningen eller lämna frågan tom. En fråga krävs inte i den här guiden.|
| [**Azure-Blob Storage**](search-howto-indexing-azure-blob-storage.md) |Kraven innefattar lagringskonto och en container. Om blob-namnet följer en virtuell namngivningskonvention i grupperingssyfte, kan du också ange den virtuella katalogdelen av namnet som en mapp under containern. Se [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md) för mer information. |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |Kraven innefattar lagringskontot och ett tabellnamn. Du kan också ange en fråga för att hämta en delmängd av tabellerna. Se [Indexera Table Storage](search-howto-indexing-azure-tables.md) för mer information. |

## <a name="wizard-output"></a>Guidens utdata

Guiden skapar, konfigurerar och anropar följande objekt i bakgrunden. När guiden har körts kan du hitta dess utdata på Portal sidorna. Översikts sidan för din tjänst har listor över index, indexerare, data källor och färdighetsuppsättningar. Index definitioner kan visas i hela JSON i portalen. För andra definitioner kan du använda [REST API](/rest/api/searchservice/) för att hämta vissa objekt.

| Objekt | Beskrivning | 
|--------|-------------|
| [Datakälla](/rest/api/searchservice/create-data-source)  | Sparar anslutnings information för källdata, inklusive autentiseringsuppgifter. Ett data käll objekt används enbart med indexerare. | 
| [Index](/rest/api/searchservice/create-index) | Fysisk data struktur som används för full texts ökning och andra frågor. | 
| [Färdigheter](/rest/api/searchservice/create-skillset) | En fullständig uppsättning instruktioner för att manipulera, transformera och forma innehåll, inklusive analys och extrahering av information från bildfiler. Förutom för mycket enkla och begränsade strukturer innehåller den en referens till en Cognitive Services-resurs som ger berikning. Alternativt kan det även innehålla en kunskaps lager definition.  | 
| [Indexerare](/rest/api/searchservice/create-indexer)  | Ett konfigurations objekt som anger en data källa, ett mål index, ett valfritt färdigheter, valfritt schema och valfria konfigurations inställningar för fel hand och bas-64-kodning. |


## <a name="how-to-start-the-wizard"></a>Starta guiden

Guiden Importera data startas från kommando fältet på sidan tjänst översikt.

1. I [Azure Portal](https://portal.azure.com)öppnar du sidan Sök tjänst från instrument panelen eller [söker efter tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i tjänst listan.

2. På sidan tjänst översikt längst upp klickar du på **Importera data**.

   ![Importera data kommando i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

Du kan också starta **Importera data** från andra Azure-tjänster, inklusive Azure Cosmos DB, Azure SQL Database, SQL-hanterad instans och Azure Blob Storage. Sök efter **Lägg till Azure-kognitiv sökning** i det vänstra navigerings fönstret på sidan tjänst översikt.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Så här redigerar eller slutför du ett index schema i guiden

Guiden genererar ett ofullständigt index som kommer att fyllas i med dokument som hämtats från Indatakällan för indata. Se till att du har definierat följande element för ett funktionellt index.

1. Är fält listan slutförd? Lägg till nya fält som samplingen missade och ta bort alla som inte lägger till värden i en Sök funktion eller som inte används i ett [filter uttryck](search-query-odata-filter.md) eller en [bedömnings profil](index-add-scoring-profiles.md).

1. Är data typen lämplig för inkommande data? Azure Kognitiv sökning stöder [data typerna Entity Data Model (EDM)](/rest/api/searchservice/supported-data-types). För Azure SQL data finns [mappnings diagram](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) som innehåller motsvarande värden. Mer bakgrunds information finns i [fält mappningar och transformeringar](search-indexer-field-mappings.md).

1. Har du ett fält som kan användas som *nyckel*? Det här fältet måste vara en EDM. String och måste identifiera ett dokument unikt. För Relations data kan den mappas till en primär nyckel. För blobbar kan det vara `metadata-storage-path` . Om fältvärdena innehåller blank steg eller tank streck, måste du ange alternativet **bas-64-kodning** i steget **skapa ett indexerare** , under **Avancerade alternativ** för att utelämna verifierings kontrollen för dessa tecken.

1. Ange attribut för att avgöra hur fältet används i ett index. 

   Ta din tid med det här steget eftersom attributen bestämmer det fysiska uttrycket för fält i indexet. Om du vill ändra attribut senare, även via programmering, så behöver du nästan alltid släppa och återskapa indexet. Core-attribut som **sökbar** och **hämtnings** bar har en [försumbar inverkan på lagringen](search-what-is-an-index.md#index-size). Genom att aktivera filter och använda förslag ökar du lagrings kraven. 
   
   + **Sökbart** möjliggör full texts ökning. Alla fält som används i kostnads fria formulär frågor eller i frågeuttryck måste ha det här attributet. Inverterade index skapas för varje fält som du markerar som **sökbart**.

   + **Hämtnings** bar returnerar fältet i Sök resultatet. Alla fält som tillhandahåller innehåll till Sök resultat måste ha det här attributet. Att ställa in det här fältet påverkar inte index storleken märkbart.

   + **Filterable** tillåter att fältet refereras till i filter uttryck. Alla fält som används i ett **$filter**  -uttryck måste ha det här attributet. Filter uttryck är för exakta matchningar. Eftersom text strängar förblir intakta krävs ytterligare lagrings utrymme för att rymma orda Grant-innehållet.

   + **Fasettable** aktiverar fältet för fasett-navigering. Endast fält som har marker ATS som **Filterable** kan markeras som **fasettable**.

   + **Sorterbar** gör att fältet kan användas i en sortering. Alla fält som används i ett **$OrderBy** -uttryck måste ha det här attributet.

1. Behöver du en [lexikalisk analys](search-lucene-query-architecture.md#stage-2-lexical-analysis)? För EDM. **String-fält** som är sökbara kan du ange en **analys** om du vill använda språkförbättrad indexering och frågor. 

   Standardvärdet är standard *Lucene* men du kan välja *Microsoft English* om du vill använda Microsofts analys för avancerad lexikal bearbetning, till exempel att lösa oregelbundna Substantiv-och verb-formulär. Det går bara att ange språk analys verktyg i portalen. Att använda en anpassad analys eller en icke-språks analys som nyckelord, mönster och så vidare, måste utföras program mässigt. Mer information om analys verktyg finns i [lägga till språk analys](search-language-support.md)verktyg.

1. Behöver du typeahead-funktioner i form av Autoavsluta eller föreslagna resultat? Markera kryss rutan **föreslå** för att aktivera [typeahead fråge förslag och Autoavsluta](index-add-suggesters.md) för markerade fält. Förslags koder lägger till antalet tokens i ditt index och använder därmed mer lagrings utrymme.


## <a name="next-steps"></a>Nästa steg

Det bästa sättet att förstå fördelarna och begränsningarna i guiden är att gå igenom den. Följande snabb start vägleder dig genom varje steg.

> [!div class="nextstepaction"]
> [Skapa ett Azure Kognitiv sökning-index med hjälp av Azure Portal](search-get-started-portal.md)