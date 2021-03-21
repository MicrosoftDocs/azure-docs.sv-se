---
title: Aviseringshantering lösning i Azure Log Analytics | Microsoft Docs
description: Aviseringshantering-lösningen i Log Analytics hjälper dig att analysera alla aviseringar i din miljö.  Förutom att konsolidera aviseringar som genereras inom Log Analytics importeras aviseringar från anslutna System Center Operations Manager hanterings grupper till Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 6f3761b1c933d113bf65c70370c29bcfa7950eea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728662"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Aviseringshantering lösning i Azure Log Analytics

![Aviseringshantering ikon](media/alert-management-solution/icon.png)

Aviseringshantering-lösningen hjälper dig att analysera alla aviseringar i Log Analytics-lagringsplatsen.  Dessa aviseringar kan komma från en rad olika källor, inklusive de källor [som skapats av Log Analytics](../alerts/alerts-overview.md) eller [importer ATS från nagios eller zabbix](../vm/quick-collect-linux-computer.md). Lösningen importerar även aviseringar från alla [anslutna System Center Operations Manager hanterings grupper](../agents/om-agents.md).

## <a name="prerequisites"></a>Förutsättningar
Lösningen fungerar med alla poster i Log Analytics-lagringsplatsen med en typ av **avisering**, så du måste utföra vilken konfiguration som krävs för att samla in dessa poster.

- Skapa aviserings [regler](../alerts/alerts-overview.md) för att skapa aviserings poster direkt i databasen för Log Analytics aviseringar.
- För nagios-och zabbix-aviseringar [konfigurerar du servrarna](../vm/quick-collect-linux-computer.md) för att skicka aviseringar till Log Analytics.
- För System Center Operations Manager aviseringar [ansluter du Operations Manager hanterings gruppen till din Log Analytics-arbetsyta](../agents/om-agents.md).  Aviseringar som skapats i System Center Operations Manager importeras till Log Analytics.  

## <a name="configuration"></a>Konfiguration
Lägg till Aviseringshantering-lösningen i Log Analytics arbets ytan med hjälp av processen som beskrivs i [Lägg till lösningar](../insights/solutions.md). Det krävs ingen ytterligare konfiguration.

## <a name="management-packs"></a>Hanteringspaket
Om din System Center Operations Manager hanterings grupp är ansluten till din Log Analytics arbets yta installeras följande hanterings paket i System Center Operations Manager när du lägger till den här lösningen.  Det finns ingen konfiguration eller underhåll av de hanterings paket som krävs.

* Microsoft System Center Advisor Aviseringshantering (Microsoft. IntelligencePacks. AlertManagement)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../agents/om-agents.md).

## <a name="data-collection"></a>Datainsamling
### <a name="agents"></a>Agenter
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|:--- |:--- |:--- |
| [Windows-agenter](../agents/agent-windows.md) | Inga |Direkta Windows-agenter genererar inte aviseringar.  Log Analytics aviseringar kan skapas från händelser och prestanda data som samlas in från Windows-agenter. |
| [Linux-agenter](../vm/quick-collect-linux-computer.md) | Inga |Direkta Linux-agenter genererar inte aviseringar.  Log Analytics aviseringar kan skapas från händelser och prestanda data som samlas in från Linux-agenter.  Nagios-och zabbix-aviseringar samlas in från de servrar som kräver Linux-agenten. |
| [System Center Operations Manager-hanteringsgrupp](../agents/om-agents.md) |Ja |Aviseringar som genereras på Operations Manager agenter levereras till hanterings gruppen och vidarebefordras sedan till Log Analytics.<br><br>En direkt anslutning från Operations Manager agenter till Log Analytics krävs inte. Aviserings data vidarebefordras från hanterings gruppen till Log Analytics-lagringsplatsen. |


### <a name="collection-frequency"></a>Insamlingsfrekvens
- Aviserings poster är tillgängliga för lösningen så snart de lagras i lagrings platsen.
- Aviserings data skickas från Operations Manager hanterings grupp till Log Analytics var tredje minut.  

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till Aviseringshantering-lösningen i Log Analytics arbets ytan läggs **aviseringshantering** panel till på instrument panelen.  Den här panelen visar ett antal och en grafisk representation av antalet aktiva aviseringar som har genererats under de senaste 24 timmarna.  Du kan inte ändra det här tidsintervallet.

![Aviseringshantering panel](media/alert-management-solution/tile.png)

Öppna instrument panelen **aviseringshantering** genom att klicka på panelen **aviseringshantering** .  Instrumentpanelen innehåller kolumnerna i följande tabell.  Varje kolumn listar de 10 vanligaste aviseringarna genom att räkna matchningen av kolumnens kriterier för det angivna omfånget och tidsintervallet.  Du kan köra en loggs ökning som ger hela listan genom att klicka på **Visa alla** längst ned i kolumnen eller genom att klicka på kolumn rubriken.

