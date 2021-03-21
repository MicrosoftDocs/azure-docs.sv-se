---
title: Sök över Azure SQL-data
titleSuffix: Azure Cognitive Search
description: Importera data från Azure SQL Database-eller SQL-hanterad instans med hjälp av indexerare för full texts ökning i Azure Kognitiv sökning. Den här artikeln beskriver anslutningar, indexerings konfiguration och data inmatning.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 04e4801c26b0ac8ef91af0b028d9dc2bb9a3cd1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358634"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Ansluta till och indexera Azure SQL-innehåll med hjälp av Azure Kognitiv sökning-indexeraren

Innan du kan skicka frågor till ett [Azure kognitiv sökning-index](search-what-is-an-index.md)måste du fylla i det med dina data. Om data finns i Azure SQL Database-eller SQL-hanterad instans kan en **azure kognitiv sökning-indexerare för Azure SQL Database** (eller **Azure SQL-indexeraren** för kort) automatisera indexerings processen, vilket innebär att mindre kod kan skriva och mindre infrastruktur för att vara försiktig.

Den här artikeln beskriver Mechanics för användning av [indexerare](search-indexer-overview.md), men beskriver även funktioner som endast är tillgängliga med Azure SQL Database eller SQL-hanterad instans (till exempel integrerad ändrings spårning). 

