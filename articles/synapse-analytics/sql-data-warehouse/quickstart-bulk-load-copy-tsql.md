---
title: 'Snabb start: Mass inläsning av data med ett enda T-SQL-uttryck'
description: Mass inläsning av data med hjälp av KOPIERINGs instruktionen
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 547f697c4f8ffb903006e48e94086433d64225c7
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681344"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Snabb start: Mass inläsning av data med hjälp av KOPIERINGs instruktionen

I den här snabb starten ska du samla in data i den dedikerade SQL-poolen med hjälp av den enkla och flexibla [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) för data inmatning med stora data flöden. KOPIERINGs instruktionen är det rekommenderade inläsnings verktyget, som du kan använda för att sömlöst och flexibelt läsa in data genom att tillhandahålla funktioner för att:

- Tillåt att lägre privilegierade användare läser in utan att du behöver strikt behörighet för data lagret
- Använd endast ett enda T-SQL-uttryck utan att behöva skapa några ytterligare databas objekt
- Utnyttja en bättre behörighets modell utan att exponera lagrings konto nycklar med hjälp av dela åtkomst-signaturer (SAS)
- Ange ett annat lagrings konto för ERRORFILE-platsen (REJECTED_ROW_LOCATION)
- Anpassa standardvärden för varje mål kolumn och ange käll data fält som ska läsas in i specifika mål kolumner
- Ange en anpassad rads avslutning för CSV-filer
- Escape-sträng, fält och rad avgränsare för CSV-filer
- Utnyttja SQL Server datum format för CSV-filer
- Ange jokertecken och flera filer i sökvägen till lagrings platsen

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten förutsätter att du redan har en särskild SQL-pool. Om en dedikerad SQL-pool inte har skapats använder du snabb starten [skapa och Anslut-portalen](create-data-warehouse-portal.md) .

## <a name="set-up-the-required-permissions"></a>Konfigurera de behörigheter som krävs

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Skapa mål tabellen

I det här exemplet kommer vi att läsa in data från New York taxi-datauppsättningen. Vi läser in en tabell med namnet resa som representerar taxi resor som fattas under ett och samma år. Kör följande för att skapa tabellen:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Kör KOPIERINGs instruktionen

Kör följande KOPIERINGs instruktion som läser in data från Azure Blob Storage-kontot i rese tabellen.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Övervaka belastningen

Kontrol lera om din belastning gör att processen körs genom att regelbundet köra följande fråga:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Nästa steg

- Metod tips för inläsning av data finns i [metod tips för att läsa in data](./guidance-for-loading-data.md).
- Information om hur du hanterar resurserna för dina data belastningar finns i [arbets belastnings isolering](./quickstart-configure-workload-isolation-tsql.md).