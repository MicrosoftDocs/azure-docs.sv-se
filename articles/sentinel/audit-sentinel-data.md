---
title: Granska Azure Sentinel-frågor och-aktiviteter | Microsoft Docs
description: Den här artikeln beskriver hur du granskar frågor och aktiviteter som utförs i Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a02be0938b1ab925fb0343351ce1c414cc59c615
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044846"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Granska Azure Sentinel-frågor och-aktiviteter

I den här artikeln beskrivs hur du kan visa gransknings data för frågor som körs och aktiviteter som utförs i din Azure Sentinel-arbetsyta, till exempel för interna och externa krav på arbets ytan säkerhets åtgärder (SOC).

Azure Sentinel ger åtkomst till:

- Tabellen **AzureActivity** som innehåller information om alla åtgärder som vidtas i Azure Sentinel, till exempel redigerings aviserings regler. **AzureActivity** -tabellen registrerar inte vissa frågedata. Mer information finns i [granskning med Azure aktivitets loggar](#auditing-with-azure-activity-logs).

- Tabellen **LAQueryLogs** som innehåller information om frågorna som körs i Log Analytics, inklusive frågor som körs från Azure Sentinel. Mer information finns i [granskning med LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Förutom de manuella frågor som beskrivs i den här artikeln tillhandahåller Azure Sentinel en inbyggd arbets bok som hjälper dig att granska aktiviteterna i din SOC-miljö.
>
> I området Azure Sentinel- **arbetsböcker** söker du efter arbets **ytans gransknings** arbets bok.



## <a name="auditing-with-azure-activity-logs"></a>Granskning med Azures aktivitets loggar

Gransknings loggarna i Azure Sentinel behålls i [Azures aktivitets loggar](../azure-monitor/essentials/platform-logs-overview.md)där **AzureActivity** -tabellen innehåller alla åtgärder som vidtas i din Azure Sentinel-arbetsyta.

Du kan använda **AzureActivity** -tabellen när du granskar aktiviteter i din SOC-miljö med Azure Sentinel.

**Så här frågar du AzureActivity-tabellen**:

1. Anslut [Azure-aktivitetens](connect-azure-activity.md) data källa för att börja strömma gransknings händelser till en ny tabell på skärmen **loggar** som kallas AzureActivity.

1. Sedan kan du köra frågor mot data med KQL, precis som med andra tabeller.

    Tabellen **AzureActivity** innehåller data från många tjänster, inklusive Azure Sentinel. Om du bara vill filtrera i data från Azure Sentinel startar du din fråga med följande kod:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Om du till exempel vill ta reda på vem som senast hade redigerat en viss analys regel använder du följande fråga (Ersätt `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` med regel-ID för regeln som du vill kontrol lera):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Lägg till fler parametrar till din fråga för att utforska **AzureActivities** -tabellen ytterligare, beroende på vad du behöver göra i rapporten. I följande avsnitt finns andra exempel frågor att använda vid granskning med **AzureActivity** tabell data. 

Mer information finns i [Azure Sentinel-data som ingår i Azures aktivitets loggar](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Hitta alla åtgärder som vidtagits av en speciell användare under de senaste 24 timmarna

Följande **AzureActivity** tabell fråga visar alla åtgärder som vidtas av en särskild Azure AD-användare under de senaste 24 timmarna.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Sök alla borttagnings åtgärder

Följande **AzureActivity** tabell fråga visar en lista över alla borttagnings åtgärder som utförts i din Azure Sentinel-arbetsyta.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Azure Sentinel-data som ingår i Azures aktivitets loggar
 
Azure Sentinels gransknings loggar underhålls i [Azures aktivitets loggar](../azure-monitor/essentials/platform-logs-overview.md)och innehåller följande typer av information:

|Åtgärd  |Informations typer  |
|---------|---------|
|**Skapad**     |Aviseringsregler <br> Ärende kommentarer <br>Incident kommentarer <br>Sparade sökningar<br>Watchlists    <br>Arbetsböcker     |
|**Borttaget**     |Aviseringsregler <br>Bokmärken <br>Dataanslutningar <br>Incidenter <br>Sparade sökningar <br>Inställningar <br>Hot informations rapporter <br>Watchlists      <br>Arbetsböcker <br>Arbetsflöde  |
|**Samlingsuppdateras**     |  Aviseringsregler<br>Bokmärken <br> Fall <br> Dataanslutningar <br>Incidenter <br>Incident kommentarer <br>Hot informations rapporter <br> Arbetsböcker <br>Arbetsflöde       |
|     |         |

Du kan också använda Azures aktivitets loggar för att kontrol lera användarens auktorisering och licenser. 

I följande tabell visas till exempel de valda åtgärder som finns i Azure aktivitets loggar med den angivna resursen som loggdata hämtas från.

|Åtgärdsnamn|    Resurstyp|
|----|----|
|Skapa eller uppdatera arbets bok  |Microsoft. Insights/arbets böcker|
|Ta bort arbets bok    |Microsoft. Insights/arbets böcker|
|Ange arbets flöde   |Microsoft. Logic/arbets flöden|
|Ta bort arbets flöde    |Microsoft. Logic/arbets flöden|
|Skapa Sparad sökning    |Microsoft. OperationalInsights/arbets ytor/savedSearches|
|Ta bort Sparad sökning    |Microsoft. OperationalInsights/arbets ytor/savedSearches|
|Uppdatera aviserings regler |Microsoft. SecurityInsights/alertRules|
|Ta bort aviserings regler |Microsoft. SecurityInsights/alertRules|
|Uppdatera svars åtgärder för aviserings regeln |Microsoft. SecurityInsights/alertRules/Actions|
|Ta bort svars åtgärder för aviserings regel |Microsoft. SecurityInsights/alertRules/Actions|
|Uppdatera bok märken   |Microsoft. SecurityInsights/bok märken|
|Ta bort bok märken   |Microsoft. SecurityInsights/bok märken|
|Uppdatera ärenden   |Microsoft. SecurityInsights/fall|
|Uppdatera ärende undersökning  |Microsoft. SecurityInsights/fall/undersökningar|
|Skapa ärende kommentarer   |Microsoft. SecurityInsights/Cases/comments|
|Uppdatera data anslutningar |Microsoft. SecurityInsights/dataConnectors|
|Ta bort data anslutningar |Microsoft. SecurityInsights/dataConnectors|
|Uppdateringsinställningar    |Microsoft. SecurityInsights/inställningar|
| | |

Mer information finns i [händelse schema för Azure aktivitets logg](../azure-monitor/essentials/activity-log-schema.md).


## <a name="auditing-with-laquerylogs"></a>Granskning med LAQueryLogs

**LAQueryLogs** -tabellen innehåller information om logg frågor som körs i Log Analytics. Eftersom Log Analytics används som Azure Sentinel-underliggande data lager, kan du konfigurera systemet för att samla in LAQueryLogs-data i Azure Sentinel-arbetsytan.

LAQueryLogs-data innehåller information som:

- När frågor kördes
- Som körde frågor i Log Analytics
- Vilket verktyg användes för att köra frågor i Log Analytics, till exempel Azure Sentinel
- Själva fråge texten
- Prestanda data för varje fråga som körs

> [!NOTE]
> - Tabellen **LAQueryLogs** innehåller endast frågor som har körts på bladet loggar i Azure Sentinel. Den omfattar inte de frågor som körs av schemalagda analys regler, med hjälp av **undersökningen** eller på sidan om Azure Sentinel- **jakt** .
> - Det kan finnas en kort fördröjning mellan tiden som en fråga körs och data fylls i i **LAQueryLogs** -tabellen. Vi rekommenderar att du väntar cirka 5 minuter att fråga **LAQueryLogs** -tabellen för gransknings data.
>


**Så här frågar du LAQueryLogs-tabellen**:

1. Tabellen **LAQueryLogs** är inte aktive rad som standard i din Log Analytics-arbetsyta. Om du vill använda **LAQueryLogs** -data vid granskning i Azure Sentinel aktiverar du först **LAQueryLogs** i området för Log Analytics arbets ytans **diagnostikinställningar** .

    Mer information finns i [gransknings frågor i Azure Monitor loggar](../azure-monitor/logs/query-audit.md).


1. Sedan kan du köra frågor mot data med KQL, precis som med andra tabeller.

    Följande fråga visar till exempel hur många frågor som kördes under den senaste veckan, per dag:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

I följande avsnitt visas fler exempel frågor att köra i **LAQueryLogs** -tabellen när du granskar aktiviteter i din SOC-miljö med Azure Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Antal frågor som körs där svaret inte var "OK"

Följande **LAQueryLogs** tabell fråga visar antalet frågor som körs, där allt annat än ett HTTP-svar på **200 OK** togs emot. Det här antalet innehåller till exempel frågor som inte kunde köras.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Visa användare för CPU-intensiva frågor

Följande **LAQueryLogs** tabell fråga visar en lista över de användare som körde de mest CPU-intensiva frågorna, baserat på CPU-användning och längden på fråge tiden.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Visa användare som körde de flesta frågor den senaste veckan

Följande **LAQueryLogs** tabell fråga visar en lista över de användare som körde de flesta frågorna under den senaste veckan.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Konfigurera aviseringar för Azure Sentinel-aktiviteter

Du kanske vill använda Azures kontroll gransknings resurser för att skapa proaktiva aviseringar.

Om du till exempel har känsliga tabeller i Azure Sentinel-arbetsytan, använder du följande fråga för att meddela dig varje tidpunkt då dessa tabeller frågas:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Nästa steg

I Azure Sentinel använder du arbets **ytans gransknings** arbets bok för att granska aktiviteterna i din SOC-miljö.

Mer information finns i [Självstudier: visualisera och övervaka dina data](tutorial-monitor-your-data.md).