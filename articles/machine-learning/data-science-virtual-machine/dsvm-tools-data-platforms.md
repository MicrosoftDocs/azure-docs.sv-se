---
title: Data plattformar som stöds
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de data plattformar och verktyg som stöds för Azure Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 927e945a0d045abcd1caa2951dbd484224b2f425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519549"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data plattformar som stöds på Data Science Virtual Machine

Med en Data Science Virtual Machine (DSVM) kan du bygga upp din analys mot en mängd olika data plattformar. Förutom gränssnitt till fjärrdatakälla tillhandahåller DSVM en lokal instans för snabb utveckling och prototypering.

Följande data plattforms verktyg stöds på DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Kategori | Värde |
| ------------- | ------------- |
| Vad är det?   | En lokal Relations databas instans      |
| DSVM-versioner som stöds      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Vanliga användnings områden      | <ul><li>Snabb utveckling lokalt med mindre data uppsättning</li><li>Kör in-Database R</li></ul> |
| Länkar till exempel      | <ul><li>Ett litet exempel på en ny data uppsättning i Göteborg i Göteborg läses in i SQL-databasen:<br/>  `nyctaxi`</li><li>Jupyter-exempel som visar Microsoft Machine Learning Server och databas analys finns på:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| Relaterade verktyg på DSVM       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC-drivrutiner</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> SQL Server Developer Edition kan endast användas i utvecklings-och test syfte. Du behöver en licens eller någon av de virtuella datorerna SQL Server för att köra den i produktion.


### <a name="setup"></a>Konfiguration

Databas servern är redan förkonfigurerad och Windows-tjänster som är relaterade till SQL Server (som `SQL Server (MSSQLSERVER)` ) är inställda på att köras automatiskt. Det enda manuella steget innebär att du aktiverar databas analyser med hjälp av Microsoft Machine Learning Server. Du kan aktivera analys genom att köra följande kommando som en engångs åtgärd i SQL Server Management Studio (SSMS). Kör det här kommandot när du har loggat in som dator administratör, öppna en ny fråga i SSMS och kontrol lera att den valda databasen är `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Ersätt% COMPUTERNAME% med namnet på den virtuella datorn.)

Om du vill köra SQL Server Management Studio kan du söka efter "SQL Server Management Studio" i program listan eller använda Windows Search för att hitta och köra den. När du uppmanas att ange autentiseringsuppgifter väljer du **Windows-autentisering** och använder dator namnet eller ```localhost``` i fältet **SQL Server namn** .

### <a name="how-to-use-and-run-it"></a>Använda och köra den

Som standard körs databas servern med standard databas instansen automatiskt. Du kan använda verktyg som SQL Server Management Studio på den virtuella datorn för att få åtkomst till SQL Server databasen lokalt. Lokala administratörs konton har administratörs behörighet för databasen.

Dessutom levereras DSVM med ODBC-och JDBC-drivrutiner för att kommunicera med SQL Server, Azure SQL-databaser och Azure Synapse Analytics från program som har skrivits på flera språk, inklusive python och Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hur konfigureras den och installeras på DSVM? 

 SQL Server installeras på standard sätt. Den finns på `C:\Program Files\Microsoft SQL Server` . Machine Learning Server-instansen i databasen finns på `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . DSVM har också en separat fristående Machine Learning Server instans, som installeras på `C:\Program Files\Microsoft\R Server\R_SERVER` . Dessa två Machine Learning Server-instanser delar inte bibliotek.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2. x (fristående)

| Kategori | Värde |
| ------------- | ------------- |
| Vad är det?   | En fristående instans (enkel nod i processen) för den populära Apache Spark plattformen. ett system för snabb, storskalig data bearbetning och maskin inlärning     |
| DSVM-versioner som stöds      | Linux     |
| Vanliga användnings områden      | <ul><li>Snabb utveckling av Spark/PySpark-program lokalt med en mindre data uppsättning och senare distribution på stora Spark-kluster som Azure HDInsight</li><li>Test Microsoft Machine Learning Server Spark-kontext</li><li>Använd SparkML eller Microsofts [MMLSpark](https://github.com/Azure/mmlspark) -bibliotek med öppen källkod för att skapa ml-program</li></ul> |
| Länkar till exempel      |    Jupyter-exempel:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark-kontext):/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| Relaterade verktyg på DSVM       | <ul><li>PySpark, Scala</li><li>Jupyter (Spark/PySpark-kernel)</li><li>Microsoft Machine Learning Server, sparker, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Använd så här
Du kan skicka Spark-jobb på kommando raden genom att köra `spark-submit` `pyspark` kommandot eller. Du kan också skapa en Jupyter-anteckningsbok genom att skapa en ny antecknings bok med Spark-kärnan.

Du kan använda Spark från R genom att använda bibliotek som sparker, Sparklyr och Microsoft Machine Learning Server, som är tillgängliga på DSVM. Se pekare till exempel i föregående tabell.

### <a name="setup"></a>Konfiguration
Innan du kör i en spark-kontext i Microsoft Machine Learning Server på Ubuntu Linux DSVM-versionen måste du slutföra ett engångs inställnings steg för att aktivera en lokal enskild nod Hadoop HDFS-och garn-instans. Hadoop-tjänster installeras som standard men inaktive ras på DSVM. Om du vill aktivera dem kör du följande kommandon som rot första gången:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Du kan stoppa de Hadoop-relaterade tjänsterna när du inte längre behöver dem genom att köra ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Ett exempel som visar hur du utvecklar och testar fru i en fjärran sluten Spark-kontext (som är den fristående Spark-instansen på DSVM) finns i och är tillgänglig i `/dsvm/samples/MRS` katalogen.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hur konfigureras den och installeras på DSVM? 
|Plattform|Installations plats ($SPARK _HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotek för att komma åt data från Azure Blob Storage eller Azure Data Lake Storage, som använder Microsoft MMLSpark Machine-Learning-bibliotek, förinstalleras i $SPARK _HOME/Jars. Dessa JAR v7 läses automatiskt in när Spark startar. Som standard använder Spark data på den lokala disken. 

För Spark-instansen på DSVM för att komma åt data som lagras i Blob Storage eller Azure Data Lake Storage, måste du skapa och konfigurera `core-site.xml` filen baserat på mallen som finns i $SPARK _HOME/conf/core-site.xml. mall. Du måste också ha rätt autentiseringsuppgifter för att få åtkomst till Blob Storage och Azure Data Lake Storage. (Observera att mallfilerna använder plats hållare för Blob Storage och Azure Data Lake Storage konfigurationer.)

Mer detaljerad information om hur du skapar Azure Data Lake Storage autentiseringsuppgifter för tjänsten finns i [autentisering med Azure Data Lake Storage gen1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). När autentiseringsuppgifterna för Blob Storage eller Azure Data Lake Storage har angetts i core-site.xml-filen kan du referera till de data som lagras i dessa källor via URI-prefixet wasb://eller adl://.