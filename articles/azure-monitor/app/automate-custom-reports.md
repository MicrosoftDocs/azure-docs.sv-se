---
title: Automatisera anpassade rapporter med Azure Application Insights-data
description: Automatisera anpassade dagliga/veckovis/månads Visa rapporter med Azure Application Insights-data
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: 39970227fc94d3073688b23cc530462f368a6ecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590037"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatisera anpassade rapporter med Azure Application Insights-data

Periodiska rapporter hjälper till att hålla ett team informerad om hur deras affärs kritiska tjänster fungerar. Utvecklare, DevOps/SRE-team och deras chefer kan vara produktiva med automatiserade rapporter som ger insikter på ett tillförlitligt sätt utan att behöva logga in på portalen. Sådana rapporter kan också hjälpa till att identifiera gradvisa ökningar i svars tider, belastnings-eller fel frekvenser som inte kan utlösa några varnings regler

Varje företag har sina unika rapporterings behov, till exempel:

* Vissa percentiler av mått eller anpassade mått i en rapport.
* Ha olika rapporter för daglig, veckovis och månatlig Sammanfattning av data för olika mål grupper.
* Segmentering efter anpassade attribut som region eller miljö.
* Gruppera vissa AI-resurser tillsammans i en enda rapport, även om de kan finnas i olika prenumerationer eller resurs grupper osv.
* Separera rapporter som innehåller känsliga mått som skickas till en selektiv mål grupp.
* Rapporter till intressenter som kanske inte har åtkomst till Portal resurserna.

> [!NOTE] 
> Vecko Application Insights sammanfattnings-e-postmeddelandet tillät inte anpassningar och kommer att upphöra att gälla för de anpassade alternativen i listan nedan. Den senaste vecko Visa e-postmeddelandet kommer att skickas den 11 juni 2018. Konfigurera något av följande alternativ för att få liknande anpassade rapporter (Använd den fråga som föreslås nedan).

## <a name="to-automate-custom-report-emails"></a>Automatisera anpassade rapport-e-postmeddelanden

