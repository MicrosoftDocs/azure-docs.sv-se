---
title: Importera och exportera data mellan server lös Apache Spark pooler och SQL-pooler
description: Den här artikeln innehåller information om hur du använder det anpassade anslutnings programmet för att flytta data mellan dedikerade SQL-pooler och Server lös Apache Spark pooler.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 66a804f645ee56cce62053a51e6aa4fc25b3c1a9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671330"
---
# <a name="introduction"></a>Introduktion

Azure Synapse-Apache Spark till Synapse SQL Connector är utformad för att effektivt överföra data mellan server lös Apache Spark pooler och dedikerade SQL-pooler i Azure Synapse. Azure Synapse-Apache Spark till Synapse SQL Connector fungerar bara på dedikerade SQL-pooler, den fungerar inte med SQL-poolen utan server.

> [!WARNING]
> **Sqlanalytics ()-** funktions namnet har ändrats till **synapsesql ()**. Sqlanalytics-funktionen fortsätter att fungera men kommer att bli inaktuell.  Ändra alla referenser från **sqlanalytics ()** till **synapsesql ()** för att förhindra störningar i framtiden.

## <a name="design"></a>Design

Överföring av data mellan Spark-pooler och SQL-pooler kan göras med JDBC. Men med tanke på två distribuerade system som Spark-och SQL-pooler, är JDBC att vara en Flask hals med seriell data överföring.

Azure Synapse Apache Spark-poolen till Synapse SQL Connector är en implementering av data källa för Apache Spark. Den använder Azure Data Lake Storage Gen2 och PolyBase i dedikerade SQL-pooler för att effektivt överföra data mellan Spark-klustret och den dedikerade Synapse SQL-instansen.

![Kopplings arkitektur](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autentisering i Azure Synapse Analytics

Autentisering mellan system sker sömlöst i Azure Synapse Analytics. Token-tjänsten ansluter med Azure Active Directory för att få säkerhetstoken som ska användas vid åtkomst till lagrings kontot eller data lager servern.

Därför behöver du inte skapa autentiseringsuppgifter eller ange dem i anslutnings-API: n så länge som Azure AD-Auth har kon figurer ATS på lagrings kontot och data lager servern. Annars kan SQL-autentisering anges. Mer information finns i [användnings](#usage) avsnittet.

## <a name="constraints"></a>Villkor

- Den här kopplingen fungerar bara i Scala.
- Information om pySpark finns i avsnittet [använda python](#use-pyspark-with-the-connector) .
- Den här anslutningen har inte stöd för frågor mot SQL-vyer.

## <a name="prerequisites"></a>Förutsättningar

- Måste vara medlem i **db_exporter** -rollen i den databas/SQL-pool som du vill överföra data till/från.
- Måste vara medlem i rollen Storage BLOB data Contributor på standard lagrings kontot.

Om du vill skapa användare ansluter du till databasen för SQL-poolen och följer de här exemplen:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Så här tilldelar du en roll:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Användning

Import instruktionerna är inte obligatoriska, de är redan importerade för den bärbara datorn.

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-attached-within-the-workspace"></a>Överföra data till eller från en dedikerad SQL-pool som är kopplad i arbets ytan

> [!NOTE]
> **Importer som inte behövs i Notebook-upplevelsen**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs-API

```scala
val df = spark.read.synapsesql("<DBName>.<Schema>.<TableName>")
```

Ovanstående API fungerar både för interna (hanterade) och externa tabeller i SQL-poolen.

#### <a name="write-api"></a>Skriv-API

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

Skriv-API: et skapar tabellen i den dedikerade SQL-poolen och anropar sedan PolyBase för att läsa in data.  Tabellen får inte finnas i den dedikerade SQL-poolen eller så returneras ett fel som anger att det redan finns ett objekt med namnet...

TableType-värden

- Konstanter. intern hanterad tabell i dedikerad SQL-pool
- Konstanter. extern extern tabell i dedikerad SQL-pool

SQL-pool-hanterad tabell

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Extern SQL-pool

Om du vill skriva till en dedikerad extern SQL-pool måste det finnas en extern DATA källa och ett externt fil FORMAT på den dedikerade SQL-poolen.  Mer information finns i [skapa en extern data källa](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) och [externa fil format](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) i en dedikerad SQL-pool.  Nedan visas exempel på hur du skapar en extern data källa och externa fil format i en dedikerad SQL-pool.

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

Ett externt CREDENTIAL-objekt är inte nödvändigt när du använder Azure Active Directory direktautentisering till lagrings kontot.  Se till att du är medlem i rollen "Storage BLOB data Contributor" på lagrings kontot.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    synapsesql("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-or-database-outside-the-workspace"></a>Överföra data till eller från en dedikerad SQL-pool eller databas utanför arbets ytan

> [!NOTE]
> Importer som inte behövs i Notebook-upplevelsen

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs-API

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv-API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-azure-ad"></a>Använd SQL-autentisering i stället för Azure AD

#### <a name="read-api"></a>Läs-API

För närvarande har kopplingen inte stöd för tokenbaserad autentisering till en dedikerad SQL-pool utanför arbets ytan. Du måste använda SQL-autentisering.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv-API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-pyspark-with-the-connector"></a>Använda PySpark med Connector

> [!NOTE]
> I det här exemplet får du bara den bärbara dator upplevelsen som är i åtanke.

Anta att du har en dataframe "pyspark_df" som du vill skriva till DW.

Skapa en temporär tabell med hjälp av dataframe i PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Kör en Scala-cell i den PySpark Notebook med MAGICS:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

scala_df.write.synapsesql("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

På samma sätt kan du i Läs scenariot läsa data med Scala och skriva till en temporär tabell och använda Spark SQL i PySpark för att fråga Temp-tabellen till en dataframe.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Tillåt att andra användare använder Azure-Synapse Apache Spark för att Synapse SQL-anslutning på din arbets yta

Du måste vara ägare av Storage BLOB-data på ADLS Gen2 lagrings konto som är anslutet till arbets ytan för att ändra behörigheter som saknas för andra. Se till att användaren har åtkomst till arbets ytan och behörigheterna för att köra antecknings böcker.

### <a name="option-1"></a>Alternativ 1

- Gör användaren till en Storage BLOB data-deltagare/-ägare

### <a name="option-2"></a>Alternativ 2

- Ange följande ACL: er i mappstrukturen:

| Mapp | / | synapse | arbetsytor  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Åtkomst behörigheter | --X | --X | --X | --X | --X | --X | -WX |
| Standard behörigheter | ---| ---| ---| ---| ---| ---| ---|

- Du bör kunna ACL: er alla mappar från "Synapse" och nedåt från Azure Portal. Om du vill ACL-rotmappen, följer du anvisningarna nedan.

- Ansluta till lagrings kontot som är anslutet till arbets ytan från Storage Explorer med Azure AD
- Välj ditt konto och ange ADLS Gen2-URL och standard fil system för arbets ytan
- När du kan se det lagrings konto som visas i listan högerklickar du på arbets ytan lista och väljer Hantera åtkomst
- Lägg till användaren i mappen/-mappen med åtkomst behörigheten "kör". Välj OK

> [!IMPORTANT]
> Se till att du inte väljer standard om du inte vill ha det.


## <a name="next-steps"></a>Nästa steg

- [Skapa en dedikerad SQL-pool med hjälp av Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Skapa en ny Apache Spark pool med hjälp av Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
