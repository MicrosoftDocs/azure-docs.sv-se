---
title: Använda Log Analytics aviserings REST API
description: 'Med Log Analytics aviserings REST API kan du skapa och hantera aviseringar i Log Analytics, som är en del av Log Analytics.  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.'
ms.subservice: logs
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6c347b7b8ddaac103019e15e32fb5c06219e0064
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625804"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Skapa och hantera aviserings regler i Log Analytics med REST API 

> [!IMPORTANT]
> Som meddelades [, Log Analytics-arbetsytor](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)som skapats efter den *1 juni 2019* hantera aviserings regler med det aktuella [scheduledQueryRules-API: et](/rest/api/monitor/scheduledqueryrules/). Kunderna uppmanas att [Växla till aktuellt API](./alerts-log-api-switch.md) i äldre arbets ytor för att utnyttja Azure Monitor scheduledQueryRules- [förmåner](./alerts-log-api-switch.md#benefits). Den här artikeln beskriver hur du hanterar aviserings regler med hjälp av det äldre API: et.

Med Log Analytics aviserings REST API kan du skapa och hantera aviseringar i Log Analytics.  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.

Log Analytics Sök REST API är RESTful och kan nås via Azure Resource Manager REST API. I det här dokumentet hittar du exempel där API: et kan nås från en PowerShell-kommandorad med hjälp av  [ARMClient](https://github.com/projectkudu/ARMClient), ett kommando rads verktyg med öppen källkod som gör det enklare att anropa Azure Resource Manager API. Användning av ARMClient och PowerShell är ett av många alternativ för att få åtkomst till API: et för Log Analytics search. Med dessa verktyg kan du använda RESTful-Azure Resource Manager-API: et för att skapa anrop till Log Analytics arbets ytor och utföra Sök kommandon i dem. API: n kommer att mata ut Sök resultat till dig i JSON-format, så att du kan använda Sök resultaten på många olika sätt program mässigt.

## <a name="prerequisites"></a>Förutsättningar
Aviseringar kan för närvarande bara skapas med en sparad sökning i Log Analytics.  Du kan referera till [loggs öknings REST API](../log-query/log-query-overview.md) för mer information.

## <a name="schedules"></a>Scheman
En sparad sökning kan ha ett eller flera scheman. Schemat definierar hur ofta sökningen ska köras och det tidsintervall under vilket villkoren identifieras.
Schemana har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Intervall |Hur ofta sökningen körs. Mätt i minuter. |
| QueryTimeSpan |Det tidsintervall under vilket villkoret utvärderas. Måste vara lika med eller större än Interval. Mätt i minuter. |
| Version |Den API-version som används.  För närvarande ska detta alltid vara inställt på 1. |

Anta till exempel en händelse fråga med ett intervall på 15 minuter och ett TimeSpan på 30 minuter. I det här fallet skulle frågan köras var 15: e minut och en avisering skulle utlösas om villkoren fortsätter att lösas till sant över 30 minuters intervall.

### <a name="retrieving-schedules"></a>Hämtar scheman
Använd Get-metoden för att hämta alla scheman för en sparad sökning.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

Använd Get-metoden med ett schema-ID för att hämta ett visst schema för en sparad sökning.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

Följande är ett exempel svar för ett schema.

```json
{
   "value": [{
      "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
      "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
      "properties": {
         "Interval": 15,
         "QueryTimeSpan": 15,
         "Enabled": true,
      }
   }]
}
```

### <a name="creating-a-schedule"></a>Skapa ett schema
Använd metoden för att skapa ett nytt schema med ett unikt schema-ID.  Två scheman kan inte ha samma ID även om de är kopplade till olika sparade sökningar.  När du skapar ett schema i Log Analytics-konsolen skapas ett GUID för schema-ID: t.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics API måste vara i gemener.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Redigera ett schema
Använd metoden för att skicka med ett befintligt schema-ID för samma sparade sökning för att ändra schemat. i exemplet nedan är schemat inaktiverat. Bröd texten i begäran måste innehålla *etag* för schemat.

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Tar bort scheman
Använd metoden Delete med ett schema-ID för att ta bort ett schema.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer som ska utföras, till exempel för att skicka ett e-postmeddelande eller starta en Runbook, eller så kan den definiera ett tröskelvärde som avgör när resultatet av en sökning matchar vissa villkor.  Vissa åtgärder definierar båda för att processerna ska utföras när tröskelvärdet är uppfyllt.

Alla åtgärder har egenskaperna i följande tabell.  Olika typer av aviseringar har olika ytterligare egenskaper, som beskrivs nedan.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |Typ av åtgärd.  För närvarande är de möjliga värdena avisering och webhook. |
| `Name` |Visnings namn för aviseringen. |
| `Version` |Den API-version som används.  För närvarande ska detta alltid vara inställt på 1. |

### <a name="retrieving-actions"></a>Hämtar åtgärder

Använd Get-metoden för att hämta alla åtgärder för ett schema.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Använd Get-metoden med åtgärds-ID: t för att hämta en viss åtgärd för ett schema.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Skapa eller redigera åtgärder
Använd metoden för att spara med ett åtgärds-ID som är unikt för schemat för att skapa en ny åtgärd.  När du skapar en åtgärd i Log Analytics-konsolen är ett GUID för åtgärds-ID: t.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics API måste vara i gemener.

Använd metoden för att skicka med ett befintligt åtgärds-ID för samma sparade sökning för att ändra schemat.  Bröd texten i begäran måste innehålla etag för schemat.

Förfrågnings formatet för att skapa en ny åtgärd varierar beroende på åtgärds typ, så dessa exempel anges i avsnitten nedan.

### <a name="deleting-actions"></a>Tar bort åtgärder

Använd metoden Delete med åtgärds-ID: t för att ta bort en åtgärd.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Aviserings åtgärder
Ett schema bör ha en och endast en aviserings åtgärd.  Aviserings åtgärder har ett eller flera av avsnitten i följande tabell.  Var och en beskrivs i detalj nedan.

| Avsnitt | Description | Användning |
|:--- |:--- |:--- |
| Tröskelvärde |Villkor för när åtgärden körs.| Krävs för varje avisering, innan eller efter att de har utökats till Azure. |
| Allvarlighetsgrad |Etikett som används för att klassificera avisering när den utlöses.| Krävs för varje avisering, innan eller efter att de har utökats till Azure. |
| Dämpa |Alternativ för att stoppa aviseringar. | Valfritt för varje avisering, före eller efter att de utökats till Azure. |
| Åtgärdsgrupper |ID: n för Azure-ActionGroup där åtgärder krävs anges, t. ex. e-post, SMSs, röst samtal, Webhooks, Automation runbooks, ITSM-kopplingar osv.| Krävs när aviseringar har utökats till Azure|
| Anpassa åtgärder|Ändra standardutdata för urvals åtgärder från ActionGroup| Valfritt för varje avisering kan användas när aviseringar har utökats till Azure. |

### <a name="thresholds"></a>Tröskelvärden
En aviserings åtgärd bör ha ett och endast ett tröskelvärde.  När resultatet av en sparad sökning matchar tröskelvärdet i en åtgärd som associeras med den här sökningen, körs alla andra processer i den åtgärden.  En åtgärd kan även innehålla en tröskel så att den kan användas med åtgärder av andra typer som inte innehåller tröskelvärden.

Tröskelvärdena har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Operator` |Operator för jämförelse av tröskel. <br> gt = större än <br> lt = mindre än |
| `Value` |Värde för tröskelvärdet. |

Anta till exempel att du har en händelse fråga med ett intervall på 15 minuter, ett TimeSpan på 30 minuter och ett tröskelvärde på större än 10. I det här fallet skulle frågan köras var 15: e minut och en avisering aktive ras om den returnerade 10 händelser som har skapats under ett 30-minuters intervall.

Följande är ett exempel svar för en åtgärd med endast ett tröskelvärde.  

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Version": 1
}
```

Använd metoden metoden med ett unikt åtgärds-ID för att skapa en ny tröskel åtgärd för ett schema.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Använd metoden för att skicka med ett befintligt åtgärds-ID för att ändra en tröskel åtgärd för ett schema.  Bröd texten i begäran måste innehålla etag för åtgärden.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Allvarlighetsgrad
Med Log Analytics kan du klassificera dina aviseringar i kategorier, så att det blir enklare att hantera och prioritering. Den angivna allvarlighets graden för aviseringen är: information, varning och kritiskt. Dessa mappas till den normaliserade allvarlighets graden för Azure-aviseringar som:

|Log Analytics allvarlighets grad  |Allvarlighets grad för Azure-aviseringar  |
|---------|---------|
|`critical` |Allvarlighets grad 0|
|`warning` |Allvarlighets grad 1|
|`informational` | Allv.grad 2|

Följande är ett exempel svar för en åtgärd med endast ett tröskelvärde och en allvarlighets grad. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

Använd metoden för att skicka med ett unikt åtgärds-ID för att skapa en ny åtgärd för ett schema med allvarlighets grad.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Använd metoden för att skicka med ett befintligt åtgärds-ID för att ändra en allvarlighets åtgärd för ett schema.  Bröd texten i begäran måste innehålla etag för åtgärden.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Dämpa
Aviseringar om Log Analyticsbaserade frågor utlöses varje gång tröskelvärdet är uppfyllt eller överskrids. Baserat på den logik som är underförstådd i frågan kan detta resultera i att aviseringar utlöses av en serie intervall och att meddelanden även skickas hela tiden. För att förhindra det här scenariot kan en användare ställa in alternativet för att förhindra att Log Analytics väntar under en angiven tids period innan meddelandet utlöses den andra gången för varnings regeln. Så om utelämna är inställt i 30 minuter; sedan kommer aviseringen att starta första gången och skicka meddelanden som kon figurer ATS. Men vänta i 30 minuter innan aviserings regeln används igen. Under över gångs perioden fortsätter varnings regeln att endast köra Log Analytics aviseringar under den angivna tiden, oavsett hur många gånger varnings regeln utlöses under denna period.

Egenskapen utelämna för Log Analytics varnings regeln anges med hjälp av *begränsning* svärdet och under trycknings perioden med *DurationInMinutes* -värdet.

Följande är ett exempel svar för en åtgärd med endast egenskapen Threshold, allvarlighets grad och utelämna

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Throttling": {
   "DurationInMinutes": 30
   },
   "Severity": "critical",
   "Version": 1
}
```

Använd metoden för att skicka med ett unikt åtgärds-ID för att skapa en ny åtgärd för ett schema med allvarlighets grad.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Använd metoden för att skicka med ett befintligt åtgärds-ID för att ändra en allvarlighets åtgärd för ett schema.  Bröd texten i begäran måste innehålla etag för åtgärden.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Åtgärdsgrupper
Alla aviseringar i Azure, Använd åtgärds grupp som standardmekanism för hantering av åtgärder. Med åtgärds gruppen kan du ange åtgärder en gång och sedan koppla åtgärds gruppen till flera aviseringar – i Azure. Utan behovet av att upprepade gånger deklarera samma åtgärder över och över igen. Åtgärds grupper har stöd för flera åtgärder, inklusive e-post, SMS, röst samtal, ITSM-anslutning, Automation Runbook, webhook-URI med mera. 

För användare som har utökat sina aviseringar till Azure bör ett schema nu ha åtgärds grupp information som skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. E-postinformation, webhook-URL: er, information om Runbook-automatisering och andra åtgärder måste definieras på sidan en åtgärds grupp först innan en avisering skapas. en kan skapa en [Åtgärds grupp från Azure Monitor](./action-groups.md) i portalen eller använda [Åtgärds grupp-API](/rest/api/monitor/actiongroups).

Om du vill lägga till en Association av åtgärds gruppen i en avisering anger du det unika Azure Resource Manager-ID: t för åtgärds gruppen i aviserings definitionen. En exempel illustration finns nedan:

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ]
   },
   "Severity": "critical",
   "Version": 1
}
```

