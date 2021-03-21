---
title: Övervaka hälsan för Log Analytics arbets ytan i Azure Monitor
description: Beskriver hur du övervakar hälso tillståndet för din Log Analytics-arbetsyta med hjälp av data i åtgärds tabellen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 9eda0acc15badfe7bb2e754d887786aa990d6e24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034973"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Övervaka hälsan för Log Analytics arbets ytan i Azure Monitor
För att upprätthålla prestanda och tillgänglighet för din Log Analytics-arbetsyta i Azure Monitor måste du kunna identifiera eventuella problem som uppstår i proaktivt. Den här artikeln beskriver hur du övervakar hälso tillståndet för din Log Analytics-arbetsyta med hjälp av data i [Åtgärds](/azure/azure-monitor/reference/tables/operation) tabellen. Den här tabellen ingår i varje Log Analytics arbets yta och innehåller fel och varningar som inträffar i din arbets yta. Du bör regelbundet granska dessa data och skapa aviseringar för att proaktivt meddelas när det finns viktiga incidenter på arbets ytan.

## <a name="_logoperation-function"></a>_LogOperation funktion

Azure Monitor loggar skickar information om eventuella problem till [Åtgärds](/azure/azure-monitor/reference/tables/operation) tabellen på arbets ytan där problemet uppstod. Systemfunktionen **_LogOperation** baseras på **Åtgärds** tabellen och innehåller en förenklad uppsättning information för analys och avisering.

## <a name="columns"></a>Kolumner

Funktionen **_LogOperation** returnerar kolumnerna i följande tabell.

| Kolumn | Beskrivning |
|:---|:---|
| TimeGenerated | Tid då incidenten inträffade i UTC. |
| Kategori  | Åtgärds kategori grupp. Kan användas för att filtrera efter typer av åtgärder och för att skapa mer exakta system granskningar och aviseringar. Se avsnittet nedan om du vill se en lista över kategorier. |
| Åtgärd  | Beskrivning av åtgärds typen. Detta kan tyda på en av Log Analytics gränser, typ av åtgärd eller del av en process. |
| Nivå | Problemets allvarlighets grad:<br>-Info: ingen särskild uppmärksamhet krävs.<br>-Varning: processen slutfördes inte som förväntat och det krävs en åtgärd.<br>-Fel: processen misslyckades och en brådskande åtgärd krävs. 
| Detalj | Detaljerad beskrivning av åtgärden inkluderar ett detaljerat fel meddelande om det finns. |
| _ResourceId | Resurs-ID för den Azure-resurs som är relaterad till åtgärden.  |
| Dator | Dator namn om åtgärden är relaterad till en Azure Monitor-Agent. |
| CorrelationId | Används för att gruppera sammanhängande relaterade åtgärder. |


## <a name="categories"></a>Kategorier

I följande tabell beskrivs kategorierna från _LogOperation-funktionen. 

| Kategori | Beskrivning |
|:---|:---|
| Datainmatning           | Åtgärder som är en del av data inmatnings processen. Se nedan för mer information. |
| Agent               | Indikerar ett problem med Agent installationen. |
| Datainsamling     | Åtgärder som rör data insamlings processer. |
| Lösnings mål  | Åtgärden av typen *ConfigurationScope* bearbetades. |
| Utvärderings lösning | En utvärderings process utfördes. |


