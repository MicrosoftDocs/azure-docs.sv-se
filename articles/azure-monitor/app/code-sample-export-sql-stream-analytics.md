---
title: Exportera till SQL från Azure Application Insights | Microsoft Docs
description: Exportera Application Insights data kontinuerligt till SQL med Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 25d28bb0fc35ef76231c085dc1d9d8a1234a264c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587758"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Genom gång: exportera till SQL från Application Insights med Stream Analytics
Den här artikeln visar hur du flyttar dina telemetridata från [Azure Application Insights][start] till Azure SQL Database genom att använda [kontinuerlig export][export] och [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Vid kontinuerlig export flyttas dina telemetridata till Azure Storage i JSON-format. Vi kommer att parsa JSON-objekten med Azure Stream Analytics och skapa rader i en databas tabell.

(Mer allmänt är att kontinuerlig export är ett sätt att göra din egen analys av telemetri som dina appar skickar till Application Insights. Du kan anpassa det här kod exemplet för att göra andra saker med den exporterade Telemetrin, till exempel agg regerings data.)

Vi börjar med antagandet att du redan har den app som du vill övervaka.

I det här exemplet kommer vi att använda sid visnings data, men samma mönster kan enkelt utökas till andra data typer, till exempel anpassade händelser och undantag. 

## <a name="add-application-insights-to-your-application"></a>Lägg till Application Insights i ditt program
Så här kommer du igång:

1. [Konfigurera Application Insights för dina webb sidor](./javascript.md). 
   
    (I det här exemplet fokuserar vi på bearbetning av sid visnings data från klientens webbläsare, men du kan också konfigurera Application Insights för Server sidan i [Java](./java-get-started.md) -eller [ASP.net](./asp-net.md) -appen och bearbeta begäran, beroende och annan server telemetri.)
2. Publicera din app och se telemetri-data som visas i din Application Insights-resurs.

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Kontinuerlig export matar alltid ut data till ett Azure Storage-konto, så du måste skapa lagringen först.

1. Skapa ett lagrings konto i din prenumeration i [Azure Portal][portal].
   
    ![I Azure Portal väljer du ny, data, lagring. Välj klassisk och sedan skapa. Ange ett lagrings namn.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Skapa en container
   
    ![I det nya lagrings utrymmet väljer du behållare, klickar på behållare-panelen och lägger sedan till](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Kopiera lagrings åtkomst nyckeln
   
    Du behöver det snart att konfigurera indatan till Stream Analytics-tjänsten.
   
    ![I lagrings utrymmet öppnar du inställningar, nycklar och tar en kopia av den primära åtkomst nyckeln](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta kontinuerlig export till Azure Storage
1. I Azure Portal bläddrar du till den Application Insights resurs som du har skapat för ditt program.
   
    ![Välj Bläddra, Application Insights, ditt program](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Skapa en löpande export.
   
    ![Välj inställningar, löpande export, Lägg till](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Välj det lagrings konto som du skapade tidigare:

    ![Ange export målet](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Ange de händelse typer som du vill se:

    ![Välj händelse typer](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Låt vissa data ackumuleras. Luta dig tillbaka och låt användarna använda programmet en stund. Telemetri kommer in och du ser statistik diagram i [Metric Explorer](../essentials/metrics-charts.md) och enskilda händelser i [diagnostisk sökning](./diagnostic-search.md). 
   
    Informationen kommer också att exporteras till lagringen. 
2. Granska exporterade data, antingen i portalen – Välj **Bläddra**, Välj ditt lagrings konto och sedan **behållare** – eller i Visual Studio. I Visual Studio väljer du **Visa/Cloud Explorer** och öppna Azure/Storage. (Om du inte har det här meny alternativet måste du installera Azure SDK: öppna dialog rutan nytt projekt och öppna Visual C#/Cloud/Get Microsoft Azure SDK för .NET.)
   
    ![Öppna Server webbläsare, Azure, lagring i Visual Studio](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Anteckna den gemensamma delen av Sök vägs namnet, som härleds från program namnet och Instrumentation-nyckeln. 

Händelserna skrivs till BLOB-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Vi skulle läsa händelse data och filtrera bort de fält som vi vill. Det finns alla typer av saker som vi kan göra med data, men vårt abonnemang är idag att använda Stream Analytics för att flytta data till SQL Database. Det gör det enkelt att köra många intressanta frågor.

## <a name="create-an-azure-sql-database"></a>Skapa en Azure SQL Database
När du har börjat starta från din prenumeration i [Azure Portal][portal]skapar du databasen (och en ny server, om du inte redan har en) som du skriver data till.

![Ny, data, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Kontrol lera att servern tillåter åtkomst till Azure-tjänster:

![Bläddra, servrar, Server, inställningar, brand vägg, Tillåt åtkomst till Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Skapa en tabell i Azure SQL Database
Anslut till databasen som du skapade i föregående avsnitt med det önskade hanterings verktyget. I den här genom gången ska vi använda [hanteringsverktyg för SQL Server](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

![Ansluta till Azure SQL Database](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Skapa en ny fråga och kör följande T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![Skapa PageViewsTable](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

I det här exemplet använder vi data från sid visningar. Om du vill se andra tillgängliga data kontrollerar du JSON-utdata och går till [Exportera data modell](./export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en Azure Stream Analytics-instans
Välj tjänsten Azure Stream Analytics från [Azure Portal](https://portal.azure.com/)och skapa ett nytt Stream Analytics jobb:

![Skärm bild som visar sidan Stream Analytics-jobb med knappen Skapa markerad.](./media/code-sample-export-sql-stream-analytics/SA001.png)

![Nytt Stream Analytics-jobb](./media/code-sample-export-sql-stream-analytics/SA002.png)

När det nya jobbet skapas väljer **du gå till resurs**.

![Skärm bild som visar meddelandet slutförd distribution och gå till resurs knappen.](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Lägg till en ny inmatare

![Skärm bild som visar sidan indata med knappen Lägg till markerad.](./media/code-sample-export-sql-stream-analytics/SA004.png)

Ange att den ska ta med inmatad från din kontinuerliga export-BLOB:

![Skärm bild som visar de nya indata-fönstret med list Rute alternativen för indata-alias, källa och lagrings konto valt.](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Nu behöver du den primära åtkomst nyckeln från ditt lagrings konto, som du noterade tidigare. Ange detta som lagrings konto nyckel.

#### <a name="set-path-prefix-pattern"></a>Ange mönster för Sök vägs prefix

**Se till att ange datum formatet ÅÅÅÅ-MM-DD (med streck).**

Mönstret Path prefix anger hur Stream Analytics hittar indatafilerna i lagringen. Du måste ange den som motsvarar hur fort löp ande export lagrar data. Ställ in den så här:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

I det här exemplet:

* `webapplication27` är namnet på Application Insights resursen, **allt i gemener**. 
* `1234...` är Instrumentation-nyckeln för Application Insights resursen **med streck borttagna**. 
* `PageViews` är den typ av data som vi vill analysera. Vilka typer som är tillgängliga beror på vilket filter du angav i löpande export. Granska exporterade data för att se de andra tillgängliga typerna och se [export data modellen](./export-data-model.md).
* `/{date}/{time}` är ett mönster skrivet bokstavligen.

Om du vill hämta namnet och iKey för din Application Insights-resurs öppnar du Essentials på sidan Översikt eller så öppnar du inställningar.

> [!TIP]
> Använd exempel funktionen för att kontrol lera att du har angett inmatad sökväg korrekt. Om det Miss lyckas: kontrol lera att det finns data i lagrings utrymmet för det tidsintervall som du har valt. Redigera indatamängden och kontrol lera att du har angett lagrings kontot, Sök vägs prefixet och datum formatet korrekt.

 
## <a name="set-query"></a>Ställ in fråga
Öppna avsnittet fråga:

Ersätt standard frågan med:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Observera att de första egenskaperna är speciella för sid visnings data. Exporter av andra typer av telemetri kommer att ha olika egenskaper. Se den [detaljerade data modell referensen för egenskaps typerna och värdena.](./export-data-model.md)

## <a name="set-up-output-to-database"></a>Konfigurera utdata till databas
Välj SQL som utdata.

![I Stream Analytics väljer du utdata](./media/code-sample-export-sql-stream-analytics/SA006.png)

Ange databasen.

![Fyll i informationen om din databas](./media/code-sample-export-sql-stream-analytics/SA007.png)

Stäng guiden och vänta tills ett meddelande visas om att utdata har kon figurer ATS.

## <a name="start-processing"></a>Starta bearbetning
Starta jobbet från åtgärds fältet:

![I Stream Analytics klickar du på Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

Du kan välja om du vill börja bearbeta data som börjar från och med nu, eller om du vill börja med tidigare data. Den senare är användbar om du redan har en kontinuerlig export som redan körs en stund.

Efter några minuter går du tillbaka till Hanteringsverktyg för SQL Server och tittar på de data som flödar in. Använd exempelvis en fråga som detta:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="related-articles"></a>Relaterade artiklar
* [Exportera till Power BI med Stream Analytics](./export-power-bi.md)
* [detaljerad data modell referens för egenskaps typerna och värdena.](./export-data-model.md)
* [Löpande export i Application Insights](./export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[export]: ./export-telemetry.md
[metrics]: ../essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