Förutom Azure SQL Database-och SQL-hanterad instans tillhandahåller Azure Kognitiv sökning indexerare för [Azure Cosmos DB](search-howto-index-cosmosdb.md) [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)och [Azure Table Storage](search-howto-indexing-azure-tables.md). Om du vill begära support för andra data källor anger du din feedback i [Azure kognitiv sökning feedback-forumet](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexerare och data källor

En **data källa** anger vilka data som ska indexeras, autentiseringsuppgifter för data åtkomst och principer som effektivt identifierar ändringar i data (nya, ändrade eller borttagna rader). Den definieras som en oberoende resurs så att den kan användas av flera indexerare.

En **indexerare** är en resurs som ansluter en enskild data källa med ett mål Sök index. En indexerare används på följande sätt:

* Utför en eng ång slö kopia av data för att fylla ett index.
* Uppdatera ett index med ändringar i data källan enligt ett schema.
* Kör på begäran för att uppdatera ett index efter behov.

En enskild indexerare kan bara använda en tabell eller vy, men du kan skapa flera indexerare om du vill fylla flera Sök index. Mer information om begrepp finns i [Indexer åtgärder: typiskt arbets flöde](/rest/api/searchservice/Indexer-operations#typical-workflow).

Du kan konfigurera och konfigurera en Azure SQL-indexerare med hjälp av:

* Guiden Importera data i [Azure Portal](https://portal.azure.com)
* Azure Kognitiv sökning [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)
* Azure Kognitiv sökning [REST API](/rest/api/searchservice/indexer-operations)

I den här artikeln använder vi REST API för att skapa **indexerare** och **data källor**.

## <a name="when-to-use-azure-sql-indexer"></a>När du ska använda Azure SQL-indexeraren
Beroende på flera faktorer som är relaterade till dina data kan användningen av Azure SQL-indexeraren eventuellt vara lämplig. Om dina data uppfyller följande krav kan du använda Azure SQL-indexeraren.

| Kriterie | Information |
|----------|---------|
| Data härstammar från en enskild tabell eller vy | Om data är spridda över flera tabeller kan du skapa en enskild vy av data. Men om du använder en vy kan du inte använda SQL Server integrerad ändrings identifiering för att uppdatera ett index med stegvisa ändringar. Mer information finns i [fånga ändrade och borttagna rader](#CaptureChangedRows) nedan. |
| Data typerna är kompatibla | De flesta, men inte alla SQL-typer, stöds i ett Azure Kognitiv sökning-index. En lista finns i [mappa data typer](#TypeMapping). |
| Data synkronisering i real tid krävs inte | En indexerare kan indexera om din tabell högst var femte minut. Om data ändras ofta och ändringarna måste avspeglas i indexet inom några sekunder eller minuter rekommenderar vi att du använder [REST API](/rest/api/searchservice/AddUpdate-or-Delete-Documents) eller [.NET SDK](./search-get-started-dotnet.md) för att skicka uppdaterade rader direkt. |
| Stegvis indexering är möjlig | Om du har en stor data uppsättning och planerar att köra indexeraren enligt ett schema, måste Azure Kognitiv sökning effektivt kunna identifiera nya, ändrade eller borttagna rader. Icke-stegvis indexering är bara tillåtet om du indexerar på begäran (inte enligt schema) eller indexerar färre än 100 000 rader. Mer information finns i [fånga ändrade och borttagna rader](#CaptureChangedRows) nedan. |

> [!NOTE] 
> Azure Kognitiv sökning stöder endast SQL Server autentisering. Om du behöver stöd för Azure Active Directory lösenordsautentisering, bör du rösta [på detta.](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)

## <a name="create-an-azure-sql-indexer"></a>Skapa en Azure SQL-indexerare

1. Skapa data källan:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Anslutnings strängen kan följa något av följande format:
    1. Du kan hämta anslutnings strängen från [Azure Portal](https://portal.azure.com). Använd `ADO.NET connection string` alternativet.
    1. En anslutnings sträng för hanterad identitet som inte innehåller någon konto nyckel med följande format: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;` . Om du vill använda den här anslutnings strängen följer du anvisningarna för att konfigurera [en Indexer-anslutning till en Azure SQL Database med hjälp av en hanterad identitet](search-howto-managed-identities-sql.md).

2. Skapa ett Azure Kognitiv sökning-index om du inte redan har en. Du kan skapa ett index med hjälp av [portalen](https://portal.azure.com) eller [skapa index-API](/rest/api/searchservice/Create-Index). Se till att schemat för mål indexet är kompatibelt med schemat för käll tabellen – se [mappning mellan SQL-och Azure-funktionen Sök data typer](#TypeMapping).

3. Skapa indexeraren genom att ge den ett namn och referera till data källan och mål indexet:

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

En indexerare som skapats på det här sättet har inget schema. Den körs automatiskt när den skapas. Du kan köra den igen när som helst med hjälp av en **körnings indexerare** -begäran:

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

Du kan anpassa flera aspekter av indexerings beteende, till exempel batchstorleken och hur många dokument som kan hoppas över innan en indexerare-körning Miss lyckas. Mer information finns i [skapa indexerings-API](/rest/api/searchservice/Create-Indexer).

Du kan behöva tillåta Azure-tjänster att ansluta till din databas. Se [ansluta från Azure](../azure-sql/database/firewall-configure.md) för instruktioner om hur du gör det.

Om du vill övervaka indexerings status och körnings historik (antal objekt som indexeras, Miss lyckas osv.) använder du en **indexerare status** förfrågan:

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

Svaret bör se ut ungefär så här:

```
    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

Körnings historiken innehåller upp till 50 av de senaste slutförda körningarna, som sorteras i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).
Ytterligare information om svaret finns i [Hämta index status](/rest/api/searchservice/get-indexer-status)

## <a name="run-indexers-on-a-schedule"></a>Köra indexerare enligt ett schema
Du kan också ordna indexeraren så att den körs regelbundet enligt ett schema. Det gör du genom att lägga till egenskapen **schema** när du skapar eller uppdaterar indexeraren. I exemplet nedan visas en skicka-begäran om att uppdatera indexeraren:

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Parametern **Interval** måste anges. Intervallet avser tiden från starten av två efterföljande körningar av indexerare. Det minsta tillåtna intervallet är 5 minuter; det längsta är en dag. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [varaktighets värde på ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Mönstret för detta är: `P(nD)(T(nH)(nM))` . Exempel: `PT15M` för var 15: e `PT2H` timme, för var 2: e timme.

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Skapa nya, ändrade och borttagna rader

Azure Kognitiv sökning använder **stegvis indexering** för att undvika att behöva indexera om hela tabellen eller vyn varje gång en indexerare körs. Azure Kognitiv sökning innehåller två principer för ändrings identifiering som stöder stegvis indexering. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL-integrerad Ändringsspårning princip
Om SQL-databasen stöder [ändrings spårning](/sql/relational-databases/track-changes/about-change-tracking-sql-server)rekommenderar vi att du använder **SQL-integrerad ändringsspårning princip**. Detta är den mest effektiva principen. Dessutom tillåter Azure Kognitiv sökning att identifiera borttagna rader utan att du behöver lägga till en explicit "mjuk borttagning"-kolumn i tabellen.

#### <a name="requirements"></a>Krav 

+ Versions krav för databas:
  * SQL Server 2012 SP3 och senare, om du använder SQL Server på virtuella Azure-datorer.
  * Azure SQL Database-eller SQL-hanterad instans.
+ Endast tabeller (inga vyer). 
+ [Aktivera ändrings spårning](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) för tabellen på-databasen. 
+ Ingen sammansatt primär nyckel (en primär nyckel som innehåller mer än en kolumn) i tabellen.  

#### <a name="usage"></a>Användning

Om du vill använda den här principen skapar eller uppdaterar du din data källa så här:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

När du använder en integrerad SQL-princip för ändrings spårning anger du inte en separat princip för identifiering av data borttagning – den här principen har inbyggt stöd för att identifiera borttagna rader. Men för att borttagningarna ska identifieras "automagic" måste dokument nyckeln i Sök indexet vara samma som den primära nyckeln i SQL-tabellen. 

> [!NOTE]  
> När du använder [truncate Table](/sql/t-sql/statements/truncate-table-transact-sql) för att ta bort ett stort antal rader från en SQL-tabell måste indexeraren [återställas](/rest/api/searchservice/reset-indexer) för att återställa ändrings spårnings statusen för att ta bort rader.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Princip för ändrings identifiering av hög vatten märke

Den här princip för ändrings identifiering förlitar sig på en kolumn med "högt vatten märke" som fångar in versionen eller tiden när en rad senast uppdaterades. Om du använder en vy måste du använda en hög vatten märkes princip. Den övre vatten märkes kolumnen måste uppfylla följande krav.

#### <a name="requirements"></a>Krav 

* Alla infogningar anger ett värde för kolumnen.
* Alla uppdateringar av ett objekt ändrar också värdet i kolumnen.
* Värdet för den här kolumnen ökar med varje infogning eller uppdatering.
* Frågor med följande satser och ORDER BY-satser kan köras effektivt: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Vi rekommenderar starkt att du använder data typen [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql) för kolumnen med hög vatten märke. Om någon annan datatyp används är ändrings spårning inte garanterat att samla in alla ändringar i närvaro av transaktioner som körs samtidigt med en indexerare-fråga. När du använder **ROWVERSION** i en konfiguration med skrivskyddade repliker måste du peka indexeraren på den primära repliken. Det går bara att använda en primär replik för data synkronisering.

#### <a name="usage"></a>Användning

Om du vill använda en hög vatten märkes princip skapar eller uppdaterar du din data källa så här:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> Om käll tabellen inte har något index för kolumnen med hög vatten märke kan det hända att frågor som används av SQL-indexeraren har nått sin tids gräns. I synnerhet `ORDER BY [High Water Mark Column]` kräver satsen att ett index körs effektivt när tabellen innehåller många rader.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

Om du använder data typen [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql) för den övre vatten märkes kolumnen, bör du överväga att använda `convertHighWaterMarkToRowVersion` konfigurations inställningen indexeraren. `convertHighWaterMarkToRowVersion` gör två saker:

* Använd data typen ROWVERSION för kolumnen hög vatten märke i SQL-frågan Indexer. Genom att använda rätt datatyp får du bättre prestanda för indexerings frågor.
* Subtrahera 1 från ROWVERSION-värdet innan frågan körs i indexet. Vyer med 1 till många kopplingar kan ha rader med dubbla ROWVERSION-värden. Om man subtraherar 1 bevaras inte de här raderna i indexerings frågan.

Om du vill aktivera den här funktionen skapar eller uppdaterar du indexeraren med följande konfiguration:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

Om du råkar ut för tids gräns fel kan du använda `queryTimeout` konfigurations inställningen indexeraren för att ange ett värde som är högre än standardvärdet på 5 minuter. Om du till exempel vill ställa in tids gränsen på 10 minuter skapar eller uppdaterar du indexeraren med följande konfiguration:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

Du kan också inaktivera- `ORDER BY [High Water Mark Column]` satsen. Detta rekommenderas dock inte eftersom körningen av indexeraren avbryts av ett fel, men indexeraren måste bearbeta alla rader på nytt om den körs senare – även om indexeraren redan har bearbetat nästan alla rader efter den tid som den avbröts. Om du vill inaktivera `ORDER BY` satsen använder du `disableOrderByHighWaterMarkColumn` inställningen i index definitions definitionen:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Princip för borttagning av mjuk borttagnings kolumn
När rader tas bort från käll tabellen, vill du förmodligen även ta bort dessa rader från Sök indexet. Om du använder SQL-integrerad ändrings spårnings princip, är det viktigt för dig. Den övre vatten märkets princip för ändrings spårning hjälper dock inte dig med borttagna rader. Vad bör jag göra?

Om raderna tas bort fysiskt från tabellen har Azure Kognitiv sökning inget sätt att härleda förekomst av poster som inte längre finns.  Du kan dock använda metoden "mjuk borttagning" för att logiskt ta bort rader utan att ta bort dem från tabellen. Lägg till en kolumn i tabellen eller vyn och markera rader som borttagna med den kolumnen.

När du använder metoden för att använda mjuk borttagning kan du ange principen för mjuk borttagning enligt följande när du skapar eller uppdaterar data källan:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**SoftDeleteMarkerValue** måste vara en sträng – Använd sträng representationen för det faktiska värdet. Om du till exempel har en heltals kolumn där borttagna rader är markerade med värdet 1, använder du `"1"` . Om du har en BIT-kolumn där borttagna rader har marker ATS med det booleska sanna värdet, använder du sträng literalen `True` eller `true` så spelar det ingen roll.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mappning mellan SQL-och Azure Kognitiv sökning data typer
| SQL-datatyp | Tillåtna fält typer för mål index | Kommentarer |
| --- | --- | --- |
| bit |EDM. Boolean, EDM. String | |
| int, smallint, tinyint |EDM. Int32, EDM. Int64, EDM. String | |
| bigint |EDM. Int64, EDM. String | |
| verkligt, flyttal |EDM. Double, EDM. String | |
| smallmoney, Money decimal tal |Edm.String |Azure Kognitiv sökning stöder inte konvertering av decimal typer till EDM. Double eftersom detta skulle förlora precisionen |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |En SQL-sträng kan användas för att fylla i ett samlings fält (EDM. String) om strängen representerar en JSON-matris med strängar: `["red", "white", "blue"]` |
| smalldatetime, DateTime, datetime2, date, DateTimeOffset |EDM. DateTimeOffset, EDM. String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |Endast geografi instanser av typ punkt med SRID 4326 (vilket är standard) stöds |
| rowversion |Ej tillämpligt |Rad versions kolumner kan inte lagras i Sök indexet, men de kan användas för ändrings spårning |
| tid, TimeSpan, Binary, varbinary, bild, XML, geometri, CLR-typer |Ej tillämpligt |Stöds inte |

## <a name="configuration-settings"></a>Konfigurations inställningar
SQL-indexeraren visar flera konfigurations inställningar:

| Inställning | Datatyp | Syfte | Standardvärde |
| --- | --- | --- | --- |
| queryTimeout |sträng |Anger tids gränsen för körning av SQL-fråga |5 minuter ("00:05:00") |
| disableOrderByHighWaterMarkColumn |boolesk |Gör att SQL-frågan som används av den övre vatten märkes principen utesluter ORDER BY-satsen. Se [hög vatten märkes princip](#HighWaterMarkPolicy) |falskt |

De här inställningarna används i `parameters.configuration` objektet i index definitions definitionen. Om du till exempel vill ange tids gräns för frågan till 10 minuter skapar eller uppdaterar du indexeraren med följande konfiguration:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>Vanliga frågor

**F: kan jag använda Azure SQL-indexeraren med SQL-databaser som körs på virtuella IaaS-datorer i Azure?**

Ja. Du måste dock tillåta att din Sök tjänst ansluter till din databas. Mer information finns i [Konfigurera en anslutning från en azure kognitiv sökning-indexerare till SQL Server på en virtuell Azure-dator](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**F: kan jag använda Azure SQL-indexeraren med SQL-databaser som körs lokalt?**

Inte direkt. Vi rekommenderar eller stöder inte direkt anslutning, eftersom det kräver att du öppnar dina databaser till Internet trafik. Kunder har slutfört det här scenariot med hjälp av bro teknik som Azure Data Factory. Mer information finns i [skicka data till ett Azure kognitiv sökning-index med hjälp av Azure Data Factory](../data-factory/v1/data-factory-azure-search-connector.md).

**F: kan jag använda Azure SQL-indexeraren med andra databaser än SQL Server som körs i IaaS i Azure?**

Nej. Vi har inte stöd för det här scenariot eftersom vi inte har testat indexeraren med några andra databaser än SQL Server.  

**F: kan jag skapa flera indexerare som körs enligt ett schema?**

Ja. Endast en indexerare kan dock köras på en nod i taget. Överväg att skala upp Sök tjänsten till fler än en Sök enhet om du behöver flera indexerare som körs samtidigt.

**F: påverkar min frågans arbets belastning genom att köra en indexerare?**

Ja. Indexeraren körs på en av noderna i Sök tjänsten och den nodens resurser delas mellan indexeringen och betjänar frågans trafik och andra API-begäranden. Om du kör intensiv indexering och frågar efter arbets belastningar och får en hög grad av 503 fel eller ökar svars tiderna kan du överväga att [skala upp Sök tjänsten](search-capacity-planning.md).

**F: kan jag använda en sekundär replik i ett [redundanskluster](../azure-sql/database/auto-failover-group-overview.md) som en data Källa?**

Det beror på. För fullständig indexering av en tabell eller vy kan du använda en sekundär replik. 

För stegvis indexering stöder Azure Kognitiv sökning två principer för ändrings identifiering: SQL-integrerad ändrings spårning och hög vatten märke.

SQL Database stöder inte integrerad ändrings spårning på skrivskyddade repliker. Därför måste du använda en hög vatten märknings princip. 

Vår standard rekommendation är att använda data typen ROWVERSION för den övre vatten märkes kolumnen. Men att använda ROWVERSION förlitar sig på `MIN_ACTIVE_ROWVERSION` funktionen, vilket inte stöds på skrivskyddade repliker. Därför måste du peka indexeraren till en primär replik om du använder ROWVERSION.

Om du försöker använda ROWVERSION på en skrivskyddad replik visas följande fel: 

Det finns inte stöd för att använda en ROWVERSION-kolumn för ändrings spårning på sekundära (skrivskyddade) tillgänglighets repliker. Uppdatera data källan och ange en anslutning till den primära tillgänglighets repliken. Den aktuella databasens egenskap för uppdaterings uppdatering är READ_ONLY.

**F: kan jag använda en alternativ, icke-ROWVERSION kolumn för ändrings spårning med hög vatten märke?**

Det rekommenderas inte. Endast **ROWVERSION** tillåter tillförlitlig datasynkronisering. Beroende på din program logik kan det dock vara säkert om:

+ Du kan se till att när indexeraren körs finns det inga utestående transaktioner i tabellen som indexeras (till exempel, alla tabell uppdateringar utförs som en batch enligt ett schema och Azure Kognitiv sökning Indexer-schemat är inställt på att undvika överlappande med tabell uppdaterings schema).  

+ Du gör regelbundet en fullständig omindexning för att hämta eventuella missade rader.