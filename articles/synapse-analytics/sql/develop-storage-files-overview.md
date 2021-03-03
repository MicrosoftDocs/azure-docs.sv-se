---
title: Komma åt filer på lagrings platsen i SQL-poolen utan Server
description: Beskriver hur du frågar Storage-filer med Server lös SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 855765648d68756598f2942e0c994fa939bcbda0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695676"
---
# <a name="access-external-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Få åtkomst till extern lagring med Server lös SQL-pool i Azure Synapse Analytics

Det här dokumentet beskriver hur kan användare läsa data från filerna som lagras på Azure Storage i SQL-poolen utan server. Användare har följande alternativ för åtkomst till lagring:

- [OpenRowSet](develop-openrowset.md) -funktionen som aktiverar ad hoc-frågor över filerna i Azure Storage.
- [Extern tabell](develop-tables-external-tables.md) som är en fördefinierad data struktur som skapats ovanpå en uppsättning externa filer.

Användaren kan använda [olika autentiseringsmetoder](develop-storage-files-storage-access-control.md) , till exempel Azure AD passthrough-autentisering (standard för Azure AD-huvudobjekt) och SAS-autentisering (standard för SQL-huvudobjekt).

## <a name="query-files-using-openrowset"></a>Köra frågefiler med OpenRowSet

OpenRowSet gör det möjligt för användare att fråga externa filer på Azure Storage om de har åtkomst till lagrings utrymmet. En användare som är ansluten till en server lös SQL-pool bör använda följande fråga för att läsa innehållet i filerna i Azure Storage:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

Användaren kan komma åt lagringen med följande åtkomst regler:

- Azure AD-användare – `OPENROWSET` kommer att använda Azure AD-identitet för anroparen för att få åtkomst till Azure Storage eller åtkomst till lagring med anonym åtkomst.
- SQL-användare – `OPENROWSET` kommer att få åtkomst till lagring med anonym åtkomst eller kan personifieras med SAS-token eller hanterad identitet för arbets ytan.

### <a name="impersonation"></a>[Personifiering](#tab/impersonation)

SQL-huvudobjekt kan också använda OpenRowSet för att direkt fråga filer som skyddas med SAS-token eller hanterad identitet på arbets ytan. Om en SQL-användare kör den här funktionen måste en privilegie rad användare med `ALTER ANY CREDENTIAL` behörighet skapa en server begränsad autentiseringsuppgift som matchar URL i funktionen (med hjälp av lagrings namn och behållare) och beviljar behörighet för den här autentiseringsuppgiften till anroparen i OpenRowSet-funktionen:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES ON CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Om det inte finns några AUTENTISERINGSUPPGIFTER på server nivå som matchar URL: en, eller om SQL-användaren inte har referens behörighet för den här autentiseringsuppgiften, returneras felet. SQL-huvudobjekt kan inte personifiera med en viss Azure AD-identitet.

### <a name="direct-access"></a>[Direkt åtkomst](#tab/direct-access)

Ingen ytterligare konfiguration krävs för att Azure AD-användare ska kunna komma åt filerna med sina identiteter.
Alla användare kan komma åt Azure Storage som tillåter anonym åtkomst (ytterligare konfiguration behövs inte).

---

> [!NOTE]
> Den här versionen av OpenRowSet är utformad för snabb och enkel data utforskning med standardautentisering. Om du vill utnyttja personifiering eller hanterad identitet använder du OpenRowSet med DATASOURCE enligt beskrivningen i nästa avsnitt.

## <a name="query-data-sources-using-openrowset"></a>Fråga data källor med hjälp av OpenRowSet

OpenRowSet gör det möjligt för användaren att fråga filerna som placerats på en extern data Källa:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

Användaren som kör den här frågan måste kunna komma åt filerna. Användarna måste personifieras med SAS- [token](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) eller [hanterad identitet för arbets ytan](develop-storage-files-storage-access-control.md?tabs=managed-identity) om de inte kan komma åt filerna direkt med sin [Azure AD-identitet](develop-storage-files-storage-access-control.md?tabs=user-identity) eller [anonym åtkomst](develop-storage-files-storage-access-control.md?tabs=public-access).

### <a name="impersonation"></a>[Personifiering](#tab/impersonation)

`DATABASE SCOPED CREDENTIAL` anger hur du kommer åt filer på den refererade data källan (för närvarande SAS och hanterad identitet). Privilegierade användare med `CONTROL DATABASE` behörighet måste skapa `DATABASE SCOPED CREDENTIAL` som kommer att användas för att komma åt lagringen och `EXTERNAL DATA SOURCE` anger URL: en till data källan och autentiseringsuppgiften som ska användas:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

Anroparen måste ha någon av följande behörigheter för att köra OpenRowSet-funktionen:

- En av behörigheterna för att köra OpenRowSet:
  - `ADMINISTER BULK OPERATIONS` aktiverar inloggning för att köra OpenRowSet-funktionen.
  - `ADMINISTER DATABASE BULK OPERATIONS` gör det möjligt för databas omfattnings användare att köra OpenRowSet-funktionen.
