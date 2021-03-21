---
title: Guide för U-SQL-programmering för Azure Data Lake
description: Lär dig mer om U-SQL-översikt och UDF-programmering i Azure Data Lake Analytics så att du kan skapa ett utmärkt USQL-skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96510870"
---
# <a name="u-sql-programmability-guide-overview"></a>Översikt över U-SQL-programmerings guide

U-SQL är ett frågespråk som är utformat för stora data typer av arbets belastningar. En av de unika funktionerna i U-SQL är kombinationen av SQL-liknande deklarativ språk med utöknings barhet och programmerbar het som tillhandahålls av C#. I den här guiden fokuserar vi på utöknings barhet och programmerbar het för det U-SQL-språk som har Aktiver ATS av C#.

## <a name="requirements"></a>Krav

Hämta och installera [Azure Data Lake verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Kom igång med U-SQL  

Titta på följande U-SQL-skript:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Det här skriptet definierar två rad uppsättningar: `@a` och `@results` . Rad uppsättningen `@results` definieras från `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-typer och-uttryck i U-SQL-skript

Ett U-SQL-uttryck är ett C#-uttryck kombinerat med logiska U-SQL-åtgärder `AND` , `OR` och `NOT` . U-SQL-uttryck kan användas med SELECT, EXTRACT, WHERE, HAVING, GROUP BY och DECLARE. Till exempel parsar följande skript en sträng som ett DateTime-värde.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Följande kodfragment parsar en sträng som DateTime-värde i en DECLARE-instruktion.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Använda C#-uttryck för data typ konverteringar

Följande exempel visar hur du kan göra en datetime-datakonvertering med hjälp av C#-uttryck. I det här specifika scenariot konverteras sträng DatumTid-data till standard-datetime med tids notation midnatt 00:00:00.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Använd C#-uttryck för dagens datum

För att hämta dagens datum kan vi använda följande C#-uttryck: `DateTime.Now.ToString("M/d/yyyy")`

Här är ett exempel på hur du använder det här uttrycket i ett skript:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Använda .NET-sammansättningar

U-SQL: s utöknings modell är mycket beroende av möjligheten att lägga till anpassad kod från .NET-sammansättningar. 

### <a name="register-a-net-assembly"></a>Registrera en .NET-sammansättning

Använd `CREATE ASSEMBLY` instruktionen för att placera en .net-sammansättning i en U-SQL Database. Därefter kan U-SQL-skript använda dessa sammansättningar med hjälp av `REFERENCE ASSEMBLY` instruktionen. 

Följande kod visar hur du registrerar en sammansättning:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Följande kod visar hur du refererar till en sammansättning:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Mer information finns i [anvisningarna för monterings registrering](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) som täcker det här avsnittet.


### <a name="use-assembly-versioning"></a>Använd sammansättnings version
För närvarande använder U-SQL .NET Framework-versionen 4.7.2. Se till att dina egna sammansättningar är kompatibla med den versionen av körnings miljön.

Som tidigare nämnts kör U-SQL kod i ett 64-bitars format (x64). Se därför till att din kod kompileras för att köras på x64. Annars får du ett felaktigt format fel som visas tidigare.

Varje Uppladdad sammansättnings-DLL och resurs fil, till exempel en annan körning, en intern sammansättning eller en konfigurations fil, kan vara högst 400 MB. Den totala storleken på distribuerade resurser, antingen via distribuera resurs eller via referenser till sammansättningar och deras ytterligare filer, får inte överstiga 3 GB.

Observera att varje U-SQL-databas bara kan innehålla en version av en specifik sammansättning. Om du till exempel behöver både version 7 och version 8 av NewtonSoft Json.NET-biblioteket måste du registrera dem i två olika databaser. Dessutom kan varje skript bara referera till en version av en specifik sammansättnings-DLL. I detta avseende är U-SQL följande semantik för sammansättnings hantering och versions hantering i C#.

## <a name="use-user-defined-functions-udf"></a>Använd användardefinierade funktioner: UDF
U-SQL-användardefinierade funktioner eller UDF, är programmerings rutiner som accepterar parametrar, utför en åtgärd (till exempel en komplex beräkning) och returnerar resultatet av åtgärden som ett värde. Returvärdet för UDF kan bara vara en enda skalär. U-SQL UDF kan anropas i U-SQL Base-skript, precis som vilken annan C#-skalär funktion som helst.

Vi rekommenderar att du initierar U-SQL-användardefinierade funktioner som **offentliga** och **statiska**.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Först ska vi titta på det enkla exemplet med att skapa en UDF.

I det här scenariot för användnings fall måste vi fastställa räkenskaps perioden, inklusive räkenskaps kvartal och räkenskaps månad för den första inloggningen för den aktuella användaren. Den första räkenskaps månaden för året i vårt scenario är juni.

För att beräkna räkenskaps perioden introducerar vi följande C#-funktion:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Den beräknar bara räkenskaps månad och kvartal och returnerar ett sträng värde. I juni, den första månaden i det första räkenskaps kvartalet använder vi "Q1: P1". För juli använder vi "Q1: P2" och så vidare.

Det här är en vanlig C#-funktion som vi ska använda i vårt U-SQL-projekt.

Så här ser avsnittet bakomliggande kod ut i det här scenariot:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Nu ska vi anropa den här funktionen från det grundläggande U-SQL-skriptet. För att göra detta måste vi tillhandahålla ett fullständigt kvalificerat namn för funktionen, inklusive namn området, som i det här fallet är namnrymd. class. function (parameter).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Följande är det faktiska U-SQL-bas skriptet:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Följande är utdatafilen för skript körningen:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Det här exemplet visar en enkel användning av infogad UDF i U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Behåll tillstånd mellan UDF-anrop
U-SQL C# programmerbara objekt kan vara mer avancerade och använda interaktivitet genom bakomliggande globala variabler i bakomliggande kod. Nu ska vi titta på följande scenario för affärs användnings fall.

I stora organisationer kan användare byta mellan olika sorters interna program. Dessa kan omfatta Microsoft Dynamics CRM, Power BI och så vidare. Kunder kanske vill använda en telemetri analys av hur användare växlar mellan olika program, vad användnings trender är och så vidare. Målet för verksamheten är att optimera program användningen. De kan också vilja kombinera olika program eller speciella inloggnings rutiner.

För att uppnå det här målet måste vi fastställa sessions-ID: n och fördröjnings tiden mellan den senaste sessionen som inträffat.

Vi måste hitta en tidigare inloggning och sedan tilldela den här inloggningen till alla sessioner som genereras till samma program. Den första utmaningen är att bas skriptet U-SQL inte tillåter att vi använder beräkningar över redan beräknade kolumner med funktionen fördröjning. Den andra utmaningen är att vi måste behålla den aktuella sessionen för alla sessioner inom samma tids period.

För att lösa det här problemet använder vi en global variabel i ett avsnitt med bakomliggande kod: `static public string globalSession;` .

Den här globala variabeln tillämpas på hela rad uppsättningen vid skript körningen.

Här är avsnittet bakomliggande kod i vårt U-SQL-program:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

I det här exemplet visas den globala variabel `static public string globalSession;` som används i `getStampUserSession` funktionen och ominitieras varje gång parametern session ändras.

U-SQL Base-skriptet är följande:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Funktionen `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` anropas här under beräkningen av den andra minnes rad uppsättningen. `UserSessionTimestamp`Kolumnen skickas och värdet returneras tills `UserSessionTimestamp` har ändrats.

Utdatafilen är följande:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Det här exemplet visar en mer komplicerad användnings Falls situation där vi använder en global variabel i ett bakomliggande kod-avsnitt som tillämpas på hela minnes rad uppsättningen.

## <a name="next-steps"></a>Nästa steg
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [Guide för U-SQL-programmering – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)