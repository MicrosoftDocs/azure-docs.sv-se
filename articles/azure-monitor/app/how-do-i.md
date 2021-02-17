---
title: Hur gör jag för att... i Azure Application Insights | Microsoft Docs
description: Vanliga frågor och svar i Application Insights.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 74a4d7ee65dccead132cfcebd9bf8c0de9b761a5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584164"
---
# <a name="how-do-i--in-application-insights"></a>Hur kan jag ... i Application Insights?
## <a name="get-an-email-when-"></a>Få ett e-postmeddelande när...
### <a name="email-if-my-site-goes-down"></a>E-posta om min webbplats slutar fungera
Ange ett [webb test för tillgänglighet](./monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-posta om min webbplats är överbelastad
Ange en [avisering](../alerts/alerts-log.md) på **Server svars tiden**. Ett tröskelvärde mellan 1 och 2 sekunder bör fungera.

![Skärm bild som visar hur du ställer in en avisering på Server svars tiden.](./media/how-do-i/030-server.png)

Din app kan också visa tecken på stammar genom att returnera felkoder. Ange en avisering om **misslyckade förfrågningar**.

Om du vill ställa in en avisering på **Server undantag** kan du behöva göra [ytterligare inställningar](./asp-net-exceptions.md) för att kunna se data.

### <a name="email-on-exceptions"></a>E-post vid undantag
1. [Konfigurera undantags övervakning](./asp-net-exceptions.md)
2. [Ange en avisering](../alerts/alerts-log.md) för måttet för antalet undantag

### <a name="email-on-an-event-in-my-app"></a>Skicka ett e-postmeddelande till en händelse i min app
Låt oss anta att du vill få ett e-postmeddelande när en enskild händelse inträffar. Application Insights tillhandahåller inte den här funktionen direkt, men den kan [skicka en avisering när ett mått överskrider ett tröskelvärde](../alerts/alerts-log.md).

Aviseringar kan anges för [anpassade mått](./api-custom-events-metrics.md#trackmetric), även om inte anpassade händelser. Skriv kod för att öka ett mått när händelsen inträffar:

```csharp
telemetry.TrackMetric("Alarm", 10);
```

eller:

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

Eftersom aviseringar har två tillstånd måste du skicka ett lågt värde när du anser att aviseringen har avslut ATS:

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

Skapa ett diagram i [Metric Explorer](../essentials/metrics-charts.md) för att se ditt larm:

![Skärm bild som visar hur du skapar ett diagram i Metric Explorer för att se ditt larm.](./media/how-do-i/010-alarm.png)

Nu ska du ange en avisering för att utlösa när måttet hamnar ovanför ett värde för en kort period:

![Skärm bild som visar hur du ställer in en avisering för att utlösa när måttet är över mitt värde under en kort period.](./media/how-do-i/020-threshold.png)

Ställ in genomsnitts perioden på minimivärdet.

Du får e-postmeddelanden både när måttet hamnar ovanför och under tröskelvärdet.

Några saker att tänka på:

* En avisering har två tillstånd ("varning" och "felfri"). Statusen utvärderas endast när ett mått tas emot.
* Ett e-postmeddelande skickas endast när tillstånd ändras. Det är därför du måste skicka både höga och låga värden.
* För att utvärdera aviseringen tar genomsnittet av de mottagna värdena under föregående period. Detta inträffar varje gång ett mått tas emot, så att e-postmeddelanden kan skickas oftare än den period som du anger.
* Eftersom e-postmeddelanden skickas både på "varning" och "felfri", kanske du vill överväga att fundera på att titta på en händelse i två tillstånd. I stället för händelsen "jobbet har slutförts" har du till exempel ett villkor för "pågående jobb" där du får e-postmeddelanden i början och slutet av ett jobb.

### <a name="set-up-alerts-automatically"></a>Konfigurera aviseringar automatiskt
[Använd PowerShell för att skapa nya aviseringar](../alerts/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>Använd PowerShell för att hantera Application Insights
* [Skapa nya resurser](./create-new-resource.md#creating-a-resource-automatically)
* [Skapa nya aviseringar](../alerts/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>Separera telemetri från olika versioner

* Flera roller i en app: Använd en enda Application Insights resurs och filtrera på [cloud_Rolename](./app-map.md).
* Separera utvecklings-, test-och versions versioner: Använd olika Application Insights resurser. Hämta instrumentande tangenter från web.config. [Läs mer](./separate-resources.md)
* Rapport versions versioner: Lägg till en egenskap med hjälp av en telemetri-initierare. [Läs mer](./separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Övervaka backend-servrar och skrivbordsappar
[Använd Windows Server SDK-modulen](./windows-desktop.md).

## <a name="visualize-data"></a>Visualisera data
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Instrument panel med mått från flera appar
* I [Metric Explorer](../essentials/metrics-charts.md)anpassar du ditt diagram och sparar det som en favorit. Fäst den på Azure-instrumentpanelen.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Instrument panel med data från andra källor och Application Insights
* [Exportera telemetri till Power BI](./export-power-bi.md).

Eller

* Använd SharePoint som instrument panel och visa data i SharePoint-webbdelar. [Använd kontinuerlig export och Stream Analytics för att exportera till SQL](./code-sample-export-sql-stream-analytics.md).  Använd PowerView för att undersöka databasen och skapa en SharePoint-webbdel för PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrera bort anonyma eller autentiserade användare
Om dina användare loggar in kan du ange det [autentiserade användar-ID: t](./api-custom-events-metrics.md#authenticated-users). (Det sker inte automatiskt.)

Sedan kan du:

* Sök efter vissa användar-ID: n

![Skärm bild som visar alternativen för search i vissa användar-ID: n.](./media/how-do-i/110-search.png)

* Filtrera mått till antingen anonyma eller autentiserade användare

![Skärm bild som visar filtrering av metrixs till antingen anonyma eller autentiserade användare.](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Ändra egenskaps namn eller värden
Skapa ett [filter](./api-filtering-sampling.md#filtering). På så sätt kan du ändra eller filtrera telemetri innan det skickas från din app till Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista vissa användare och deras användning
Om du bara vill [söka efter vissa användare](#search-specific-users)kan du ange det [autentiserade användar-ID: t](./api-custom-events-metrics.md#authenticated-users).

Om du vill ha en lista över användare med data, till exempel vilka sidor de tittar på eller hur ofta de loggar in, har du två alternativ:

* [Ange autentiserat användar-ID](./api-custom-events-metrics.md#authenticated-users), [Exportera till en databas](./code-sample-export-sql-stream-analytics.md) och Använd lämpliga verktyg för att analysera dina användar data där.
* Om du bara har ett litet antal användare kan du skicka anpassade händelser eller mått med data från intresse som mått värde eller händelse namn och ange användar-ID som en egenskap. Om du vill analysera sidvyer ersätter du standard-JavaScript-trackPageView-anropet. Om du vill analysera telemetri på Server sidan, använder du en telemetri-initierare för att lägga till användar-ID: t till all Server telemetri. Sedan kan du filtrera och segmentera mått och söka efter användar-ID.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Minska trafiken från min app till Application Insights
* I [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)inaktiverar du alla moduler som du inte behöver, t. ex. prestanda räknar insamlaren.
* Använd [sampling och filtrering](./api-filtering-sampling.md) på SDK: n.
* På dina webb sidor begränsar du antalet AJAX-anrop som rapporteras för varje sid visning. I skript utdraget efter `instrumentationKey:...` infogar du: `,maxAjaxCallsPerView:3` (eller ett lämpligt nummer).
* Om du använder [TrackMetric](./api-custom-events-metrics.md#trackmetric)beräknar du summan av batchar med mått värden innan du skickar resultatet. Det finns en överlagring av TrackMetric () som tillhandahåller för det.

Läs mer om [priser och kvoter](./pricing.md).

## <a name="disable-telemetry"></a>Inaktivera telemetri
För att **dynamiskt stoppa och starta** insamling och överföring av telemetri från servern:

### <a name="aspnet-classic-applications"></a>Klassiska ASP.NET-program

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Andra program
Vi rekommenderar inte att du använder `TelemetryConfiguration.Active` singleton på konsolen eller ASP.net Core program.
Om du skapade `TelemetryConfiguration` instansen själv-Ställ in `DisableTelemetry` på `true` .

För ASP.NET Core program kan du komma åt `TelemetryConfiguration` instansen med [ASP.net Core beroende inmatning](/aspnet/core/fundamentals/dependency-injection/). Mer information finns i [ApplicationInsights för ASP.net Core Applications](./asp-net-core.md) -artikeln.

## <a name="disable-selected-standard-collectors"></a>Inaktivera markerade standard insamlare
Du kan inaktivera standard insamlare (till exempel prestanda räknare, HTTP-begäranden eller beroenden)

* **ASP.NET-program** – ta bort eller kommentera de relevanta raderna i [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)
* **ASP.net Core program** – följa konfigurations alternativen för telemetri i [ApplicationInsights ASP.net Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Visa system prestanda räknare
Bland de mått som du kan visa i mått Utforskaren finns en uppsättning system prestanda räknare. Det finns ett fördefinierat blad med rubriken **servrar** som visar flera av dem.

![Öppna Application Insights resurs och klicka på servrar](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Om du inte ser några prestanda räknar data
* **IIS-server** på din egen dator eller på en virtuell dator. [Installera statusövervakare](./monitor-performance-live-website-now.md).
* **Azure Web Site** – vi stöder inte prestanda räknare ännu. Det finns flera mått som du kan få som en standard del av kontroll panelen i Azure-webbplatsen.
* **UNIX-server**  -  [Installera insamlad](./java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Visa fler prestanda räknare
* Lägg först [till ett nytt diagram](../essentials/metrics-charts.md) och se om räknaren finns i den grundläggande uppsättning som vi erbjuder.
* Om inte, [lägger du till räknaren i uppsättningen som samlas in av modulen prestanda räknare](./performance-counters.md).

