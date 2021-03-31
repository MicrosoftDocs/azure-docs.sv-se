---
title: Läs in Contosos detalj handels data till dedikerade SQL-pooler
description: Använd PolyBase-och T-SQL-kommandon för att läsa in två tabeller från Contosos detalj handels data till dedikerade SQL-pooler.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 13e78c707ca3bda338f9255c015c0e926fca90d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606148"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Läs in Contosos detalj handels data i dedikerade SQL-pooler i Azure Synapse Analytics

I den här självstudien lär du dig att använda PolyBase-och T-SQL-kommandon för att läsa in två tabeller från Contosos detalj handels data till dedikerade SQL-pooler.

I den här självstudien kommer du att:

1. Konfigurera PolyBase för att läsa in från Azure Blob Storage
2. Läs in offentliga data i databasen
3. Utför optimeringar när belastningen är färdig.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna köra den här självstudien behöver du ett Azure-konto som redan har en särskild SQL-pool. Om du inte har ett data lager som har skapats, se [skapa ett informations lager och ange brand Väggs regel på server nivå](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Konfigurera data källan

PolyBase använder externa T-SQL-objekt för att definiera platsen och attributen för externa data. De externa objekt definitionerna lagras i dedikerade SQL-pooler. Data lagras externt.

## <a name="create-a-credential"></a>Skapa en autentiseringsuppgift

**Hoppa över det här steget** om du läser in offentliga contoso-data. Du behöver inte säker åtkomst till offentliga data eftersom den redan är tillgänglig för alla.

**Hoppa inte över det här steget** om du använder den här självstudien som mall för att läsa in dina data. Om du vill få åtkomst till data via en autentiseringsuppgift använder du följande skript för att skapa en databas med begränsade autentiseringsuppgifter. Använd den när du definierar platsen för data källan.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>Skapa den externa data källan

Använd det här kommandot [skapa extern data källa](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att lagra data platsen och data typen.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Om du väljer att göra dina Azure Blob Storage-behållare offentliga måste du komma ihåg att som data ägare debiteras du för avgifter för utgående data när data lämnar data centret.

## <a name="configure-the-data-format"></a>Konfigurera data formatet

Data lagras i textfiler i Azure Blob Storage, och varje fält skiljs åt med en avgränsare. I SSMS kör du följande kommando för att skapa externt fil FORMAT för att ange formatet för data i textfilerna. Contoso-data är okomprimerade och pipe-avgränsade.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-schema-for-the-external-tables"></a>Skapa schemat för de externa tabellerna

Nu när du har angett data källan och fil formatet är du redo att skapa schemat för de externa tabellerna.

Skapa ett schema för att skapa en plats för att lagra contoso-data i databasen.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Skapa externa tabeller

Kör följande skript för att skapa DimProduct-och FactOnlineSales-externa tabeller. Allt du gör här är att definiera kolumn namn och data typer och att binda dem till platsen och formatet för Azure Blob Storage-filerna. Definitionen lagras i data lagret och data finns fortfarande i Azure Storage Blob.

Parametern  **location** är mappen under rotmappen i Azure Storage blob. Varje tabell finns i en annan mapp.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>Läsa in data

Det finns olika sätt att komma åt externa data.  Du kan fråga efter data direkt från de externa tabellerna, läsa in data i nya tabeller i informations lagret eller lägga till externa data i befintliga data lager tabeller.  

### <a name="create-a-new-schema"></a>Skapa ett nytt schema

CTAS skapar en ny tabell som innehåller data.  Börja med att skapa ett schema för Contoso-data.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Läs in data i nya tabeller

Använd uttrycket [CREATE TABLE as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att läsa in data från Azure Blob Storage till data lager tabellen. När du läser in med [CTAs](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) utnyttjas de mest skrivna externa tabellerna som du har skapat. Om du vill läsa in data i nya tabeller använder du ett CTAS-uttryck per tabell.

CTAS skapar en ny tabell och fyller den med resultatet av en SELECT-instruktion. CTAS definierar den nya tabellen för att ha samma kolumner och data typer som resultatet av SELECT-instruktionen. Om du väljer alla kolumner från en extern tabell är den nya tabellen en replik av kolumnerna och data typerna i den externa tabellen.

I det här exemplet skapar vi både dimensionen och fakta tabellen som hash-distribuerade tabeller.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Spåra inläsnings förloppet

Du kan följa förloppet för din belastning med hjälp av DMV: er (Dynamic Management views).

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>Optimera columnstore-komprimering

Som standard lagrar dedikerade SQL-pooler tabellen som ett grupperat columnstore-index. När en belastning är klar kanske vissa av data raderna inte komprimeras till columnstore.  Det kan bero på olika orsaker. Mer information finns i [Hantera columnstore-index](sql-data-warehouse-tables-index.md).

Om du vill optimera prestanda och columnstore-komprimering efter en belastning måste du återskapa tabellen för att tvinga columnstore-indexet att komprimera alla rader.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Mer information om hur du behåller columnstore-index finns i artikeln [Hantera columnstore-index](sql-data-warehouse-tables-index.md) .

## <a name="optimize-statistics"></a>Optimera statistik

Det är bäst att skapa statistik med en kolumn direkt efter en belastning. Om du vet att vissa kolumner inte kommer att finnas i frågesyntaxen kan du hoppa över att skapa statistik för dessa kolumner. Om du skapar en statistik med en kolumn på varje kolumn kan det ta lång tid att återskapa all statistik.

Om du väljer att skapa en statistik med en kolumn på varje kolumn i varje tabell, kan du använda exemplet på den lagrade procedur koden `prc_sqldw_create_stats` i [statistik](sql-data-warehouse-tables-statistics.md) artikeln.

I följande exempel visas en start punkt för att skapa statistik. Den skapar statistik med en kolumn för varje kolumn i tabellen dimension och i varje kopplings kolumn i fakta tabellerna. Du kan alltid lägga till en eller flera kolumn statistik i andra fakta tabell kolumner senare.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Utmärkelse upplåst!

Du har läst in offentliga data i informations lagret. Bra jobbat!

Nu kan du börja fråga tabellerna och utforska dina data. Kör följande fråga för att ta reda på total försäljning per varumärke:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Nästa steg

Om du vill läsa in den fullständiga data uppsättningen kör du exemplet [Läs in det fullständiga contoso-informations lagret](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) från Microsoft SQL Server exempel lagrings plats.
Mer utvecklings tips finns i [design beslut och kod metoder för informations lager](sql-data-warehouse-overview-develop.md).
