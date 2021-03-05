---
title: Funktioner för flera modeller
description: Med Microsoft Azure SQL kan du arbeta med flera data modeller i samma databas.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: b16a2fc9f107a8420fb7d05667807a869fa3e00a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172765"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Funktioner för flera modeller i Azure SQL Database & SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Med flera modell databaser kan du lagra och arbeta med data som representeras i flera data format, till exempel Relations data, grafer, JSON/XML-dokument, nyckel/värde-par och så vidare.

## <a name="when-to-use-multi-model-capabilities"></a>När du ska använda funktioner för flera modeller

[Azure SQL-familjen med produkter](azure-sql-iaas-vs-paas-what-is-overview.md) är utformad för att fungera med den relations modell som ger bästa möjliga prestanda i de flesta fall för en rad generella program. Azure SQL-familjen för produkter är dock inte begränsad till endast Relations data. Med produkter i Azure SQL-familjen kan du använda en mängd olika icke-relationella format som är nära integrerade i Relations modellen.
Du bör överväga att använda funktioner för flera modeller i Azure SQL-serien med produkter i följande fall:

- Du har en del information eller strukturer som passar bättre för NoSQL-modeller och du inte vill använda separata NoSQL-databaser.
- En majoritet av dina data lämpar sig för Relations modeller och du behöver modellera vissa delar av dina data i NoSQL-format.
- Du vill använda Rich-SQL-språk för att fråga och analysera både Relations-och NoSQL data och integrera den med en rad olika verktyg och program som kan använda SQL-språket.
- Du vill använda databas funktioner som [minnes intern teknik](in-memory-oltp-overview.md) för att förbättra prestandan för din analys eller bearbetning av dina NoSQL data strukturer [, använda Transaktionsreplikering](managed-instance/replication-transactional-overview.md) eller [läsbara repliker](database/read-scale-out.md) för att skapa en kopia av dina data på den andra platsen och avlasta vissa analytiska arbets belastningar från den primära databasen.

## <a name="overview"></a>Översikt

Azure SQL-serien med produkter innehåller följande funktioner för flera modeller:

