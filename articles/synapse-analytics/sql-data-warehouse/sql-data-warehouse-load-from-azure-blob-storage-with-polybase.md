---
title: Läsa in Contosos detaljhandelsdata till dedikerade SQL-pooler
description: Använd PolyBase- och T-SQL-kommandon för att läsa in två tabeller från Contosos detaljhandelsdata till dedikerade SQL-pooler.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b1afcdfa74245eb566663d5dec6ce2e2276fbdc8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568241"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Läsa in Contosos detaljhandelsdata i dedikerade SQL-pooler i Azure Synapse Analytics

I den här självstudien lär du dig att använda PolyBase- och T-SQL-kommandon för att läsa in två tabeller från Contosos detaljhandelsdata till dedikerade SQL-pooler.

I den här självstudien kommer du att:

1. Konfigurera PolyBase för inläsning från Azure Blob Storage
2. Läsa in offentliga data i databasen
3. Utför optimeringar när inläsningen är klar.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill köra den här självstudien behöver du ett Azure-konto som redan har en dedikerad SQL-pool. Om du inte har ett etablerat informationslager kan du se Skapa ett [informationslager och ange brandväggsregel på servernivå.](create-data-warehouse-portal.md)

## <a name="configure-the-data-source"></a>Konfigurera datakällan

PolyBase använder externa T-SQL-objekt för att definiera platsen och attributen för externa data. Definitionerna för externa objekt lagras i dedikerade SQL-pooler. Data lagras externt.

## <a name="create-a-credential"></a>Skapa en autentiseringsidentifiering

**Hoppa över det** här steget om du läser in offentliga Contoso-data. Du behöver inte säker åtkomst till offentliga data eftersom de redan är tillgängliga för alla.

**Hoppa inte över det här steget om** du använder den här självstudien som mall för att läsa in dina egna data. Om du vill komma åt data via en autentiseringsuppgifterna använder du följande skript för att skapa en databasomfattning för autentiseringsuppgifter. Använd den sedan när du definierar platsen för datakällan.

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

## <a name="create-the-external-data-source"></a>Skapa den externa datakällan

Använd det [här kommandot CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SKAPA EXTERN DATAKÄLLA) för att lagra platsen för data och datatypen.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Om du väljer att göra dina Azure Blob Storage-containrar offentliga bör du komma ihåg att som dataägare debiteras du för avgifter för utgående data när data lämnar datacentret.

## <a name="configure-the-data-format"></a>Konfigurera dataformatet

Data lagras i textfiler i Azure Blob Storage och varje fält avgränsas med en avgränsare. I SSMS kör du följande create external file format-kommando för att ange formatet för data i textfilerna. Contosos data är okomprimerade och pipe avgränsade.

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

Nu när du har angett datakällan och filformatet är du redo att skapa schemat för de externa tabellerna.

Skapa ett schema för att skapa en plats där Contoso-data ska lagras i databasen.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Skapa de externa tabellerna

Kör följande skript för att skapa de externa tabellerna DimProduct och FactOnlineSales. Allt du gör här är att definiera kolumnnamn och datatyper och binda dem till platsen och formatet för Azure Blob Storage-filerna. Definitionen lagras i informationslagret och data finns fortfarande i Azure Storage Blob.

Parametern  **LOCATION** är mappen under rotmappen i Azure Storage Blob. Varje tabell finns i en annan mapp.

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

Det finns olika sätt att komma åt externa data.  Du kan fråga efter data direkt från de externa tabellerna, läsa in data i nya tabeller i informationslagret eller lägga till externa data i befintliga informationslagertabeller.  

### <a name="create-a-new-schema"></a>Skapa ett nytt schema

CTAS skapar en ny tabell som innehåller data.  Skapa först ett schema för contoso-data.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Läsa in data i nya tabeller

Om du vill läsa in data från Azure Blob Storage till [informationslagertabellen använder du CREATE TABLE AS SELECT-instruktionen (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Inläsning [med CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) utnyttjar de starkt typade externa tabeller som du har skapat. Om du vill läsa in data i nya tabeller använder du en CTAS-instruktion per tabell.

CTAS skapar en ny tabell och fyller den med resultatet av en select-instruktion. CTAS definierar att den nya tabellen ska ha samma kolumner och datatyper som resultatet av select-instruktionen. Om du väljer alla kolumner från en extern tabell blir den nya tabellen en replik av kolumnerna och datatyperna i den externa tabellen.

I det här exemplet skapar vi både dimensionen och faktatabellen som hash-distribuerade tabeller.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Spåra inläsningsförloppet

Du kan följa förloppet för inläsningen med dynamiska hanteringsvyer (DMV:er).

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

## <a name="optimize-columnstore-compression"></a>Optimera kolumnlagringskomprimering

Som standard lagrar dedikerade SQL-pooler tabellen som ett grupperat columnstore-index. När inläsningen är klar kanske vissa datarader inte komprimeras till columnstore.  Det finns olika orsaker till varför detta kan inträffa. Mer information finns i [Hantera columnstore-index.](sql-data-warehouse-tables-index.md)

Om du vill optimera frågeprestanda och columnstore-komprimering efter en inläsning återskapar du tabellen för att tvinga columnstore-indexet att komprimera alla rader.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Mer information om hur du underhåller columnstore-index finns i [artikeln Hantera kolumnlagringsindex.](sql-data-warehouse-tables-index.md)

## <a name="optimize-statistics"></a>Optimera statistik

Det är bäst att skapa statistik med en kolumn direkt efter en inläsning. Om du vet att vissa kolumner inte kommer att finnas i fråge predikat kan du hoppa över att skapa statistik för dessa kolumner. Om du skapar statistik med en kolumn för varje kolumn kan det ta lång tid att återskapa all statistik.

Om du bestämmer dig för att skapa statistik med en kolumn för varje kolumn i varje tabell kan du använda kodexempel för lagrad `prc_sqldw_create_stats` procedur i [statistikartikeln.](sql-data-warehouse-tables-statistics.md)

Följande exempel är en bra startpunkt för att skapa statistik. Den skapar statistik med en kolumn för varje kolumn i dimensionstabellen och för varje sammanfogning av kolumn i faktatabellerna. Du kan alltid lägga till statistik med en eller flera kolumner i andra faktatabellkolumner senare.

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

## <a name="achievement-unlocked"></a>Achievement upplåst!

Du har läst in offentliga data i informationslagret. Bra jobbat!

Nu kan du börja fråga tabellerna för att utforska dina data. Kör följande fråga för att ta reda på total försäljning per varumärke:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Nästa steg

Om du vill läsa in den fullständiga datauppsättningen kör du exemplet och läser in det fullständiga [Contosos](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) butiksdatalager från lagringsplatsen Microsoft SQL Server exempel.
Fler utvecklingstips finns i [Designbeslut och kodningstekniker för informationslager.](sql-data-warehouse-overview-develop.md)
