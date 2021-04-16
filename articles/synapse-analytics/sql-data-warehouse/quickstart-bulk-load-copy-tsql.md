---
title: 'Snabbstart: Massinläsning av data med en enda T-SQL-instruktion'
description: Massinläsning av data med COPY-instruktionen
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2b41342ab7d267c37b8e68fdbcaa9d570034ac17
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568445"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Snabbstart: Massinläsning av data med COPY-instruktionen

I den här snabbstarten massinläsningar du data till [](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) din dedikerade SQL-pool med hjälp av den enkla och flexibla COPY-instruktionen för datainmatning med högt dataflöde. COPY-instruktionen är det inläsningsverktyg som rekommenderas eftersom du smidigt och flexibelt kan läsa in data genom att tillhandahålla funktioner för att:

- Tillåt lägre privilegierade användare att läsa in utan att behöva strikta kontrollbehörigheter för informationslagret
- Utnyttja endast en enda T-SQL-instruktion utan att behöva skapa ytterligare databasobjekt
- Utnyttja en finare behörighetsmodell utan att exponera lagringskontonycklar med hjälp av signaturer för resursåtkomst (SAS)
- Ange ett annat lagringskonto för ERRORFILE-platsen (REJECTED_ROW_LOCATION)
- Anpassa standardvärden för varje målkolumn och ange källdatafält som ska läsas in i specifika målkolumner
- Ange en anpassad radparentator för CSV-filer
- Escape-sträng, fält och radavgränsare för CSV-filer
- Utnyttja SQL Server för CSV-filer
- Ange jokertecken och flera filer i sökvägen till lagringsplatsen

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten förutsätter att du redan har en dedikerad SQL-pool. Om en dedikerad SQL-pool inte har skapats använder du [snabbstarten Skapa och ansluta –](create-data-warehouse-portal.md) portalen.

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

## <a name="create-the-target-table"></a>Skapa måltabellen

I det här exemplet ska vi läsa in data från New York Taxi-datamängden. Vi läser in en tabell med namnet Trip (Resa) som representerar taxiresor som tagits inom ett enda år. Kör följande för att skapa tabellen:

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

## <a name="run-the-copy-statement"></a>Kör COPY-instruktionen

Kör följande COPY-instruktion som läser in data från Azure Blob Storage-kontot till resetabellen.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Övervaka belastningen

Kontrollera om inläsningen går framåt genom att köra följande fråga regelbundet:

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

- Metodtips för inläsning av data finns i [Metodtips för att läsa in data.](./guidance-for-loading-data.md)
- Information om hur du hanterar resurserna för dina databelastningar finns i [Arbetsbelastningsisolering.](./quickstart-configure-workload-isolation-tsql.md)