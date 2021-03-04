---
title: Samla in IIS-loggar med Log Analytics agent i Azure Monitor
description: Internet Information Services (IIS) lagrar användar aktivitet i loggfiler som kan samlas in av Azure Monitor.  Den här artikeln beskriver hur du konfigurerar insamling av IIS-loggar och information om de poster som de skapar i Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 484e49219b94f5974661b1e76f533236666ebcfb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050740"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Samla in IIS-loggar med Log Analytics agent i Azure Monitor
Internet Information Services (IIS) lagrar användar aktivitet i loggfiler som kan samlas in av Log Analytics-agenten och lagras i [Azure Monitor loggar](../data-platform.md).

> [!IMPORTANT]
> Den här artikeln beskriver hur du samlar in IIS-loggar med [Log Analytics agent](./log-analytics-agent.md) som är en av de agenter som används av Azure Monitor. Andra agenter samlar in olika data och konfigureras på olika sätt. Se [Översikt över Azure Monitor agenter](../agents/agents-overview.md) för en lista över tillgängliga agenter och de data som de kan samla in.

![IIS-loggar](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurera IIS-loggar
Azure Monitor samlar in poster från loggfiler som skapats av IIS, så du måste [Konfigurera IIS för loggning](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

Azure Monitor stöder endast IIS-loggfiler som lagras i W3C-format och stöder inte anpassade fält eller avancerad IIS-loggning. Det samlar inte in loggar i NCSA eller ursprungligt IIS-format.

Konfigurera IIS-loggar i Azure Monitor från [agent konfigurations menyn](../agents/agent-data-sources.md#configuring-data-sources) för Log Analytics agenten.  Ingen konfiguration krävs förutom att välja **samla in W3C-format IIS-loggfiler**.


## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in IIS-loggposter från varje agent varje gång loggens tidsstämpel ändras. Loggen läses var **5: e minut**. Om IIS inte uppdaterar tidsstämpeln före förnyelse tiden när en ny fil skapas, kommer posterna att samlas in när den nya filen skapas. Frekvensen för att skapa nya filer styrs av inställningen för **förnyelse av logg filen** för IIS-platsen, vilket är en gång om dagen som standard. Om inställningen anges **per timme** Azure Monitors loggen varje timme. Om inställningen är **dagligen** samlar Azure monitor in loggen var 24: e timme.

> [!IMPORTANT]
> Vi rekommenderar att du ställer in **schemat för förnyelse av logg filen** på **varje timme**. Om det är inställt på **daglig**, kan det uppstå toppar i dina data eftersom de bara samlas in en gång per dag.

## <a name="iis-log-record-properties"></a>Egenskaper för logg poster i IIS
Poster i IIS-loggen har en typ av **W3CIISLog** och har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Namnet på datorn som händelsen samlades in från. |
| cIP |Klientens IP-adress. |
| csMethod |Metod för begäran, till exempel GET eller POST. |
| csReferer |Platsen som användaren följt av en länk från till den aktuella platsen. |
| csUserAgent |Webbläsarens typ av klient. |
| csUserName |Namnet på den autentiserade användare som har åtkomst till servern. Anonyma användare anges med ett bindestreck. |
| csUriStem |Målet för begäran, till exempel en webb sida. |
| csUriQuery |Fråga, om det finns, som klienten försökte utföra. |
| ManagementGroupName |Namnet på hanterings gruppen för Operations Managers agenter.  För andra agenter är detta AOI-\<workspace ID\> |
| RemoteIPCountry |Land/region för klientens IP-adress. |
| RemoteIPLatitude |Latitud för klientens IP-adress. |
| RemoteIPLongitude |Longitud för klientens IP-adress. |
| scStatus |HTTP-statuskod. |
| scSubStatus |Felkod för under status. |
| scWin32Status |Windows-statuskod. |
| sIP |IP-adressen för webb servern. |
| SourceSystem |OpsMgr |
| sPort |Port på den server som klienten är ansluten till. |
| sSiteName |Namnet på IIS-webbplatsen. |
| TimeGenerated |Datum och tid då posten loggades. |
| TimeTaken |Tids längd för att bearbeta begäran i millisekunder. |

## <a name="log-queries-with-iis-logs"></a>Logga frågor med IIS-loggar
Följande tabell innehåller olika exempel på logg frågor som hämtar poster i IIS-loggen.

| Söka i data | Beskrivning |
|:--- |:--- |
| W3CIISLog |Alla logg poster i IIS. |
| W3CIISLog &#124; där scStatus = = 500 |Alla poster i IIS-loggen med retur status 500. |
| W3CIISLog &#124; sammanfatta antal () efter cIP |Antal IIS-loggfiler efter klient-IP-adress. |
| W3CIISLog &#124; där csHost = = "www \. contoso.com" &#124; sammanfatta antal () efter csUriStem |Antal IIS-loggfiler efter URL för värd-www- \. contoso.com. |
| W3CIISLog &#124; sammanfatta sum (csBytes) efter dator &#124; ta 500000 |Totalt antal byte som tagits emot av varje IIS-dator. |

## <a name="next-steps"></a>Nästa steg
* Konfigurera Azure Monitor för att samla in andra [data källor](../agents/agent-data-sources.md) för analys.
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.