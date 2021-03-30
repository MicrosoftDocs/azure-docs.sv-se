---
title: Utöka U-SQL-skript med R i Azure Data Lake Analytics
description: Lär dig hur du kör R-kod i U-SQL-skript med hjälp av Azure Data Lake Analytics. Bädda in R-kod infogad eller referens från filer.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.openlocfilehash: ab28ba1bb6185f5f0ab90df99f0abea7895b1bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218008"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Utöka U-SQL-skript med R-kod i Azure Data Lake Analytics

I följande exempel visas de grundläggande stegen för att distribuera R-kod:

* Använd `REFERENCE ASSEMBLY` instruktionen för att aktivera R-tillägg för U-SQL-skriptet.
* Använd `REDUCE` åtgärden för att partitionera indata på en nyckel.
* R-tilläggen för U-SQL innehåller en inbyggd minskning ( `Extension.R.Reducer` ) som kör R-kod på varje hörn som tilldelas till minskningen.
* Användning av dedikerade namngivna data ramar som kallas respektive `inputFromUSQL` `outputToUSQL` för att skicka data mellan U-SQL och R. DataFrame identifierade ID: n för indata och utdata är fasta (det vill säga att användare inte kan ändra dessa fördefinierade namn på indata och utdata DataFrame Identifier).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Bädda in R-kod i U-SQL-skriptet

Du kan infoga R-koden till U-SQL-skriptet med hjälp av kommando parametern för `Extension.R.Reducer` . Du kan till exempel deklarera R-skriptet som en sträng variabel och skicka det som en parameter till minskningen.

```usql
REFERENCE ASSEMBLY [ExtR];

DECLARE @myRScript = @"
inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
#do not return readonly columns and make sure that the column names are the same in usql and r cripts,
outputToUSQL=data.frame(summary(lm.fit)$coefficients)
colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
outputToUSQL
";

@RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, ReturnType:"dataframe");
```

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Behåll R-koden i en separat fil och referera till den i U-SQL-skriptet

I följande exempel visas en mer komplicerad användning. I det här fallet distribueras R-koden som en resurs som är U-SQL-skriptet.

Spara den här R-koden som en separat fil.

```usql
load("my_model_LM_Iris.rda")
outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
```

Använd ett U-SQL-skript för att distribuera R-skriptet med DISTRIBUTIONs resurs instruktionen.

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
DECLARE @PartitionCount int = 10;
@InputData =
    EXTRACT
        SepalLength double,
        SepalWidth double,
        PetalLength double,
        PetalWidth double,
        Species string
    FROM @IrisData
    USING Extractors.Csv();
@ExtendedData =
    SELECT
        Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
        SepalLength,
        SepalWidth,
        PetalLength,
        PetalWidth
    FROM @InputData;
// Predict Species
@RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, fit double, lwr double, upr double
    READONLY Par
    USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
    OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();
```

## <a name="how-r-integrates-with-u-sql"></a>Hur R integreras med U-SQL

### <a name="datatypes"></a>Data typer

* Sträng och numeriska kolumner från U-SQL konverteras som de är mellan R DataFrame och U-SQL [typer som stöds: `double` ,,,, `string` `bool` `integer` `byte` ].
* `Factor`Data typen stöds inte i U-SQL.
* `byte[]` måste serialiseras som en Base64-kodad `string` .
* U-SQL-strängar kan konverteras till faktorer i R-kod, när U-SQL skapa R-dataframe eller genom att ange parametern för minskning `stringsAsFactors: true` .

### <a name="schemas"></a>Scheman

* U-SQL-datauppsättningar får inte ha dubbletter av kolumn namn.
* Kolumn namn för U-SQL-datauppsättningar måste vara strängar.
* Kolumn namn måste vara samma i U-SQL-och R-skript.
* ReadOnly-kolumnen kan inte ingå i utdata-dataframe. Eftersom ReadOnly-kolumner automatiskt matas in i U-SQL-tabellen om det är en del av schemat för UDO.

### <a name="functional-limitations"></a>Funktionella begränsningar

* R-motorn kan inte instansieras två gånger i samma process.
* U-SQL stöder för närvarande inte kombinations Katalogentiteter för förutsägelse med partitionerade modeller som genereras med hjälp av Katalogentiteter. Användare kan deklarera partitionerade modeller som resurser och använda dem i sina R-skript (se exempel kod `ExtR_PredictUsingLMRawStringReducer.usql` )

### <a name="r-versions"></a>R-versioner

Endast R 3.2.2 stöds.

### <a name="standard-r-modules"></a>Standard R-moduler

```usql
base
boot
Class
Cluster
codetools
compiler
datasets
doParallel
doRSR
foreach
foreign
Graphics
grDevices
grid
iterators
KernSmooth
lattice
MASS
Matrix
Methods
mgcv
nlme
Nnet
Parallel
pkgXMLBuilder
RevoIOQ
revoIpe
RevoMods
RevoPemaR
RevoRpeConnector
RevoRsrConnector
RevoScaleR
RevoTreeView
RevoUtils
RevoUtilsMath
Rpart
RUnit
spatial
splines
Stats
stats4
survival
Tcltk
Tools
translations
utils
XML
```

### <a name="input-and-output-size-limitations"></a>Storleks begränsningar för indata och utdata

Varje hörn har en begränsad mängd minne som tilldelats till den. Eftersom in-och utdata-DataFrames måste finnas i minnet i R-koden får den totala storleken för indata och utdata inte överstiga 500 MB.

### <a name="sample-code"></a>Exempelkod

Mer exempel kod finns i Data Lake Store-kontot när du har installerat avancerade analys tillägg för U-SQL. Sökvägen till fler exempel kod är: `<your_account_address>/usqlext/samples/R` .

## <a name="deploying-custom-r-modules-with-u-sql"></a>Distribuera anpassade R-moduler med U-SQL

Först skapar du en anpassad R-modul och zip-filen och laddar sedan upp den komprimerade R-anpassade modulen till ADL-butiken. I exemplet laddar vi upp magittr_1.5.zip till roten för standard kontot ADLS för det ADLA-konto som vi använder. När du har laddat upp modulen till ADL Store måste du deklarera den som Använd distribuera resurs för att göra den tillgänglig i U-SQL-skriptet och anropa `install.packages` för att installera den.

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/magrittr_1.5.zip";
DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";
// R script to run
DECLARE @myRScript = @"
# install the magrittr package,
install.packages('magrittr_1.5.zip', repos = NULL),
# load the magrittr package,
require(magrittr),
# demonstrate use of the magrittr package,
2 %>% sqrt
";
@InputData =
EXTRACT SepalLength double,
SepalWidth double,
PetalLength double,
PetalWidth double,
Species string
FROM @IrisData
USING Extractors.Csv();
@ExtendedData =
SELECT 0 AS Par,
*
FROM @InputData;
@RScriptOutput = REDUCE @ExtendedData ON Par
PRODUCE Par, RowId int, ROutput string
READONLY Par
USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");
OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();
```

## <a name="next-steps"></a>Nästa steg

* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda U-SQL-fönstrets funktioner för Azure Data Lake Analytics jobb](./data-lake-analytics-u-sql-get-started.md)