---
title: Läsa in data från CSV-filen till en databas (BCP)
description: För mindre datastorlekar används bcp för att importera data till Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 216410d423a70037588a6fb596771a702a13c003
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472236"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Läsa in data från CSV till Azure SQL Database eller SQL-hanterad instans (flata filer)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Du kan använda kommando rads verktyget BCP för att importera data från en CSV-fil till Azure SQL Database eller Azure SQL-hanterad instans.

## <a name="before-you-begin"></a>Innan du börjar

### <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln behöver du:

* En databas i Azure SQL Database
* Kommandoradsverktyget bcp installerat
* Kommandoradsverktyget sqlcmd installerat

Du kan hämta BCP-och SQLCMD-verktygen från [Microsoft SQLCMD-dokumentationen] [ https://docs.microsoft.com/sql/tools/sqlcmd-utility?view=sql-server-ver15 ].

### <a name="data-in-ascii-or-utf-16-format"></a>Data i ASCII- eller UTF-16-format

Om du använder egna data i självstudierna, måste de använda sig av ASCII- eller UTF-16-kodning eftersom bcp inte stöder UTF-8.

## <a name="1-create-a-destination-table"></a>1. skapa en mål tabell

Definiera en tabell i SQL Database som måltabellen. Kolumnerna i tabellen måste motsvara data i varje rad i din datafil.

För att skapa en tabell, öppnar du en kommandotolk och använder sqlcmd.exe för att köra följande kommando:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. skapa en käll data fil

Öppna Anteckningar och kopiera följande datarader till en ny textfil. Spara sedan filen till din lokala temp-katalog, C:\Temp\DimDate2.txt. Den här datan är i ASCII-format.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Valfritt) Om du vill exportera dina egna data från en SQL Server-databas, öppnar du en kommandotolk och kör följande kommando. Ersätt TableName, ServerName, DatabaseName, Username och Password med din egen information.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. Läs in data

För att läsa in data, öppnar du en kommandotolk och kör följande kommando, där du ersätter värdena för servernamn, databasnamn, användarnamn och lösenord med din egen information.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Använd det här kommandot för att verifiera att data har lästs in korrekt

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Resultatet borde se ut så här:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Nästa steg

Om du vill migrera en SQL Server-databas hittar du mer information i [SQL Server-databasmigrering](database/migrate-to-database-from-sql-server.md).

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
