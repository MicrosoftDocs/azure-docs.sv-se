---
title: Använd Spark-anslutaren med Microsoft Azure SQL och SQL Server
description: Lär dig hur du använder Spark-anslutningen med Azure SQL Database, Azure SQL-hanterad instans och SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 2e01e74f5086f7f1eb7e85661fbd35f452d8dae8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790210"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Påskynda real tids analys i real tid med Spark-anslutaren
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Från och med sep 2020 behålls inte den här anslutningen aktivt. [Apache Spark Connector för SQL Server och Azure SQL](/sql/connect/spark/connector) är nu tillgängligt, med stöd för python-och R-bindningar, ett enklare sätt att använda gränssnittet för att massredigera data och många andra förbättringar. Vi rekommenderar starkt att du utvärderar och använder den nya anslutningen i stället för den här. Informationen om den gamla anslutningen (den här sidan) bevaras endast för arkivering.

Spark-anslutaren aktiverar databaser i Azure SQL Database, Azure SQL-hanterad instans och SQL Server att fungera som indata-eller utgående data för Spark-jobb. Det gör att du kan använda transaktions data i real tid i stor data analys och bevara resultat för ad hoc-frågor eller rapporter. Jämfört med den inbyggda JDBC-anslutningen ger den här anslutningen möjlighet att lägga till data i databasen. Det kan avsevärt från rad till rad med 10X för att 20x snabbare prestanda. Spark-anslutaren stöder Azure Active Directory (Azure AD)-autentisering för att ansluta till Azure SQL Database och Azure SQL-hanterad instans, så att du kan ansluta databasen från Azure Databricks med ditt Azure AD-konto. Den innehåller liknande gränssnitt med den inbyggda JDBC-anslutningen. Det är enkelt att migrera dina befintliga Spark-jobb till att använda den här nya anslutningen.

## <a name="download-and-build-a-spark-connector"></a>Hämta och bygg en spark-anslutning

GitHub-lagrings platsen för den gamla koppling som tidigare var länkade till från den här sidan behålls inte aktivt. I stället rekommenderar vi starkt att du utvärderar och använder den [nya anslutningen](https://github.com/microsoft/sql-spark-connector).

### <a name="official-supported-versions"></a>Officiella versioner som stöds

| Komponent                             | Version                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 eller senare           |
| Scala                                 | 2,10 eller senare            |
| Microsoft JDBC-drivrutin för SQL Server  | 6,2 eller senare             |
| Microsoft SQL Server                  | SQL Server 2008 eller senare |
| Azure SQL Database                    | Stöds                |
| Azure SQL Managed Instance            | Stöds                |

Spark-anslutaren använder Microsoft JDBC-drivrutinen för SQL Server för att flytta data mellan Spark-arbetsnoder och databaser:

Data flödet är följande:

1. Spark-huvudnoden ansluter till databaser i SQL Database eller SQL Server och läser in data från en speciell tabell eller använder en speciell SQL-fråga.
2. Spark-huvudnoden distribuerar data till arbetsnoder för omvandling.
3. Worker-noden ansluter till databaser som ansluter till SQL Database och SQL Server och skriver data till databasen. Användaren kan välja att använda rad-för-rad-infogning eller Mass infogning.

Följande diagram illustrerar data flödet.

   ![Diagrammet visar det beskrivna flödet, med en huvud nod som ansluter direkt till databasen och ansluter till tre arbetsnoder, som ansluter till databasen.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Bygg Spark-anslutningen

För närvarande använder kopplings projektet Maven. Om du vill skapa kopplingen utan beroenden kan du köra:

- mvn Clean-paket
- Hämta de senaste versionerna av JAR från mappen release
- Ta med SQL Database Spark-JAR

## <a name="connect-and-read-data-using-the-spark-connector"></a>Anslut och läsa data med Spark-anslutaren

Du kan ansluta till databaser i SQL Database och SQL Server från ett Spark-jobb för att läsa eller skriva data. Du kan också köra en DML-eller DDL-fråga i databaser i SQL Database och SQL Server.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Läs data från Azure SQL och SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Läs data från Azure SQL och SQL Server med angiven SQL-fråga

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Skriva data till Azure SQL och SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Köra DML-eller DDL-frågor i Azure SQL och SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Ansluta från Spark med Azure AD-autentisering

Du kan ansluta till Azure SQL Database-och SQL-hanterad instans med Azure AD-autentisering. Använd Azure AD-autentisering för att centralt hantera identiteter för databas användare och som ett alternativ till att SQL Server autentisering.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Ansluta med ActiveDirectoryPassword-autentiseringsläge

#### <a name="setup-requirement"></a>Krav för installation

Om du använder ActiveDirectoryPassword-autentiseringsläget måste du ladda ned [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java-build-sökvägen.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>Ansluta med hjälp av en åtkomsttoken

#### <a name="setup-requirement"></a>Krav för installation

Om du använder det åtkomst-token-baserade autentiseringsläget måste du ladda ned [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java-build-sökvägen.

Se [använda Azure Active Directory autentisering för autentisering](authentication-aad-overview.md) för att lära dig hur du hämtar en åtkomsttoken till databasen i Azure SQL Database eller Azure SQL-hanterad instans.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>Skriv data med Mass infogning

Den traditionella JDBC-anslutningen skriver data i databasen med hjälp av rad-för-rad-infogning. Du kan använda Spark-anslutningen för att skriva data till Azure SQL och SQL Server att använda Mass infogning. Det förbättrar markant skriv prestanda vid inläsning av stora data uppsättningar eller inläsning av data i tabeller där ett kolumn lagrings index används.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du hämta Spark-anslutaren från [Azure-SQLDB-Spark GitHub-lagringsplatsen](https://github.com/Azure/azure-sqldb-spark) och utforska de ytterligare resurserna i lagrings platsen:

- [Exempel på Azure Databricks antecknings böcker](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Exempel skript (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Du kanske också vill läsa [guiden Apache Spark SQL, DataFrames och data uppsättningar](https://spark.apache.org/docs/latest/sql-programming-guide.html) och [Azure Databricks-dokumentationen](/azure/azure-databricks/).