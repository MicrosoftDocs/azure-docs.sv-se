---
title: Agenthälsa lösning i Azure Monitor | Microsoft Docs
description: Den här artikeln är avsedd att hjälpa dig att förstå hur du använder den här lösningen för att övervaka hälso tillståndet för agenter som rapporterar direkt till Log Analytics eller System Center Operations Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 171230dc2ce6189e36c601c6c7d3b3612fce160c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711067"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Agenthälsa lösning i Azure Monitor
Agenthälsa lösning i Azure hjälper dig att förstå, för alla agenter som rapporterar direkt till arbets ytan Log Analytics i Azure Monitor eller en System Center Operations Manager hanterings grupp som är ansluten till Azure Monitor, som inte svarar och skickar drift data.  Du kan också hålla reda på hur många agenter distribueras, var de är fördelade geografiskt och utföra andra frågor för att övervaka distributionen av agenter i Azure, andra miljöer i molnet eller lokalt.    

## <a name="prerequisites"></a>Förutsättningar
Innan du distribuerar den här lösningen ska du kontrol lera att du för närvarande har stöd för [Windows-agenter](../agents/agent-windows.md) som rapporteras till Log Analytics arbets yta eller rapportering till en [Operations Manager hanterings grupp](../agents/om-agents.md) som är integrerad med arbets ytan

## <a name="solution-components"></a>Lösningskomponenter
Lösningen består av följande resurser som läggs till i din arbetsyta och ansluter direkt agenter eller Operations Manager-anslutna hanteringsgrupper.

### <a name="management-packs"></a>Hanteringspaket
Om din System Center Operations Manager hanterings grupp är ansluten till en Log Analytics arbets yta installeras följande hanterings paket i Operations Manager.  Dessa hanteringspaket också har installerats på direktanslutna Windows-datorer när du lägger till den här lösningen. Det finns inget att konfigurera eller hantera med dessa hanteringspaket.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../agents/om-agents.md).

## <a name="configuration"></a>Konfiguration
Lägg till Agenthälsa-lösningen i Log Analytics arbets ytan med hjälp av processen som beskrivs i [Lägg till lösningar](solutions.md). Det krävs ingen ytterligare konfiguration.


