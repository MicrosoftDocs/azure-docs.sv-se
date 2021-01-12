---
title: Självstudier läsa in data från Azure Data Lake Storage
description: Använd COPY-instruktionen för att läsa in data från Azure Data Lake Storage för dedikerade SQL-pooler.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 4f881dcc0d26589c4029246019119fa0555dcd5d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117783"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Läs in data från Azure Data Lake Storage i dedikerade SQL-pooler i Azure Synapse Analytics

Den här guiden beskriver hur du använder Copy- [instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) för att läsa in data från Azure Data Lake Storage. Exempel på hur du använder COPY-instruktionen för alla autentiseringsmetoder finns i följande dokumentation: [Läs in data på ett säkert sätt med dedikerade SQL-pooler](./quickstart-bulk-load-copy-tsql-examples.md).

> [!NOTE]  
> Skicka ett e-postmeddelande till följande distributions lista för att ge feedback eller rapportera problem på KOPIERINGs instruktionen: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Skapa mål tabellen för att läsa in data från Azure Data Lake Storage.
> * Skapa COPY-instruktionen för att läsa in data till data lagret.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

För att kunna köra den här självstudien behöver du:

* En dedikerad SQL-pool. Se [skapa en dedikerad SQL-pool och fråga efter data](create-data-warehouse-portal.md).
* Ett Data Lake Storage konto. Se [Kom igång med Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). För det här lagrings kontot måste du konfigurera eller ange någon av följande autentiseringsuppgifter som ska läsas in: en lagrings konto nyckel, en signatur för delad åtkomst (SAS), en Azure Directory-programanvändare eller en AAD-användare som har rätt Azure-roll till lagrings kontot.

## <a name="create-the-target-table"></a>Skapa mål tabellen

Anslut till din dedikerade SQL-pool och skapa mål tabellen som du ska läsa in till. I det här exemplet skapar vi en produkt dimensions tabell.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>Skapa KOPIERINGs instruktionen

Anslut till din SQL-dedikerade pool och kör KOPIERINGs instruktionen. En fullständig lista över exempel finns i följande dokumentation: [Läs in data på ett säkert sätt med hjälp av dedikerade SQL-pooler](./quickstart-bulk-load-copy-tsql-examples.md).

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Optimera columnstore-komprimering

Som standard definieras tabeller som ett grupperat columnstore-index. När en belastning är klar kanske vissa av data raderna inte komprimeras till columnstore.  Det finns en mängd olika orsaker till varför detta kan inträffa. Mer information finns i [Hantera columnstore-index](sql-data-warehouse-tables-index.md).

Om du vill optimera prestanda och columnstore-komprimering efter en belastning måste du återskapa tabellen för att tvinga columnstore-indexet att komprimera alla rader.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimera statistik

Det är bäst att skapa statistik med en kolumn direkt efter en belastning. Det finns några val för statistik. Om du till exempel skapar en statistik med en kolumn på varje kolumn kan det ta lång tid att bygga om all statistik. Om du vet att vissa kolumner inte kommer att finnas i frågesyntaxen kan du hoppa över att skapa statistik för dessa kolumner.

Om du väljer att skapa en statistik med en kolumn på varje kolumn i varje tabell, kan du använda exemplet på den lagrade procedur koden `prc_sqldw_create_stats` i [statistik](sql-data-warehouse-tables-statistics.md) artikeln.

I följande exempel visas en start punkt för att skapa statistik. Den skapar statistik med en kolumn för varje kolumn i tabellen dimension och i varje kopplings kolumn i fakta tabellerna. Du kan alltid lägga till en eller flera kolumn statistik i andra fakta tabell kolumner senare.

## <a name="achievement-unlocked"></a>Utmärkelse upplåst!

Du har läst in data i informations lagret. Bra jobbat!

## <a name="next-steps"></a>Nästa steg
Inläsning av data är det första steget för att utveckla en informations lager lösning med Azure Synapse Analytics. Kolla våra utvecklings resurser.

> [!div class="nextstepaction"]
> [Lär dig hur du utvecklar tabeller för data lager hantering](sql-data-warehouse-tables-overview.md)

Läs följande dokumentation om du vill läsa mer om exempel och referenser:
- [Referens dokumentation för KOPIERINGs instruktion](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Kopiera exempel för varje autentiseringsmetod](./quickstart-bulk-load-copy-tsql-examples.md)
- [Kopiera snabb start för en enskild tabell](./quickstart-bulk-load-copy-tsql.md)