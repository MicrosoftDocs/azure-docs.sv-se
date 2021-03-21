---
title: Kopiera data till/från Azure Synapse Analytics
description: Lär dig hur du kopierar data till och från Azure Synapse Analytics med hjälp av Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aa364ec434db980bf226008537ca928628fcac1b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392093"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-using-azure-data-factory"></a>Kopiera data till och från Azure Synapse Analytics med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory kan du läsa mer i [Azure Synapse Analytics Connector i v2](../connector-azure-sql-data-warehouse.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data till/från Azure Synapse Analytics. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

> [!TIP]
> Använd PolyBase för att läsa in data i Azure Synapse Analytics för att uppnå bästa prestanda. Mer information finns i avsnittet [använda PolyBase för att läsa in data i avsnittet om Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) . För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Synapse Analytics** till följande data lager:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande data lager **till Azure Synapse Analytics**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure Synapse Analytics, om tabellen inte finns i mål lagret, kan Data Factory automatiskt skapa tabellen i Azure Synapse Analytics med hjälp av tabellens schema i käll data lagret. Mer information finns i [Skapa tabell med automatisk tabell](#auto-table-creation) .

## <a name="supported-authentication-type"></a>Autentiseringstyp som stöds
Azure Synapse Analytics Connector stöder grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från en Azure Synapse-analys med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline som kopierar data till/från Azure Synapse Analytics är att använda guiden Kopiera data. Se [självstudie: Läs in data i Azure Synapse Analytics med Data Factory](../load-azure-sql-data-warehouse.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera pipeliner. 
2. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik. Om du till exempel kopierar data från en Azure Blob Storage till en Azure Synapse-analys, skapar du två länkade tjänster för att länka ditt Azure Storage-konto och Azure Synapse-analys till din data fabrik. För länkade tjänst egenskaper som är speciella för Azure Synapse Analytics, se avsnittet [länkade tjänst egenskaper](#linked-service-properties) . 
3. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. I exemplet som nämns i det sista steget skapar du en data uppsättning för att ange BLOB-behållaren och mappen som innehåller indata. Du kan också skapa en annan data uppsättning för att ange tabellen i Azure Synapse Analytics som innehåller data som kopieras från blob-lagringen. För data uppsättnings egenskaper som är speciella för Azure Synapse Analytics, se avsnittet [Egenskaper för data mängd](#dataset-properties) .
4. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. I exemplet ovan använder du BlobSource som källa och SqlDWSink som mottagare för kopierings aktiviteten. På samma sätt använder du SqlDWSource och BlobSink i kopierings aktiviteten om du kopierar från Azure Synapse Analytics till Azure Blob Storage. Information om kopiera aktivitets egenskaper som är speciella för Azure Synapse Analytics finns i avsnittet [Kopiera aktivitets egenskaper](#copy-activity-properties) . Om du vill ha mer information om hur du använder ett data lager som källa eller mottagare klickar du på länken i föregående avsnitt för ditt data lager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till och från Azure Synapse Analytics finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Synapse Analytics:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för den länkade Azure Synapse Analytics-tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **AzureSqlDW** |Ja |
| Begär |Ange information som krävs för att ansluta till Azure Synapse Analytics-instansen för egenskapen connectionString. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database-brandväggen](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) och databas servern så att [Azure-tjänster får åtkomst till servern](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Om du kopierar data till Azure Synapse Analytics från utanför Azure, inklusive från lokala data källor med Data Factory Gateway, konfigurerar du också lämpligt IP-adressintervall för datorn som skickar data till Azure Synapse Analytics.

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet typeProperties är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för data uppsättningen av typen **AzureSqlDWTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i Azure Synapse Analytics-databasen som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopierings aktiviteten tar bara en indata och producerar bara ett resultat.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

### <a name="sqldwsource"></a>SqlDWSource
När källan är av typen **SqlDWSource** finns följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: Välj * från tabellen tabell. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från käll tabellen. |Namnet på den lagrade proceduren. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |

Om **sqlReaderQuery** har angetts för SqlDWSource kör kopierings aktiviteten den här frågan mot Azure Synapse Analytics-källan för att hämta data.

Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger någon av sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definierats i avsnittet struktur i JSON-datauppsättnings-JSON för att skapa en fråga som ska köras mot Azure Synapse Analytics. Exempel: `select column1, column2 from mytable`. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.

#### <a name="sqldwsource-example"></a>SqlDWSource-exempel

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Definitionen för den lagrade proceduren:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för kopierings aktivitet som ska köras så att data i en angiven sektor rensas. Mer information finns i [avsnittet repeterbarhet](#repeatability-during-copy). |Ett frågeuttryck. |Inga |
| allowPolyBase |Anger om PolyBase ska användas (när det är tillämpligt) i stället för BULKINSERT-mekanismen. <br/><br/> **Att använda PolyBase är det rekommenderade sättet att läsa in data i Azure Synapse Analytics.** Se [använda PolyBase för att läsa in data i avsnittet om Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) för begränsningar och information. |Sant <br/>False (standard) |Inga |
| polyBaseSettings |En grupp egenskaper som kan anges när **allowPolybase** -egenskapen har angetts till **True**. |&nbsp; |Inga |
| rejectValue |Anger antalet rader eller procent av rader som kan avvisas innan frågan Miss lyckas. <br/><br/>Läs mer om polybases avvisnings alternativ i avsnittet **arguments** i avsnittet [skapa en extern tabell (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (standard), 1, 2,... |Inga |
| rejectType |Anger om alternativet rejectValue anges som ett litteralt värde eller i procent. |Värde (standard), procent |Inga |
| rejectSampleValue |Anger det antal rader som ska hämtas innan PolyBase beräknar om procent andelen avvisade rader. |1, 2,... |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen.<br/><br/>Lär dig mer om den här egenskapen från avsnittet argument i [Skapa externt fil format (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |Sant, falskt (standard) |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

#### <a name="sqldwsink-example"></a>SqlDWSink-exempel

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Använd PolyBase för att läsa in data i Azure Synapse Analytics
Användningen av **[PolyBase](/sql/relational-databases/polybase/polybase-guide)** är ett effektivt sätt att läsa in stora mängder data i Azure Synapse Analytics med högt data flöde. Du kan se en stor ökning i data flödet genom att använda PolyBase i stället för standard mekanismen för BULKINSERT. Se [referens nummer för kopierings prestanda](data-factory-copy-activity-performance.md#performance-reference) med detaljerad jämförelse. För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Om dina källdata finns i **Azure Blob eller Azure Data Lake Store** och formatet är kompatibelt med PolyBase kan du kopiera direkt till Azure Synapse Analytics med PolyBase. Mer information finns i **[direkt kopiering med PolyBase](#direct-copy-using-polybase)** .
* Om käll data lagret och formatet inte ursprungligen stöds av PolyBase kan du använda funktionen **[mellanlagrad kopia med PolyBase](#staged-copy-using-polybase)** i stället. Det ger dig också bättre data flöde genom att automatiskt konvertera data till PolyBase-kompatibelt format och lagra data i Azure Blob Storage. Den läser sedan in data i Azure Synapse Analytics.

Ställ in `allowPolyBase` egenskapen på **True** enligt följande exempel för Azure Data Factory att använda PolyBase för att kopiera data till Azure Synapse Analytics. När du anger allowPolyBase till true kan du ange PolyBase-egenskaper med hjälp av `polyBaseSettings` egenskaps gruppen. i avsnittet [SqlDWSink](#sqldwsink) finns mer information om egenskaper som du kan använda med polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Direkt kopiering med PolyBase
Azure Synapse Analytics PolyBase stöder direkt Azure blob och Azure Data Lake Store (med tjänstens huvud namn) som källa och med särskilda fil formats krav. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet kan du kopiera från käll data lagret direkt till Azure Synapse Analytics med PolyBase. Annars kan du använda [mellanlagrad kopia med PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Om du vill kopiera data från Data Lake Store till Azure Synapse Analytics effektivt kan du läsa mer från [Azure Data Factory gör det ännu enklare och bekvämt att få insikter från data när du använder data Lake Store med Azure Synapse Analytics](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Om kraven inte uppfylls kontrollerar Azure Data Factory inställningarna och återgår automatiskt till BULKINSERT-mekanismen för data förflyttning.

1. **Länkad källa** är av typen: **AzureStorage** eller **AzureDataLakeStore med autentisering av tjänstens huvud namn**.
2. **Data uppsättningen för indata** är av typen: **AzureBlob** eller **AzureDataLakeStore**, och format typen under `type` egenskaper är **OrcFormat**, **ParquetFormat** eller **textformat** med följande konfigurationer:

   1. `rowDelimiter` måste vara **\n**.
   2. `nullValue` är inställt på **tom sträng** ("") eller `treatEmptyAsNull` har angetts till **Sant**.
   3. `encodingName` anges till **UTF-8**, **vilket är standardvärdet** .
   4. `escapeChar`, `quoteChar` ,, `firstRowAsHeader` och `skipLineCount` har inte angetts.
   5. `compression` kan vara **Ingen komprimering**, **gzip** eller **DEFLATE**.

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. Det finns ingen `skipHeaderLineCount` inställning under **BlobSource** eller **AzureDataLakeStore** för kopierings aktiviteten i pipelinen.
4. Det finns ingen `sliceIdentifierColumnName` inställning under **SqlDWSink** för kopierings aktiviteten i pipelinen. (PolyBase garanterar att alla data uppdateras eller ingenting uppdateras i en enda körning. För att uppnå **repeterbarhet** kan du använda `sqlWriterCleanupScript` ).
5. Det finns inget som `columnMapping` används i den associerade kopierings aktiviteten.

### <a name="staged-copy-using-polybase"></a>Mellanlagrad kopia med PolyBase
När dina källdata inte uppfyller de kriterier som introducerades i föregående avsnitt, kan du Aktivera kopiering av data via en tillfällig mellanlagring av Azure-Blob Storage (kan inte Premium Storage). I det här fallet, Azure Data Factory automatiskt utföra omvandlingar av data för att uppfylla kraven på data format för PolyBase, sedan använda PolyBase för att läsa in data i Azure Synapse Analytics och sist rensa dina temporära data från Blob Storage. Se [mellanlagrad kopia](data-factory-copy-activity-performance.md#staged-copy) för information om hur kopiering av data via en Azure-blob i mellanlagring fungerar i allmänhet.

> [!NOTE]
> När du kopierar data från ett lokalt data lager till Azure Synapse Analytics med PolyBase och mellanlagring, om din Data Management Gateway-version är under 2,4, krävs JRE (Java Runtime Environment) på din Gateway-dator som används för att transformera källdata till rätt format. Föreslå att du uppgraderar din gateway till senaste för att undvika ett sådant beroende.
>

Om du vill använda den här funktionen skapar du en [Azure Storage länkad tjänst](data-factory-azure-blob-connector.md#azure-storage-linked-service) som refererar till det Azure Storage konto som har Interim blob-lagringen och sedan anger du `enableStaging` och `stagingSettings` egenskaperna för kopierings aktiviteten som visas i följande kod:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Metod tips när du använder PolyBase
Följande avsnitt innehåller ytterligare metod tips för de som nämns i [metod tips för Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Nödvändig databas behörighet
Om du vill använda PolyBase kräver det att den användare som används för att läsa in data i Azure Synapse Analytics har [behörigheten "kontroll"](/sql/relational-databases/security/permissions-database-engine) för mål databasen. Ett sätt att uppnå detta är att lägga till den användaren som medlem i rollen "db_owner". Lär dig hur du gör det genom att följa [det här avsnittet](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Begränsning av rad storlek och data typ
PolyBase-inläsningar är begränsade till inläsning av rader som är mindre än **1 MB** och inte kan läsas in på VARCHR (max), nvarchar (max) eller varbinary (max). Läs [här](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Om du har källdata med en storlek som är större än 1 MB, kanske du vill dela upp käll tabellerna lodrätt i flera små där den största rad storleken för var och en av dem inte överskrider gränsen. De mindre tabellerna kan sedan läsas in med PolyBase och sammanfogas tillsammans i Azure Synapse Analytics.

### <a name="azure-synapse-analytics-resource-class"></a>Resurs klass för Azure Synapse Analytics
För att uppnå bästa möjliga data flöde bör du överväga att tilldela större resurs klass till den användare som används för att läsa in data i Azure Synapse Analytics via PolyBase. Lär dig hur du gör detta genom att följa [exemplet på Ändra en användar resurs klass](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md).

### <a name="tablename-in-azure-synapse-analytics"></a>tableName i Azure Synapse Analytics
Följande tabell innehåller exempel på hur du anger egenskapen **TableName** i datauppsättnings-JSON för olika kombinationer av schema-och tabell namn.

| DB-schema | Tabellnamn | tableName JSON-egenskap |
| --- | --- | --- |
| dbo |MyTable |Min tabell eller dbo. Min tabell eller [dbo]. MyTable |
| dbo1 |MyTable |dbo1. Min tabell eller [dbo1]. MyTable |
| dbo |My. table |[My. table] eller [dbo]. [My. table] |
| dbo1 |My. table |[dbo1]. [My. table] |

Om du ser följande fel kan det bero på ett problem med värdet som du angav för egenskapen tableName. Se tabellen för korrekt sätt att ange värden för JSON-egenskapen tableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden
PolyBase-funktionen i Data Factory accepterar för närvarande bara samma antal kolumner som i mål tabellen. Anta att du har en tabell med fyra kolumner och en av dem definieras med ett standardvärde. Indata ska fortfarande innehålla fyra kolumner. Att tillhandahålla en data uppsättning i tre kolumner skulle ge ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL-värde är en särskild form av standardvärde. Om kolumnen kan ha värdet null kan indata (i BLOB) för den kolumnen vara tomma (kan inte saknas i indata-datauppsättningen). PolyBase infogar NULL för dem i Azure Synapse Analytics.

## <a name="auto-table-creation"></a>Skapa tabell automatiskt
Om du använder kopierings guiden för att kopiera data från SQL Server eller Azure SQL Database till Azure Synapse Analytics och den tabell som motsvarar käll tabellen inte finns i mål lagret, kan Data Factory automatiskt skapa tabellen i data lagret med hjälp av käll tabellens schema.

Data Factory skapar tabellen i mål lagret med samma tabell namn i käll data lagret. Data typerna för kolumner väljs utifrån följande typ mappning. Vid behov utför den typ konverteringar för att åtgärda eventuella inkompatibiliteter mellan käll-och mål arkiv. Den använder även en distribution av Round Robin-tabell.

| Käll SQL Database kolumn typ | Mål Azure Synapse Analytics-kolumn typ (storleks begränsning) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bitmask | Bitmask |
| Decimal | Decimal |
| Numerisk | Decimal |
| Float | Float |
| Money (Pengar) | Money (Pengar) |
| Verkligen | Verkligen |
| SmallMoney | SmallMoney |
| Binär | Binär |
| Varbinary | Varbinary (upp till 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Tid | Tid |
| DateTimeOffset | DateTimeOffset |
| Datatyp | Datatyp |
| Text | Varchar (upp till 8000) |
| NText | NVarChar (upp till 4000) |
| Bild | VarBinary (upp till 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (upp till 8000) |
| NVarChar | NVarChar (upp till 4000) |
| Xml | Varchar (upp till 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Typ mappning för Azure Synapse Analytics
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande 2-steg-metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data till & från Azure Synapse Analytics används följande mappningar från SQL-typ till .NET-typ och vice versa.

Mappningen är samma som [SQL Server data typs mappning för ADO.net](/dotnet/framework/data/adonet/sql-server-data-type-mappings).

| SQL Server typ av databas motor | .NET Framework typ |
| --- | --- |
| bigint |Int64 |
| binary |Byte [] |
| bit |Boolesk |
| char |Sträng, char [] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary (max)) |Byte [] |
| Float |Double |
| image |Byte [] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, char [] |
| ntext |Sträng, char [] |
| numeric |Decimal |
| nvarchar |Sträng, char [] |
| real |Enkel |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Jobbobjektet |
| text |Sträng, char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Sträng, char [] |
| xml |Xml |

Du kan också mappa kolumner från käll data uppsättningen till kolumner från Sink-datauppsättningen i kopierings aktivitets definitionen. Mer information finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>JSON-exempel för att kopiera data till och från Azure Synapse Analytics
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Synapse Analytics och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Exempel: kopiera data från Azure Synapse Analytics till Azure Blob
Exemplet definierar följande Data Factory entiteter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [SqlDWSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar Time-Series-data (varje timme, varje dag osv.) från en tabell i Azure Synapse Analytics-databasen till en BLOB varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad Azure Synapse Analytics-tjänst:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Länkad Azure Blob Storage-tjänst:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Data uppsättning för Azure Synapse Analytics-indata:**

Exemplet förutsätter att du har skapat en tabell "Tabell" i Azure Synapse Analytics och att den innehåller en kolumn med namnet "timestampcolumn" för Time Series-data.

Inställningen "extern": "true" informerar den Data Factory tjänsten att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Kopiera aktivitet i en pipeline med SqlDWSource och BlobSink:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **SqlDWSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som angetts för egenskapen **SqlReaderQuery** väljer data under den senaste timmen som ska kopieras.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> I exemplet anges **sqlReaderQuery** för SqlDWSource. Kopierings aktiviteten kör den här frågan mot Azure Synapse Analytics-källan för att hämta data.
>
> Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).
>
> Om du inte anger någon av sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definierats i avsnittet struktur i JSON-datauppsättnings-JSON för att skapa en fråga (Välj column1, column2 från tabellen tabell) för att köra mot Azure Synapse-analys. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Exempel: kopiera data från Azure blob till Azure Synapse Analytics
Exemplet definierar följande Data Factory entiteter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlDWSink](#copy-activity-properties).

Exemplet kopierar Time Series-data (varje timme, varje dag osv.) från Azure blob till en tabell i en Azure Synapse Analytics-databas varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad Azure Synapse Analytics-tjänst:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Länkad Azure Blob Storage-tjänst:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Data uppsättning för Azure Blob-indata:**

Data hämtas från en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden och dag delen av start tiden och fil namnet använder Tim delen av start tiden. inställningen "extern": "true" informerar Data Factory tjänsten som den här tabellen är extern i data fabriken och produceras inte av en aktivitet i data fabriken.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Data uppsättning för Azure Synapse Analytics-utdata:**

Exemplet kopierar data till en tabell med namnet "min tabell" i Azure Synapse Analytics. Skapa tabellen i Azure Synapse Analytics med samma antal kolumner som du förväntar dig att BLOB CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kopiera aktivitet i en pipeline med BlobSource och SqlDWSink:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **BlobSource** och **mottagar** typ är inställd på **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
En genom gång finns i se [load 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md) och [läsa in data med Azure Data Factory](../load-azure-sql-data-warehouse.md) artikel i Azure Synapse Analytics-dokumentationen.

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.