---
title: Fråga mellan resurser med Azure Monitor | Microsoft Docs
description: I den här artikeln beskrivs hur du kan fråga mot resurser från flera arbetsytor och App Insights-appen i din prenumeration.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/11/2021
ms.openlocfilehash: 19cc85751fc5e4a165b646ac89d9d6b6e90c4408
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379561"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Utföra loggfrågor i Azure Monitor som sträcker sig över arbetsytor och appar

Azure Monitor-loggar stöder frågor över flera Log Analytics-arbetsytor och Application Insights i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta ger dig en systemomfattande vy över dina data.

Det finns två metoder för att fråga efter data som lagras i flera arbetsytor och appar:
1. Uttryckligen genom att ange information om arbetsytan och appen. Den här tekniken beskrivs i den här artikeln.
2. Implicit användning av [resurskontextfrågor](./design-logs-deployment.md#access-mode). När du frågar i kontexten för en specifik resurs, resursgrupp eller en prenumeration hämtas relevanta data från alla arbetsytor som innehåller data för dessa resurser. Application Insights data som lagras i appar hämtas inte.

> [!IMPORTANT]
> Om du använder en [arbetsytebaserad Application Insights lagras](../app/create-workspace-resource.md) resurstelemetri på en Log Analytics-arbetsyta med alla andra loggdata. Använd uttrycket workspace() för att skriva en fråga som innehåller program på flera arbetsytor. För flera program på samma arbetsyta behöver du inte en fråga mellan arbetsytor.


## <a name="cross-resource-query-limits"></a>Frågebegränsningar mellan resurser 

* Antalet resurser Application Insights Log Analytics-arbetsytor som du kan inkludera i en enskild fråga är begränsat till 100.
* Frågor mellan resurser stöds inte i Vydesignern. Du kan skapa en fråga i Log Analytics och fästa den på Azure-instrumentpanelen för att [visualisera en loggfråga](../visualize/tutorial-logs-dashboards.md) eller inkludera den i [Arbetsböcker.](../visualize/workbooks-overview.md)
* Frågor mellan resurser i logga aviseringar stöds endast i den aktuella [scheduledQueryRules API.](/rest/api/monitor/scheduledqueryrules) Om du använder det äldre API:et för Log Analytics-aviseringar måste du [växla till det aktuella API:et](../alerts/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Fråga mellan Log Analytics-arbetsytor och från Application Insights
Om du vill referera till [](../logs/workspace-expression.md) en annan arbetsyta i din fråga använder du arbetsyteidentifieraren och för en app Application Insights du [*appidentifieraren.*](./app-expression.md)  

### <a name="identifying-workspace-resources"></a>Identifiera arbetsyteresurser
I följande exempel visas frågor över Log Analytics-arbetsytor för att returnera sammanfattade antal loggar från uppdateringstabellen på en arbetsyta med namnet *contosoretail-it*. 

Du kan identifiera en arbetsyta på flera olika sätt:

* Resursnamn – är ett läsbart namn på arbetsytan, som ibland kallas *komponentnamn.* 

    >[!IMPORTANT]
    >Eftersom app- och arbetsytenamn inte är unika kan den här identifieraren vara tvetydig. Vi rekommenderar att referensen är efter kvalificerat namn, arbetsyte-ID eller Azure-resurs-ID.

    `workspace("contosoretail-it").Update | count`

* Kvalificerat namn – är arbetsytans "fullständiga namn" och består av prenumerationsnamnet, resursgruppen och komponentnamnet i följande format: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan den här identifieraren vara tvetydig.

* Id för arbetsyta – Ett arbetsyte-ID är den unika, oföränderliga identifierare som tilldelas varje arbetsyta som representeras som en globalt unik identifierare (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure-resurs-ID – arbetsytans Azure-definierade unika identitet. Du använder resurs-ID:t när resursnamnet är tvetydigt.  För arbetsytor är formatet: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Exempel:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identifiera ett program
I följande exempel returneras ett sammanfattat antal begäranden som görs mot en app med *namnet fabrikamapp* i Application Insights. 

Att identifiera ett program Application Insights kan utföras med *uttrycket app(Identifier).*  Argumentet *Identifierare* anger appen med något av följande:

* Resursnamn – är ett läsbart namn på appen, som ibland kallas *komponentnamnet*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Att identifiera ett program efter namn förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet misslyckas frågan på grund av tvetydigheten. I det här fallet måste du använda någon av de andra identifierarna.

* Kvalificerat namn – är appens "fullständiga namn" och består av prenumerationens namn, resursgrupp och komponentnamn i det här formatet: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan den här identifieraren vara tvetydig. 
    >

* ID – programmets app-GUID.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure-resurs-ID – appens Azure-definierade unika identitet. Du använder resurs-ID:t när resursnamnet är tvetydigt. Formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName*.  

    Exempel:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Utföra en fråga över flera resurser
Du kan köra frågor mot flera resurser från någon av dina resursinstanser. Dessa kan vara arbetsytor och appar som kombineras.
    
Exempel för fråga mellan två arbetsytor:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Använda frågor mellan resurser för flera resurser
När du använder frågor mellan resurser för att korrelera data från flera Log Analytics-arbetsytor och Application Insights resurser kan frågan bli komplex och svår att underhålla. Du bör använda [funktioner i Azure Monitor för](./functions.md) att separera frågelogiken från frågeresursernas omfång, vilket förenklar frågestrukturen. I följande exempel visas hur du kan övervaka flera Application Insights resurser och visualisera antalet misslyckade begäranden efter programnamn. 

Skapa en fråga som liknar följande som refererar till omfånget för Application Insights resurser. Kommandot `withsource= SourceApp` lägger till en kolumn som anger det programnamn som skickade loggen. [Spara frågan som funktion med](./functions.md#create-a-function) _aliasprogrammenKopiera_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Du kan nu [använda den här](./functions.md#use-a-function) funktionen i en fråga mellan resurser som följande. Funktionens _aliasprogramScoping_ returnerar union av begärandetabellen från alla definierade program. Frågan filtrerar sedan efter misslyckade begäranden och visualiserar trenderna efter program. _Parse-operatorn_ är valfri i det här exemplet. Det extraherar programnamnet från _egenskapen SourceApp._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Den här metoden kan inte användas med logga aviseringar eftersom åtkomstvalidering av aviseringsregelresurser, inklusive arbetsytor och program, utförs när aviseringen skapas. Det finns inte stöd för att lägga till nya resurser i funktionen när aviseringen har skapats. Om du föredrar att använda funktionen för resursomfång i loggaviseringar måste du redigera aviseringsregeln i portalen eller med en Resource Manager-mall för att uppdatera de begränsade resurserna. Du kan också ta med listan över resurser i loggaviseringsfrågan.


![Tidsschema](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Nästa steg

- Granska [Analysera loggdata i Azure Monitor](./log-query-overview.md) för en översikt över loggfrågor och hur Azure Monitor är strukturerade.