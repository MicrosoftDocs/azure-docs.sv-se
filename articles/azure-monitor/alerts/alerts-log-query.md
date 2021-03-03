---
title: Optimera logg aviserings frågor | Microsoft Docs
description: Rekommendationer för att skriva effektiva aviserings frågor
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: cda3af012a83342d5650c542fafdcd6bc36bd8e3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717986"
---
# <a name="optimizing-log-alert-queries"></a>Optimera logg aviserings frågor
Den här artikeln beskriver hur du skriver och konverterar [logg aviserings](./alerts-unified-log.md) frågor för att uppnå optimala prestanda. Optimerade frågor minskar svars tiden och belastningen på aviseringar som körs ofta.

## <a name="how-to-start-writing-an-alert-log-query"></a>Hur du börjar skriva en varnings logg fråga

Aviserings frågor startar från att [köra frågor mot loggdata i Log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) som visar problemet. Du kan använda [aviserings frågans exempel ämne](../logs/example-queries.md) för att förstå vad du kan identifiera. Du kan också [komma igång med att skriva en egen fråga](../logs/log-analytics-tutorial.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Frågor som visar problemet och inte aviseringen

Aviserings flödet skapades för att transformera resultaten som indikerar ett problem med en avisering. Till exempel, i ett fall av en fråga som:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Om syftet med användaren är att varna när den här händelse typen inträffar, lägger aviserings logiken till i `count` frågan. Frågan som ska köras är:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Du behöver inte lägga till aviserings logik i frågan och göra detta kan till och med orsaka problem. I exemplet ovan, om du inkluderar `count` i din fråga, resulterar det alltid i värdet 1, eftersom aviserings tjänsten kommer att göra `count` `count` .

### <a name="avoid-limit-and-take-operators"></a>Undvik `limit` och `take` operatörer

Att använda `limit` och `take` i frågor kan öka svars tiden och belastningen på aviseringar eftersom resultatet inte är konsekvent över tid. Det är önskvärt att du bara använder den om det behövs.

## <a name="log-query-constraints"></a>Begränsningar för logg frågor
[Logg frågor i Azure Monitor](../logs/log-query-overview.md) börjar med antingen en tabell, [`search`](/azure/kusto/query/searchoperator) eller- [`union`](/azure/kusto/query/unionoperator) operator.

Frågor för logg varnings regler ska alltid börja med en tabell för att definiera en tydlig omfattning, vilket förbättrar både frågans prestanda och relevansen för resultatet. Frågor i varnings regler körs ofta, så användningen av `search` och `union` kan leda till onödig omkostnader för att lägga till svars tid i aviseringen, eftersom det måste genomsökas över flera tabeller. Dessa operatörer minskar också möjligheten för aviserings tjänsten att optimera frågan.

Vi stöder inte att du skapar eller ändrar logg aviserings regler som använder `search` eller `union` -operatorer, förväntar sig för frågor över hela resursen.

Följande aviserings fråga är till exempel begränsad till tabellen _SecurityEvent_ och söker efter ett särskilt händelse-ID. Det är den enda tabell som frågan måste bearbeta.

``` Kusto
SecurityEvent
| where EventID == 4624
```

Logg varnings regler som använder [kors resurs frågor](../logs/cross-workspace-query.md) påverkas inte av den här ändringen eftersom frågor över olika resurser använder en typ av `union` , vilket begränsar frågans omfång till vissa resurser. Följande exempel är en giltig logg aviserings fråga:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [Frågor över resurser](../logs/cross-workspace-query.md) stöds i det nya scheduledQueryRules- [API: et](/rest/api/monitor/scheduledqueryrules). Om du fortfarande använder det [äldre Log Analytics varnings-API: et](./api-alerts.md) för att skapa logg aviseringar kan du läsa mer om att växla [här](../alerts/alerts-log-api-switch.md).

## <a name="examples"></a>Exempel
Följande exempel innehåller logg frågor som använder `search` och `union` ger instruktioner som du kan använda för att ändra dessa frågor för användning i aviserings regler.

### <a name="example-1"></a>Exempel 1
Du vill skapa en logg aviserings regel med hjälp av följande fråga som hämtar prestanda information med `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Om du vill ändra den här frågan börjar du med att använda följande fråga för att identifiera tabellen som egenskaperna tillhör:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

Resultatet av den här frågan visar att egenskapen _CounterName_ kommer från tabellen _perf_ .

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för aviserings regeln:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Exempel 2
Du vill skapa en logg aviserings regel med hjälp av följande fråga som hämtar prestanda information med `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Om du vill ändra den här frågan börjar du med att använda följande fråga för att identifiera tabellen som egenskaperna tillhör:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

Resultatet av den här frågan visar att egenskapen _ObjectName_ och _CounterName_ kommer från tabellen _perf_ .

Du kan använda det här resultatet för att skapa följande fråga som du skulle använda för aviserings regeln:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Exempel 3

Du vill skapa en logg aviserings regel med hjälp av följande fråga som använder både `search` och `union` för att hämta prestanda information: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Om du vill ändra den här frågan börjar du med att använda följande fråga för att identifiera tabellen som egenskaperna i den första delen av frågan tillhör: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

Resultatet av den här frågan visar att alla dessa egenskaper kommer från tabellen _perf_ .

Används nu `union` med `withsource` kommando för att identifiera vilken käll tabell som har bidragit till varje rad.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

Resultatet av den här frågan visar att dessa egenskaper också kommer från tabellen _perf_ .

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för aviserings regeln: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Exempel 4
Du vill skapa en logg aviserings regel med hjälp av följande fråga som förenar resultatet av två `search` frågor:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Om du vill ändra frågan börjar du med att använda följande fråga för att identifiera den tabell som innehåller egenskaperna på vänster sida av kopplingen: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

Resultatet visar att egenskaperna på vänster sida av kopplingen tillhör _SecurityEvent_ -tabellen. 

Använd nu följande fråga för att identifiera tabellen som innehåller egenskaperna på höger sida av kopplingen: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
Resultatet visar att egenskaperna till höger i kopplingen tillhör _pulsslags_ tabellen.

Du kan använda dessa resultat för att skapa följande fråga som du skulle använda för aviserings regeln: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [logg aviseringar](alerts-log.md) i Azure Monitor.
- Lär dig mer om [logg frågor](../logs/log-query-overview.md).