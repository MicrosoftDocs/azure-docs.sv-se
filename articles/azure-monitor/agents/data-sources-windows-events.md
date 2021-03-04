---
title: Samla in Windows händelse logg data källor med Log Analytics agent i Azure Monitor
description: Beskriver hur du konfigurerar Windows-händelseloggen med Azure Monitor och information om de poster som de skapar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: a3baa83e2ae306f1e43aee52e29a151bad6f85d9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036611"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Samla in data källor för Windows-händelseloggen med Log Analytics agent
Windows-händelseloggar är en av de vanligaste [data källorna](../agents/agent-data-sources.md) för Log Analytics agenter på virtuella Windows-datorer eftersom många program skriver till händelse loggen i Windows.  Du kan samla in händelser från standard loggar som system och program, förutom att ange anpassade loggar som skapats av program som du behöver övervaka.

> [!IMPORTANT]
> Den här artikeln beskriver hur du samlar in Windows-händelser med [Log Analytics agent](./log-analytics-agent.md) som är en av de agenter som används av Azure Monitor. Andra agenter samlar in olika data och konfigureras på olika sätt. Se [Översikt över Azure Monitor agenter](../agents/agents-overview.md) för en lista över tillgängliga agenter och de data som de kan samla in.

![Windows-händelser](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurera händelse loggar i Windows
Konfigurera Windows-händelseloggar från [konfigurations menyn agenter](../agents/agent-data-sources.md#configuring-data-sources) för Log Analytics arbets ytan.

Azure Monitor samlar endast in händelser från Windows-händelseloggen som anges i inställningarna.  Du kan lägga till en händelse logg genom att skriva namnet på loggen och klicka på **+** .  Endast händelser med de valda allvarlighets graderna samlas in för varje logg.  Kontrol lera allvarlighets graderna för den specifika logg som du vill samla in.  Du kan inte ange några ytterligare kriterier för att filtrera händelser.

När du skriver namnet på en händelse logg ger Azure Monitor förslag på vanliga händelse logg namn. Om loggen som du vill lägga till inte visas i listan kan du fortfarande lägga till den genom att skriva i det fullständiga namnet på loggen. Du kan hitta det fullständiga namnet på loggen med hjälp av logg boken. I logg boken öppnar du sidan *Egenskaper* för loggen och kopierar strängen från fältet *fullständigt namn* .

[![Konfigurera Windows-händelser](media/data-sources-windows-events/configure.png)](media/data-sources-windows-events/configure.png#lightbox)

> [!NOTE]
> Kritiska händelser från händelse loggen i Windows har allvarlighets graden "Error" i Azure Monitor loggar.

## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in varje händelse som matchar en vald allvarlighets grad från en övervakad händelse logg när händelsen skapas.  Agenten registrerar sitt ställe i varje händelse logg som samlas in från.  Om agenten blir offline under en viss tids period samlar den in händelser från den sista som den senast slutade, även om dessa händelser skapades medan agenten var offline.  Det är möjligt att dessa händelser inte samlas in om händelse loggen bryts med insamlade händelser som skrivs över medan agenten är offline.

>[!NOTE]
>Azure Monitor samlar inte in gransknings händelser som skapats av SQL Server från källan *MSSQLServer* med händelse-ID 18453 som innehåller nyckelord – *klassisk* eller *Granska lyckade* och nyckelords *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Egenskaper för Windows händelse poster
Händelse poster i Windows har en typ av **händelse** och har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Namnet på datorn som händelsen samlades in från. |
| EventCategory |Händelsens kategori. |
| EventData |Alla händelse data i RAW-format. |
| EventID |Händelsens nummer. |
| EventLevel |Händelsens allvarlighets grad i numerisk form. |
| EventLevelName |Allvarlighets grad för händelsen i text form. |
| EventLog |Namnet på händelse loggen som händelsen samlades in från. |
| ParameterXml |Händelse parameter värden i XML-format. |
| ManagementGroupName |Namnet på hanterings gruppen för System Center Operations Managers agenter.  För andra agenter är det här värdet `AOI-<workspace ID>` |
| RenderedDescription |Händelse Beskrivning med parameter värden |
| Källa |Händelsens källa. |
| SourceSystem |Typ av agent som händelsen samlades in från. <br> OpsManager – Windows-agent, antingen direkt anslutning eller Operations Manager hanterat <br> Linux – alla Linux-agenter  <br> AzureStorage – Azure-diagnostik |
| TimeGenerated |Datum och tid då händelsen skapades i Windows. |
| Användarnamn |Användar namnet för det konto som loggade händelsen. |

## <a name="log-queries-with-windows-events"></a>Logga frågor med Windows-händelser
Följande tabell innehåller olika exempel på logg frågor som hämtar Windows-händelseloggar.

| Söka i data | Beskrivning |
|:---|:---|
| Händelse |Alla Windows-händelser. |
| Händelse &#124; där EventLevelName = = "Error" |Alla Windows-händelser med allvarlighets graden fel. |
| Event &#124; summerings antal () efter källa |Antal Windows-händelser efter källa. |
| Händelse &#124; där EventLevelName = = "fel" &#124; sammanfatta antal () efter källa |Antal Windows fel händelser per källa. |


## <a name="next-steps"></a>Nästa steg
* Konfigurera Log Analytics för att samla in andra [data källor](../agents/agent-data-sources.md) för analys.
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.  
* Konfigurera [insamling av prestanda räknare](data-sources-performance-counters.md) från dina Windows-agenter.