Du kan [köra program mässigt fråga Application Insights](https://dev.applicationinsights.io/) data för att skapa anpassade rapporter enligt ett schema. Följande alternativ kan hjälpa dig att komma igång snabbt:

* [Automatisera rapporter med energi automatisering](../logs/logicapp-flow-connector.md)
* [Automatisera rapporter med Logic Apps](automate-with-logic-apps.md)
* Använd [Azure Function](../../azure-functions/functions-get-started.md) -mallen "Application Insights schemalagd Sammanfattning" i övervaknings scenariot. Den här funktionen använder SendGrid för att leverera e-postmeddelandet. 

    ![Azure Function-mall](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exempel fråga för e-postmeddelande med veckovis Sammanfattning
Följande fråga visar koppling mellan flera data uppsättningar för ett vecko sammandrag av e-post som rapport. Anpassa den efter behov och Använd de alternativ som anges ovan för att automatisera en vecko rapport.

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Application Insights schemalagd sammanfattad rapport

1. Skapa ett Azure-Funktionsapp. (Application Insights _på_ krävs bara om du vill övervaka nya Funktionsapp med Application Insights)

   Besök Azure Functions-dokumentationen och lär dig hur du [skapar en Function-app](../../azure-functions/functions-get-started.md)

2. När din nya Funktionsapp har slutfört distributionen väljer **du gå till resurs**.

3. Välj **ny funktion**.

   ![Skapa en ny skärm skärm](./media/automate-custom-reports/new-function.png)

4. Välj **_Application Insights schemalagd sammanfattad mall_**.

     > [!NOTE]
     > Som standard skapas Function Apps med körnings version 3. x. Du måste ha [mål Azure Functions runtime version](../../azure-functions/set-runtime-version.md) **1. x** för att kunna använda den schemalagda sammanfattnings mal len Application Insights. Gå till konfiguration > funktion körnings inställningar för att ändra kör tids versionen. ![skärm bild för körning](./media/automate-custom-reports/change-runtime-v.png)

   ![Skärm bild för ny funktion Application Insights mall](./media/automate-custom-reports/function-app-04.png)

5. Ange en lämplig mottagares e-postadress för rapporten och välj **skapa**.

   ![Skärm bild för funktions inställningar](./media/automate-custom-reports/scheduled-digest.png)

6. Välj konfiguration av **Funktionsapp**  >  **plattforms funktioner**  >  .

    ![Skärm bild för Azure Function program-inställningar](./media/automate-custom-reports/config.png)

7. Skapa tre nya program inställningar med lämpliga motsvarande värden ``AI_APP_ID`` , ``AI_APP_KEY`` och ``SendGridAPI`` . Välj **Spara**.

     ![Skärm bild för funktions integrations gränssnitt](./media/automate-custom-reports/app-settings.png)
    
    (AI_ värden finns under API-åtkomst för den Application Insights resurs som du vill rapportera om. Om du inte har en Application Insights-API-nyckel finns det möjlighet att **skapa en API-nyckel**.)
    
   * AI_APP_ID = program-ID
   * AI_APP_KEY = API-nyckel
   * SendGridAPI = SendGrid API-nyckel

     > [!NOTE]
     > Om du inte har ett SendGrid-konto kan du skapa ett. SendGrid-dokumentationen för Azure Functions finns [här](../../azure-functions/functions-bindings-sendgrid.md). Om du bara vill ha en minimal förklaring av hur du ställer in SendGrid och genererar en API-nyckel som anges i slutet av den här artikeln. 

8. Välj **integrera** och under utdata klickar du på **SendGrid ($Return)**.

     ![Skärm bild av utdata](./media/automate-custom-reports/integrate.png)

9. Under **inställningen SendGridAPI Key app** väljer du din nyligen skapade app-inställning för **SendGridAPI**.

     ![Kör Funktionsapp skärm bild](./media/automate-custom-reports/sendgrid-output.png)

10. Kör och testa din Funktionsapp.

     ![Testa skärm bild](./media/automate-custom-reports/function-app-11.png)

11. Kontrol lera e-postmeddelandet för att bekräfta att meddelandet har skickats/tagits emot.

     ![E-postmeddelandets ämnesrad](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid med Azure

De här stegen gäller endast om du inte redan har ett SendGrid-konto konfigurerat.

1. Från Azure Portal väljer du **skapa en resurs** > söker efter **SendGrid e-postleverans** > klickar du på **skapa** > fyller i SendGrid-instruktionerna för att skapa.

     ![Skärm bild för att skapa SendGrid-resurs](./media/automate-custom-reports/sendgrid.png)

2. När du har skapat under SendGrid konton väljer du **Hantera**.

     ![Skärm bild av inställningar för API-nyckel](./media/automate-custom-reports/sendgrid-manage.png)

3. SendGrid-platsen startas. Välj **Inställningar**  >  **API-nycklar**.

     ![Skärm bild för att skapa och Visa API-nyckel](./media/automate-custom-reports/function-app-15.png)

4. Skapa en API-nyckel > Välj **skapa & vy**. (Granska SendGrid-dokumentationen om begränsad åtkomst för att avgöra vilken behörighets nivå som är lämplig för din API-nyckel. Fullständig åtkomst är valt här endast i exempel syfte.)

   ![Skärm bild av fullständig åtkomst](./media/automate-custom-reports/function-app-16.png)

5. Kopiera hela nyckeln. det här värdet är det du behöver i Funktionsapp inställningar som värde för SendGridAPI

   ![Skärm bild för kopierings-API-nyckel](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att skapa [analys frågor](../logs/get-started-queries.md).
* Lär dig mer om att [fråga Application Insights data](https://dev.applicationinsights.io/) på ett program
* Läs mer om [Logic Apps](../../logic-apps/logic-apps-overview.md).
* Lär dig mer om [Microsoft Power automatisering](https://ms.flow.microsoft.com).
