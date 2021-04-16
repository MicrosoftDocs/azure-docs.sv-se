---
title: Övervaka hälsotillståndet för Log Analytics-arbetsyta i Azure Monitor
description: Beskriver hur du övervakar hälsotillståndet för Log Analytics-arbetsytan med hjälp av data i åtgärdstabellen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538843"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Övervaka hälsotillståndet för Log Analytics-arbetsyta i Azure Monitor
För att upprätthålla prestanda och tillgänglighet för Log Analytics-arbetsytan i Azure Monitor måste du kunna identifiera eventuella problem som uppstår proaktivt. I den här artikeln beskrivs hur du övervakar hälsotillståndet för Log Analytics-arbetsytan med hjälp av data i [åtgärdstabellen.](/azure/azure-monitor/reference/tables/operation) Den här tabellen ingår i varje Log Analytics-arbetsyta och innehåller fel och varningar som inträffar på din arbetsyta. Du bör regelbundet granska dessa data och skapa aviseringar för att proaktivt meddelas när det finns viktiga incidenter på din arbetsyta.

## <a name="_logoperation-function"></a>_LogOperation-funktion

Azure Monitor loggar skickar information om eventuella problem till [åtgärdstabellen](/azure/azure-monitor/reference/tables/operation) på arbetsytan där problemet uppstod. Funktionen **_LogOperation** baseras på **åtgärdstabellen** och ger en förenklad uppsättning information för analys och avisering.

## <a name="columns"></a>Kolumner

Funktionen **_LogOperation** returnerar kolumnerna i följande tabell.

| Kolumn | Beskrivning |
|:---|:---|
| TimeGenerated | Tiden då incidenten inträffade i UTC. |
| Kategori  | Åtgärdskategorigrupp. Kan användas för att filtrera på typer av åtgärder och hjälpa till att skapa mer exakt systemgranskning och aviseringar. Se avsnittet nedan för en lista över kategorier. |
| Åtgärd  | Beskrivning av åtgärdstypen. Detta kan tyda på någon av Log Analytics-gränserna, typen av åtgärd eller en del av en process. |
| Nivå | Problemets allvarlighetsgrad:<br>– Information: Ingen särskild uppmärksamhet krävs.<br>– Varning: Processen slutfördes inte som förväntat och det krävs åtgärder.<br>– Fel: Processen misslyckades och brådskande åtgärder krävs. 
| Detalj | En detaljerad beskrivning av åtgärden innehåller ett specifikt felmeddelande om den finns. |
| _ResourceId | Resurs-ID för Azure-resursen som är relaterad till åtgärden.  |
| Dator | Datornamn om åtgärden är relaterad till en Azure Monitor agent. |
| CorrelationId | Används för att gruppera efterföljande relaterade åtgärder. |


## <a name="categories"></a>Kategorier

I följande tabell beskrivs kategorierna från _LogOperation funktionen. 

| Kategori | Beskrivning |
|:---|:---|
| Datainmatning           | Åtgärder som ingår i datainmatningsprocessen. Se nedan för mer information. |
| Agent               | Anger ett problem med agentinstallationen. |
| Datainsamling     | Åtgärder som rör processer för datasamlingar. |
| Lösningsmål  | Åtgärden av typen *ConfigurationScope* bearbetades. |
| Utvärderingslösning | En utvärderingsprocess utfördes. |


