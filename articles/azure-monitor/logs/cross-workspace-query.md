---
title: Fråga över resurser med Azure Monitor | Microsoft Docs
description: Den här artikeln beskriver hur du kan fråga mot resurser från flera arbets ytor och App Insights-appen i din prenumeration.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/11/2021
ms.openlocfilehash: e007f5af214dcfa475eb59a5981bc580b9499915
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314243"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Utföra logg frågor i Azure Monitor som sträcker sig över arbets ytor och appar

Azure Monitor loggar stöder frågor över flera Log Analytics arbets ytor och Application Insights app i samma resurs grupp, en annan resurs grupp eller en annan prenumeration. Detta ger dig en systemomfattande vy över dina data.

Det finns två metoder för att fråga data som lagras i flera arbets ytor och appar:
1. Uttryckligen genom att ange information om arbets yta och appar. Den här tekniken beskrivs i den här artikeln.
2. Implicit användning av [resurs kontext frågor](./design-logs-deployment.md#access-mode). När du frågar i kontexten för en resurs, resurs grupp eller en prenumeration hämtas relevanta data från alla arbets ytor som innehåller data för dessa resurser. Application Insights data som lagras i appar kommer inte att hämtas.

> [!IMPORTANT]
> Om du använder en [arbets yta som baseras på arbets ytans Application Insights resurs](../app/create-workspace-resource.md) telemetri lagras i en Log Analytics arbets yta med alla andra loggdata. Använd uttrycket arbets yta () för att skriva en fråga som inkluderar program i flera arbets ytor. För flera program i samma arbets yta behöver du inte en fråga om flera arbets ytor.


## <a name="cross-resource-query-limits"></a>Begränsningar för kors resurs frågor 

* Antalet Application Insights-resurser och Log Analytics arbets ytor som du kan ta med i en enskild fråga är begränsade till 100.
* Frågan över resurser stöds inte i View Designer. Du kan redigera en fråga i Log Analytics och fästa den på Azure-instrumentpanelen för att [visualisera en logg fråga](../visualize/tutorial-logs-dashboards.md) eller ta med i [arbets böcker](../visualize/workbooks-overview.md).
* Frågor över resurser i logg aviseringar stöds bara i det aktuella [scheduledQueryRules-API: et](/rest/api/monitor/scheduledqueryrules). Om du använder äldre API för Log Analytics-varningar måste du [Växla till det aktuella API: et](../alerts/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Fråga i Log Analytics arbets ytor och från Application Insights
Om du vill referera till en annan arbets yta i din fråga använder du ID för [*arbets ytan*](../logs/workspace-expression.md) och för en app från Application Insights använder du [*app*](./app-expression.md) -ID: t.  

### <a name="identifying-workspace-resources"></a>Identifiera arbets ytans resurser
Följande exempel visar frågor i Log Analytics arbets ytor för att returnera sammanfattade antal loggar från uppdaterings tabellen på en arbets yta med namnet *ContosoRetail*. 

Att identifiera en arbets yta kan utföras på flera sätt:

* Resurs namn – är ett läsbart namn på arbets ytan, ibland kallat *komponent namn*. 

    >[!Note]
    >Eftersom app-och arbetsyte namn inte är unika kan den här identifieraren vara tvetydig. Om det finns flera instanser av resurs namnet ska referensen vara av kvalificerat namn, resurs-ID eller Azure-resurs-ID.

    `workspace("contosoretail-it").Update | count`

* Kvalificerat namn – är det fullständiga namnet på arbets ytan, som består av prenumerations namnet, resurs gruppen och komponent namnet i följande format: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Eftersom Azures prenumerations namn inte är unika kan den här identifieraren vara tvetydig.

* Arbetsyte-ID – ett arbetsyte-ID är den unika, oföränderliga, identifierare som tilldelats varje arbets yta som representerar en globalt unik identifierare (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure-resurs-ID – den Azure-definierade unika identiteten för arbets ytan. Du använder resurs-ID: t när resurs namnet är tvetydigt.  För arbets ytor är formatet: */Subscriptions/subscriptionId/ResourceGroups/resourceGroup/providers/Microsoft. OperationalInsights/arbets ytor/componentName*.  

    Exempel:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identifiera ett program
I följande exempel returneras ett sammanfattat antal begär Anden som gjorts mot en app med namnet *fabrikamapp* i Application Insights. 

Att identifiera ett program i Application Insights kan utföras med ett *app-uttryck (Identifier)* .  Argumentet *Identifier* anger appen med något av följande:

* Resurs namn – är ett läsligt namn på appen, som ibland kallas för *komponent namn*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Att identifiera ett program efter namn förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet, Miss lyckas frågan på grund av tvetydigheten. I så fall måste du använda någon av de andra identifierarna.

* Kvalificerat namn – är det fullständiga namnet på appen, som består av prenumerations namnet, resurs gruppen och komponent namnet i följande format: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Eftersom Azures prenumerations namn inte är unika kan den här identifieraren vara tvetydig. 
    >

* ID – programmets GUID för programmet.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure-resurs-ID – den Azure-definierade unika identiteten för appen. Du använder resurs-ID: t när resurs namnet är tvetydigt. Formatet är: */Subscriptions/subscriptionId/ResourceGroups/resourceGroup/providers/Microsoft. OperationalInsights/Components/componentName*.  

    Exempel:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Utföra en fråga över flera resurser
Du kan fråga flera resurser från någon av dina resurs instanser, och dessa kan vara arbets ytor och appar kombinerade.
    
Exempel för fråga över två arbets ytor:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Använda frågor över flera resurser för flera resurser
När du använder frågor över flera resurser för att korrelera data från flera arbets ytor Log Analytics och Application Insights resurser, kan frågan bli komplex och svår att underhålla. Du bör använda [funktioner i Azure Monitor logg frågor](./functions.md) för att avgränsa fråge logiken från omfånget för frågans resurser, vilket fören klar fråge strukturen. I följande exempel visas hur du kan övervaka flera Application Insights resurser och visualisera antalet misslyckade förfrågningar efter program namn. 

Skapa en fråga som refererar till omfånget för Application Insights resurser. `withsource= SourceApp`Kommandot lägger till en kolumn som anger det program namn som skickade loggen. [Spara frågan som funktion](./functions.md#create-a-function) med aliaset _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Du kan nu [använda den här funktionen](./functions.md#use-a-function) i en kors resurs fråga som liknar följande. Funktions Ali Aset _applicationsScoping_ returnerar unionen av begär ande tabellen från alla definierade program. Frågan filtrerar sedan efter misslyckade förfrågningar och visualiserar trender efter program. Operatorn _parse_ är valfri i det här exemplet. Den extraherar program namnet från egenskapen _SourceApp_ .

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Den här metoden kan inte användas med logg aviseringar eftersom åtkomst verifieringen av aviserings regel resurserna, inklusive arbets ytor och program, utförs när aviseringen skapas. Det går inte att lägga till nya resurser till funktionen när aviseringen har skapats. Om du föredrar att använda funktionen för resurs omfång i logg aviseringar måste du redigera aviserings regeln i portalen eller med en Resource Manager-mall för att uppdatera de omfångs resurserna. Du kan också inkludera listan över resurser i logg aviserings frågan.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Nästa steg

- Granska [analysera loggdata i Azure Monitor](./log-query-overview.md) om du vill ha en översikt över logg frågor och hur Azure Monitor loggdata är strukturerade.