---
title: Datavisualisering i realtid av data Azure IoT Hub – Power BI
description: Använd Power BI för att visualisera temperatur- och luftfuktighetsdata som samlas in från sensorn och skickas till din Azure IoT Hub.
author: robinsh
keywords: datavisualisering i realtid, visualisering av realtidsdata, sensordatavisualisering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 0b099f4ce91fd24e8d7baec054bcfc5a6cf0b032
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567119"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualisera sensordata i realtid från Azure IoT Hub med Power BI

![Diagram från slutet till slut](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

I den här artikeln får du lära dig att visualisera sensordata i realtid som din Azure IoT-hubb tar emot med hjälp av Power BI. Om du vill försöka visualisera data i din IoT-hubb med en webbapp kan du se Använda en webbapp för att visualisera [sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="prerequisites"></a>Förutsättningar

* Slutför [självstudien om Raspberry Pi-onlinesimulatorn](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av självstudierna om enheten. Du kan till exempel gå till [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md) eller till någon av snabbstarterna för att skicka [telemetri.](quickstart-send-telemetry-dotnet.md) De här artiklarna omfattar följande krav:
  
  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb i din prenumeration.
  * Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.

* Ett Power BI-konto. ([Prova Power BI kostnadsfritt](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics jobb

Vi börjar med att skapa ett Stream Analytics jobb. När du har skapat jobbet definierar du indata, utdata och den fråga som används för att hämta data.

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I den [Azure Portal](https://portal.azure.com)väljer du **Skapa en resurs**  >  **Sakernas Internet**  >  **Stream Analytics jobb**.

2. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Resursgrupp:** Använd samma resursgrupp som din IoT Hub använder.

   **Plats:** Använd samma plats som resursgruppen.

   ![Skapa ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Välj **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics jobbet.

2. Välj **Indata** under **Jobbtopologi**.

3. I fönstret **Indata** väljer du **Lägg till strömindata** och **IoT Hub** i listrutan. Ange följande information i det nya indatafönstret:

   **Indataalias:** Ange ett unikt alias för indata.

   **Välj IoT Hub i din prenumeration:** Välj den här alternativknappen.

   **Prenumeration:** Välj den Azure-prenumeration som du använder för den här självstudien.

   **IoT Hub:** Välj den IoT Hub som du använder för den här självstudien.

   **Slutpunkt**: Välj **Meddelanden**.

   **Namn på princip för delad** åtkomst: Välj namnet på den princip för delad åtkomst som du vill att Stream Analytics ska använda för din IoT-hubb. I den här självstudien kan du välja *tjänst*. *Tjänstprincipen* skapas som standard på nya IoT-hubbar och ger behörighet att skicka och ta emot slutpunkter på molnsidan som exponeras av IoT-hubben. Mer information finns i [Åtkomstkontroll och behörigheter.](iot-hub-devguide-security.md#access-control-and-permissions)

   **Nyckel för princip för delad** åtkomst: Det här fältet fylls i automatiskt baserat på ditt val av namn på principen för delad åtkomst.

   **Konsumentgrupp:** Välj den konsumentgrupp som du skapade tidigare.

   Lämna standardvärdena för alla andra fält.

   ![Lägga till indata till ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Välj **Utdata** under **Jobbtopologi**.

2. I fönstret **Utdata** väljer du Lägg **till** och **Power BI**.

3. I fönstret **Power BI – Nya utdata** väljer **du Auktorisera** och följer anvisningarna för att logga in på ditt Power BI konto.

4. När du har loggat in på Power BI anger du följande information:

   **Utdataalias:** Ett unikt alias för utdata.

   **Grupparbetsyta:** Välj din målgruppsarbetsyta.

   **Namn på datauppsättning:** Ange ett datauppsättningsnamn.

   **Tabellnamn:** Ange ett tabellnamn.

   **Autentiseringsläge:** Lämna standardinställningen.

   ![Lägga till utdata till Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Välj **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Välj **Fråga** under **Jobbtopologi**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

   ![Lägga till en fråga i Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Välj **Spara fråga.**

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics jobb väljer **du Översikt** och sedan **Starta**  >  **nu**  >  **Starta.** När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Skapa och publicera en Power BI rapport för att visualisera data

Följande steg visar hur du skapar och publicerar en rapport med hjälp av Power BI-tjänst. Du kan följa dessa steg, med vissa ändringar, om du vill använda det "nya utseendet" i Power BI. Information om skillnaderna och hur du navigerar i det "nya utseendet" finns i Det [nya utseendet för Power BI-tjänst](/power-bi/consumer/service-new-look).

1. Kontrollera att exempelprogrammet körs på enheten. Annars kan du gå till självstudierna under [Konfigurera din enhet.](./iot-hub-raspberry-pi-kit-node-get-started.md)

2. Logga in på ditt [Power BI](https://powerbi.microsoft.com/en-us/)-konto.

3. Välj den arbetsyta som du använde, **Min arbetsyta.**

4. Välj **Datauppsättningar**.

   Du bör se den datauppsättning som du angav när du skapade utdata för Stream Analytics jobbet.

5. För den datauppsättning som du skapade väljer **du Lägg till** rapport (den första ikonen till höger om datauppsättningens namn).

   ![Skapa en Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   1. I fönstret **Visualiseringar** på sidan för att skapa rapporten väljer du ikonen för linjediagram för att lägga till ett linjediagram.

   2. I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet.

   3. Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.

   4. Dra **temperatur** till **Värden**.

      Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

      ![Lägga till ett linjediagram för temperatur i en Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. Det gör du genom att klicka på en tom del av arbetsytan och följa samma steg ovan  för att placera **EventEnqueuedUtcTime** på x-axeln och luftfuktigheten på y-axeln.

   ![Lägga till ett linjediagram för luftfuktighet i en Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Välj **Spara** för att spara rapporten.

9. Välj **Rapporter** i det vänstra fönstret och välj sedan den rapport som du nyss skapade.

10. Välj File Publish to web **(Fil**  >  **publicera på webben).**

    ![Välj Publicera på webben för Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Om du får ett meddelande om att kontakta administratören för att aktivera skapande av inbäddningskod kan du behöva kontakta dem. Du måste ha aktiverat skapande av inbäddningskod innan du kan slutföra det här steget.
    >
    > ![Kontakta din administratörsavisering](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Välj **Skapa inbäddningskod** och välj sedan **Publicera.**

Du får rapportlänken som du kan dela med vem som helst för rapportåtkomst och ett kodfragment som du kan använda för att integrera rapporten i din blogg eller webbplats.

![Publicera en Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Microsoft erbjuder även [Power BI mobilappar för](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) att visa och interagera med Power BI instrumentpaneler och rapporter på din mobila enhet.

## <a name="next-steps"></a>Nästa steg

Du har nu använt Power BI för att visualisera sensordata i realtid från din Azure IoT Hub.

Ett annat sätt att visualisera data från Azure IoT Hub finns i Använda en webbapp för att visualisera [sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
