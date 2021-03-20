---
title: 'Självstudie: Database Migration Tool för Azure Cosmos DB'
description: 'Självstudie: Lär dig hur du använder Azure Cosmos DB för data migration med öppen källkod för att importera data till Azure Cosmos DB från olika källor, inklusive MongoDB, SQL Server, Table Storage, Amazon DynamoDB, CSV och JSON-filer. Konvertering av CSV till JSON.'
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: dech
ms.openlocfilehash: 1cee4d2ad1bc7f362a045a5991624ec43521b8d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341656"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Självstudie: Använda datamigreringsverktyget för att migrera data till Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här självstudien innehåller anvisningar för hur du använder datamigreringsverktyget i Azure Cosmos DB, som kan importera data från olika källor till containrar och tabeller i Azure Cosmos DB. Du kan importera från JSON-filer, CSV-filer, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB och till och med Azure Cosmos DB SQL API-samlingar. Du migrerar dessa data till samlingar och tabeller för användning med Azure Cosmos DB. Datamigreringsverktyget kan också användas när du migrerar från en enda partitionssamling till en samling med flera partitioner för SQL API.

> [!NOTE]
> Verktyget Azure Cosmos DB datamigrering är ett verktyg för öppen källkod som har utformats för små migreringar. För större migreringar, se vår [Guide för att](cosmosdb-migrationchoices.md)mata in data.