## <a name="data-collection"></a>Datainsamling
### <a name="supported-agents"></a>Agenter som stöds
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter | Ja | Pulsslagshändelser som samlas in direkt från Windows-agenter.|
| System Center Operations Manager-hanteringsgrupp | Ja | Pulsslags händelser samlas in från agenter som rapporterar till hanterings gruppen var 60 sekund och vidarebefordras sedan till Azure Monitor. En direkt anslutning från Operations Manager agenter till Azure Monitor krävs inte. Pulsslags händelse data vidarebefordras från hanterings gruppen till Log Analytics-arbetsytan.|

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till lösningen i Log Analytics-arbetsytan läggs panelen **agenthälsa** till på instrument panelen. Den här panelen visar det totala antalet agenter och antalet agenter som inte har svarat under de senaste 24 timmarna.<br><br> ![Panelen för Agenthälsa på instrumentpanelen](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Klicka på panelen **Agenthälsa** för att öppna instrumentpanelen för **Agenthälsa**.  Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn visar de översta 10 händelserna med antalet som matchar den kolumnens kriterier för det angivna tidsintervallet. Du kan köra en loggsökning som innehåller hela listan genom att välja **Se alla** längst ned i varje kolumn, eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|--------|-------------|
| Agentantal över tid | En trend över agentantal under en period på sju dagar för Windows- och Linux-agenter.|
| Antal agenter som inte svarar | En lista över agenter som inte har skickat ett pulsslag under de senaste 24 timmarna.|
| Distribution enligt OS-typ | En partition av hur många Windows och Linux-agenter du har i din miljö.|
| Distribution enligt Agent-version | En partition av olika agentversioner som installerats i din miljö och antalet av vardera.|
| Distribution enligt Agent-kategori | En partition av olika typer av agenter som skickar händelser för pulsslag: direkta agenter, OpsMgr-agenter eller OpsMgr Management Server.|
| Distribution enligt hanteringsgrupp | En partition av de olika Operations Manager hanterings grupperna i din miljö.|
| Geoplats för agenter | En partition av de olika länder/regioner där du har agenter och ett totalt antal agenter som har installerats i varje land/region.|
| Antalet installerade Gateways | Antalet servrar som har Log Analytics Gateway installerat och en lista över dessa servrar.|

![Agenthälsa på instrumentpanelen - exempel](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor logg poster
Lösningen skapar en typ av post i Log Analytics-arbetsytan.  

### <a name="heartbeat-records"></a>Pulsslagsposter
En post med en typ av **pulsslag** skapas.  Dessa poster har egenskaper enligt följande tabell.  

| Egenskap | Beskrivning |
| --- | --- |
| `Type` | *Pulsslag*|
| `Category` | Värdet är *Direct Agent*, *SCOM Agent* eller *SCOM Management Server*.|
| `Computer` | Datornamn.|
| `OSType` | Windows- eller Linux-operativsystem.|
| `OSMajorVersion` | Högre operativsystemversion.|
| `OSMinorVersion` | Lägre operativsystemversion.|
| `Version` | Log Analytics agent-eller Operations Manager agent-version.|
| `SCAgentChannel` | Värdet är *Direkt* och/eller *SCManagementServer*.|
| `IsGatewayInstalled` | Om Log Analytics Gateway är installerat är värdet *Sant*, annars är värdet *falskt*.|
| `ComputerIP` | Datorns offentliga IP-adress. På virtuella Azure-datorer visas den offentliga IP-adressen om en sådan finns tillgänglig. För virtuella datorer som använder privata IP-adresser visas Azure SNAT-adressen (inte den privata IP-adressen). |
| `RemoteIPCountry` | Geografisk plats där datorn har distribuerats.|
| `ManagementGroupName` | Namn på Operations Manager-hanteringsgrupp.|
| `SourceComputerId` | Unikt ID för datorn.|
| `RemoteIPLongitude` | Longituden för datorns geografiska plats.|
| `RemoteIPLatitude` | Latituden för datorns geografiska plats.|

Varje agent som rapporterar till en Operations Manager hanterings server skickar två pulsslag och värdet för egenskapen SCAgentChannel innehåller både **direkt** och **SCManagementServer** beroende på vilka data källor och övervaknings lösningar som du har aktiverat i din prenumeration. Om du återkallar, skickas data från lösningar direkt från en Operations Manager hanterings server till Azure Monitor, eller på grund av den mängd data som samlas in på agenten, skickas direkt från agenten till Azure Monitor. För pulsslaghändelser som har värdet **SCManagementServer**, är ComputerIP-värdet IP-adressen för hanteringsservern eftersom data faktiskt har laddats upp av den.  För pulsslag där SCAgentChannel är inställt på **Direkt** är det den offentliga IP-adressen för agenten.  

## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på sökningar i loggen för poster som har samlats in av den här lösningen.

| Fråga | Beskrivning |
|:---|:---|
| Heartbeat &#124; distinct Computer |Totalt antal agenter |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Antal agenter som inte har svarat de senaste 24 timmarna |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Antal agenter som inte har svarat de senaste 15 minuterna |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Datorer online (under de senaste 24 timmarna) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Totalt antal agenter som har varit offline de senaste 30 minuterna (för de senaste 24 timmarna) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Hämta en trend över antalet agenter över tid enligt OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution enligt OS-typ |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution enligt Agent-version |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution enligt Agent-kategori |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution enligt hanteringsgrupp |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geoplats för agenter |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Antal installerade Log Analytics-gatewayer |




## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [aviseringar i Azure Monitor](../alerts/alerts-overview.md) för information om hur du genererar aviseringar från logg frågor. 

