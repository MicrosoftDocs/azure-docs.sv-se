---
title: Kör U-SQL-jobb i python, R och C# – Azure Data Lake Analytics
description: Lär dig hur du använder kod bakom python, R och C# för att skicka jobb i Azure Data Lake.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/22/2017
ms.custom: devx-track-python
ms.openlocfilehash: d6066bd6ec2a4c986ae17ad0cce3e7f6f73b21e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219980"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Utveckla U-SQL med python, R och C# för Azure Data Lake Analytics i Visual Studio Code
Lär dig hur du använder Visual Studio Code (VSCode) för att skriva python-, R-och C#-kod bakom med U-SQL och skicka jobb till Azure Data Lake-tjänsten. Mer information om Azure Data Lake verktyg för VSCode finns i [använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Innan du skriver kod bakom anpassad kod måste du öppna en mapp eller en arbets yta i VSCode.


## <a name="prerequisites-for-python-and-r"></a>Krav för python och R
Registrera python-och R Extensions-sammansättningar för ditt ADL-konto. 
1. Öppna ditt konto i portalen.
   - Välj **Översikt**. 
   - Klicka på **exempel skript**.
2. Klicka på **Mer**.
3. Välj **Installera U-SQL-tillägg**. 
4. Bekräftelse meddelande visas efter att U-SQL-tilläggen har installerats. 

   ![Konfigurera miljön för python och R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > För bästa möjliga upplevelse av python-och R-språktjänsten installerar du VSCode python och R Extension. 

## <a name="develop-python-file"></a>Utveckla python-fil
1. Klicka på den **nya filen** i din arbets yta.
2. Skriv koden i U-SQL. Följande är ett kod exempel.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Högerklicka på en skript fil och välj sedan **ADL: generera python-kod bakom fil**. 
4. **Xxx.usql.py** -filen genereras i arbetsmappen. Skriv koden i python-filen. Följande är ett kod exempel.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Högerklicka på **USQL** -filen, du kan klicka på **kompilera skript** eller **Skicka jobb** till jobbet som körs.

## <a name="develop-r-file"></a>Utveckla R-fil
1. Klicka på den **nya filen** i din arbets yta.
2. Skriv koden i U-SQL-filen. Följande är ett kod exempel.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Högerklicka på filen **USQL** och välj sedan **ADL: generera R-kod bakom fil**. 
4. Filen **xxx. usql. r** skapas i arbetsmappen. Skriv koden i R-filen. Följande är ett kod exempel.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Högerklicka på **USQL** -filen, du kan klicka på **kompilera skript** eller **Skicka jobb** till jobbet som körs.

## <a name="develop-c-file"></a>Utveckla C#-fil
En fil med bakomliggande kod är en C#-fil som associeras med ett enda U-SQL-skript. Du kan definiera ett skript som är avsett för UDO, UDA, UDT och UDF i filen bakomliggande kod. UDO, UDA, UDT och UDF kan användas direkt i skriptet utan att först registrera sammansättningen. Filen bakomliggande kod placeras i samma mapp som dess peering U-SQL-skriptfil. Om skriptet heter xxx. usql, namnges bakomliggande kod som xxx. usql. cs. Om du manuellt tar bort filen med bakomliggande kod är funktionen bakomliggande kod inaktive rad för det associerade U-SQL-skriptet. Mer information om hur du skriver kund kod för U-SQL-skript finns i [skriva och använda anpassad kod i U-SQL: User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klicka på den **nya filen** i din arbets yta.
2. Skriv koden i U-SQL-filen. Följande är ett kod exempel.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Högerklicka på filen **USQL** och välj sedan **ADL: generera CS-kod bakom fil**. 
4. Filen **xxx. usql. cs** skapas i arbetsmappen. Skriv koden i CS-fil. Följande är ett kod exempel.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Högerklicka på **USQL** -filen, du kan klicka på **kompilera skript** eller **Skicka jobb** till jobbet som körs.

## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL lokal körning och lokal fel sökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Kom igång med Data Lake Analytics med PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
* [Använd Data Lake verktyg för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda Data Lake Analytics-katalogen (U-SQL)](./data-lake-analytics-u-sql-get-started.md)