* **[SQL-API](./introduction.md)** – du kan använda något av de käll alternativ som anges i verktyget datamigrering för att importera data till en liten skala. [Lär dig mer om migrerings alternativ för att importera data i stor skala](cosmosdb-migrationchoices.md).
* **[Tabell-API](table-introduction.md)** – du kan importera data med hjälp av verktyget datamigrering eller [AzCopy](table-import.md#migrate-data-by-using-azcopy) . Se [Importera data för användning med Azure Cosmos DB Table-API](table-import.md) för mer information.
* **[Azure Cosmos DB s API för MongoDB](mongodb-introduction.md)** – verktyget datamigrering stöder inte Azure Cosmos DBS API för MongoDB antingen som en källa eller som mål. Om du vill migrera data i eller ut ur samlingar i Azure Cosmos DB, se [hur du migrerar MongoDB-data till en Cosmos-databas med Azure Cosmos DB s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) för instruktioner. Du kan fortfarande använda datamigreringsverktyget till att exportera data från MongoDB till Azure Cosmos DB SQL API-samlingar för användning med SQL API.
* **[API för Cassandra](graph-introduction.md)** -verktyget datamigrering är inte ett import verktyg som stöds för API för Cassandra-konton. [Lär dig mer om migrerings alternativ för att importera data till API för Cassandra](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api)
* **[Gremlin API](graph-introduction.md)** – Datamigreringsverktyget är ett importverktyg som saknar stöd för Gremlin API-konton för närvarande. [Lär dig mer om migrerings alternativ för att importera data till Gremlin-API](cosmosdb-migrationchoices.md#other-apis) 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Installera datamigreringsverktyget
> * Importera data från olika datakällor
> * Exportera från Azure Cosmos DB till JSON

## <a name="prerequisites"></a><a id="Prerequisites"></a>Förutsättningar

Innan du följer anvisningarna i den här artikeln bör du se till att du utför följande steg:

* **Installera** [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) eller högre.

* **Öka dataflödet**: Hur lång tid datamigreringen tar beror på hur stort dataflöde du anger för en enskild samling eller en uppsättning samlingar. Vi rekommenderar att du ökar dataflödet för större datamigreringar. När du har slutfört migreringen minskar du dataflödet för att spara kostnader. Mer information om hur du ökar dataflödet i Azure-portalen finns i avsnitten om [prestandanivåer](performance-levels.md) och [prisnivåer](https://azure.microsoft.com/pricing/details/cosmos-db/) i Azure Cosmos DB.

* **Skapa Azure Cosmos DB-resurser:** Innan du börjar migrera data skapar du alla dina samlingar i förväg från Azure-portalen. Om du vill migrera till ett Azure Cosmos DB konto som har data flöde på databas nivå, anger du en partitionsnyckel när du skapar Azure Cosmos-behållare.

> [!IMPORTANT]
> För att se till att verktyget datamigrering använder Transport Layer Security (TLS) 1,2 när du ansluter till dina Azure Cosmos-konton, använder du .NET Framework version 4,7 eller följer instruktionerna i [den här artikeln](/dotnet/framework/network-programming/tls).

## <a name="overview"></a><a id="Overviewl"></a>Översikt

Datamigreringsverktyget är en lösning med öppen källkod som importerar data till Azure Cosmos DB från olika källor, inklusive:

* JSON-filer
* MongoDB
* SQL Server
* CSV-filer
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure Cosmos-containrar

Även om importverktyget innehåller ett grafiskt användargränssnitt (dtui.exe), kan det också köras från kommandoraden (dt.exe). Det finns faktiskt ett alternativ till att få utdata från ett associerat kommando när du har konfigurerat en import via användargränssnittet. Du kan transformera tabellkälldata, till exempel SQL Server- eller CSV-filer, för att skapa hierarkiska relationer (underdokument) vid import. Om du vill kan du läsa mer om källalternativ, exempelkommandon för att importera från varje källa, målalternativ och hur du ser de importerade resultaten.

> [!NOTE]
> Du bör endast använda verktyget Azure Cosmos DB migrering för små migreringar. För stora migreringar, se vår [Guide för att](cosmosdb-migrationchoices.md)mata in data.

## <a name="installation"></a><a id="Install"></a>Installation

Källkoden för migreringsverktyget finns i GitHub på [den här lagringsplatsen](https://github.com/azure/azure-documentdb-datamigrationtool). Du kan ladda ned och kompilera lösningen lokalt, eller [ladda ned en förkompilerad binär kod](https://aka.ms/csdmtool) och sedan köra något av följande:

* **Dtui.exe**: Grafisk gränssnittsversion av verktyget
* **DT.exe**: Kommandoradsversion av verktyget

## <a name="select-data-source"></a>Välj datakälla

När du har installerat verktyget är det dags att importera dina data. Vilken typ av data vill du importera?

* [JSON-filer](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB-exportfiler](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-filer](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos-containrar](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB-massimport](#SQLBulkTarget)
* [Azure Cosmos DB:s import av sekventiella poster](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>Importera JSON-filer

Med importverktygets alternativ för JSON-filkällor kan du importera ett eller flera dokument med JSON-filer, eller flera JSON-filer som var och en har en matris med JSON-dokument. När du lägger till mappar som har JSON-filer som ska importeras har du möjlighet att rekursivt söka efter filerna i undermappar.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="Skärmbild av alternativ för JSON-filkällor – Verktyg för databasmigrering":::

Anslutnings strängen har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* `<CosmosDB Endpoint>`Är slut punktens URI. Du kan hämta det här värdet från Azure Portal. Navigera till ditt Azure Cosmos-konto. Öppna **översikts** fönstret och kopiera **URI** -värdet.
* `<AccountKey>`Är "lösen ordet" eller **primär nyckel**. Du kan hämta det här värdet från Azure Portal. Navigera till ditt Azure Cosmos-konto. Öppna fönstret **anslutnings strängar** eller **nycklar** och kopiera värdet "lösen ord" eller **primär nyckel** .
* `<CosmosDB Database>`Är namnet på CosmosDB-databasen.

Exempel: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Använd kommandot verifiera för att se till att det Cosmos DB konto som anges i fältet anslutnings sträng kan nås.

Här följer några kommandoradsexempel för import av JSON-filer:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>Importera från MongoDB

> [!IMPORTANT]
> Om du importerar till ett Cosmos-konto som har konfigurerats med Azure Cosmos DB API för MongoDB, följer du dessa [instruktioner](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

Med importverktygets alternativ för MongoDB-källor kan du importera från en enskild MongoDB-samling, filtrera dokument med hjälp av en fråga (valfritt) och ändra dokumentstrukturen med hjälp av en projektion.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="Skärmbild av MongoDB-källalternativ":::

Anslutningssträngen är i standardformatet för MongoDB:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna MongoDB-instansen är tillgänglig i anslutningssträngens fält.

Ange namnet på den samling som data ska importeras från. Du kan om du vill ange en fil för en fråga, till exempel `{pop: {$gt:5000}}`, eller en projektion såsom `{loc:0}`, för att både filtrera och utforma de data som du importerar.

Här följer några kommandoradsexempel för import från MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>Importera MongoDB-exportfiler

> [!IMPORTANT]
> Om du importerar till ett Azure Cosmos DB-konto med stöd för MongoDB, följer du dessa [anvisningar](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

Med importverktygets alternativ för MongoDB-export av JSON-filkällor, kan du importera en eller flera JSON-filer som skapats med verktyget mongoexport.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="Skärmbild av MongoDB-exportkällans alternativ":::

När du lägger till mappar som har MongoDB-exportens JSON-filer som ska importeras har du möjlighet att rekursivt söka efter filerna i undermappar.

Här följer ett kommandoradsexempel när import görs från MongoDB-exportens JSON-filer:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>Importera från SQLServer

Med importverktygets alternativ för SQL-källor kan du importera från en enskild SQL Server-databas och du kan också filtrera de poster som ska importeras med hjälp av en fråga. Du kan dessutom ändra dokumentets struktur genom att ange en kapslad avgränsare (mer information om detta kommer längre fram).  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="Skärmbild av SQL-källans alternativ – Verktyg för databasmigrering":::

Formatet för anslutningssträngen är standardformatet för SQL-anslutningssträngar.

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna SQL Server-instansen är tillgänglig i anslutningssträngens fält.

Egenskapen för kapslade avgränsare används för att skapa hierarkiska relationer (underdokument) under importen. Fundera över följande SQL-fråga:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Som returnerar följande (partiella) värden:

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="Skärmbild av SQL-frågeresultat":::

Observera alias som t.ex. Address.AddressType och Address.Location.StateProvinceName. När du anger den kapslade avgränsaren '.', skapar importverktyget underdokumenten Address och Address.Location under importen. Här är ett exempel på ett resulterande dokument i Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Här följer några kommandoradsexempel för att importera från SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>Importera CSV-filer och konvertera CSV till JSON

Med importverktygets alternativ för CSV-filkällor kan du importera en eller flera CSV-filer. När du lägger till mappar som har CSV-filer som ska importeras har du möjlighet att rekursivt söka efter filerna i undermappar.

:::image type="content" source="media/import-data/csvsource.png" alt-text="Skärmbild av CSV-källans alternativ – CSV till JSON":::

På samma sätt som med SQL-källan kan egenskapen för kapslade avgränsare användas till att skapa hierarkiska relationer (underdokument) under importen. Fundera över följande CSV-rubrikrad och datarader:

:::image type="content" source="./media/import-data/csvsample.png" alt-text="Skärmbild av CSV-exemplets poster – CSV till JSON":::

Observera alias som t.ex. DomainInfo.Domain_Name och RedirectInfo.Redirecting. När du anger den kapslade avgränsaren '.', skapar importverktyget underdokumenten DomainInfo och RedirectInfo under importen. Här är ett exempel på ett resulterande dokument i Azure Cosmos DB:

*{"DomainInfo": {"Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https: \/ /www.ACUS.gov"}, "Federal Agency": "administrations konferensen för USA", "RedirectInfo": {"omdirigera": "0", "Redirect_Destination": ""}, "ID": "9cc565c5-EBCD-1c03-ebd3-cc3e2ecd814d"}*

Importverktyget försöker härleda typinformationen för värden utan citattecken i CSV-filer (värden inom citattecken behandlas alltid som strängar).  Typer identifieras i följande ordning: nummer, datetime, booleskt värde.  

Det finns två saker att lägga märke till när det gäller CSV-import:

1. Som standard tas värden utan citattecken alltid bort för flikar och blanksteg, medan värden inom citattecken sparas som de är. Den här funktionen kan åsidosättas med kryssrutan Trim quoted values (Rensa värden inom citattecken) eller kommandoradsalternativet /s.TrimQuoted.
2. Som standard behandlas null utan citattecken som ett null-värde. Den här funktionen kan åsidosättas (dvs. behandla null utan citattecken som en null-sträng) med kryssrutan Treat unquoted NULL as string (Behandla NULL utan citattecken som en sträng) eller kommandoradsalternativet /s.NoUnquotedNulls.

Här följer ett kommandoradsexempel för CSV-import:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importera från Azure Table Storage

Med importverktygets alternativ för Azure Table Storage-källor kan du importera från en enskild Azure Table Storage-tabell. Du kan också du filtrera tabellentiteterna som ska importeras.

Du kan mata ut data som importerades från Azure Table Storage till Azure Cosmos DB-tabeller och -entiteter för användning med tabell-API. Importerade data kan även vara utdata till samlingar och dokument för användning med SQL API. Dock är tabell-API endast tillgängligt som mål i kommandoradsverktyget. Du kan inte exportera till tabell-API med hjälp av användargränssnittet för datamigreringsverktyget. Se [Importera data för användning med Azure Cosmos DB Table-API](table-import.md) för mer information.

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Skärmbild av Azure Table Storage-källans alternativ":::

Formatet för anslutningssträngen till Azure Table Storage är:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Table Storage-instansen är tillgänglig i anslutningssträngens fält.

Ange namnet på Azure-tabellen som du ska importera från. Du kan också ange ett [filter](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings).

Importverktygets alternativ för Azure Table Storage-källor innehåller dessutom följande extra alternativ:

1. Inkludera interna fält
   1. Alla – Inkludera alla interna fält (PartitionKey, RowKey och Timestamp)
   2. Ingen – Undanta alla interna fält
   3. RowKey – Inkludera bara fältet RowKey
2. Välja kolumner
   1. Azure Table Storage-filter stöder inte projektioner. Om du endast vill importera specifika entitetsegenskaper från Azure-tabellen, lägger du till dem i listan Välj kolumner. Alla andra entitetsegenskaper ignoreras.

Här är ett kommandoradsexempel på hur du importerar från Azure Table Storage:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importera från Amazon DynamoDB

Med alternativet för Amazon DynamoDB-källimport kan du importera från en enskild Amazon DynamoDB-tabell. Det kan även filtrera de entiteter som ska importeras om du väljer det. Det finns flera olika mallar att välja bland, för att konfigurationen av importen ska vara så enkel som möjligt.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Skärm bild av Amazon DynamoDB-käll alternativ – Migreringsverktyg för databas.":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="Skärm bild av Amazon DynamoDB-källdata med Migreringsverktyg för databas.":::

Formatet på Amazon DynamoDB-anslutningssträngen är:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Amazon DynamoDB-instansen är tillgänglig i anslutningssträngens fält.

Här är ett kommandoradsexempel på hur du importerar från Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Importera från Azure Blob Storage

Med importverktygets alternativ för källor från JSON-filen, MongoDB-exportfilen och CSV-filen, kan du importera en eller flera filer från Azure Blob Storage. När du har angett en URL för blobcontainern och en kontonyckel, använder du ett reguljärt uttryck till att välja vilka filer du vill importera.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Skärmbild över alternativ för blobfilens källa":::

Här är ett kommandoradsexempel på hur du kan importera JSON-filer från Azure Blob Storage:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>Importera från en SQL API-samling

Med alternativet Azure Cosmos DB source importer kan du importera data från en eller flera Azure Cosmos-behållare och eventuellt filtrera dokument med hjälp av en fråga.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Skärmbild över alternativ för Azure Cosmos DB-källor":::

Anslutningssträngen för Azure Cosmos DB har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Du kan hämta Azure Cosmos DB-kontoanslutningssträngen från sidan Nycklar i Azure-portalen, enligt beskrivningen i [Så här hanterar du ett Azure Cosmos DB-konto](./how-to-manage-database-account.md). Dock måste namnet på databasen läggas till i anslutningssträngen i följande format:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Cosmos DB-instansen är tillgänglig i anslutningssträngens fält.

Om du vill importera från en enda Azure Cosmos-behållare anger du namnet på den samling som du vill importera data från. Om du vill importera från fler än en Azure Cosmos-behållare anger du ett reguljärt uttryck som matchar ett eller flera samlings namn (till exempel collection01 | collection02 | collection03). Du kan om du vill ange en fil för en fråga för att både filtrera och utforma de data som du importerar.

> [!NOTE]
> Eftersom samlingsfältet accepterar reguljära uttryck kommer dessa tecken att hoppas över, om du importerar från en enda samling vars namn innehåller tecken i reguljära uttryck.

Importverktygets alternativ för Azure Cosmos DB-källor innehåller följande avancerade alternativ:

1. Inkludera interna fält: Anger om du vill inkludera Azure Cosmos DB-dokumentegenskaper för system i exporten (till exempel _rid, _ts).
2. Antal återförsök vid fel: Anger hur många gånger systemet försöker återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
3. Återförsöksintervall: Anger hur lång väntetiden är vid försök att återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
4. Anslutningsläge: Anger det anslutningsläge som ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutningslägena är snabbare, medan gatewayläget är mer brandväggsanpassat eftersom det endast använder port 443.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Skärmbild över avancerade alternativ för Azure Cosmos DB-källor":::

> [!TIP]
> Importverktyget använder som standard anslutningsläget DirectTcp. Om det uppstår brandväggsproblem kan du växla till anslutningsläget Gateway, eftersom det endast kräver port 443.

Här följer några kommandoradsexempel på hur du importerar från Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> Dataimportverktyget i Azure Cosmos DB har även stöd för import av data från [Azure Cosmos DB-emulatorn](local-emulator.md). När du importerar data från en lokal emulator anger du slutpunkten till `https://localhost:<port>`.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>Importera från HBase

Med importverktygets alternativ för HBase-källor kan du importera data från en HBase-tabell och du kan också filtrera datan. Det finns flera olika mallar att välja bland, för att konfigurationen av importen ska vara så enkel som möjligt.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="Skärm bild av alternativ för HBase-källor.":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="Skärm bild av alternativen för HBase-källa med snabb menyn för filter expanderad.":::

Anslutningssträngen för HBase Stargate har följande format:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna HBase-instansen är tillgänglig i anslutningssträngens fält.

Här är ett kommandoradsexempel på hur du importerar från HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>Importera till SQL API (massimport)

Med massimportverktyget för Azure Cosmos DB kan du importera från något av källalternativen, med hjälp av en Azure Cosmos DB-lagrad procedur som effektiviserar processen. Verktyget stöder import till en enda partitionerad Azure Cosmos-behållare. Det stöder också shardade-import där data partitioneras i mer än en partitionerad Azure Cosmos-behållare. Mer information om partitionering av data finns i [Partitionering och skalning i Azure Cosmos DB](partitioning-overview.md). Verktyget skapar, kör och tar sedan bort den lagrade proceduren från målsamlingarna.  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Skärmbild över alternativ för Azure Cosmos DB-massimport":::

Anslutningssträngen för Azure Cosmos DB har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Anslutningssträngen för Azure Cosmos DB-kontot kan hämtas från sidan Nycklar i Azure Portal, enligt beskrivningen i [Så här hanterar du ett konto i Azure Cosmos DB](./how-to-manage-database-account.md), men namnet på databasen måste läggas till i anslutningssträngen i följande format:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Cosmos DB-instansen är tillgänglig i anslutningssträngens fält.

Om du ska importera till en enskild samling anger du namnet på samlingen som du ska importera data från. Klicka sedan på knappen Lägg till. Om du vill importera till fler än en samling anger du antingen varje samlingsnamn individuellt, eller så använder du följande syntax för att ange fler än en samling: *collection_prefix*[start index - end index]. Tänk på följande riktlinjer när du anger fler än en samling med hjälp av ovannämnda syntax:

1. Det är bara namnmönster i heltalsintervall som stöds. Om du till exempel anger collection[0-3] skapas följande samlingar: collection0, collection1, collection2 och collection3.
2. Du kan använda en förkortad syntax: collection[3] skapar samma uppsättning samlingar som nämns i steg 1.
3. Mer än en ersättning kan anges. Till exempel genererar collection[0-1] [0-9] 20 samlingsnamn med inledande nollor (collection01, ..02, ..03).

När samlingsnamnen har angetts väljer du önskad dataflöde för samlingarna (från 400 till 10 000 RU:er). Välj ett högre dataflöde för att få bästa prestandan för importen. Mer information om prestandanivåer finns i avsnittet [Prestandanivåer i Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Inställningen av prestandans dataflöde gäller bara när samlingar skapas. Om den angivna samlingen redan finns ändras inte dess dataflöde.

När du importerar till fler än en samling stöder importverktyget hash-baserad horisontell partitionering. I det här scenariot anger du den dokumentegenskap som du vill använda som partitionsnyckel. (Om partitionsnyckeln lämnas tom partitioneras dokument horisontellt över målsamlingarna.)

Du kan om du vill ange vilket fält i importkällan som ska användas som Azure ID-egenskap för Cosmos DB-dokument under importen. Om dokument inte har den här egenskapen genererar importverktyget ett GUID som ID-egenskapsvärde.

Det finns ett antal avancerade alternativ under importen. Även om verktyget innehåller en standardlagrad procedur för massimport (BulkInsert.js), kan du ange en egen lagrad importprocedur:

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Skärmbild över sproc-alternativ för Azure Cosmos DB-massinfogning":::

När du importerar datumtyper (till exempel från SQL Server eller MongoDB) kan du dessutom välja mellan tre importalternativ:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Skärmbild över importalternativ för datum och tid i Azure Cosmos DB":::

* Sträng: Spara som ett strängvärde
* Epok: Spara som ett epoknummervärde
* Båda: Spara både strängvärden och epoknummervärden. Det här alternativet skapar ett underdokument, till exempel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Massimportverktyget för Azure Cosmos DB innehåller följande avancerade extra alternativ:

1. Batchstorlek: Verktyget har som standard en batchstorlek på 50.  Om de dokument som ska importeras är stora kan du minska batchstorleken. Om de dokument som ska importeras är små kan du öka batchstorleken.
2. Maxstorlek för skript (byte): Verktyget har som standard en maxstorlek för skript på 512 kB.
3. Inaktivera automatisk ID-generering: Om alla dokument som ska importeras har ett ID-fält kan det här alternativet öka prestandan. Dokument som saknar ett fält för unikt ID importeras inte.
4. Uppdatera befintliga dokument: Verktyget ersätter som standard inte befintliga dokument med ID-konflikter. Med det här alternativet kan du skriva över befintliga dokument med matchande ID:n. Funktionen är användbar vid schemalagda datamigreringar som uppdaterar befintliga dokument.
5. Antal återförsök vid fel: Anger hur ofta systemet försöker återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
6. Återförsöksintervall: Anger hur lång väntetiden är vid försök att återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
7. Anslutningsläge: Anger det anslutningsläge som ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutningslägena är snabbare, medan gatewayläget är mer brandväggsanpassat eftersom det endast använder port 443.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Skärmbild över avancerade alternativ för Azure Cosmos DB-massimport":::

> [!TIP]
> Importverktyget använder som standard anslutningsläget DirectTcp. Om det uppstår brandväggsproblem kan du växla till anslutningsläget Gateway, eftersom det endast kräver port 443.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>Importera till SQL API (import av sekventiella poster)

Med importverktyget för sekventiella poster i Azure Cosmos DB kan du importera från ett tillgängligt källalternativ post för post. Du kan välja det här alternativet om du importerar till en befintlig samling som har uppnått sin kvot av lagrade procedurer. Verktyget stöder import till en enskild Azure Cosmos-behållare (både en partition och flera partitioner). Det stöder också shardade-import där data partitioneras i mer än en Azure Cosmos-behållare med en partition eller flera partitioner. Mer information om partitionering av data finns i [Partitionering och skalning i Azure Cosmos DB](partitioning-overview.md).

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Skärmbild över importalternativ för sekventiella poster i Azure Cosmos DB":::

Anslutningssträngen för Azure Cosmos DB har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Du kan hämta anslutningssträngen för Azure Cosmos DB-konto från sidan Nycklar i Azure-portalen, enligt beskrivningen i [Så här hanterar du ett Azure Cosmos DB-konto](./how-to-manage-database-account.md). Dock måste namnet på databasen läggas till i anslutningssträngen i följande format:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Cosmos DB-instansen är tillgänglig i anslutningssträngens fält.

Om du ska importera till en enskild samling anger du namnet på den samling som data ska importeras till och klickar sedan på knappen Lägg till. Ange varje samlingsnamn separat om du vill importera till mer än en samling. Du kan även använda följande syntax för att ange mer än en samling: *collection_prefix*[start index - end index]. Tänk på följande riktlinjer när du anger fler än en samling via ovannämnda syntax:

1. Det är bara namnmönster i heltalsintervall som stöds. Om du till exempel anger collection[0-3] skapas följande samlingar: collection0, collection1, collection2 och collection3.
2. Du kan använda en förkortad syntax: collection[3] skapar samma uppsättning samlingar som nämns i steg 1.
3. Mer än en ersättning kan anges. Till exempel skapar samling [0-1] [0-9] 20 samlingsnamn med inledande nollor (collection01, ..02, ..03).

När samlingsnamnen har angetts väljer du önskat dataflöde för samlingarna (från 400 till 250 000 RU:er). Välj ett högre dataflöde för att få bästa prestandan för importen. Mer information om prestandanivåer finns i avsnittet [Prestandanivåer i Azure Cosmos DB](performance-levels.md). Om du importerar till samlingar med dataflöde > 10 000 RU:er måste du ha en partitionsnyckel. Om du vill ha fler än 250 000 RU:er, måste du skicka en begäran i portalen om att öka ditt konto.

> [!NOTE]
> Inställningen av dataflödet gäller bara när samlingar eller databaser skapas. Om den angivna samlingen redan finns ändras inte dess dataflöde.

Vid import till fler än en samling stöder importverktyget hash-baserad horisontell partitionering. I det här scenariot anger du den dokumentegenskap som du vill använda som partitionsnyckel. (Om partitionsnyckeln lämnas tom partitioneras dokument horisontellt över målsamlingarna.)

Du kan om du vill ange vilket fält i importkällan som ska användas som Azure ID-egenskap för Cosmos DB-dokument under importen. (Om dokument inte har den här egenskapen genererar importverktyget ett GUID som ID-egenskapsvärde.)

Det finns ett antal avancerade alternativ under importen. När du importerar datumtyper (till exempel från SQL Server eller MongoDB) kan du välja mellan tre importalternativ:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Skärmbild över importalternativ för datum och tid i Azure Cosmos DB":::

* Sträng: Spara som ett strängvärde
* Epok: Spara som ett epoknummervärde
* Båda: Spara både strängvärden och epoknummervärden. Det här alternativet skapar ett underdokument, till exempel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB – Importverktyget för sekventiella poster innehåller följande avancerade alternativ:

1. Antal parallella begäranden: Verktyget kan som standard hantera två parallella begäranden. Om de dokument som ska importeras är små, kan du öka antalet parallella begäranden. Om antalet ökas för mycket kan en nätverksbegränsning uppstå vid importen.
2. Inaktivera automatisk ID-generering: Om alla dokument som ska importeras har ett ID-fält kan det här alternativet öka prestandan. Dokument som saknar ett fält för unikt ID importeras inte.
3. Uppdatera befintliga dokument: Verktyget ersätter som standard inte befintliga dokument med ID-konflikter. Med det här alternativet kan du skriva över befintliga dokument med matchande ID:n. Funktionen är användbar vid schemalagda datamigreringar som uppdaterar befintliga dokument.
4. Antal återförsök vid fel: Anger hur ofta systemet försöker återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
5. Återförsöksintervall: Anger hur lång väntetiden är vid försök att återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
6. Anslutningsläge: Anger det anslutningsläge som ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutningslägena är snabbare, medan gatewayläget är mer brandväggsanpassat eftersom det endast använder port 443.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Skärmbild över avancerade alternativ för import av sekventiella poster i Azure Cosmos DB":::

> [!TIP]
> Importverktyget använder som standard anslutningsläget DirectTcp. Om det uppstår brandväggsproblem kan du växla till anslutningsläget Gateway, eftersom det endast kräver port 443.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>Ange en indexeringsprincip

Om du tillåter att migreringsverktyget skapar Azure Cosmos DB SQL API-samlingar under importen, kan du ange indexeringsprincipen för samlingarna. Gå till Indexeringsprincip i avsnittet med avancerade alternativ i alternativen för Azure Cosmos DB-massimport och sekventiella poster i Azure Cosmos DB.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Skärm bild av Azure Cosmos DB avancerade alternativ för indexerings princip.":::

I de avancerade alternativen för indexeringsprinciper kan du välja en indexeringsprincipfil, manuellt ange en indexeringsprincip, eller välja bland en uppsättning standardmallar (genom att högerklicka i textrutan för indexeringsprinciper).

Principmallarna i verktyget är:

* Standard. Den här principen är bäst när du utför likhetsfrågor mot strängar. Den fungerar även om du använder ORDER BY, intervall och likhetsfrågor för tal. Den här principen har lägre omkostnad för indexlagring än Intervall.
* Intervall. Den här principen passar bäst när du använder ORDER BY, intervall och likhetsfrågor för både tal och strängar. Principen har högre omkostnad för indexlagring än Standard och Hash.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="Skärm bild av Azure Cosmos DB avancerade alternativ för indexerings princip som anger mål information.":::

> [!NOTE]
> Om du inte anger någon indexeringsprincip används standardprincipen. Mer information om indexeringsprinciper finns i [Azure Cosmos DB-indexeringsprinciper](index-policy.md).

## <a name="export-to-json-file"></a>Exportera till JSON-fil

Med exportverktyget Azure Cosmos DB JSON kan du exportera alla tillgängliga källalternativ till en JSON-fil som har en matris av JSON-dokument. Verktyget hanterar exporten åt dig. Alternativt kan du visa resulterande migreringskommando och köra kommandot själv. Den resulterande JSON-filen kan vara lagrad lokalt eller i Azure Blob Storage.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Skärmbild över exportalternativ för lokal fil i Azure Cosmos DB JSON":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Skärmbild över exportalternativ för Azure Cosmos DB Azure Blob Storage":::

Du kan välja att förenkla resulterande JSON. Den här åtgärden ökar storleken på det resulterande dokumentet samtidigt som innehållet blir mer läsbart.

* Standard JSON-export

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Förenklad JSON-export

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Här är ett kommandoradsexempel på hur du kan exportera JSON-filen till Azure Blob Storage:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Avancerad konfiguration

På skärmen Avancerad konfiguration anger du platsen för loggfilen där du vill att eventuella fel skrivs. Följande regler gäller för den här sidan:

1. Om ett filnamn inte anges returneras alla fel till sidan Resultat.
2. Om ett filnamn anges utan en katalog, skapas filen (eller skrivs över) i den aktuella katalogen i miljön.
3. Om du väljer en befintlig fil skrivs den över. Det finns inte något alternativ för att lägga till filen.
4. Välj sedan om du vill logga alla, kritiska eller inga felmeddelanden. Bestäm slutligen hur ofta den på överföringsmeddelandet på skärmen ska uppdateras med förloppet.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="Skärmbild av skärmen för avancerad konfiguration":::

## <a name="confirm-import-settings-and-view-command-line"></a>Bekräfta importinställningarna och visa kommandoraden

1. När du har angett källinformation, målinformation och avancerad konfiguration granskar du migreringsöversikten och visar eller kopierar det resulterande migreringskommandot om du vill. (Att kopiera kommandot är användbart för att automatisera importåtgärder.)

    :::image type="content" source="./media/import-data/summary.png" alt-text="Skärm bild av sammanfattnings skärmen.":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="Skärm bild av sammanfattnings skärm med kommando rads förhands granskning.":::

2. När du är nöjd med dina käll- och målalternativ klickar du på **Importera**. Förfluten tid, antal överförda och felinformation (om du inte angav ett filnamn i Avancerad konfiguration) uppdateras medan importen pågår. När installationen är klar kan du exportera resultaten (till exempel för att åtgärda eventuella importfel).

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Skärm bild av alternativet Azure Cosmos DB JSON-export.":::

3. Du kan även starta en ny import genom att antingen återställa alla värden eller behålla de befintliga inställningarna. (Till exempel kan du behålla information om anslutningssträng, val av källa och mål och mer.)

    :::image type="content" source="./media/import-data/newimport.png" alt-text="Skärm bild av alternativet Azure Cosmos DB JSON-export med dialog rutan ny import bekräftelse.":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Installerat datamigreringsverktyget
> * Importerat data från olika datakällor
> * Exporterat från Azure Cosmos DB till JSON

Nu kan du gå vidare till nästa självstudie och lära dig att fråga efter data med hjälp av Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Hur frågar jag efter data?](../cosmos-db/tutorial-query-sql-api.md)
