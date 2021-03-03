---
title: Exportera med hjälp av Stream Analytics från Azure Application Insights | Microsoft Docs
description: Stream Analytics kan kontinuerligt omvandla, filtrera och dirigera data som du exporterar från Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: a517bddd8981554b7fb5044d33b6c6777df51e36
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719805"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Använd Stream Analytics för att bearbeta exporterade data från Application Insights

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) är det perfekta verktyget för att bearbeta data som [exporter ATS från Application Insights](export-telemetry.md). Stream Analytics kan hämta data från flera olika källor. Den kan transformera och filtrera data och sedan dirigera den till en rad olika mottagare.

I det här exemplet ska vi skapa en adapter som hämtar data från Application Insights, byter namn på och bearbetar några av fälten och kopplar den till Power BI.

> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights data i Power BI](./export-power-bi.md). Sökvägen som illustreras här är bara ett exempel på hur du kan bearbeta exporterade data.

![Blockera diagram för export via SA till PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Kontinuerlig export matar alltid ut data till ett Azure Storage-konto, så du måste skapa lagringen först.

1. Skapa ett "Klassiskt" lagrings konto i din prenumeration i [Azure Portal](https://portal.azure.com).
   
   ![I Azure Portal väljer du ny, data, lagring](./media/export-stream-analytics/030.png)
2. Skapa en container
   
    ![I det nya lagrings utrymmet väljer du behållare, klickar på behållare-panelen och lägger sedan till](./media/export-stream-analytics/040.png)
3. Kopiera lagrings åtkomst nyckeln
   
    Du behöver det snart att konfigurera indatan till Stream Analytics-tjänsten.
   
    ![I lagrings utrymmet öppnar du inställningar, nycklar och tar en kopia av den primära åtkomst nyckeln](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta kontinuerlig export till Azure Storage

[Kontinuerlig export](export-telemetry.md) flyttar data från Application Insights till Azure Storage.

1. I Azure Portal bläddrar du till den Application Insights resurs som du har skapat för ditt program.
   
    ![Välj Bläddra, Application Insights, ditt program](./media/export-stream-analytics/050.png)
2. Skapa en löpande export.
   
    ![Välj inställningar, löpande export, Lägg till](./media/export-stream-analytics/060.png)

    Välj det lagrings konto som du skapade tidigare:

    ![Ange export målet](./media/export-stream-analytics/070.png)

    Ange de händelse typer som du vill se:

    ![Välj händelse typer](./media/export-stream-analytics/080.png)

1. Låt vissa data ackumuleras. Luta dig tillbaka och låt användarna använda programmet en stund. Telemetri kommer in och du ser statistik diagram i [Metric Explorer](../essentials/metrics-charts.md) och enskilda händelser i [diagnostisk sökning](./diagnostic-search.md).
   
    Informationen kommer också att exporteras till lagringen. 
2. Granska exporterade data. I Visual Studio väljer du **Visa/Cloud Explorer** och öppna Azure/Storage. (Om du inte har det här meny alternativet måste du installera Azure SDK: öppna dialog rutan nytt projekt och öppna Visual C#/Cloud/Get Microsoft Azure SDK för .NET.)
   
    ![Skärm bild som visar hur du ställer in de händelse typer som du vill se.](./media/export-stream-analytics/04-data.png)
   
    Anteckna den gemensamma delen av Sök vägs namnet, som härleds från program namnet och Instrumentation-nyckeln.

Händelserna skrivs till BLOB-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Vi skulle läsa händelse data och filtrera bort de fält som vi vill. Det finns alla typer av saker som vi kan göra med data, men vår plan idag är att använda Stream Analytics för att skicka data till Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en Azure Stream Analytics-instans

Välj tjänsten Azure Stream Analytics från [Azure Portal](https://portal.azure.com/)och skapa ett nytt Stream Analytics jobb:

![Skärm bild som visar huvud sidan för att skapa Stream Analytics jobb i Azure Portal.](./media/export-stream-analytics/SA001.png)

![Skärm bild som visar den information som behövs när du skapar ett nytt Stream Analytics jobb.](./media/export-stream-analytics/SA002.png)

När det nya jobbet skapas väljer **du gå till resurs**.

![Skärm bild som visar meddelandet som togs emot när den nya Stream Analytics jobb distributionen har slutförts.](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Lägg till en ny inmatare

![Skärm bild som visar hur du lägger till indata till Stream Analytics jobbet.](./media/export-stream-analytics/SA004.png)

Ange att den ska ta med inmatad från din kontinuerliga export-BLOB:

![Skärm bild som visar hur du konfigurerar Stream Analyticss jobbet för att ta inmatat från en kontinuerlig export-blob.](./media/export-stream-analytics/SA0005.png)

Nu behöver du den primära åtkomst nyckeln från ditt lagrings konto, som du noterade tidigare. Ange detta som lagrings konto nyckel.

### <a name="set-path-prefix-pattern"></a>Ange mönster för Sök vägs prefix

**Se till att ange datum formatet ÅÅÅÅ-MM-DD (med streck).**

Mönstret Path prefix anger var Stream Analytics hittar indatafilerna i lagringen. Du måste ange den som motsvarar hur fort löp ande export lagrar data. Ställ in den så här:

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

I det här exemplet:

* `webapplication27` är namnet på Application Insights resursen med **gemener**.
* `1234...` är Instrumentation-nyckeln för Application Insightss resurs, **utan bindestreck**. 
* `PageViews` är den typ av data som du vill analysera. Vilka typer som är tillgängliga beror på vilket filter du angav i löpande export. Granska exporterade data för att se de andra tillgängliga typerna och se [export data modellen](export-data-model.md).
* `/{date}/{time}` är ett mönster skrivet bokstavligen.

> [!NOTE]
> Kontrol lera lagringen för att se till att du får rätt sökväg.

## <a name="add-new-output"></a>Lägg till nya utdata

Välj nu jobbet > **utdata**  >  **Lägg till**.

![Skärm bild som visar hur du väljer Stream Analytics jobb för att lägga till en ny utmatning.](./media/export-stream-analytics/SA006.png)


![Välj den nya kanalen, klicka på utdata, Lägg till Power BI](./media/export-stream-analytics/SA010.png)

Ange ditt **arbets-eller skol konto** för att ge Stream Analytics åtkomst till din Power BI-resurs. Skriv sedan ett namn för utdata och för mål Power BI data uppsättning och tabell.

## <a name="set-the-query"></a>Ange frågan

Frågan styr översättningen från indata till utdata.

Använd funktionen test för att kontrol lera att du får rätt utdata. Ge den exempel data som du tog från sidan indata.

### <a name="query-to-display-counts-of-events"></a>Fråga för att visa antal händelser

Klistra in den här frågan:

```SQL
SELECT
  flat.ArrayValue.name,
  count(*)
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.[event]) as flat
GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-indata är det alias som vi gav för Stream-indata
* PBI – utdata är aliaset som vi har definierat
* Vi använder [yttre Apply-get Elements](/stream-analytics-query/apply-azure-stream-analytics) eftersom händelse namnet finns i en KAPSLAd JSON-matris. Sedan väljer Välj för att plocka händelse namnet, tillsammans med antalet instanser med det namnet under tids perioden. [Group by](/stream-analytics-query/group-by-azure-stream-analytics) -satsen grupperar elementen i tids perioder på en minut.

### <a name="query-to-display-metric-values"></a>Fråga för att visa mått värden

```SQL
SELECT
  A.context.data.eventtime,
  avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.context.custom.metrics) as flat
GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime
```

* Den här frågan går in i måttet telemetri för att hämta händelse tid och mått värde. Måttets värden finns i en matris, så vi använder det yttre applicera get Elements-mönstret för att extrahera raderna. "mina mått" är namnet på måttet i det här fallet.

### <a name="query-to-include-values-of-dimension-properties"></a>Fråga för att inkludera värden för dimensions egenskaper

```SQL
WITH flat AS (
SELECT
  MySource.context.data.eventTime as eventTime,
  InstanceId = MyDimension.ArrayValue.InstanceId.value,
  BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
FROM MySource
OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
)
SELECT
  eventTime,
  InstanceId,
  BusinessUnitId
INTO AIOutput
FROM flat
```

* Den här frågan innehåller värden för dimensions egenskaper utan beroende på en viss dimension vid ett fast index i dimensions matrisen.

## <a name="run-the-job"></a>Kör jobbet

Du kan välja ett datum i det förflutna för att starta jobbet från.

![Välj jobbet och klicka på fråga. Klistra in exemplet nedan.](./media/export-stream-analytics/SA008.png)

Vänta tills jobbet körs.

## <a name="see-results-in-power-bi"></a>Se resultat i Power BI

> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights data i Power BI](./export-power-bi.md). Sökvägen som illustreras här är bara ett exempel på hur du kan bearbeta exporterade data.


Öppna Power BI med ditt arbets-eller skol konto och välj den data uppsättning och tabell som du definierade som utdata för Stream Analyticss jobbet.

![I Power BI väljer du data uppsättning och fält.](./media/export-stream-analytics/200.png)

Nu kan du använda den här data uppsättningen i rapporter och instrument paneler i [Power BI](https://powerbi.microsoft.com).

![Skärm bild som visar ett rapport exempel som gjorts från en data uppsättning i Power BI.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Ser du inga data?

* Kontrol lera att du [har angett datum formatet](#set-path-prefix-pattern) korrekt till åååå-mm-dd (med streck).

## <a name="next-steps"></a>Nästa steg
* [Löpande export](export-telemetry.md)
* [Detaljerad data modell referens för egenskaps typerna och värdena.](export-data-model.md)
* [Application Insights](./app-insights-overview.md)