### <a name="ingestion"></a>Datainmatning
Inmatningsåtgärder är problem som uppstod under datainmatningen, inklusive meddelanden om att gränsen för Azure Log Analytics-arbetsytan har nåtts. Feltillstånd i den här kategorin kan tyda på dataförlust, så de är särskilt viktiga att övervaka. Tabellen nedan innehåller information om dessa åtgärder. Se [Azure Monitor tjänstbegränsningar](../service-limits.md#log-analytics-workspaces) för tjänstbegränsningar för Log Analytics-arbetsytor.


| Åtgärd | Nivå | Detalj | Relaterad artikel |
|:---|:---|:---|:---|
| Anpassad logg | Fel   | Kolumngränsen för anpassade fält har nåtts. | [Azure Monitor-tjänstbegränsningar](../service-limits.md#log-analytics-workspaces) |
| Anpassad logg | Fel   | Inmatningen av anpassade loggar misslyckades. | |
| Metadata. | Fel | Konfigurationsfel har identifierats. | |
| Datainsamling | Fel   | Data togs bort eftersom begäran skapades tidigare än antalet dagar. | [Hantera användning och kostnader med Azure Monitor-loggar](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Datainsamling | Information    | Konfiguration av insamlingsdator har identifierats.| |
| Datainsamling | Information    | Datainsamlingen startade på grund av en ny dag. | [Hantera användning och kostnader med Azure Monitor-loggar](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Datainsamling | Varning | Datainsamlingen stoppades på grund av att den dagliga gränsen uppnåddes.| [Hantera användning och kostnader med Azure Monitor-loggar](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Databearbetning | Fel   | Ogiltigt JSON-format. | [Skicka loggdata till Azure Monitor MED HTTP Data Collector API (offentlig förhandsversion)](../logs/data-collector-api.md#request-body) | 
| Databearbetning | Varning | Värdet har trimmats till den maximala tillåtna storleken. | [Azure Monitor-tjänstbegränsningar](../service-limits.md#log-analytics-workspaces) |
| Databearbetning | Varning | Fältvärde som trimmats när storleksgränsen har nåtts. | [Azure Monitor-tjänstbegränsningar](../service-limits.md#log-analytics-workspaces) | 
| Datainmatningshastighet | Information | Inmatningshastigheten närmar sig 70 %. | [Azure Monitor-tjänstbegränsningar](../service-limits.md#log-analytics-workspaces) |
| Datainmatningshastighet | Varning | Inmatningshastigheten närmar sig gränsen. | [Azure Monitor-tjänstbegränsningar](../service-limits.md#log-analytics-workspaces) |
| Datainmatningshastighet | Fel   | Hastighetsgränsen har nåtts. | [Azure Monitor-tjänstbegränsningar](../service-limits.md#log-analytics-workspaces) |
| Storage | Fel   | Det går inte att komma åt lagringskontot eftersom de autentiseringsuppgifter som används är ogiltiga.  |



   

## <a name="alert-rules"></a>Aviseringsregler
Använd [logga query-aviseringar](../alerts/alerts-log-query.md) i Azure Monitor att meddelas proaktivt när ett problem identifieras i Log Analytics-arbetsytan. Du bör använda en strategi som gör att du snabbt kan hantera problem samtidigt som du minimerar kostnaderna. Din prenumeration debiteras för varje aviseringsregel med en kostnad beroende på hur ofta den utvärderas.

En rekommenderad strategi är att börja med två aviseringsregler baserat på problemets nivå. Använd en kort frekvens, till exempel var 5:e minut för Fel och en längre frekvens, till exempel 24 timmar för Varningar. Eftersom fel indikerar potentiell dataförlust vill du svara på dem snabbt för att minimera eventuella förluster. Varningar indikerar vanligtvis ett problem som inte kräver omedelbar uppmärksamhet, så du kan granska dem dagligen.

Använd processen i [Skapa, visa och hantera logga aviseringar med](../alerts/alerts-log.md) hjälp Azure Monitor för att skapa loggvarningsregler. I följande avsnitt beskrivs informationen för varje regel.


| Fråga | Tröskelvärde | Period | Frekvens |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

De här aviseringsreglerna svarar på samma sätt på alla åtgärder med Fel eller Varning. När du blir mer bekant med de åtgärder som genererar aviseringar kanske du vill svara på olika sätt för specifika åtgärder. Du kanske till exempel vill skicka meddelanden till olika personer för specifika åtgärder. 

Om du vill skapa en aviseringsregel för en viss åtgärd använder du en fråga som innehåller **kolumnerna Kategori** **och** Åtgärd. 

I följande exempel skapas en varningsavisering när inmatningsvolymen har nått 80 % av gränsen.

- Mål: Välj din Log Analytics-arbetsyta
- Kriterier:
  - Signalnamn: Anpassad loggsökning
  - Sökfråga: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Baserat på: Antal resultat
  - Villkor: Större än
  - Tröskelvärde: 0
  - Period: 5 (minuter)
  - Frekvens: 5 (minuter)
- Namn på aviseringsregel: Den dagliga datagränsen har nåtts
- Allvarlighetsgrad: Varning (allvarlighetsgrad 1)


I följande exempel skapas en varningsavisering när datainsamlingen har nått den dagliga gränsen. 

- Mål: Välj din Log Analytics-arbetsyta
- Kriterier:
  - Signalnamn: Anpassad loggsökning
  - Sökfråga: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - Baserat på: Antal resultat
  - Villkor: Större än
  - Tröskelvärde: 0
  - Period: 5 (minuter)
  - Frekvens: 5 (minuter)
- Namn på aviseringsregel: Den dagliga datagränsen har nåtts
- Allvarlighetsgrad: Varning (allvarlighetsgrad 1)



## <a name="next-steps"></a>Nästa steg

- Läs mer om [logga aviseringar](../alerts/alerts-log.md).
- [Samla in frågegranskningsdata](./query-audit.md) för din arbetsyta.
