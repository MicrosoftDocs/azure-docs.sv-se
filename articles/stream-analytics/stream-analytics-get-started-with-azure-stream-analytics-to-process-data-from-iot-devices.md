---
title: Bearbeta IoT-dataströmmar i real tid med Azure Stream Analytics
description: IoT-sensortaggar och dataströmmar med dataströmsanalys och realtidsbearbetning av data
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a438401ff93c20d8759e6128936c3626bd3de484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012690"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Bearbeta IoT-dataströmmar i real tid med Azure Stream Analytics

I den här artikeln får du lära dig hur du skapar Stream-bearbetnings logik för att samla in data från Sakernas Internet-enheter (IoT). Du kan använda ett IoT-användningsfall (Real-World Sakernas Internet) för att demonstrera hur du skapar din lösning snabbt och ekonomiskt.

## <a name="prerequisites"></a>Förutsättningar

* Skapa en kostnads fri [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/).
* Hämta exempel fråga och datafiler från [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenario

Contoso, ett företag inom industriell automation, har helt automatiserat sin produktionsprocess. Maskinerna på den här anläggningen har sensorer som sänder dataströmmar i realtid. I detta scenario vill en av företagets fabrikschefer få realtidsinsikter från dessa sensordata för att identifiera mönster och vidta lämpliga åtgärder utifrån resultatet. Du kan använda Stream Analytics frågespråk (SAQL) via sensor informationen för att hitta intressanta mönster från den inkommande data strömmen.

I det här exemplet genereras data från en sensor märkes enhet för Texas Instruments. Nyttolasten för dessa data är i JSON-format och ser ut ungefär så här:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

I ett verkligt scenario har du flera hundra av dessa sensorer som genererar händelser som en dataström. I en idealisk situation skulle det finnas en gateway-enhet som kör kod för att skicka dessa händelser till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Ditt Stream Analytics-jobb skulle mata in de här händelserna från Event Hubs och köra analysfrågor i realtid mot dataströmmarna. Sedan kunde du skicka resultaten till någon av de [utdatatyper som stöds](stream-analytics-define-outputs.md).

För att förenkla användningen av den här komma igång-guiden ingår en exempeldatafil med data från verkliga SensorTag-enheter. Du kan köra frågor på dessa exempeldata och se resultaten. I efterföljande självstudiekurser lär du dig hur du kopplar jobbet till in- och utdataenheter och hur du distribuerar det till Azure-tjänsten.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com)väljer du **+ skapa en resurs** i den vänstra navigerings menyn. Välj sedan **Stream Analytics jobb** från **Analytics**.
   
    ![Skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Ange ett unikt jobbnamn och kontrollera att prenumerationen är korrekt för jobbet. Skapa en ny resurs grupp eller Välj en befintlig från din prenumeration.

1. Välj en plats för jobbet. Använd samma plats för resurs gruppen och alla resurser för att öka bearbetnings hastigheten och minska kostnaderna. När du har gjort konfigurationerna väljer du **skapa**.
   
    ![Information om att skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Skapa en Azure Stream Analytics-fråga
Nästa steg när jobbet har skapats är att skriva en fråga. Du kan testa frågor mot exempel data utan att ansluta indata eller utdata till ditt jobb.

Ladda ned [HelloWorldASA-InputStream.js](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) från GitHub. Navigera sedan till ditt Azure Stream Analytics jobb i Azure Portal.

Välj **fråga** under **jobb sto pol Ogin** på den vänstra menyn. Välj sedan **överför inmatade exempel**. Ladda upp `HelloWorldASA-InputStream.json` filen och välj **OK**.

![Panels fråga för Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Observera att en förhands granskning av data fylls i automatiskt i tabellen **förhands granskning av inmatning** .

![Förhands granskning av indata för exempel](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Fråga: Arkivera dina rådata

Den enklaste typen av fråga är en anslutningsfråga som arkiverar alla indata till dess angivna utdataenheter. Den här frågan är standard frågan som fylls i i ett nytt Azure Stream Analytics jobb.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Välj **test fråga** och visa resultaten i tabellen med **test resultat** .

![Test resultat för Stream Analytics fråga](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Fråga: Filtrera dina data baserat på ett villkor

Nu ska vi försöka filtrera resultaten baserat på ett villkor. Vi vill bara visa resultat för de händelser som kommer från "Sensora".

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Klistra in frågan i redigeraren och välj **test fråga** för att granska resultaten.

![Filtrera en dataström](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Fråga: Varna för att utlösa ett företagsarbetsflöde

Nu gör vi vår fråga mer detaljerad. För varje sensortyp vill vi övervaka genomsnittstemperaturen per 30-sekundersfönster och visa resultat bara om genomsnittet överstiger 100 grader.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30 sekunders-filterfråga](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Du bör se resultat som bara innehåller 245 rader och namn på sensorer där genomsnitts temperaturen är större än 100. Den här frågan grupperar händelseströmmen efter **dspl**, vilket är sensornamnet, under ett **Rullande fönster** på 30 sekunder. Temporala frågor måste ange hur lång tid som ska förloppet. Med hjälp av **timestamp by** -satsen har du angett kolumnen **OUTPUTTIME** för att associera tider med alla temporala beräkningar. Detaljerad information finns i [tids hantering](/stream-analytics-query/time-management-azure-stream-analytics) och [fönster funktioner](/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Fråga: Identifiera avsaknad av händelser

Hur kan vi skriva en fråga för att hitta brist på inmatningshändelser? Nu ska vi hitta den senaste gången en sensor skickade data och sedan inte skickade några händelser under de närmaste 5 sekunderna.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Identifiera avsaknad av händelser](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Här använder vi en **VÄNSTER YTTRE** koppling för samma dataström (självkoppling). För en **INRE** koppling returneras resultatet bara om en matchning hittas.  Om en händelse från vänster sida i kopplingen är omatchad i en **VÄNSTER YTTRE** koppling returneras en rad med NULL för alla kolumner i den högra sidan. Den här tekniken är väldigt användbar för att hitta frånvaro av händelser. Mer information finns i [delta](/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Slutsats

Syftet med den här artikeln är att demonstrera hur du skriver olika frågor för Stream Analytics frågor och se resultatet i webbläsaren. Detta är dock bara att komma igång. Stream Analytics stöder olika typer av indata och utdata och kan även använda funktioner i Azure Machine Learning, vilket gör det till ett stabilt verktyg för att analysera dataströmmar. Mer information om hur du skriver frågor finns i artikeln om [vanliga frågemönster](stream-analytics-stream-analytics-query-patterns.md).