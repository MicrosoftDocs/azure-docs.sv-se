---
title: Kom igång med U-SQL-språk i Azure Data Lake Analytics
description: Lär dig grunderna för U-SQL-språket i Azure Data Lake Analytics. Skriv din första fråga med variabler för extra data från filer, transformera rad uppsättningen och samla in data.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/23/2017
ms.openlocfilehash: 5920f2b5823e568d56d13107200eb0025a654db0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219929"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Kom igång med U-SQL i Azure Data Lake Analytics

U-SQL är ett språk som kombinerar deklarativ SQL med tvingande C# så att du kan bearbeta data i valfri skala. Genom den skalbara distribuerade fråge funktionen i U-SQL kan du effektivt analysera data i Relations lager, till exempel Azure SQL Database. Med U-SQL kan du bearbeta ostrukturerade data genom att använda schema vid läsning och infoga anpassad logik och UDF: er. U-SQL innehåller dessutom utöknings barhet som ger dig detaljerad kontroll över hur du kör i skala.

## <a name="learning-resources"></a>Utbildningsresurser

* [U-SQL-kursen](https://aka.ms/usqltutorial) innehåller en guidad genom gång av det mesta av U-SQL-språket. Det här dokumentet rekommenderar läsning för alla utvecklare som vill lära sig U-SQL.
* Detaljerad information om **språksyntaxen i u-SQL** finns i [språk referens för u-SQL](/u-sql/).
* Information om **design filosofi i u-SQL** finns i Visual Studio-bloggen [Introduktion till U-SQL – ett språk som gör stor data bearbetning lätt](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Förutsättningar

Innan du går igenom U-SQL-exemplen i det här dokumentet, Läs och fullständig [självstudie: utveckla U-SQL-skript med hjälp av data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Den här självstudien förklarar Mechanics med att använda U-SQL med Azure Data Lake verktyg för Visual Studio.

## <a name="your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande U-SQL-skript är enkelt och låter oss utforska många aspekter av U-SQL-språket.

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Det här skriptet har inga omvandlings steg. Den läser från käll filen `SearchLog.tsv` , schematizes den, och skriver rad uppsättningen tillbaka till en fil med namnet SearchLog-first-u-sql.csv.

Lägg märke till frågetecknet bredvid data typen i `Duration` fältet. Det innebär att `Duration` fältet kan vara null.

### <a name="key-concepts"></a>Viktiga begrepp

* **Rad uppsättnings variabler**: varje frågeuttryck som skapar en rad uppsättning kan tilldelas till en variabel. U-SQL följer namngivnings mönstret T-SQL-variabeln ( `@searchlog` till exempel) i skriptet.
* Nyckelordet **Extract** läser data från en fil och definierar schemat vid läsning. `Extractors.Tsv` är en inbyggd U-SQL-Extractor för filer med Tabbavgränsade värden. Du kan utveckla anpassade Extraherare.
* **Utdata** skriver data från en rad uppsättning till en fil. `Outputters.Csv()` är en inbyggd U-SQL-utsparatillfil för att skapa en fil med kommaavgränsade värden. Du kan utveckla anpassade utsparatillfiler.

### <a name="file-paths"></a>Fil Sök vägar

Satserna EXTRACT och OUTPUT använder fil Sök vägar. Fil Sök vägar kan vara absoluta eller relativa:

Följande absoluta fil Sök väg refererar till en fil i en Data Lake Store med namnet `mystore` :

```usql
adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv
```

Följande fil Sök väg börjar med `"/"` . Den refererar till en fil i standard Data Lake Stores kontot:

```usql
/output/SearchLog-first-u-sql.csv
```

## <a name="use-scalar-variables"></a>Använda skalära variabler

Du kan även använda skalära variabler för att förenkla skript underhållet. Det tidigare U-SQL-skriptet kan också skrivas som:

```usql
DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
DECLARE @out string = "/output/SearchLog-scalar-variables.csv";
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv();
OUTPUT @searchlog
    TO @out
    USING Outputters.Csv();
```

## <a name="transform-rowsets"></a>Omvandla rad uppsättningar

Använd **Välj** för att omvandla rad uppsättningar:

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
OUTPUT @rs1
    TO "/output/SearchLog-transform-rowsets.csv"
    USING Outputters.Csv();
```

WHERE-satsen använder ett [booleskt C#-uttryck](/dotnet/csharp/language-reference/operators/index). Du kan använda C#-uttrycks språket för att skapa egna uttryck och funktioner. Du kan till och med utföra mer komplex filtrering genom att kombinera dem med logiska samANDser () och disknutor (ORs).

Följande skript använder metoden DateTime. parse () och en kombination.

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");
OUTPUT @rs1
    TO "/output/SearchLog-transform-datetime.csv"
    USING Outputters.Csv();
```

 >[!NOTE]
 >Den andra frågan körs på resultatet av den första rad uppsättningen, vilket skapar en sammansatt uppsättning av de två filtren. Du kan också återanvända ett variabel namn, och namnen begränsas till lexikalt.

## <a name="aggregate-rowsets"></a>Aggregerade rad uppsättningar

U-SQL ger dig den välkända ORDER BY-, GROUP BY-och agg regeringar.

Följande fråga söker efter total varaktighet per region och visar sedan de fem främsta varaktigheterna i ordning.

U-SQL-raduppsättningarna bevarar inte ordningen för nästa fråga. Om du vill beställa utdata måste du därför lägga till ORDER BY i utmatnings instruktionen:

```usql
DECLARE @outpref string = "/output/Searchlog-aggregation";
DECLARE @out1    string = @outpref+"_agg.csv";
DECLARE @out2    string = @outpref+"_top5agg.csv";
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
GROUP BY Region;
@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;
OUTPUT @rs1
    TO @out1
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
OUTPUT @res
    TO @out2
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

U-SQL ORDER BY-satsen kräver att du använder FETCH-satsen i ett SELECT-uttryck.

U-SQL HAVING-satsen kan användas för att begränsa utdata till grupper som uppfyller HAVING-villkoret:

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;
OUTPUT @res
    TO "/output/Searchlog-having.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

Avancerade samlings scenarier finns i referens dokumentationen för U-SQL för [funktioner för mängd, analys och referens](/u-sql/built-in-functions)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)