| Kolumn | Beskrivning |
|:--- |:--- |
| Kritiska aviseringar |Alla aviseringar med allvarlighets graden kritisk grupperat efter aviserings namn.  Klicka på ett aviserings namn för att köra en loggs ökning som returnerar alla poster för den aviseringen. |
| Varnings aviseringar |Alla aviseringar med allvarlighets graden varning grupperade efter aviserings namn.  Klicka på ett aviserings namn för att köra en loggs ökning som returnerar alla poster för den aviseringen. |
| Aktiva System Center Operations Manager-aviseringar |Alla aviseringar som samlats in från Operations Manager med andra tillstånd än *stängda* grupperade efter källa som genererade aviseringen. |
| Alla aktiva aviseringar |Alla aviseringar med valfri allvarlighets grad grupperade efter aviserings namn. Innehåller bara Operations Manager aviseringar med något annat tillstånd än *stängt*. |

Om du rullar till höger innehåller instrument panelen flera vanliga frågor som du kan klicka på för att utföra en [loggs ökning](../logs/log-query-overview.md) för aviserings data.

![Aviseringshantering instrument panel](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-poster
Aviseringshantering-lösningen analyserar alla poster med en typ av **avisering**.  Aviseringar som skapats av Log Analytics eller samlas in från nagios eller zabbix samlas inte direkt in i lösningen.

Lösningen importerar aviseringar från System Center Operations Manager och skapar en motsvarande post för var och en med en typ av **avisering** och en SourceSystem av **OpsManager**.  Dessa poster har egenskaperna i följande tabell:  

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |*Varning* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Information om det data objekt som orsakade att aviseringen genererades i XML-format. |
| `AlertDescription` |Detaljerad beskrivning av aviseringen. |
| `AlertId` |GUID för aviseringen. |
| `AlertName` |Aviseringens namn. |
| `AlertPriority` |Aviseringens prioritets nivå. |
| `AlertSeverity` |Aviseringens allvarlighets grad. |
| `AlertState` |Senaste lösnings tillstånd för aviseringen. |
| `LastModifiedBy` |Namnet på den användare som senast ändrade aviseringen. |
| `ManagementGroupName` |Namnet på hanterings gruppen där aviseringen skapades. |
| `RepeatCount` |Antal gånger samma avisering genererades för samma övervakade objekt sedan de matchades. |
| `ResolvedBy` |Namnet på den användare som löste aviseringen. Tomt om aviseringen ännu inte har lösts. |
| `SourceDisplayName` |Visnings namn för det övervaknings objekt som skapade aviseringen. |
| `SourceFullName` |Fullständigt namn på det övervaknings objekt som skapade aviseringen. |
| `TicketId` |Biljett-ID för aviseringen om System Center Operations Managers miljön är integrerad med en process för att tilldela biljetter för aviseringar.  Tom av inget biljett-ID har tilldelats. |
| `TimeGenerated` |Datum och tid då aviseringen skapades. |
| `TimeLastModified` |Datum och tid då aviseringen senast ändrades. |
| `TimeRaised` |Datum och tid då aviseringen genererades. |
| `TimeResolved` |Datum och tid då aviseringen löstes. Tomt om aviseringen ännu inte har lösts. |

## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på loggs ökningar för aviserings poster som samlas in av den här lösningen: 

| Fråga | Beskrivning |
|:---|:---|
| Aviserings &#124; där SourceSystem = = "OpsManager" och AlertSeverity = = "Error" och TimeRaised > sedan (24 timmarna) |Kritiska aviseringar som genererats under de senaste 24 timmarna |
| Aviserings &#124; där AlertSeverity = = "varning" och TimeRaised > sedan (24 timmarna) |Varnings aviseringar som har genererats under de senaste 24 timmarna |
| Aviserings &#124; där SourceSystem = = "OpsManager" och in alertstate! = "closed" och TimeRaised > sedan (24 timmarna) &#124; sammanfatta antal = Count () efter SourceDisplayName |Källor med aktiva aviseringar som har genererats under de senaste 24 timmarna |
| Varnings &#124; där SourceSystem = = "OpsManager" och AlertSeverity = = "Error" och TimeRaised > sedan (24 timmarna) och in alertstate! = "closed" |Kritiska aviseringar som genererats under de senaste 24 timmarna som fortfarande är aktiva |
| Aviserings &#124; där SourceSystem = = "OpsManager" och TimeRaised > sedan (24 timmarna) och in alertstate = = "closed" |Aviseringar som genererats under de senaste 24 timmarna som nu har stängts |
| Aviserings &#124; där SourceSystem = = "OpsManager" och TimeRaised > sedan (1d) &#124; sammanfatta antal = antal () efter AlertSeverity |Aviseringar som genererats under den senaste 1 dagen grupperade efter allvarlighets grad |
| Aviserings &#124; där SourceSystem = = "OpsManager" och TimeRaised > sedan (1d) &#124; sortera efter RepeatCount DESC |Aviseringar som genererats under den senaste 1 dagen sorteras efter deras REPEAT Count-värde |



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Aviseringar i Log Analytics](../alerts/alerts-overview.md) för information om att generera aviseringar från Log Analytics.