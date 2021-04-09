---
title: Förena flera Azure Monitor Application Insights resurser | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder en funktion i Azure Monitor loggar för att fråga flera Application Insights resurser och visualisera dessa data.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7f49bd23347b53f9a5287973c6b2aba82da50ca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100624929"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Förena flera Azure Monitor Application Insights resurser 
I den här artikeln beskrivs hur du frågar och visar alla dina Application Insights loggdata på ett ställe, även om de finns i olika Azure-prenumerationer, som ersättning för utfasningen av Application Insights-anslutningsprogram. Antalet Application Insights-resurser som du kan ta med i en enskild fråga är begränsat till 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Rekommenderad metod för att fråga flera Application Insights resurser 
Att visa flera Application Insights resurser i en fråga kan vara besvärligt och svårt att underhålla. I stället kan du använda funktionen för att avgränsa fråge logiken från program omfånget.  

Det här exemplet visar hur du kan övervaka flera Application Insights resurser och visualisera antalet misslyckade förfrågningar efter program namn.

Skapa en funktion med operatorn union med listan över program och spara sedan frågan i arbets ytan som funktion med aliaset *applicationsScoping*. 

Du kan ändra de program som visas när som helst i portalen genom att gå till Utforskaren på arbets ytan och välja funktionen för redigering och sedan Spara eller använda `SavedSearch` PowerShell-cmdleten. 

>[!NOTE]
>Den här metoden kan inte användas med logg aviseringar eftersom åtkomst verifieringen av aviserings regel resurserna, inklusive arbets ytor och program, utförs när aviseringen skapas. Det går inte att lägga till nya resurser till funktionen när aviseringen har skapats. Om du föredrar att använda funktionen för resurs omfång i logg aviseringar måste du redigera aviserings regeln i portalen eller med en Resource Manager-mall för att uppdatera de omfångs resurserna. Du kan också inkludera listan över resurser i logg aviserings frågan.

`withsource= SourceApp`Kommandot lägger till en kolumn i resultatet som anger det program som skickade loggen. Operatorn parse är valfri i det här exemplet och använder för att extrahera program namnet från egenskapen SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Du är nu redo att använda funktionen applicationsScoping i frågan över resurser:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Frågan använder Application Insights schema, även om frågan körs i arbets ytan eftersom funktionen applicationsScoping returnerar Application Insights data strukturen. Funktions Ali Aset returnerar union av begär Anden från alla definierade program. Frågan filtrerar sedan efter misslyckade förfrågningar och visualiserar trender efter program.

![Exempel på resultat över frågor](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Frågor över resurser](../logs/cross-workspace-query.md) i logg aviseringar stöds bara i det aktuella [scheduledQueryRules-API: et](/rest/api/monitor/scheduledqueryrules). Om du använder äldre API för Log Analytics-varningar måste du [Växla till det aktuella API: et](../alerts/alerts-log-api-switch.md). [Se exempel på mallar](../alerts/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Schema skillnader för Application Insights och Log Analytics arbets yta
I följande tabell visas schema skillnaderna mellan Log Analytics och Application Insights.  

| Egenskaper för Log Analytics arbets yta| Application Insights resurs egenskaper|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | Program|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | varaktighet |
| AvailabilityMessage | meddelande |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Webbläsare | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Dator | cloud_RoleInstance | 
| Land | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | meddelande | 
| Undantag | typ |
| OperationID | operation_id |
| OperationName | operation_Name | 
| Operativsystem | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | varaktighet | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | varaktighet | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | lyckades | 
| ResponseCode | resultCode | 
| Roll | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | typ |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Nästa steg

Använd [loggs ökningen](../logs/log-query-overview.md) för att visa detaljerad information om dina Application Insights-appar.

