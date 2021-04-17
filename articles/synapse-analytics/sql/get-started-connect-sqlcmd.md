---
title: Ansluta till Synapse SQL med sqlcmd
description: Använd kommandoradsverktyget sqlcmd för att ansluta till och fråga serverlös SQL-pool och dedikerad SQL-pool.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 3abdd44f0684282e92da147dff996ff54f0ef23f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565482"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Ansluta till Synapse SQL med sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Du kan använda [kommandoradsverktyget sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) för att ansluta till och fråga en serverlös SQL-pool och dedikerad SQL-pool inom Synapse SQL.  

## <a name="1-connect"></a>1. Anslut
Kom igång med [sqlcmd genom](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)att öppna kommandotolken och ange **sqlcmd** följt av anslutningssträngen för din Synapse SQL databas. Anslutningssträngen kräver följande parametrar:

* **Server (-S):** Server i formatet `<`servernamn`>`. database.windows.net
* **Databas (-d):** Databasnamn
* **Aktivera identifierare inom citattecken (-I):** Identifierare inom citattecken måste vara aktiverade för att ansluta till Synapse SQL instans

Om du SQL Server autentisering måste du lägga till parametrarna för användarnamn och lösenord:

* **Användare (-U):** Serveranvändare i formatet `<`Användare`>`
* **Lösenord (-P):** Lösenord som är associerat med användaren

Anslutningssträngen kan se ut som i följande exempel:

**Serverlös SQL-pool**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Dedikerad SQL-pool**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Om du vill använda Azure Active Directory-integrerad autentisering måste du lägga till Azure Active Directory-parametrar:

* **Azure Active Directory-autentisering (-G):** använder Azure Active Directory för autentisering

Anslutningssträngen kan se ut som i följande exempel:

**Serverlös SQL-pool**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Dedikerad SQL-pool**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Du måste [aktivera Azure Active Directory-autentisering](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för att kunna autentisera med Active Directory.

## <a name="2-query"></a>2. Fråga

### <a name="use-dedicated-sql-pool"></a>Använda dedikerad SQL-pool

Efter anslutningen kan du utfärda alla [Transact-SQL-instruktioner](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) som stöds mot instansen. I det här exemplet skickas frågor i interaktivt läge:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

För dedikerad SQL-pool visar följande exempel hur du kör frågor i batchläge med alternativet -Q eller att skicka din SQL till sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Använda en serverlös SQL-pool

När du har anslutit kan du utfärda alla [Transact-SQL-instruktioner](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) som stöds mot instansen.  I följande exempel skickas frågor i interaktivt läge:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

För serverlös SQL-pool visar exemplen nedan hur du kör frågor i batchläge med alternativet -Q eller att skicka din SQL till sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Nästa steg

Mer information om sqlcmd-alternativ finns i [sqlcmd-dokumentationen.](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)
