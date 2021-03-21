---
title: Exportera till Power BI från Azure Application Insights | Microsoft Docs
description: Analys frågor kan visas i Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 4bdae09d193d5ae9c59fe9352e07b8219a76841d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584315"
---
# <a name="feed-power-bi-from-application-insights"></a>Flödes Power BI från Application Insights
[Power BI](https://www.powerbi.com/) är en uppsättning affärs verktyg som hjälper dig att analysera data och dela insikter. Det finns omfattande instrument paneler på alla enheter. Du kan kombinera data från många källor, inklusive analys frågor från [Azure Application insikter](./app-insights-overview.md).

Det finns tre sätt att exportera Application Insights data till Power BI:

* [**Exportera Analytics-frågor**](#export-analytics-queries). Detta är föredragen metod. Skriv en fråga som du vill ha och exportera den till Power BI. Du kan placera den här frågan på en instrument panel, tillsammans med andra data.
* [**Löpande export och Azure Stream Analytics**](./export-stream-analytics.md). Den här metoden är användbar om du vill lagra dina data under långa tids perioder. Om du inte har ett krav för utökad data lagring använder du fråge metoden för export analys. Kontinuerlig export och Stream Analytics omfattar mer arbete för att konfigurera och ytterligare lagrings kostnader.
* **Power BI adapter**. Uppsättningen med diagram är fördefinierad, men du kan lägga till egna frågor från andra källor.

> [!NOTE]
> Power BI adaptern är nu **föråldrad**. De fördefinierade diagrammen för den här lösningen fylls med statiska frågor som inte kan redige ras. Du har inte möjlighet att redigera dessa frågor och beroende på vissa egenskaper för dina data är det möjligt att anslutningen till Power BI lyckas, men inga data fylls. Detta beror på undantags kriterier som anges i hårdkodad-frågan. Även om den här lösningen kanske fortfarande fungerar för vissa kunder, på grund av bristen på flexibiliteten hos kortet, är den rekommenderade lösningen att använda fråge funktionen för [**export analys**](#export-analytics-queries) .

## <a name="export-analytics-queries"></a>Exportera analys frågor
Med den här vägen kan du skriva en analys fråga som du vill, eller exportera från användnings Tratter, och sedan exportera den till en Power BI instrument panel. (Du kan lägga till den instrument panel som skapats av kortet.)

### <a name="one-time-install-power-bi-desktop"></a>En tid: installera Power BI Desktop
Om du vill importera din Application Insights fråga använder du Skriv bords versionen av Power BI. Sedan kan du publicera den på webben eller till din Power BI moln arbets yta. 

Installera [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportera en Analytics-fråga
1. [Öppna Analytics och skriv frågan](../logs/log-analytics-tutorial.md).
2. Testa och förfina frågan tills du är nöjd med resultatet. Kontrol lera att frågan körs korrekt i Analytics innan du exporterar den.
3. På menyn **Exportera** väljer du **Power BI (M)**. Spara textfilen.
   
    ![Skärm bild av analys, med exportera-menyn markerad](./media/export-power-bi/analytics-export-power-bi.png)
4. I Power BI Desktop väljer du **Hämta data**  >  **Tom fråga**. Sedan väljer du **avancerad redigerare** i Frågeredigeraren under **Visa**.

    Klistra in det exporterade språk skriptet för M i Avancerad redigerare.

    ![Skärm bild av Power BI Desktop, med Avancerad redigerare markerat](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Om du vill tillåta Power BI åtkomst till Azure kan du behöva ange autentiseringsuppgifter. Använd **organisations konto** för att logga in med din Microsoft-konto.
   
    ![Skärm bild av dialog rutan Power BI frågeinställningar](./media/export-power-bi/power-bi-import-sign-in.png)

    Om du behöver verifiera autentiseringsuppgifterna använder du Meny kommandot **Inställningar för data källa** i Frågeredigeraren. Se till att ange de autentiseringsuppgifter som du använder för Azure, vilket kan skilja sig från dina autentiseringsuppgifter för Power BI.
6. Välj en visualisering för frågan och välj fält för x-axel, y-axel och segment dimension.
   
    ![Skärm bild av Power BI Desktop visualiserings alternativ](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publicera rapporten till din Power BI moln arbets yta. Därifrån kan du bädda in en synkroniserad version i andra webb sidor.
   
    ![Skärm bild av Power BI Desktop med knappen publicera markerad](./media/export-power-bi/publish-power-bi.png)
8. Uppdatera rapporten manuellt med intervall eller konfigurera en schemalagd uppdatering på sidan alternativ.

### <a name="export-a-funnel"></a>Exportera en tratt
1. [Skapa din tratt](./usage-funnels.md).
2. Välj **Power BI**.

   ![Skärm bild av knappen Power BI](./media/export-power-bi/button.png)

3. I Power BI Desktop väljer du **Hämta data**  >  **Tom fråga**. Sedan väljer du **avancerad redigerare** i Frågeredigeraren under **Visa**.

   ![Skärm bild av Power BI Desktop, där knappen tom fråga är markerad](./media/export-power-bi/blankquery.png)

   Klistra in det exporterade språk skriptet för M i Avancerad redigerare. 

   ![Skärm bild som visar Power BI Desktop, med Avancerad redigerare markerat](./media/export-power-bi/advancedquery.png)

4. Välj objekt från frågan och välj en tratt-visualisering.

   ![Skärm bild som visar visualiserings alternativ för Power BI Desktop tratt](./media/export-power-bi/selectsequence.png)

5. Ändra rubriken så att den blir meningsfull och publicera rapporten till din Power BI moln arbets yta. 

   ![Skärm bild av Power BI Desktop med rubrik ändring markerad](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Felsökning

Du kan stöta på fel som rör autentiseringsuppgifter eller storleken på data uppsättningen. Här är lite information om vad du kan göra om de här felen.

### <a name="unauthorized-401-or-403"></a>Ej auktoriserad (401 eller 403)
Detta kan inträffa om uppdateringstoken inte har uppdaterats. Prova de här stegen för att se till att du fortfarande har åtkomst:

1. Logga in på Azure Portal och kontrol lera att du har åtkomst till resursen.
2. Försök att uppdatera autentiseringsuppgifterna för instrument panelen.
3. Försök rensa cacheminnet från Power BI Desktop.


   Om du har åtkomst och uppdaterar autentiseringsuppgifterna inte fungerar öppnar du ett support ärende.

### <a name="bad-gateway-502"></a>Felaktig gateway (502)
Detta orsakas vanligt vis av en analys fråga som returnerar för mycket data. Försök att använda ett kortare tidsintervall för frågan. 

Om minska data uppsättningen som kommer från Analytics-frågan inte uppfyller dina krav kan du överväga att använda [API: et](https://dev.applicationinsights.io/documentation/overview) för att hämta en större data uppsättning. Så här konverterar du M-Query-exporten för att använda API: et.

1. Skapa en [API-nyckel](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Uppdatera det Power BI M-skript som du exporterade från analysen genom att ersätta Azure Resource Manager URL med Application Insights-API: et.
   * Ersätt **https: \/ /Management.Azure.com/Subscriptions/...**
   * med, **https: \/ /API.applicationinsights.io/beta/Apps/...**
3. Slutligen uppdaterar du autentiseringsuppgifterna till Basic och använder din API-nyckel.

**Befintligt skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Uppdaterat skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Om sampling
Beroende på hur mycket data som skickas av ditt program kanske du vill använda funktionen för anpassningsbar sampling, som bara skickar en procent andel av din telemetri. Samma sak gäller om du har angett sampling manuellt antingen i SDK eller vid inmatning. [Lär dig mer om sampling](./sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI kort (inaktuellt)
Den här metoden skapar en komplett instrument panel för telemetri åt dig. Den ursprungliga data uppsättningen är fördefinierad, men du kan lägga till mer data i den.

### <a name="get-the-adapter"></a>Hämta kortet
1. Logga in i [Power BI](https://app.powerbi.com/).
2. Öppna **Hämta data** ![ skärm bild av GetData-ikonen i det nedre vänstra hörnet ](./media/export-power-bi/001.png) , **tjänster**.

    ![Skärm dum par visar knappen Hämta i fönstret tjänster.](./media/export-power-bi/002.png)

3. Välj **Hämta nu** under Application Insights.

   ![Skärm dum par av hämta från Application Insights data Källa](./media/export-power-bi/003.png)
4. Ange information om din Application Insights-resurs och logga sedan **in**.

    ![Skärm bild som visar Anslut till Application Insights-fönstret.](./media/export-power-bi/005.png)

     Den här informationen finns i fönstret Application Insights översikt:

     ![Skärm bild av hämta från Application Insights data Källa](./media/export-power-bi/004.png)

5. Öppna den nyligen skapade Application Insights Power BI-appen.

6. Vänta en minut eller två för data som ska importeras.

    ![Skärm bild av Power BI adapter](./media/export-power-bi/010.png)

Du kan redigera instrument panelen, kombinera Application Insights diagram med andra källor och med analys frågor. Du kan hämta fler diagram i visualiserings galleriet och varje diagram innehåller parametrar som du kan ange.

Efter den första importen fortsätter instrument panelen och rapporterna att uppdateras dagligen. Du kan styra uppdaterings schema för data uppsättningen.

## <a name="next-steps"></a>Nästa steg
* [Power BI – lär dig](https://www.powerbi.com/learning/)
* [Självstudie för analys](../logs/log-analytics-tutorial.md)