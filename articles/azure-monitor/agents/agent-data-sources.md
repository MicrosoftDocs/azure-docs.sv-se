---
title: Log Analytics agent data källor i Azure Monitor
description: Data källor definierar de loggdata som Azure Monitor samlar in från agenter och andra anslutna källor.  I den här artikeln beskrivs hur Azure Monitor använder data källor, förklarar hur du konfigurerar dem och ger en översikt över de olika data källor som är tillgängliga.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 2947f1c484f013c2d0bef9899b849f7ea761d89e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621447"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Log Analytics agent data källor i Azure Monitor
De data som Azure Monitor samlar in från virtuella datorer med [Log Analytics](../platform/log-analytics-agent.md) agent definieras av de data källor som du konfigurerar på [arbets ytan Log Analytics](../platform/data-platform-logs.md).   Varje data källa skapar poster av en viss typ med varje typ som har en egen uppsättning egenskaper.

> [!IMPORTANT]
> Den här artikeln beskriver data källor för [Log Analytics agent](../platform/log-analytics-agent.md) som är en av de agenter som används av Azure Monitor. Andra agenter samlar in olika data och konfigureras på olika sätt. Se [Översikt över Azure Monitor agenter](agents-overview.md) för en lista över tillgängliga agenter och de data som de kan samla in.

![Logg data insamling](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> De data källor som beskrivs i den här artikeln gäller bara för virtuella datorer som kör Log Analytics agenten. 

## <a name="summary-of-data-sources"></a>Sammanfattning av data källor
I följande tabell visas de agent data källor som för närvarande är tillgängliga med Log Analytics-agenten.  Varje har en länk till en separat artikel som innehåller information om data källan.   Den innehåller också information om deras metod och frekvens för insamling. 


| Datakälla | Plattform | Log Analytics-agent | Operations Manager-agent | Azure-lagring | Operations Manager krävs. | Operations Manager agent data som skickas via hanterings gruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Anpassade loggar](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | vid ankomsten |
| [Anpassade loggar](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | vid ankomsten |
| [IIS-loggar](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |beror på inställningen för förnyelse av loggfil |
| [Prestandaräknare](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |som schemalagt, minst 10 sekunder |
| [Prestandaräknare](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |som schemalagt, minst 10 sekunder |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |från Azure Storage: 10 minuter; från agent: vid ankomsten |
| [Händelse loggar i Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | vid ankomsten |


## <a name="configuring-data-sources"></a>Konfigurera data källor
Om du vill konfigurera data källor för Log Analytics agenter går du till menyn **Log Analytics arbets ytor** i Azure Portal och väljer en arbets yta. Klicka på **Avancerade inställningar** och sedan på **data**. Välj den data källa som du vill konfigurera. Du kan följa länkarna i tabellen ovan för dokumentation för varje data källa och information om deras konfiguration.

Alla konfigurationer levereras till alla agenter som är anslutna till arbets ytan.  Du kan inte utesluta anslutna agenter från den här konfigurationen.

![Konfigurera Windows-händelser](media/agent-data-sources/configure-events.png)



## <a name="data-collection"></a>Datainsamling
Konfigurationer för data källa levereras till agenter som är direkt anslutna till Azure Monitor inom några minuter.  Angivna data samlas in från agenten och levereras direkt till Azure Monitor med intervall som är specifika för varje data källa.  Se dokumentationen för varje data källa för dessa uppgifter.

För System Center Operations Manager agenter i en ansluten hanterings grupp översätts data källans konfigurationer till hanterings paket och levereras till hanterings gruppen var femte minut som standard.  Agenten laddar ned hanterings paketet som alla andra och samlar in angivna data. Beroende på data källan skickas data antingen till en hanterings server som vidarebefordrar data till Azure Monitor eller så skickar agenten data till Azure Monitor utan att gå igenom hanterings servern. Mer information finns i information om [data insamling för övervakning av lösningar i Azure](../monitor-reference.md) .  Du kan läsa mer om att ansluta Operations Manager och Azure Monitor och ändra den frekvens som konfigurationen levereras i [Konfigurera integration med System Center Operations Manager](../platform/om-agents.md).

Om agenten inte kan ansluta till Azure Monitor eller Operations Manager fortsätter den att samla in data som den kommer att leverera när en anslutning upprättas.  Data kan gå förlorade om mängden data når den maximala cache-storleken för klienten, eller om agenten inte kan upprätta en anslutning inom 24 timmar.

## <a name="log-records"></a>Logg poster
Alla loggdata som samlas in av Azure Monitor lagras i arbets ytan som poster.  Poster som samlas in av olika data källor har sin egen uppsättning egenskaper och identifieras av deras **typ** egenskap.  Se dokumentationen för varje data källa och lösning för information om varje posttyp.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att [övervaka lösningar](../insights/solutions.md) som lägger till funktioner i Azure Monitor och även samla in data i arbets ytan.
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och övervaknings lösningar.  
* Konfigurera [aviseringar](../platform/alerts-overview.md) för att proaktivt meddela dig om viktiga data som samlas in från data källor och övervaknings lösningar.