### <a name="ingestion"></a>Datainmatning
Inmatnings åtgärder är problem som uppstod vid data inmatning, inklusive meddelande om att nå gränserna för Azure Log Analytics-arbetsyta. Fel tillstånd i den här kategorin kan föreslå data förlust, så de är särskilt viktiga för att övervaka. Tabellen nedan innehåller information om de här åtgärderna. Se [Azure Monitor tjänst begränsningar](../service-limits.md#log-analytics-workspaces) för Log Analytics-arbetsytor.


| Åtgärd | Nivå | Detalj | Relaterad artikel |
|:---|:---|:---|:---|
| Anpassad logg | Fel   | Kolumn gränsen för anpassade fält har nåtts. | [Azure Monitor tjänst gränser](../service-limits.md#log-analytics-workspaces) |
| Anpassad logg | Fel   | Det gick inte att mata in anpassade loggar. | |
| Metadatatjänst. | Fel | Ett konfigurations fel har identifierats. | |
| Datainsamling | Fel   | Data utelämnades eftersom begäran skapades tidigare än antalet angivna dagar. | [Hantera användning och kostnader med Azure Monitor-loggar](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Datainsamling | Information    | Konfiguration av samlings dator har identifierats.| |
| Datainsamling | Information    | Data insamlingen startades på grund av en ny dag. | [Hantera användning och kostnader med Azure Monitor-loggar](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Datainsamling | Varning | Data insamlingen stoppades på grund av att den dagliga gränsen har uppnåtts.| [Hantera användning och kostnader med Azure Monitor-loggar](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Databearbetning | Fel   | Ogiltigt JSON-format. | [Skicka loggdata till Azure Monitor med API: t för HTTP-datainsamling (offentlig för hands version)](../logs/data-collector-api.md#request-body) | 
| Databearbetning | Varning | Värdet har trimmats till den högsta tillåtna storleken. | [Azure Monitor tjänst gränser](../service-limits.md#log-analytics-workspaces) |
| Databearbetning | Varning | Fält värde trimmat eftersom storleks gränsen har uppnåtts. | [Azure Monitor tjänst gränser](../service-limits.md#log-analytics-workspaces) | 
| Inmatnings hastighet | Information | Gräns för inmatnings frekvens som närmar sig 70%. | [Azure Monitor tjänst gränser](../service-limits.md#log-analytics-workspaces) |
| Inmatnings hastighet | Varning | Gräns för inmatnings frekvensen närmar sig gränsen. | [Azure Monitor tjänst gränser](../service-limits.md#log-analytics-workspaces) |
| Inmatnings hastighet | Fel   | Hastighets gränsen har uppnåtts. | [Azure Monitor tjänst gränser](../service-limits.md#log-analytics-workspaces) |
| Storage | Fel   | Det går inte att komma åt lagrings kontot eftersom de autentiseringsuppgifter som används är ogiltiga.  |



   

## <a name="alert-rules"></a>Aviseringsregler
Använd [loggnings frågans aviseringar](../alerts/alerts-log-query.md) i Azure Monitor för att proaktivt meddelas när ett problem upptäcks på arbets ytan Log Analytics. Du bör använda en strategi som gör att du kan reagera i rätt tid för problem samtidigt som du minimerar kostnaderna. Din prenumeration debiteras för varje varnings regel med en kostnad beroende på den frekvens som den utvärderas.

En rekommenderad strategi är att börja med två varnings regler baserat på problemets nivå. Använd en kort frekvens som var 5: e minut för fel och en längre frekvens, till exempel 24 timmar för varningar. Eftersom fel indikerar potentiell data förlust, vill du snabbt svara på dem för att minimera eventuell förlust. Varningar indikerar vanligt vis ett problem som inte kräver omedelbara åtgärder, så att du kan granska dem dagligen.

Använd processen i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../alerts/alerts-log.md) för att skapa logg varnings reglerna. I följande avsnitt beskrivs informationen för varje regel.


| Fråga | Tröskelvärde | Period | Frekvens |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Dessa aviserings regler svarar på samma sak som alla åtgärder med fel eller varning. När du blir mer bekant med de åtgärder som genererar aviseringar kanske du vill svara på olika sätt för vissa åtgärder. Till exempel kanske du vill skicka meddelanden till olika personer för särskilda åtgärder. 

Om du vill skapa en varnings regel för en viss åtgärd använder du en fråga som innehåller kolumnerna **kategori** och **åtgärd** . 

I följande exempel skapas en varning när inmatnings volym hastigheten har nått 80% av gränsen.

- Mål: Välj din Log Analytics arbets yta
- Villkoren
  - Signal namn: anpassad loggs ökning
  - Sök fråga: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Baserat på: antal resultat
  - Villkor: större än
  - Tröskel: 0
  - Period: 5 (minuter)
  - Frekvens: 5 (minuter)
- Varnings regel namn: daglig data gräns har nåtts
- Allvarlighets grad: varning (allvarlighets grad 1)


I följande exempel skapas en varning när data insamlingen har nått den dagliga gränsen. 

- Mål: Välj din Log Analytics arbets yta
- Villkoren
  - Signal namn: anpassad loggs ökning
  - Sök fråga: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - Baserat på: antal resultat
  - Villkor: större än
  - Tröskel: 0
  - Period: 5 (minuter)
  - Frekvens: 5 (minuter)
- Varnings regel namn: daglig data gräns har nåtts
- Allvarlighets grad: varning (allvarlighets grad 1)



## <a name="next-steps"></a>Nästa steg

- Läs mer om [logg aviseringar](../alerts/alerts-log.md).
- [Samla in fråga gransknings data](./query-audit.md) för din arbets yta.