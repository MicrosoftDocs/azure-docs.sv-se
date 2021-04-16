---
title: Självstudie om att läsa in data från Azure Data Lake Storage
description: Använd COPY-instruktionen för att läsa in data Azure Data Lake Storage för dedikerade SQL-pooler.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 16f95a86169be04eba202b311fc4437b204ec8b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566536"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Läsa in data från Azure Data Lake Storage till dedikerade SQL-pooler i Azure Synapse Analytics

Den här guiden beskriver hur du använder [COPY-instruktionen för](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) att läsa in data från Azure Data Lake Storage. Snabbexempel på hur du använder COPY-instruktionen för alla autentiseringsmetoder finns i följande dokumentation: Läs in data säkert med [dedikerade SQL-pooler.](./quickstart-bulk-load-copy-tsql-examples.md)

> [!NOTE]  
> Om du vill ge feedback eller rapportera problem med COPY-instruktionen skickar du ett e-postmeddelande till följande distributionslista: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Skapa måltabellen för att läsa in data från Azure Data Lake Storage.
> * Skapa COPY-instruktionen för att läsa in data till informationslagret.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).

Om du vill köra den här självstudien behöver du:

* En dedikerad SQL-pool. Se [Skapa en dedikerad SQL-pool och fråga efter data.](create-data-warehouse-portal.md)
* Ett Data Lake Storage konto. Se [Kom igång med Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). För det här lagringskontot måste du konfigurera eller ange någon av följande autentiseringsuppgifter för att läsa in: en lagringskontonyckel, en SAS-nyckel (signatur för delad åtkomst), en Azure Directory Application-användare eller en AAD-användare som har rätt Azure-roll för lagringskontot.

## <a name="create-the-target-table"></a>Skapa måltabellen

Anslut till din dedikerade SQL-pool och skapa måltabellen som du ska läsa in till. I det här exemplet skapar vi en tabell med produktdimensioner.

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


## <a name="create-the-copy-statement"></a>Skapa COPY-instruktionen

Anslut till den dedikerade SQL-poolen och kör COPY-instruktionen. En fullständig lista över exempel finns i följande dokumentation: Läs in data [säkert med dedikerade SQL-pooler.](./quickstart-bulk-load-copy-tsql-examples.md)

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

## <a name="optimize-columnstore-compression"></a>Optimera kolumnlagringskomprimering

Som standard definieras tabeller som ett grupperat columnstore-index. När inläsningen är klar kanske vissa datarader inte komprimeras till columnstore.  Det finns en mängd olika orsaker till varför detta kan inträffa. Mer information finns i [hantera columnstore-index.](sql-data-warehouse-tables-index.md)

Om du vill optimera frågeprestanda och columnstore-komprimering efter en inläsning återskapar du tabellen för att tvinga columnstore-indexet att komprimera alla rader.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimera statistik

Det är bäst att skapa statistik med en kolumn direkt efter en inläsning. Det finns några alternativ för statistik. Om du till exempel skapar statistik med en kolumn för varje kolumn kan det ta lång tid att återskapa all statistik. Om du vet att vissa kolumner inte kommer att finnas i fråge predikat kan du hoppa över att skapa statistik för dessa kolumner.

Om du bestämmer dig för att skapa statistik med en kolumn i varje kolumn i varje tabell kan du använda kodexempel för lagrad `prc_sqldw_create_stats` procedur i [statistikartikeln.](sql-data-warehouse-tables-statistics.md)

Följande exempel är en bra utgångspunkt för att skapa statistik. Den skapar statistik med en kolumn för varje kolumn i dimensionstabellen och för varje sammanfogning av kolumn i faktatabellerna. Du kan alltid lägga till statistik med en eller flera kolumner i andra faktatabellkolumner senare.

## <a name="achievement-unlocked"></a>Achievement upplåst!

Du har läst in data i informationslagret. Bra jobbat!

## <a name="next-steps"></a>Nästa steg
Att läsa in data är det första steget för att utveckla en informationslagerlösning med Azure Synapse Analytics. Kolla in våra utvecklingsresurser.

> [!div class="nextstepaction"]
> [Lär dig hur du utvecklar tabeller för informationslager](sql-data-warehouse-tables-overview.md)

Fler inläsningsexempel och referenser finns i följande dokumentation:
- [Referensdokumentation för COPY-instruktion](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [KOPIERA exempel för varje autentiseringsmetod](./quickstart-bulk-load-copy-tsql-examples.md)
- [SNABBSTART FÖR KOPIA för en enskild tabell](./quickstart-bulk-load-copy-tsql.md)