Använd metoden use med ett unikt åtgärds-ID för att associera redan en befintlig åtgärds grupp för ett schema.  Följande är ett exempel på användnings bilden.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Använd metoden för att skicka med ett befintligt åtgärds-ID för att ändra en åtgärds grupp som är associerad med ett schema.  Bröd texten i begäran måste innehålla etag för åtgärden.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Anpassa åtgärder
Som standard åtgärder följer du standard mal len och formatet för meddelanden. Men användaren kan anpassa vissa åtgärder, även om de styrs av åtgärds grupper. För närvarande är anpassning möjlig för e-postmeddelandets ämne och webhook-nyttolast.

##### <a name="customize-e-mail-subject-for-action-group"></a>Anpassa e-postämne för åtgärds grupp
Som standard är e-postmeddelandets ämne för aviseringar: aviserings meddelande `<AlertName>` för `<WorkspaceName>` . Men detta kan anpassas så att du kan använda vissa ord eller Taggar – så att du enkelt kan använda filter regler i din inkorg. Informationen om att anpassa e-posthuvudet måste skickas tillsammans med ActionGroup, som i exemplet nedan.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
      "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
}
```

Använd metoden use med ett unikt åtgärds-ID för att associera redan en befintlig åtgärds grupp med anpassning för ett schema.  Följande är ett exempel på användnings bilden.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Använd metoden för att skicka med ett befintligt åtgärds-ID för att ändra en åtgärds grupp som är associerad med ett schema.  Bröd texten i begäran måste innehålla etag för åtgärden.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>Anpassa en webhook-nyttolast för åtgärds grupp
Som standard har webhooken som skickas via åtgärds gruppen för Log Analytics en fast struktur. Men en kan anpassa JSON-nyttolasten genom att använda särskilda variabler som stöds för att uppfylla kraven för webhook-slutpunkten. Mer information finns i [webhook-åtgärd för logg aviserings regler](./alerts-log-webhook.md). 

Informationen om att anpassa webhook måste skickas tillsammans med ActionGroup och kommer att tillämpas på alla webhook-URI: er som anges i åtgärds gruppen. som i exemplet nedan.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
   "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
   "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
},
```

Använd metoden use med ett unikt åtgärds-ID för att associera redan en befintlig åtgärds grupp med anpassning för ett schema.  Följande är ett exempel på användnings bilden.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Använd metoden för att skicka med ett befintligt åtgärds-ID för att ändra en åtgärds grupp som är associerad med ett schema.  Bröd texten i begäran måste innehålla etag för åtgärden.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Nästa steg

* Använd [REST API för att utföra loggs ökningar](../log-query/log-query-overview.md) i Log Analytics.
* Lär dig mer om [logg aviseringar i Azure Monitor](./alerts-unified-log.md)
* [Skapa, redigera eller hantera logg aviserings regler i Azure Monitor](./alerts-log.md)

