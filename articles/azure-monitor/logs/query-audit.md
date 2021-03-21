---
title: Gransknings frågor i Azure Monitor logg frågor
description: Information om gransknings loggar för logg frågor som ger telemetri om logg frågor som körs i Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: 28dfac7de8e73adf577b0a13e5fbd8740b1e3b06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047425"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Gransknings frågor i Azure Monitor loggar (förhands granskning)
Logg läsar gransknings loggar ger telemetri om logg frågor som körs i Azure Monitor. Detta omfattar information som när en fråga kördes, vem som körde den, vilket verktyg som användes, frågetexten och prestanda statistik som beskriver frågans körning.


## <a name="configure-query-auditing"></a>Konfigurera granskning av fråga
Granskning av frågor aktive ras med en [diagnostisk inställning](../essentials/diagnostic-settings.md) på arbets ytan Log Analytics. På så sätt kan du skicka gransknings data till den aktuella arbets ytan eller till en annan arbets yta i din prenumeration, till Azure Event Hubs att skicka utanför Azure eller till Azure Storage för arkivering. 

### <a name="azure-portal"></a>Azure Portal
Få åtkomst till diagnostikinställningar för en Log Analytics arbets yta i Azure Portal på någon av följande platser:

- Från **Azure Monitor** -menyn väljer du **diagnostikinställningar** och letar upp och väljer arbets ytan.

    [![Diagnostikinställningar Azure Monitor ](media/query-audit/diagnostic-setting-monitor.png)](media/query-audit/diagnostic-setting-monitor.png#lightbox) 

- Från menyn **Log Analytics arbets ytor** väljer du arbets ytan och väljer sedan **diagnostikinställningar**.

    [![Diagnostikinställningar Log Analytics arbets yta ](media/query-audit/diagnostic-setting-workspace.png)](media/query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Resource Manager-mall
Du kan hämta en exempel Resource Manager-mall från [diagnostikinställningar för Log Analytics-arbetsyta](../essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Granska data
En gransknings post skapas varje gång en fråga körs. Om du skickar data till en Log Analytics arbets yta lagras den i en tabell med namnet *LAQueryLogs*. I följande tabell beskrivs egenskaperna i varje post i gransknings data.

| Fält | Beskrivning |
|:---|:---|
| TimeGenerated         | UTC-tid när frågan skickades. |
| CorrelationId         | Unikt ID för att identifiera frågan. Kan användas i fel söknings scenarier när du kontaktar Microsoft för att få hjälp. |
| AADObjectId           | Azure Active Directory-ID för det användar konto som startade frågan.  |
| AADTenantId           | ID för klient organisationen för det användar konto som startade frågan.  |
| AADEmail              | E-postadress till klient organisationen för det användar konto som startade frågan.  |
| AADClientId           | ID och Matchat namn för det program som används för att starta frågan. |
| RequestClientApp      | Löst namn på det program som användes för att starta frågan. |
| QueryTimeRangeStart   | Början av tidsintervallet som har valts för frågan. Detta kan inte vara ifyllt i vissa scenarier, till exempel när frågan startas från Log Analytics, och tidsintervallet anges i frågan i stället för tid väljaren. |
| QueryTimeRangeEnd     | Slutet av tidsintervallet som har valts för frågan. Detta kan inte vara ifyllt i vissa scenarier, till exempel när frågan startas från Log Analytics, och tidsintervallet anges i frågan i stället för tid väljaren.  |
| QueryText             | Texten för frågan som kördes. |
| RequestTarget         | API-URL användes för att skicka frågan.  |
| RequestContext        | Lista över resurser som frågan har begärt att köras mot. Innehåller upp till tre sträng mat ris: arbets ytor, program och resurser. Prenumerations-eller resurs grupp – riktade frågor visas som *resurser*. Inkluderar det mål som underförstådda av RequestTarget.<br>Resurs-ID för varje resurs kommer att tas med om det kan lösas. Det kanske inte går att lösa problemet om ett fel returneras vid åtkomst till resursen. I det här fallet används den angivna texten från frågan.<br>Om frågan använder ett tvetydigt namn, till exempel ett namn på arbets ytan som redan finns i flera prenumerationer, används det tvetydiga namnet. |
| RequestContextFilters | Uppsättning filter som anges som en del av frågans anrop. Innehåller upp till tre möjliga sträng mat ris:<br>-ResourceTypes – typ av resurs för att begränsa frågans omfång<br>– Arbets ytor – lista över arbets ytor som begränsar frågan till<br>-WorkspaceRegions – lista över regioner för arbets ytor som begränsar frågan |
| ResponseCode          | HTTP-svarskod som returnerades när frågan skickades. |
| ResponseDurationMs    | Tid för svaret som ska returneras.  |
| ResponseRowCount     | Totalt antal rader som returnerades av frågan. |
| StatsCPUTimeMs       | Total beräknings tid som används för data behandling, parsning och data hämtning. Fylls bara i om frågan returnerar med status koden 200. |
| StatsDataProcessedKB | Mängden data som har öppnats för att bearbeta frågan. Påverkas av storleken på mål tabellen, tids rymden som används, filter tillämpas och antalet kolumner som refereras. Fylls bara i om frågan returnerar med status koden 200. |
| StatsDataProcessedStart | Tid för äldsta data som har öppnats för att bearbeta frågan. Påverkas av frågans explicita tidsintervall och filter tillämpas. Detta kan vara större än den explicita tids rymden på grund av data partitionering. Fylls bara i om frågan returnerar med status koden 200. |
| StatsDataProcessedEnd  |Tiden för de senaste data som har öppnats för att bearbeta frågan. Påverkas av frågans explicita tidsintervall och filter tillämpas. Detta kan vara större än den explicita tids rymden på grund av data partitionering. Fylls bara i om frågan returnerar med status koden 200. |
| StatsWorkspaceCount | Antal arbets ytor som används av frågan. Fylls bara i om frågan returnerar med status koden 200. |
| StatsRegionCount | Antal regioner som används av frågan. Fylls bara i om frågan returnerar med status koden 200. |

## <a name="considerations"></a>Överväganden

- Frågor loggas bara när de körs i en användar kontext. Ingen service-till-tjänst i Azure kommer att loggas. De två primära uppsättningarna med frågor denna undantags kompass är fakturerings beräkningar och automatiserade aviserings körningar. I händelse av aviseringar kommer bara den schemalagda varnings frågan inte att loggas. den första körningen av aviseringen på skärmen för att skapa aviseringar körs i en användar kontext och är tillgänglig i gransknings syfte. 
- Prestanda statistik är inte tillgänglig för frågor som kommer från Azure Datautforskaren proxy. Alla andra data för dessa frågor kommer fortfarande att fyllas i.
- *H* -tipset på strängar som [obfuscates sträng litteraler](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) kommer inte att påverka gransknings loggarna. Frågorna samlas in exakt som de skickas utan strängen som fördunklade. Du bör se till att endast användare som har behörighet att se dessa data kan göra detta med de olika Kubernetes RBAC-eller Azure RBAC-lägen som är tillgängliga i Log Analytics-arbetsytor.
- För frågor som innehåller data från flera arbets ytor, kommer frågan bara att fångas in på de arbets ytor som användaren har åtkomst till.

## <a name="costs"></a>Kostnader  
Det kostar inget för Azure Diagnostic-tillägget, men du kan debiteras avgifter för inmatade data. Kontrol lera [Azure Monitor prissättningen](https://azure.microsoft.com/pricing/details/monitor/) för målet där du samlar in data.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [diagnostikinställningar](../essentials/diagnostic-settings.md).
- Läs mer om hur du [optimerar logg frågor](query-optimization.md).