- `REFERENCES DATABASE SCOPED CREDENTIAL` till autentiseringsuppgiften som refereras till i `EXTERNAL DATA SOURCE` .

### <a name="direct-access"></a>[Direkt åtkomst](#tab/direct-access)

Användaren kan skapa en extern DATA källa utan AUTENTISERINGSUPPGIFTER som ska hänvisa till offentlig åtkomst lagring eller använda Azure AD passthrough-autentisering:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>EXTERN TABELL

Användare med behörighet att läsa tabell har åtkomst till externa filer med hjälp av en extern tabell som skapats ovanpå Azure Storage mappar och filer.

Användare som har [behörighet att skapa en extern tabell](/sql/t-sql/statements/create-external-table-transact-sql?preserve-view=true&view=sql-server-ver15#permissions) (till exempel CREATE TABLE och ändra AUTENTISERINGSUPPGIFTER eller referenser till databasens begränsade autentiseringsuppgifter) kan använda följande skript för att skapa en tabell ovanpå Azure Storage data Källa:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

Användare som läser data från den här tabellen måste kunna komma åt filerna. Användarna måste personifieras med SAS- [token](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) eller [hanterad identitet för arbets ytan](develop-storage-files-storage-access-control.md?tabs=managed-identity) om de inte kan komma åt filerna direkt med sin [Azure AD-identitet](develop-storage-files-storage-access-control.md?tabs=user-identity) eller [anonym åtkomst](develop-storage-files-storage-access-control.md?tabs=public-access).

### <a name="impersonation"></a>[Personifiering](#tab/impersonation)

DATABASens begränsade AUTENTISERINGSUPPGIFTER anger hur du kommer åt filer på den refererade data källan. Användaren med behörigheten kontrol lera databas skulle behöva skapa en databas med begränsade AUTENTISERINGSUPPGIFTER som ska användas för åtkomst till lagring och extern DATA källa som anger URL: en till data källan och autentiseringsuppgiften som ska användas:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[Direkt åtkomst](#tab/direct-access)

Användaren kan skapa en extern DATA källa utan AUTENTISERINGSUPPGIFTER som ska hänvisa till offentlig åtkomst lagring eller använda Azure AD passthrough-autentisering:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>Läsa externa filer med extern tabell

Med den externa tabellen kan du läsa data från filer som refereras via data källa med standard SQL SELECT-instruktionen:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

Anroparen måste ha följande behörigheter för att kunna läsa data:
- `SELECT` behörighet för extern tabell
- `REFERENCES DATABASE SCOPED CREDENTIAL` behörighet om `DATA SOURCE` har `CREDENTIAL`

## <a name="permissions"></a>Behörigheter

I följande tabell visas de behörigheter som krävs för de åtgärder som anges ovan.

| Söka i data | Behörigheter som krävs|
| --- | --- |
| OpenRowSet (BULK) utan DataSource | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS` eller SQL-inloggningen måste ha referenser till autentiseringsuppgifter:: \<URL> för SAS-skyddad lagring |
| OpenRowSet (BULK) med DataSource utan autentiseringsuppgifter | `ADMINISTER BULK OPERATIONS` eller `ADMINISTER DATABASE BULK OPERATIONS` , |
| OpenRowSet (BULK) med data källa med autentiseringsuppgifter | `REFERENCES DATABASE SCOPED CREDENTIAL` och en av `ADMINISTER BULK OPERATIONS` eller `ADMINISTER DATABASE BULK OPERATIONS` |
| SKAPA EXTERN DATA KÄLLA | `ALTER ANY EXTERNAL DATA SOURCE` och `REFERENCES DATABASE SCOPED CREDENTIAL` |
| SKAPA EXTERN TABELL | `CREATE TABLE`, `ALTER ANY SCHEMA` , `ALTER ANY EXTERNAL FILE FORMAT` , och `ALTER ANY EXTERNAL DATA SOURCE` |
| VÄLJ FRÅN EXTERN TABELL | `SELECT TABLE` och `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | För att skapa tabell `CREATE TABLE` , `ALTER ANY SCHEMA` ,, `ALTER ANY DATA SOURCE` och `ALTER ANY EXTERNAL FILE FORMAT` . Läsa data: `ADMINISTER BULK OPERATIONS` eller `REFERENCES CREDENTIAL` `SELECT TABLE` per tabell/vy/funktion i fråga + R/W behörighet för lagring |

## <a name="next-steps"></a>Nästa steg

Nu är du redo att fortsätta med följande artiklar:

- [Fråga efter data på lagring](query-data-storage.md)

- [Efterfråga CSV-fil](query-single-csv-file.md)

- [Efterfråga Parquet-filer](query-parquet-files.md)

- [Efterfråga JSON-filer](query-json-files.md)

- [Efterfråga mappar och flera filer](query-folders-multiple-csv-files.md)

- [Använda funktioner för partitionering och metadata](query-specific-files.md)

- [Köra frågor mot kapslade typer](query-parquet-nested-types.md)

- [Skapa och använda vyer](create-use-views.md)