- Med [Graph-funktioner](#graph-features) kan du Visa dina data som en uppsättning noder och kanter och använda standard Transact-SQL-frågor som har förbättrats med diagram `MATCH` operator för att fråga graf-data.
- Med [JSON-funktioner](#json-features) kan du använda JSON-dokument i tabeller, transformera Relations data till JSON-dokument och vice versa. Du kan använda standard språket för Transact-SQL med JSON-funktioner för att parsa dokument och använda icke-grupperade index, columnstore-index eller minnesoptimerade tabeller för att optimera dina frågor.
- Med [spatiala funktioner](#spatial-features) kan du lagra geografiska och geometriska data, indexera dem med hjälp av rums index och hämta data med hjälp av spatiala frågor.
- Med [XML-funktioner](#xml-features) kan du lagra och indexera XML-data i databasen och använda inbyggda XQuery/XPath-åtgärder för att arbeta med XML-data. Azure SQL-serien med produkter har en specialiserad inbyggd XML-frågemotor som bearbetar XML-data.
- [Nyckel/värde-par](#key-value-pairs) stöds inte uttryckligen som särskilda funktioner eftersom nyckel/värde-par kan modelleras internt som två kolumn tabeller.

  > [!Note]
  > Du kan använda JSON Path-uttryck, XQuery/XPath-uttryck, spatiala funktioner och Graph-frågeuttryck i samma Transact-SQL-fråga för att få åtkomst till alla data som du har lagrat i databasen. Dessutom kan alla verktyg eller programmeringsspråk som kan köra Transact-SQL-frågor även använda det Query-gränssnittet för att få åtkomst till data i flera modeller. Detta är den viktigaste skillnaden jämfört med databaser med flera modeller, till exempel [Azure Cosmos DB](../cosmos-db/index.yml) som tillhandahåller specialiserat API för olika data modeller.

I följande avsnitt får du lära dig mer om de viktigaste funktionerna för flera modeller i Azure SQL-serien med produkter.

## <a name="graph-features"></a>Diagramfunktioner

Azure SQL-serien med produkter ger diagram över flera-till-många-relationer i databasen. Ett diagram är en samling av noder (eller hörn) och kanter (eller relationer). En nod representerar en entitet (till exempel en person eller en organisation) och en kant representerar en relation mellan de två noder som den ansluter till (till exempel gillar eller vänner). Här följer några funktioner som gör en graf-databas unik:

- Kanter eller relationer är första klass enheter i en graf-databas och kan ha attribut eller egenskaper som är kopplade till dem.
- En enda gräns kan ansluta flera noder i en diagram databas på ett flexibelt sätt.
- Du kan enkelt matcha mönster matchning och navigerings frågor med flera hopp.
- Du kan enkelt uttrycka transitiva och polymorfa frågor.

[Graf-och Graph-frågornas funktioner](/sql/relational-databases/graphs/sql-graph-overview) är integrerade i Transact-SQL och tar emot fördelarna med att använda SQL Server Database Engine som grundläggande databas hanterings system.

### <a name="when-to-use-a-graph-capability"></a>När du ska använda en diagram funktion

Det finns ingen graf-databas som kan uppnås med hjälp av en Relations databas. En graf-databas kan dock göra det enklare att uttrycka vissa frågor. Ditt beslut om att välja ett över det andra kan baseras på följande faktorer:

- Modellera hierarkiska data där en nod kan ha flera överordnade, så HierarchyId kan inte användas
- Modellen har ett komplext antal-till-många-relationer i programmet. När programmet utvecklas läggs nya relationer till.
- Du måste analysera sammankopplade data och relationer.

## <a name="json-features"></a>JSON-funktioner

Med produkter i Azure SQL-serien kan du analysera och fråga data som representeras i JavaScript Object Notation [(JSON)](https://www.json.org/) -format och exportera Relations data som JSON-text.

JSON är ett populärt data format som används för att utbyta data i moderna webb-och mobil program. JSON används också för att lagra halv strukturerade data i loggfiler eller i NoSQL-databaser som [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Många REST-webb tjänster returnerar resultat formaterade som JSON-text eller accepterar data som formaterats som JSON. De flesta Azure-tjänster som [azure kognitiv sökning](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)och [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) har rest-slutpunkter som returnerar eller använder JSON.


Med produkter i Azure SQL-familjen kan du enkelt arbeta med JSON-data och integrera databasen med moderna tjänster och tillhandahålla följande funktioner för att arbeta med JSON-data:

![JSON-funktioner](./media/multi-model-features/image_1.png)

Om du har JSON-text kan du extrahera data från JSON eller kontrol lera att JSON är korrekt formaterad genom att använda de inbyggda funktionerna [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)och [ISJSON](/sql/t-sql/functions/isjson-transact-sql). Med funktionen [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) kan du uppdatera värdet i JSON-text. För mer avancerade frågor och analyser kan [openjson](/sql/t-sql/functions/openjson-transact-sql) -funktionen omvandla en matris med JSON-objekt till en uppsättning rader. Alla SQL-frågor kan köras i den returnerade resultat uppsättningen. Slutligen finns det en [for JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) -sats som gör att du kan formatera data som lagras i dina Relations tabeller som JSON-text.

Mer information finns i [så här arbetar du med JSON-data](database/json-features.md).
[JSON](/sql/relational-databases/json/json-data-sql-server) är en grundläggande SQL Server databas motor funktion.

### <a name="when-to-use-a-json-capability"></a>När du ska använda en JSON-funktion

Dokument modeller kan användas i stället för Relations modeller i vissa olika scenarier:

- Hög normalisering av schema ger inte avsevärda fördelar eftersom du har åtkomst till alla fält i objekten samtidigt, eller om du aldrig uppdaterar normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten för dina frågor på grund av det stora antalet tabeller som du måste koppla för att hämta data.
- Du arbetar med de program som ursprungligen använder JSON-dokument är kommunikations-eller data modeller, och du vill inte införa fler lager som transformerar Relations data till JSON och vice versa.
- Du behöver förenkla din data modell genom att normalisera de underordnade tabellerna eller entitetens objekt värde mönster.
- Du måste läsa in eller exportera data som lagras i JSON-format utan ytterligare verktyg som analyserar data.

## <a name="spatial-features"></a>Spatiala funktioner

Spatialdata representerar information om den fysiska platsen och formen på geometriska objekt. Dessa objekt kan vara plats platser eller mer komplexa objekt, till exempel länder/regioner, vägar eller sjöar.

 De två avstånds data typerna som stöds: 

- Geometri typen representerar data i ett Euclidean (flat)-koordinatsystem.
- Geografi typen representerar data i ett system för Round-jordens koordinater.

Det finns ett antal spatialdata som kan användas i Azure SQL-serien med produkter som gör det möjligt att parsa och fråga data som representeras i JavaScript Object Notation [(JSON)](https://www.json.org/) -format och exportera Relations data som JSON-text.
till exempel [punkt](/sql/relational-databases/spatial/point), [lin Est ring](/sql/relational-databases/spatial/linestring), [polygon](/sql/relational-databases/spatial/polygon)och så vidare.

Azure SQL-serien med produkter tillhandahåller också särskilda [rums index](/sql/relational-databases/spatial/spatial-indexes-overview) som kan användas för att förbättra prestandan för dina spatialdata.

[Spatial support](/sql/relational-databases/spatial/spatial-data-sql-server) är en grundläggande SQL Server databas motor funktion.

## <a name="xml-features"></a>XML-funktioner

Den SQL Server databas motorn är en kraftfull plattform för att utveckla rika program för en delvis strukturerad data hantering. Stöd för XML är integrerat i alla komponenter i databas motorn och innehåller följande:

- Data typen XML. XML-värden kan lagras internt i en kolumn av typen XML-datatyp som kan skrivas i enlighet med en samling XML-scheman eller lämnas utan Skriv. Du kan indexera XML-kolumnen.
- Möjlighet att ange en XQuery-fråga mot XML-data som lagras i kolumner och variabler i XML-typen. XQuery-funktioner kan användas i alla Transact-SQL-frågor som har åtkomst till alla data modeller som du använder i databasen.
- Indexera automatiskt alla element i XML-dokument med hjälp av [primärt XML-index](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) eller ange exakta sökvägar som ska indexeras med [sekundärt XML-index](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OpenRowSet som tillåter Mass inläsning av XML-data.
- Transformera Relations data till XML-format.

[XML](/sql/relational-databases/xml/xml-data-sql-server) är en grundläggande SQL Server databas motor funktion.

### <a name="when-to-use-an-xml-capability"></a>När du ska använda en XML-funktion

Dokument modeller kan användas i stället för Relations modeller i vissa olika scenarier:

- Hög normalisering av schema ger inte avsevärda fördelar eftersom du har åtkomst till alla fält i objekten samtidigt, eller om du aldrig uppdaterar normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten för dina frågor på grund av det stora antalet tabeller som du måste koppla för att hämta data.
- Du arbetar med de program som ursprungligen använder XML-dokument är kommunikations-eller data modeller, och du vill inte införa fler lager som transformerar Relations data till XML och vice versa.
- Du behöver förenkla din data modell genom att normalisera de underordnade tabellerna eller entitetens objekt värde mönster.
- Du måste läsa in eller exportera data som lagras i XML-format utan ytterligare verktyg som analyserar data.

## <a name="key-value-pairs"></a>Nyckel/värde-par

Azure SQL-familjen med produkter har inga specialiserade typer eller strukturer som stöder nyckel/värde-par eftersom nyckel värdes strukturer kan visas internt som standard Relations tabeller:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Du kan anpassa den här nyckel värdes strukturen så att den passar dina behov utan begränsningar. Värdet kan till exempel vara XML-dokument i stället för `nvarchar(max)` typ, om värdet är JSON-dokument, kan du ange `CHECK` en begränsning som verifierar att JSON-innehåll är giltigt. Du kan placera ett valfritt antal värden som är relaterade till en nyckel i de ytterligare kolumnerna, lägga till beräknade kolumner och index för att förenkla och optimera data åtkomst, definiera tabellen som en minnes/optimerad schema tabell för att få bättre prestanda osv.

Se [hur bwin använder In-Memory OLTP för att uppnå oöverträffad prestanda och skalning](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale) för sin ASP.net caching-lösning som nådde 1.200.000 batchar per sekund, som ett exempel på hur Relations modellen kan användas som nyckel värdes par lösning i praxis.

## <a name="next-steps"></a>Nästa steg

Funktioner för flera modeller i Azure SQL-serien är också de viktigaste SQL Server databas motor funktioner som delas av produkter i Azure SQL-serien. Mer information om dessa funktioner finns i dokumentations sidorna för SQL Relations databas:

- [Diagram bearbetning](/sql/relational-databases/graphs/sql-graph-overview)
- [JSON-data](/sql/relational-databases/json/json-data-sql-server)
- [Spatial support](/sql/relational-databases/spatial/spatial-data-sql-server)
- [XML-data](/sql/relational-databases/xml/xml-data-sql-server)
