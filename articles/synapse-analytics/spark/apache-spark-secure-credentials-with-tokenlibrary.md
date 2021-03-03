---
title: Säkra autentiseringsuppgifter för åtkomst med länkade tjänster i Apache Spark för Azure Synapse Analytics
description: Den här artikeln innehåller begrepp för hur du på ett säkert sätt kan integrera Apache Spark för Azure Synapse Analytics med andra tjänster med hjälp av länkade tjänster och token-bibliotek
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693585"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Skydda autentiseringsuppgifter med länkade tjänster med TokenLibrary

Att komma åt data från externa källor är ett vanligt mönster. Om inte den externa data källan tillåter anonym åtkomst måste du skydda anslutningen med en autentiseringsuppgift, hemlig eller anslutnings sträng.  

Synapse använder Azure Active Directory (AAD)-passthrough som standard för autentisering mellan resurser.  Om du behöver ansluta till en resurs med andra autentiseringsuppgifter använder du TokenLibrary direkt.  TokenLibrary fören klar processen att hämta SAS-token, AAD-token, anslutnings strängar och hemligheter som lagras i en länkad tjänst eller från en Azure Key Vault.

När du hämtar hemligheter från Azure Key Vault rekommenderar vi att du skapar en länkad tjänst till din Azure Key Vault.  Kontrol lera att MSI (Synapse-arbetsytans hanterade tjänst identitet) har hemliga privilegier för att få åtkomst till din Azure Key Vault.  Synapse kommer att autentiseras för Azure Key Vault med hjälp av Synapse-arbetsytans hanterade tjänst identitet. Om du ansluter direkt till Azure Key Vault utan en länkad tjänst autentiseras du med hjälp av din användar Azure Active Directory autentiseringsuppgifter.

Mer information finns i [länkade tjänster](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Användning

### <a name="tokenlibraryhelp"></a>TokenLibrary. Help ()
Den här funktionen visar hjälp dokumentationen för TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary för Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>ADLS Gen2 primär lagring

Åtkomst till filer från den primära Azure Data Lake Storage använder Azure Active Directory genom strömning för autentisering som standard och kräver inte explicit användning av TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>ADLS Gen2 lagring med länkade tjänster

Synapse tillhandahåller en integrerad länkad tjänst upplevelse vid anslutning till Azure Data Lake Storage Gen2.  Länkade tjänster kan konfigureras att autentiseras med hjälp av en **konto nyckel**, **tjänstens huvud namn**, en **hanterad identitet** eller **autentiseringsuppgift**.

När den länkade tjänstens autentiseringsmetod har angetts till **konto nyckeln** autentiserar den länkade tjänsten med den angivna lagrings konto nyckeln, begär en SAS-nyckel och tillämpar den automatiskt på lagrings förfrågan med hjälp av **LinkedServiceBasedSASProvider**.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

När den länkade tjänstens autentiseringsmetod har angetts till **hanterad identitet** eller **tjänstens huvud namn**, använder den länkade tjänsten den hanterade identiteten eller tjänstens huvud namns-token med **LinkedServiceBasedTokenProvider** -providern.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2 lagring (utan länkade tjänster)

Anslut till ADLS Gen2 lagring direkt med hjälp av en SAS-nyckel Använd **ConfBasedSASProvider** och ange SAS-nyckeln för konfigurations inställningen **Spark. Storage. Synapse. SAS** .

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>ADLS Gen2 lagring med Azure Key Vault

Anslut till ADLS Gen2 lagring med hjälp av en SAS-token lagrad i Azure Key Vault hemlighet.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary för andra länkade tjänster

Om du vill ansluta till andra länkade tjänster kan du göra ett direkt anrop till TokenLibrary.

#### <a name="getconnectionstring"></a>getConnectionString()

 Hämta anslutnings strängen med hjälp av **getConnectionString** -funktionen och skicka den **länkade tjänstens namn**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

GetConnectionStringAsMap är en hjälp funktion som är tillgänglig i Scala och python för att parsa vissa värden från ett _nyckel = värde_ -par i anslutnings strängen, till exempel

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

Använd funktionen **getConnectionStringAsMap** och skicka nyckeln för att returnera värdet.  I ovanstående anslutnings sträng exempel 

_**TokenLibrary. getConnectionStringAsMap ("DefaultEndpointsProtocol")**_

Returnerar

**_https_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Om du vill hämta en hemlighet som lagras från Azure Key Vault rekommenderar vi att du skapar en länkad tjänst som ska Azure Key Vault i arbets ytan Synapse. Synapse-arbetsytans hanterade tjänst identitet måste beviljas **Get** -behörighet till Azure Key Vault.  Den länkade tjänsten använder den hanterade tjänst identiteten för att ansluta till Azure Key Vault tjänst för att hämta hemligheten.  Annars används användarens Azure Active Directory-autentiseringsuppgift (AAD) för att ansluta direkt till Azure Key Vault.  I det här fallet måste användaren beviljas behörigheten Hämta hemlighet i Azure Key Vault.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Om du vill hämta en hemlighet från Azure Key Vault använder du funktionen **TokenLibrary. getSecret ()** .

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Nästa steg

- [Skriv till dedikerad SQL-pool](./synapse-spark-sql-pool-import-export.md)
