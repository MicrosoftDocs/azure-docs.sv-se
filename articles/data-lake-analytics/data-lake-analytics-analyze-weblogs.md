---
title: Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics
description: Lär dig hur du analyserar webbplats loggar med Azure Data Lake Analytics för att köra U-SQL-funktioner och-frågor.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: d2764e5e3994eb7b80a8e7cf62fb7f437323c97e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015306"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics
Lär dig hur du analyserar webbplats loggar med Data Lake Analytics, särskilt för att ta reda på vilka referenter som påträffades i fel när de försökte besöka webbplatsen.

## <a name="prerequisites"></a>Förutsättningar
* **Visual studio 2015 eller Visual studio 2013**.
* **[Data Lake-verktyg för Visual Studio](https://aka.ms/adltoolsvs)**.

    När Data Lake verktyg för Visual Studio har installerats visas ett **data Lake** objekt på **verktyg** -menyn i Visual Studio:

    ![Visual Studio-menyn i U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Grundläggande kunskaper om data Lake Analytics och data Lake verktyg för Visual Studio**. För att komma igång, se:

  * [Utveckla U-SQL-skript med hjälp av data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Ett Data Lake Analytics-konto.**  Se [skapa ett Azure Data Lake Analytics-konto](data-lake-analytics-get-started-portal.md).
* **Installera exempel data.** I Azure Portal öppnar du Data Lake Analytics konto och klickar på **exempel skript** på den vänstra menyn och sedan på **Kopiera exempel data**. 

## <a name="connect-to-azure"></a>Anslut till Azure
Innan du kan bygga och testa alla U-SQL-skript måste du först ansluta till Azure.

### <a name="to-connect-to-data-lake-analytics"></a>Så här ansluter du till Data Lake Analytics

1. Öppna Visual Studio.
2. Klicka på **Data Lake > alternativ och inställningar**.
3. Klicka på **Logga in**, eller **ändra användare** om någon har loggat in, och följ anvisningarna.
4. Klicka på **OK** för att stänga dialog rutan alternativ och inställningar.

### <a name="to-browse-your-data-lake-analytics-accounts"></a>Bläddra bland Data Lake Analytics-konton

1. Öppna **Server Explorer** från Visual Studio genom att trycka på **CTRL + ALT + S**.
2. Gå till **Server Explorer**, expandera **Azure** och expandera sedan **Data Lake Analytics**. En lista över dina Data Lake Analytics-konton visas om det finns några. Du kan inte skapa Data Lake Analytics konton från Studio. Information om hur du skapar ett konto finns i [komma igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md) eller [kom igång med Azure Data Lake Analytics med Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Utveckla U-SQL-program
Ett U-SQL-program är oftast ett U-SQL-skript. Mer information om U-SQL finns i [Kom igång med u-SQL](data-lake-analytics-u-sql-get-started.md).

Du kan lägga till ytterligare användardefinierade operatorer i programmet.  Mer information finns i [utveckla U-SQL-användardefinierade operatorer för data Lake Analytics jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md).

### <a name="to-create-and-submit-a-data-lake-analytics-job"></a>Skapa och skicka ett Data Lake Analytics-jobb

1. Klicka på **filen > nytt > projekt**.

2. Välj typen U-SQL-projekt.

   ![nytt U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klicka på **OK**. Visual Studio skapar en lösning med en Script.usql-fil.

4. Ange följande skript i filen script. usql:

   ```usql
   // Create a database for easy reuse, so you don't need to read from a file very time.
   CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

   // Create a Table valued function. TVF ensures that your jobs fetch data from he weblog file with the correct schema.
   DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
   CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
   RETURNS @result TABLE
   (
       s_date DateTime,
       s_time string,
       s_sitename string,
       cs_method string,
       cs_uristem string,
       cs_uriquery string,
       s_port int,
       cs_username string,
       c_ip string,
       cs_useragent string,
       cs_cookie string,
       cs_referer string,
       cs_host string,
       sc_status int,
       sc_substatus int,
       sc_win32status int,
       sc_bytes int,
       cs_bytes int,
       s_timetaken int
   )
   AS
   BEGIN

       @result = EXTRACT
           s_date DateTime,
           s_time string,
           s_sitename string,
           cs_method string,
           cs_uristem string,
           cs_uriquery string,
           s_port int,
           cs_username string,
           c_ip string,
           cs_useragent string,
           cs_cookie string,
           cs_referer string,
           cs_host string,
           sc_status int,
           sc_substatus int,
           sc_win32status int,
           sc_bytes int,
           cs_bytes int,
           s_timetaken int
       FROM @"/Samples/Data/WebLog.log"
       USING Extractors.Text(delimiter:' ');
       RETURN;
   END;

   // Create a table for storing referrers and status
   DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
   @weblog = SampleDBTutorials.dbo.WeblogsView();
   CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
   (
       INDEX idx1
       CLUSTERED(Year ASC)
       DISTRIBUTED BY HASH(Year)
   ) AS

   SELECT s_date.Year AS Year,
       s_date.Month AS Month,
       s_date.Day AS Day,
       cs_referer,
       sc_status,
       COUNT(DISTINCT c_ip) AS cnt
   FROM @weblog
   GROUP BY s_date,
           cs_referer,
           sc_status;
   ```

    Information om hur du använder U-SQL finns i [Kom igång med data Lake Analytics U-SQL-språk](data-lake-analytics-u-sql-get-started.md).

5. Lägg till ett nytt U-SQL-skript i projektet och ange följande:

   ```usql
   // Query the referrers that ran into errors
   @content =
       SELECT *
       FROM SampleDBTutorials.dbo.ReferrersPerDay
       WHERE sc_status >=400 AND sc_status < 500;

   OUTPUT @content
   TO @"/Samples/Outputs/UnsuccessfulResponses.log"
   USING Outputters.Tsv();
   ```

6. Växla tillbaka till det första U-SQL-skriptet och ange ditt analys konto bredvid knappen **Skicka** .

7. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skapa skript**. Verifiera resultatet i fönstret utdata.

8. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skicka skript**.

9. Kontrol lera att **Analytics-kontot** är det som du vill köra jobbet på och klicka sedan på **Skicka**. Resultat för skicka och jobblänk är tillgängliga i resultatfönstret för Data Lake-verktyg för Visual Studio när överföringen är klar.

10. Vänta tills jobbet har slutförts.  Om jobbet misslyckades är det troligt vis käll filen som saknas.  Se avsnittet krav i den här självstudien. Mer felsöknings information finns i [övervaka och felsöka Azure Data Lake Analytics-jobb](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    När jobbet har slutförts visas följande skärm:

    ![Data Lake Analytics analysera blogg TVF webbplats loggar](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Upprepa nu steg 7-10 för **Script1. usql**.

### <a name="to-see-the-job-output"></a>Visa jobbutdata

1. Från **Server Explorer**, expandera **Azure**, expandera **Data Lake Analytics**, expandera Data Lake Analytics-kontot, expandera **Lagringskonton**, högerklicka på standardkontot för lagring av Data Lake och klicka sedan på **Explorer**.
2. Dubbelklicka på **exempel** för att öppna mappen och dubbelklicka sedan på **utdata**.
3. Dubbelklicka på **UnsuccessfulResponses. log**.
4. Du kan också dubbelklicka på utdatafilen i diagramvyn för jobbet för att navigera direkt till utdata.

## <a name="next-steps"></a>Nästa steg
Om du vill komma igång med Data Lake Analytics med hjälp av olika verktyg, se:

* [Kom igång med Data Lake Analytics med Azure Portal](data-lake-analytics-get-started-portal.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av NET SDK](./data-lake-analytics-get-started-cli.md)