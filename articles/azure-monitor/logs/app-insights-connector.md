---
title: Visa Azure Application Insights-AppData | Microsoft Docs
description: Du kan använda Application Insights-anslutningsprogram-lösningen för att diagnostisera prestanda problem och förstå vad användarna gör med din app när de övervakas med Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c21ed461280dfa617c852fe6c18c30e5d697b704
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031284"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights-anslutningsprogram hanterings lösning (inaktuell)

![Application Insights symbol](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Med stöd för [frågor över olika resurser](../logs/cross-workspace-query.md)krävs inte längre den Application Insights-anslutningsprogram hanterings lösningen. Den har ersatts och tagits bort från Azure Marketplace, tillsammans med OMS-portalen som officiellt förbrukades den 15 januari 2019 för Azures kommersiella moln. Den kommer att dras tillbaka den 30 mars 2019 för Azures moln för amerikanska myndigheter.
>
>Befintliga anslutningar fortsätter att fungera fram till den 30 juni 2019.  Med OMS-portalen är det inte möjligt att konfigurera och ta bort befintliga anslutningar från portalen. Se [ta bort anslutningen med PowerShell](#removing-the-connector-with-powershell) nedan för ett skript på att använda PowerShell för att ta bort befintliga anslutningar.
>
>Anvisningar om hur du frågar Application Insights loggdata för flera program finns i [förena flera Azure Monitor Application Insights resurser](./unify-app-resource-data.md). Mer information om utfasningen av OMS-portalen finns i [OMS-portalen flytta till Azure](./oms-portal-transition.md).
>
> 

Med program insikts kopplings lösningen kan du diagnostisera prestanda problem och förstå vad användarna gör med din app när den övervakas med [Application Insights](../app/app-insights-overview.md). Vyer av samma programtelemetri som utvecklarna ser i Application Insights finns tillgängliga i Log Analytics. När du integrerar dina Application Insights-appar med Log Analytics ökas emellertid synligheten för dina program genom att ha åtgärds-och program data på ett och samma ställe. Med samma vyer kan du samar beta med dina Apps-utvecklare. Vanliga vyer kan hjälpa till att minska tiden för att upptäcka och lösa både program-och plattforms problem.

När du använder lösningen kan du:

- Visa alla dina Application Insights-appar på en och samma plats, även när de är i olika Azure-prenumerationer
- Korrelera infrastruktur data med program data
- Visualisera program data med perspektiv i loggs ökning
- Pivotera från Log Analytics data till din Application Insights-app i Azure Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta andra Log Analytics lösningar samlas data inte in för Application Insights-anslutningsprogram av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](./../agents/agent-windows.md) | Inga | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](../vm/quick-collect-linux-computer.md) | Inga | Lösningen samlar inte in information från Linux-agenter. |
| [SCOM-hanterings grupp](../agents/om-agents.md) | Inga | Lösningen samlar inte in information från agenter i en ansluten SCOM Management Group. |
| [Azure Storage-konto](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Inga | Lösningen samlar inte in information från Azure Storage. |

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration för att få åtkomst till Application Insights-anslutningsprogram information
- Du måste ha minst en konfigurerad Application Insights-resurs.
- Du måste vara ägare eller deltagare i Application Insights resursen.

## <a name="configuration"></a>Konfiguration

1. Aktivera Azure Web Apps-analys-lösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) eller genom att använda processen som beskrivs i [Lägg till Log Analytics lösningar från Lösningsgalleriet](../insights/solutions.md).
2. Bläddra till [Azure Portal](https://portal.azure.com). Välj **alla tjänster** för att öppna Application Insights. Sök sedan efter Application Insights. 
3. Under **prenumerationer** väljer du en prenumeration som har Application Insights resurser och väljer sedan ett eller flera program under **namn**.
4. Klicka på **Spara**.

På cirka 30 minuter blir data tillgängliga och Application Insightss panelen uppdateras med data, t. ex. följande bild:

![Application Insights panel](./media/app-insights-connector/app-insights-tile.png)

Andra saker att tänka på:

- Du kan bara länka Application Insights appar till en Log Analytics arbets yta.
- Du kan bara länka [Basic-eller Enterprise Application Insights-resurser](https://azure.microsoft.com/pricing/details/application-insights) till Log Analytics. Du kan dock använda den kostnads fria nivån av Log Analytics.

## <a name="management-packs"></a>Hanteringspaket

Den här lösningen installerar inte några hanterings paket i anslutna hanterings grupper.

## <a name="use-the-solution"></a>Använd lösningen

I följande avsnitt beskrivs hur du kan använda bladet som visas i Application Insights instrument panelen för att visa och interagera med data från dina appar.

### <a name="view-application-insights-connector-information"></a>Visa Application Insights-anslutningsprogram information

Klicka på panelen **Application Insights** för att öppna instrument panelen **Application Insights** för att se följande blad.

![Skärm bild av Application Insights instrument panel som visar bladet för program, data volym och tillgänglighet.](./media/app-insights-connector/app-insights-dash01.png)

![Skärm bild av Application Insights instrument panel som visar bladet för server begär Anden, fel och undantag.](./media/app-insights-connector/app-insights-dash02.png)

Instrument panelen innehåller de blad som visas i tabellen. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggs ökning som returnerar alla poster när du klickar på **Visa alla** längst ned på bladet eller när du klickar på bladet rubrik.


| **Kolumn** | **Beskrivning** |
| --- | --- |
| Program-antal program | Visar antalet program i program resurser. Visar även program namn och antalet program poster. Klicka på numret för att köra en loggs ökning för <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klicka på ett program namn om du vill köra en loggs ökning för programmet som visar program poster per värd, poster efter typ av telemetri och alla data efter typ (baserat på den senaste dagen). |
| Data volym – värdar som skickar data | Visar antalet dator värdar som skickar data. Visar även dator värdar och antal poster för varje värd. Klicka på numret för att köra en loggs ökning för <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klicka på ett dator namn om du vill köra en loggs ökning för värden som visar program poster per värd, poster efter typ av telemetri och alla data efter typ (baserat på den senaste dagen). |
| Tillgänglighet – webtest-resultat | Visar ett ring diagram för webb test resultat som indikerar pass eller misslyckande. Klicka på diagrammet för att köra en loggs ökning för <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Resultaten visar antalet pass och haverier för alla tester. Den visar alla Web Apps med trafik under den senaste minuten. Klicka på ett program namn om du vill visa en loggs ökning som visar information om misslyckade webbtester. |
| Server begär Anden – begär Anden per timme | Visar ett linje diagram över server begär Anden per timme för olika program. Hovra över en linje i diagrammet för att se de tre främsta programmen som tar emot begär Anden för en tidpunkt. Visar också en lista över de program som tar emot förfrågningar och antalet begär Anden för den valda perioden. <br><br>Klicka på grafen för att köra en loggs ökning <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar ett mer detaljerat linje diagram över server begär Anden per timme för olika program. <br><br> Klicka på ett program i listan om du vill köra en loggs ökning <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> som visar en lista över begär Anden, diagram för förfrågningar över tid och varaktighet och en lista över begär ande svars koder.   |
| Fel – misslyckade förfrågningar per timme | Visar ett linje diagram över misslyckade program begär Anden per timme. Hovra över diagrammet för att se de tre främsta programmen med misslyckade begär Anden för en tidpunkt. Visar även en lista med program med antalet misslyckade förfrågningar för varje. Klicka på diagrammet för att köra en loggs ökning <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar ett mer detaljerat linje diagram över misslyckade program begär Anden. <br><br>Klicka på ett objekt i listan om du vill köra en loggs ökning <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>  som visar misslyckade förfrågningar, diagram för misslyckade förfrågningar över tid och varaktighet för begäran och en lista över misslyckade svars koder för begäran. |
| Undantag – undantag per timme | Visar ett linje diagram med undantag per timme. Hovra över diagrammet för att se de tre främsta programmen med undantag för en tidpunkt. Visar även en lista med program med antalet undantag för var och en. Klicka på diagrammet för att köra en loggs ökning <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> som visar ett mer detaljerat länk diagram över undantag. <br><br>Klicka på ett objekt i listan om du vill köra en loggs ökning <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> som visar en lista över undantag, diagram för undantag över tid och misslyckade förfrågningar, samt en lista över undantags typer.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Visa Application Insights perspektivet med loggs ökning

När du klickar på ett objekt på instrument panelen visas ett Application Insights perspektiv som visas i Sök. Perspektivet innehåller en utökad visualisering, baserat på den typ av telemetri som valts. Därför ändras visualiserings innehållet för olika typer av telemetri.

När du klickar var som helst på bladet program visas standard **program** perspektivet.

![Application Insights program perspektiv](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektivet visar en översikt över det program som du har valt.

Bladet **tillgänglighet** visar en annan perspektivvy där du kan se webb test resultat och relaterade misslyckade förfrågningar.

![Perspektiv för Application Insights tillgänglighet](./media/app-insights-connector/availability-blade-drill-search.png)

När du klickar någonstans i bladet **server begär Anden** eller **haverier** ändras perspektiv komponenterna för att ge dig en visualisering som är relaterad till förfrågningar.

![Bladet Application Insightss problem](./media/app-insights-connector/server-requests-failures-drill-search.png)

När du klickar någonstans på bladet **undantag** visas en visualisering som är anpassad till undantag.

![Bladet Application Insights undantag](./media/app-insights-connector/exceptions-blade-drill-search.png)

Oavsett om du klickar på något av **Application Insights-anslutningsprogram** -instrumentpanelen på själva **Sök** sidan, visas Application Insights perspektivet i en Application Insights fråga. Om du till exempel visar Application Insights data, visar en **&#42;** fråga även fliken perspektiv som följande bild:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspektiv komponenter uppdateras beroende på Sök frågan. Det innebär att du kan filtrera resultaten genom att använda valfritt Sök fält som ger dig möjlighet att se data från:

- Alla dina program
- Ett enda markerat program
- En grupp med program

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Pivotera till en app i Azure Portal

Application Insights-anslutningsprogram blad är utformade för att du ska kunna pivotera till den valda Application Insights-appen *när du använder Azure Portal*. Du kan använda lösningen som en övervaknings plattform på hög nivå som hjälper dig att felsöka en app. När du ser ett potentiellt problem i något av dina anslutna program kan du antingen öka detalj nivån i Log Analytics Sök, eller så kan du pivotera direkt till Application Insights-appen.

Du kan pivotera genom att klicka på ellipserna (**...**) som visas i slutet av varje rad och välja **Öppna i Application Insights**.

>[!NOTE]
>**Öppna i Application Insights** är inte tillgänglig i Azure Portal.

![Öppna i Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Exempel – korrigerade data

Application Insights tillhandahåller *[provtagnings korrigering](../app/sampling.md)* som hjälper till att minska telemetri trafiken. När du aktiverar sampling i Application Insights-appen får du ett minskat antal poster som lagras både i Application Insights och i Log Analytics. Medan data konsekvens bevaras på **Application Insights-anslutningsprogram** sida och perspektiv, bör du manuellt korrigera exempel data för dina anpassade frågor.

Här är ett exempel på hur du kan sampla om korrigering i en loggs öknings fråga:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Fältet **antal exempel** finns i alla poster och visar antalet data punkter som posten representerar. Om du aktiverar sampling för din Application Insights-app är **antalet samplingar** större än 1. Om du vill räkna det faktiska antalet poster som ditt program genererar, summerar du antalet **exempel antal** .

Sampling påverkar bara det totala antalet poster som programmet genererar. Du behöver inte korrigera sampling för mått fält som **RequestDuration** eller **AvailabilityDuration**  eftersom dessa fält visar medelvärdet för de poster som visas.

## <a name="input-data"></a>Indata

Lösningen tar emot följande typer av telemetri från dina anslutna Application Insights-appar:

- Tillgänglighet
- Undantag
- Begäranden
- Sid visningar – för att din arbets yta ska kunna ta emot sidvyer måste du konfigurera dina appar för att samla in informationen. Mer information finns i [PageViews](../app/api-custom-events-metrics.md#page-views).
- Anpassade händelser – för att din arbets yta ska kunna ta emot anpassade händelser måste du konfigurera dina appar för att samla in informationen. Mer information finns i [TrackEvent](../app/api-custom-events-metrics.md#trackevent).

Data tas emot av Log Analytics från Application Insights när de blir tillgängliga.

## <a name="output-data"></a>Utdata

En post med en *typ* av *ApplicationInsights* skapas för varje typ av indata. ApplicationInsights-poster har egenskaper som visas i följande avsnitt:

### <a name="generic-fields"></a>Generiska fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tid för posten |
| ApplicationId | Instrumentation-nyckel för Application Insights-appen |
| ApplicationName | Namnet på Application Insights-appen |
| RoleInstance | ID för Server värd |
| DeviceType | Klienten het |
| ScreenResolution |   |
| Kontinent | Kontinent där begäran har sitt ursprung |
| Land | Land/region där begäran har sitt ursprung |
| Region | Provins, delstat eller nationella inställningar där begäran kommer |
| City | Ort eller stad där begäran har sitt ursprung |
| isSynthetic | Indikerar om begäran har skapats av en användare eller en automatiserad metod. Sant = automatisk metod eller falskt = genererad användare |
| SamplingRate | Procent andel telemetri som genererats av SDK som skickas till portalen. Intervallet 0,0-100,0. |
| SampledCount | 100/(SamplingRate). Till exempel 4 = &gt; 25% |
| IsAuthenticated | Sant eller falskt |
| OperationID | Objekt som har samma åtgärds-ID visas som relaterade objekt i portalen. Vanligt vis ID för begäran |
| ParentOperationID | ID för den överordnade åtgärden |
| OperationName |   |
| SessionId | GUID för att unikt identifiera den session där begäran skapades |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Tillgänglighets bara fält

| Egenskap | Beskrivning |
| --- | --- |
| TelemetryType | Tillgänglighet |
| AvailabilityTestName | Namnet på webb testet |
| AvailabilityRunLocation | Geografisk källa för http-begäran |
| AvailabilityResult | Visar resultatet av webb testet som lyckats |
| AvailabilityMessage | Meddelandet som är kopplat till webb testet |
| AvailabilityCount | 100/(samplings frekvens). Till exempel 4 = &gt; 25% |
| DataSizeMetricValue | 1,0 eller 0,0 |
| DataSizeMetricCount | 100/(samplings frekvens). Till exempel 4 = &gt; 25% |
| AvailabilityDuration | Tid i millisekunder för webb testets varaktighet |
| AvailabilityDurationCount | 100/(samplings frekvens). Till exempel 4 = &gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Unikt GUID för webb testet |
| AvailabilityTimestamp | Exakt tidsstämpel för tillgänglighets testet |
| AvailabilityDurationMin | För exempel poster visar det här fältet den minsta varaktigheten för webbtest (millisekunder) för de representerade data punkterna |
| AvailabilityDurationMax | För exempel poster visar det här fältet Maximal varaktighet för webb test (millisekunder) för de representerade data punkterna |
| AvailabilityDurationStdDev | För exempel poster visar det här fältet standard avvikelsen mellan alla webbtest-varaktigheter (millisekunder) för de representerade data punkterna |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Undantags bara fält

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Undantag |
| Undantag | Typ av undantag |
| ExceptionMethod | Metoden som skapar undantaget |
| ExceptionAssembly | Sammansättningen inkluderar ramverk och version samt token för offentlig nyckel |
| ExceptionGroup | Typ av undantag |
| ExceptionHandledAt | Anger nivån som hanterade undantaget |
| ExceptionCount | 100/(samplings frekvens). Till exempel 4 = &gt; 25% |
| ExceptionMessage | Meddelande om undantaget |
| ExceptionStack | Fullständig stack i undantaget |
| ExceptionHasStack | True, om undantag har en stack |



### <a name="request-specific-fields"></a>Request-/regionsspecifika fält

| Egenskap | Beskrivning |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Förfrågan |
| ResponseCode | HTTP-svar som skickats till klienten |
| RequestSuccess | Visar att åtgärden lyckades eller misslyckades. Sant eller falskt. |
| RequestID | ID för att unikt identifiera begäran |
| RequestName | Hämta/publicera + URL-bas |
| RequestDuration | Tid, i sekunder, för varaktighet för begäran |
| URL | URL för begäran som inte inkluderar värd |
| Värd | Webb server värd |
| URLBase | Fullständig URL för begäran |
| ApplicationProtocol | Typ av protokoll som används av programmet |
| RequestCount | 100/(samplings frekvens). Till exempel 4 = &gt; 25% |
| RequestDurationCount | 100/(samplings frekvens). Till exempel 4 = &gt; 25% |
| RequestDurationMin | För exempel poster visar det här fältet minsta varaktighet för begäran (millisekunder) för de representerade data punkterna. |
| RequestDurationMax | För exempel poster visar det här fältet Maximal varaktighet för begäran (millisekunder) för de representerade data punkterna |
| RequestDurationStdDev | För exempel poster visar det här fältet standard avvikelsen mellan alla begär ande varaktigheter (millisekunder) för de representerade data punkterna |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Den här lösningen har inte en uppsättning exempel på loggs ökningar som visas på instrument panelen. Exempel på loggs öknings frågor med beskrivningar visas i avsnittet [visa Application Insights-anslutningsprogram information](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>Ta bort anslutningen med PowerShell
Med OMS-portalen är det inte möjligt att konfigurera och ta bort befintliga anslutningar från portalen. Du kan ta bort befintliga anslutningar med följande PowerShell-skript. Du måste vara ägare eller deltagare i arbets ytan och läsaren för Application Insights resurs för att utföra den här åtgärden.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Du kan hämta en lista över program med hjälp av följande PowerShell-skript som anropar ett REST API-anrop. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Det här skriptet kräver en Bearer-autentiseringstoken för autentisering mot Azure Active Directory. Ett sätt att hämta denna token använder en artikel i [REST API dokumentations webbplats](/rest/api/loganalytics/datasources/createorupdate). Klicka på **testa** och logga in på din Azure-prenumeration. Du kan kopiera Bearer-token från **förfrågnings förhands granskningen** enligt följande bild.


![Bearer-token](media/app-insights-connector/bearer-token.png)


Du kan också hämta en lista över program som använder en logg fråga:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Nästa steg

- Använd [loggs ökningen](./log-query-overview.md) för att visa detaljerad information om dina Application Insights-appar.