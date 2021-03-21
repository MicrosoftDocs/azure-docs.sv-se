---
title: Förstå Apache Spark kod koncept för Azure Data Lake Analytics U-SQL-utvecklare.
description: I den här artikeln beskrivs Apache Spark begrepp som hjälper U-SQL-utvecklare att förstå Spark-koda koncept.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 2abd5882e310b17c633a82009f44624fad156f14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221136"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Förstå Apache Spark kod för U-SQL-utvecklare

Det här avsnittet innehåller rikt linjer för att transformera U-SQL-skript till Apache Spark.

- Den börjar med en [jämförelse av de två språkets bearbetnings paradigm](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Innehåller tips om hur du:
   - [Transformera skript](#transform-u-sql-scripts) , inklusive U-sqls [rad uppsättnings uttryck](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET-kod](#transform-net-code)
   - [Datatyper](#transform-typed-values)
   - [Katalog objekt](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Förstå U-SQL-och Spark-språket och bearbetnings paradigmen

Innan du börjar migrera Azure Data Lake Analytics U-SQL-skript till Spark, är det bra att förstå det allmänna språket och bearbeta idéer i de två systemen.

U-SQL är ett SQL-liknande deklarativ frågespråk som använder ett data flödes paradigm och gör att du enkelt kan bädda in och skala ut användar kod som skrivits i .NET (till exempel C#), python och R. Användar tilläggen kan implementera enkla uttryck eller användardefinierade funktioner, men kan även ge användaren möjlighet att implementera så kallade användardefinierade operatorer som implementerar anpassade operatörer för att utföra omvandlingar av rad uppsättnings nivåer, extraheringar och skrivning av utdata.

Spark är ett skalbart ramverk som erbjuder flera olika språk bindningar i Scala, Java, python, .NET osv. där du främst skriver din kod på något av dessa språk, skapar data abstraktioner som kallas elastiska distribuerade data uppsättningar (RDD), dataframes och data uppsättningar och använder sedan ett LINQ-liknande domänanslutet språk (DSL) för att transformera dem. Den innehåller också SparkSQL som ett deklarativt under språk på dataframe och data uppsättnings abstraktioner. DSL tillhandahåller två typer av åtgärder, omvandlingar och åtgärder. När du använder omvandlingar till data abstraktionerna körs inte omvandlingen utan i stället skapas den körnings plan som skickas för utvärdering med en åtgärd (till exempel att skriva resultatet till en temporär tabell eller fil eller att skriva ut resultatet).

När du översätter ett U-SQL-skript till ett Spark-program måste du därför bestämma vilket språk du vill använda för att minst generera data ramens abstraktion (som för närvarande är den mest använda dataabstraktionen) och om du vill skriva deklarativ data flödes omvandlingar med hjälp av DSL-eller SparkSQL. I vissa mer komplexa fall kan du behöva dela U-SQL-skriptet i en sekvens av Spark och andra steg som implementeras med Azure Batch eller Azure Functions.

Dessutom erbjuder Azure Data Lake Analytics U-SQL i en server lös jobb tjänst miljö, medan både Azure Databricks och Azure HDInsight erbjuder spark i form av en kluster tjänst. När du transformerar ditt program måste du ta hänsyn till konsekvenserna av att nu skapa, ändra storlek på, skala och ta ur bruk av klustren.

## <a name="transform-u-sql-scripts"></a>Transformera U-SQL-skript

U-SQL-skript följer följande bearbetnings mönster:

1. Data läses från antingen ostrukturerade filer med hjälp av `EXTRACT` instruktionen, en plats eller en fil uppsättnings specifikation och det inbyggda eller användardefinierade Extractor och det önskade schemat, eller från U-SQL-tabeller (hanterade eller externa tabeller). Den representeras som en rad uppsättning.
2. Rad uppsättningarna omvandlas till flera U-SQL-uttryck som använder U-SQL-uttryck för rad uppsättningar och genererar nya rad uppsättningar.
3. Slutligen matas de resulterande rad uppsättningarna in i antingen filer med hjälp av `OUTPUT` instruktionen som anger plats (er) och en inbyggd eller användardefinierad uttryckare eller i en U-SQL-tabell.

Skriptet utvärderas Lazy, vilket innebär att varje extraherings-och omvandlings steg består av ett uttrycks träd och globalt utvärderas (data flödet).

Spark-program fungerar på samma sätt som när du använder Spark-kopplingar för att läsa data och skapa dataframes. Använd sedan omvandlingarna på dataframes med antingen LINQ-like-DSL eller SparkSQL och skriv sedan resultatet i filer, tillfälliga Spark-tabeller, vissa typer av programmeringsspråk eller konsolen.

## <a name="transform-net-code"></a>Transformera .NET-kod

U-SQLs uttrycks språk är C# och erbjuder en mängd olika sätt att skala ut anpassad .NET-kod.

Eftersom Spark för närvarande inte har inbyggt stöd för att köra .NET-kod måste du antingen skriva om uttrycken till ett motsvarande Spark-, Scala-, Java-eller python-uttryck eller hitta ett sätt att anropa din .NET-kod. Om skriptet använder .NET-bibliotek har du följande alternativ:

- Översätt din .NET-kod till Scala eller python.
- Dela U-SQL-skriptet i flera steg, där du använder Azure Batch processer för att tillämpa .NET-transformationer (om du kan få en acceptabel skalning)
- Använd en .NET-språk bindning som är tillgänglig i öppen källkod som heter Moebius. Det här projektet är inte i ett tillstånd som stöds.

Om du har en stor mängd .NET-logik i dina U-SQL-skript kan du i alla fall kontakta oss via din Microsoft-representant för ytterligare vägledning.

Följande information gäller för olika fall av .NET-och C#-användning i U-SQL-skript.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Transformera uttryck för skalär infogad U-SQL C#

U-SQL: s uttrycks språk är C#. Många av de skalära U-SQL-uttrycken implementeras internt för bättre prestanda, medan mer komplexa uttryck kan köras genom anrop till .NET Framework.

Spark har sitt eget skalära uttrycks språk (antingen som en del av DSL-eller SparkSQL) och tillåter att du anropar användardefinierade funktioner skrivna på dess värd språk.

Om du har skalära uttryck i U-SQL bör du först hitta det mest lämpliga inbyggda Spark-uttrycket för att få ut mesta möjliga prestanda och sedan mappa de andra uttrycken till en användardefinierad funktion på det Spark-värd språk som du väljer.

Tänk på att .NET och C# har olika typer av semantik än Spark-värd språken och Spark: s DSL. Se [nedan](#transform-typed-values) för mer information om typ system skillnaderna.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Transformera användardefinierade skalära .NET-funktioner och användardefinierade agg regeringar

U-SQL innehåller metoder för att anropa valfria skalära .NET-funktioner och för att anropa användardefinierade agg regeringar som skrivits i .NET.

Spark erbjuder även stöd för användardefinierade funktioner och användardefinierade agg regeringar skrivna på de flesta värd språk som kan anropas från Spark: s DSL och SparkSQL.

### <a name="transform-user-defined-operators-udos"></a>Transformera användardefinierade operatorer (Katalogentiteter)

U-SQL innehåller flera kategorier av användardefinierade operatorer (katalogentiteter), till exempel Extraherare, utvinnare, avskärmare, processorer, appliers och kombinations som kan skrivas i .net (och-till viss utsträckning i python och R).

Spark erbjuder inte samma utöknings modell för operatörer, men har motsvarande funktioner för vissa.

Spark-motsvarigheten till extraktioner och utvinnare är Spark-kopplingar. För många U-SQL-Extraherare kan du hitta en motsvarande koppling i Spark-communityn. För andra måste du skriva en anpassad anslutning. Om U-SQL-inmatningen är komplex och använder flera .NET-bibliotek, kan det vara bättre att bygga en anslutning i Scala som använder interop för att anropa det .NET-bibliotek som utför den faktiska bearbetningen av data. I så fall måste du distribuera .NET Core-körningen till Spark-klustret och kontrol lera att de .NET-bibliotek som refereras är .NET standard 2,0-kompatibla.

De andra typerna av U-SQL-Katalogentiteter måste skrivas om med användardefinierade funktioner och agg regeringar och det semantiskt lämpliga Spark DLS-eller SparkSQL-uttrycket. En processor kan till exempel mappas till ett urval av en rad UDF-anrop, paketerade som en funktion som tar ett dataframe som argument och returnerar en dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Transformera U-SQLs valfria bibliotek

U-SQL innehåller en uppsättning tillvals-och demo bibliotek som erbjuder [python](data-lake-analytics-u-sql-python-extensions.md)-, [R](data-lake-analytics-u-sql-r-extensions.md)-, JSON-, XML-, [Avro-stöd](https://github.com/Azure/usql/tree/master/Examples/DataFormats)och vissa [funktioner för kognitiva tjänster](data-lake-analytics-u-sql-cognitive.md).

Spark erbjuder sin egen python-och R-integrering, pySpark respektive Sparkor och tillhandahåller kopplingar för att läsa och skriva JSON, XML och AVRO.

Om du behöver omvandla ett skript som refererar till kognitiva tjänst bibliotek rekommenderar vi att du kontaktar oss via din Microsoft-konto representant.

## <a name="transform-typed-values"></a>Transformera skrivna värden

Eftersom U-SQLs typ system baseras på .NET-typ systemet och Spark har sitt eget typ system, som påverkas av bindningen för värd språket, måste du se till att de typer som du arbetar med är nära och för vissa typer, typ intervall, precision och/eller skala kan vara något annorlunda. Dessutom behandlas U-SQL-och Spark- `null` värden på olika sätt.

### <a name="data-types"></a>Datatyper

Följande tabell innehåller motsvarande typer i Spark, Scala och PySpark för de tilldelade U-SQL-typerna.

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Mer information finns i:

- [org. apache. Spark. SQL. types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL-och DataFrames-typer](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Scala värde typer](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark. SQL. types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Behandling av NULL

I Spark, typer per standard Tillåt NULL-värden i U-SQL, markeras explicit skalär, icke-objekt som nullbar. Medan Spark låter dig definiera en kolumn som inte kan vara null kan den inte tillämpa begränsningen och [kan leda till fel resultat](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

I Spark anger NULL att värdet är okänt. Ett Spark-NULL-värde skiljer sig från vilket värde som helst, inklusive sig själv. Jämförelser mellan två Spark NULL-värden, eller mellan ett NULL-värde och ett annat värde, returnera okänt eftersom värdet för varje NULL är okänt.  

Det här beteendet skiljer sig från U-SQL, som följer C#-semantiken där `null` skiljer sig från vilket värde som helst men som är lika med sig själv.  

Därför returnerar en SparkSQL- `SELECT` instruktion som använder `WHERE column_name = NULL` noll rader även om det finns null-värden i `column_name` , medan i U-SQL returnerade raderna där `column_name` är inställt på `null` . På samma sätt returnerar en spark- `SELECT` instruktion som använder `WHERE column_name != NULL` noll rader även om det finns värden som inte är null i `column_name` , medan i U-SQL returnerade raderna som inte är null. Om du vill ha semantiken U-SQL null-kontroll bör du använda [IsNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) respektive [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (eller deras respektive DSL-motsvarighet).

## <a name="transform-u-sql-catalog-objects"></a>Transformera U-SQL Catalog-objekt

En stor skillnad är att U-SQL-skript kan använda sina katalog objekt, varav många har ingen direkt Spark-motsvarighet.

Spark ger stöd för Hive-koncept för metadata-lagring, huvudsakligen databaser och tabeller, så att du kan mappa U-SQL-databaser och-scheman till Hive-databaser och U-SQL-tabeller till Spark-tabeller (se [Flytta data som lagras i U-SQL-tabeller](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)), men det har inte stöd för vyer, tabell värdes funktioner (TVFs), lagrade procedurer, U-SQL-sammansättningar, externa data källor osv.

U-SQL-kodfragment, till exempel vyer, TVFs, lagrade procedurer och sammansättningar, kan modelleras genom kod funktioner och bibliotek i Spark och refereras till med hjälp av värd språket funktion och proceduriska abstraktions mekanismer (till exempel genom att importera python-moduler eller referera till Scala-funktioner).

Om U-SQL-katalogen har använts för att dela data och kod objekt mellan projekt och team, måste motsvarande mekanismer för delning användas (till exempel Maven för att dela kod objekt).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transformera U-SQL rowset-uttryck och SQL-baserade skalära uttryck

U-SQL: s kärn språk transformerar rad uppsättningar och baseras på SQL. Följande är en icke-fullständig lista över de vanligaste rad uppsättnings uttryck som finns i U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ Agg regeringar +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`uttryck
- `CROSS`/`OUTER``APPLY`uttryck
- `PIVOT`/`UNPIVOT` uttryck
- `VALUES` rad uppsättnings konstruktor

- Ange uttryck `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

U-SQL innehåller dessutom en rad SQL-baserade skalära uttryck som

- `OVER` fönster uttryck
- en mängd inbyggda agg regeringar och rangordnings funktioner ( `SUM` `FIRST` osv.)
- Några av de mest välkända SQL skalära uttrycken: `CASE` , `LIKE` , ( `NOT` ) `IN` , `AND` , `OR` osv.

Spark erbjuder motsvarande uttryck i både ett DSL-och SparkSQL-formulär för de flesta av dessa uttryck. Vissa uttryck som inte stöds internt i Spark måste skrivas om med en kombination av de interna Spark-uttrycken och semantiskt motsvarande mönster. Till exempel `OUTER UNION` måste översättas till motsvarande kombination av projektioner och unioner.

På grund av olika hantering av NULL-värden matchar en U-SQL-koppling alltid en rad om båda kolumnerna som jämförs innehåller ett NULL-värde, medan en koppling i Spark inte matchar sådana kolumner om explicita null-kontroller har lagts till.

## <a name="transform-other-u-sql-concepts"></a>Transformera andra U-SQL-begrepp

U-SQL erbjuder också en mängd andra funktioner och begrepp, till exempel federerade frågor mot SQL Server databaser, parametrar, skalära variabler och lambda-uttryck, systemvariabler, `OPTION` tips.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Federerade frågor mot SQL Server databaser/externa tabeller

U-SQL tillhandahåller data källa och externa tabeller samt direkta frågor mot Azure SQL Database. Medan Spark inte erbjuder samma objekt abstraktion, tillhandahåller [Spark-koppling för Azure SQL Database](../azure-sql/database/spark-connector.md) som kan användas för att fråga SQL-databaser.

### <a name="u-sql-parameters-and-variables"></a>U-SQL-parametrar och variabler

Parametrar och användarvariabler har likvärdiga begrepp i Spark och deras värd språk.

I Scala kan du till exempel definiera en variabel med `var` nyckelordet:

```
var x = 2 * 3;
println(x)
```

U-SQL: s Systemvariabler (variabler som börjar med `@@` ) kan delas upp i två kategorier:

- Ange systemvariabler som kan anges till angivna värden för att påverka skript beteendet
- Informations system variabler som frågar system-och jobb nivå information

De flesta av de inställbara systemvariablerna har ingen direkt motsvarighet i Spark. Några av de informations system variabler som kan modelleras genom att skicka informationen som argument under jobb körningen kan andra ha motsvarande funktion i Spark: s värd språk.

### <a name="u-sql-hints"></a>U-SQL-tips

U-SQL erbjuder flera syntaktiska sätt att tillhandahålla tips till optimerings-och körnings motorn för frågor:  

- Ange en U-SQL system variabel
- en `OPTION` sats som är kopplad till rad uppsättnings uttrycket för att tillhandahålla ett data-eller plan tips
- ett JOIN-tips i syntaxen för Join-uttrycket (till exempel `BROADCASTLEFT` )

Spark: s kostnadsbaserade fråge optimering har sina egna funktioner för att tillhandahålla tips och finjustera frågeresultatet. Se motsvarande dokumentation.

## <a name="next-steps"></a>Nästa steg

- [Förstå Spark data format för U-SQL-utvecklare](understand-spark-data-formats.md)
- [.NET för Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Uppgradera dina Big data Analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Transformera data med Spark-aktivitet i Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformera data med Hadoop Hive-aktivitet i Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Vad är Apache Spark i Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)