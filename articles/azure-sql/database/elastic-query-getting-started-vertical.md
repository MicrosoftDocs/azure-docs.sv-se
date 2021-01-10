---
title: Kom igång med frågor mellan databaser
description: använda elastisk databas fråga med lodrätt partitionerade databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 90f89e72193c26b71859076b99c448a6e2d4c4c6
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060137"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Kom igång med frågor mellan databaser (vertikal partitionering) (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Med elastisk databas fråga (för hands version) för Azure SQL Database kan du köra T-SQL-frågor som sträcker sig över flera databaser med hjälp av en enda anslutnings punkt. Den här artikeln gäller för [lodrätt partitionerade databaser](elastic-query-vertical-partitioning.md).  

När du är klar kan du: Lär dig hur du konfigurerar och använder en Azure SQL Database för att utföra frågor som sträcker sig över flera relaterade databaser.

Mer information om funktionen för Elastic Database-frågor finns i  [Översikt över Azure SQL Database Elastic Database Query](elastic-query-overview.md).

## <a name="prerequisites"></a>Förutsättningar

ÄNDRA behörigheter för en extern DATA källa krävs. Den här behörigheten ingår i ALTER DATABASE-behörigheten. ÄNDRA behörigheter för en extern DATA källa krävs för att referera till den underliggande data källan.

## <a name="create-the-sample-databases"></a>Skapa exempel databaserna

Börja med genom att skapa två databaser, **kunder** och **order**, antingen på samma eller på olika servrar.

Kör följande frågor på **order** databasen för att skapa **OrderInformation** -tabellen och mata in exempel data.

```tsql
CREATE TABLE [dbo].[OrderInformation](
    [OrderID] [int] NOT NULL,
    [CustomerID] [int] NOT NULL
    )
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
```

Kör nu följande fråga på **kund** databasen för att skapa tabellen **CustomerInformation** och ange exempel data.

```tsql
CREATE TABLE [dbo].[CustomerInformation](
    [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NULL,
    [Company] [varchar](50) NULL
    CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
)
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')
```

## <a name="create-database-objects"></a>Skapa databas objekt

### <a name="database-scoped-master-key-and-credentials"></a>Huvud nyckel och autentiseringsuppgifter för databas omfattning

1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Anslut till order databasen och kör följande T-SQL-kommandon:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';  
    ```

    "Master_key_password" är ett starkt lösen ord som du väljer för att kryptera anslutningens autentiseringsuppgifter. 
    "Username" och "Password" ska vara användar namn och lösen ord som används för att logga in i kund databasen.
    Autentisering med Azure Active Directory med elastiska frågor stöds inte för närvarande.

### <a name="external-data-sources"></a>Externa datakällor

Om du vill skapa en extern data källa kör du följande kommando på order-databasen:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = RDBMS,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'Customers',
    CREDENTIAL = ElasticDBQueryCred,
) ;
```

### <a name="external-tables"></a>Externa tabeller

Skapa en extern tabell i order databasen som matchar definitionen för CustomerInformation-tabellen:

```tsql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NOT NULL,
    [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Köra ett exempel på en elastisk databas T-SQL-fråga

När du har definierat din externa data källa och dina externa tabeller kan du nu använda T-SQL för att fråga dina externa tabeller. Kör den här frågan på order databasen:

```tsql
SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
FROM OrderInformation
INNER JOIN CustomerInformation
ON CustomerInformation.CustomerID = OrderInformation.CustomerID
```

## <a name="cost"></a>Kostnad

För närvarande ingår funktionen för elastiska databas frågor i kostnaden för din Azure SQL Database.  

Information om priser finns i [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](elastic-query-overview.md).
* För syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](elastic-query-vertical-partitioning.md)
* En självstudie för horisontell partitionering (horisontell partitionering) finns i [komma igång med elastisk fråga för horisontell partitionering (horisontell partitionering)](elastic-query-getting-started.md).
* För syntax och exempel frågor för vågrätt partitionerade data, se [fråga efter vågrätt partitionerade data)](elastic-query-horizontal-partitioning.md)
* Se [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) för en lagrad procedur som kör ett Transact-SQL-uttryck på en enskild fjärr Azure SQL Database eller uppsättning databaser som fungerar som Shards i ett schema med vågrät partitionering.
