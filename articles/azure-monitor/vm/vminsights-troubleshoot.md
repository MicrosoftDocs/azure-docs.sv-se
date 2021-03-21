---
title: Felsöka VM Insights
description: Felsöka VM Insights-installation.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555764"
---
# <a name="troubleshoot-vm-insights"></a>Felsöka VM Insights
Den här artikeln innehåller felsöknings information för när du har problem med att aktivera eller använda VM-insikter.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Det går inte att aktivera VM-insikter på en dator
När du registrerar en virtuell Azure-dator från Azure Portal inträffar följande steg:

- En standard arbets yta Log Analytics skapas om alternativet har valts.
- Log Analytics Agent installeras på virtuella Azure-datorer med ett VM-tillägg om agenten redan är installerad.
- Beroende agenten installeras på virtuella Azure-datorer med ett tillägg, om det är klart krävs.
  
Under onboarding-processen verifieras vart och ett av dessa steg och en meddelande status visas i portalen. Konfigurationen av arbets ytan och Agent installationen tar vanligt vis 5 till 10 minuter. Det tar ytterligare 5 till 10 minuter för data att bli tillgängliga för visning i portalen.

Om du får ett meddelande om att den virtuella datorn måste registreras när du har utfört onboarding-processen, kan du göra upp till 30 minuter för att processen ska slutföras. Om problemet kvarstår kan du läsa följande avsnitt för möjliga orsaker.

### <a name="is-the-virtual-machine-running"></a>Körs den virtuella datorn?
 Om den virtuella datorn har varit inaktive rad för tillfället, är avstängd för närvarande, eller bara nyligen har Aktiver ATS, kommer du inte att ha data att visa för en bit förrän data kommer.

### <a name="is-the-operating-system-supported"></a>Stöds operativ systemet?
Om operativ systemet inte finns i listan över [operativ system som stöds](vminsights-enable-overview.md#supported-operating-systems) , kommer tillägget inte att installeras och du ser det här meddelandet att vi väntar på att data ska tas emot.

### <a name="did-the-extension-install-properly"></a>Installerades tillägget korrekt?
Om du fortfarande ser ett meddelande om att den virtuella datorn behöver registreras, kan det betyda att ett eller båda tilläggen inte kunde installeras korrekt. Kontrol lera **tilläggs** sidan för den virtuella datorn i Azure Portal för att kontrol lera att följande tillägg finns med i listan.

| Operativsystem | Agenter | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft. Azure. Monitoring. DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Om du inte ser båda tilläggen för ditt operativ system i listan över installerade tillägg måste de installeras. Om tilläggen visas men deras status inte visas som *etableringen har slutförts*, bör tillägget tas bort och installeras om.

### <a name="do-you-have-connectivity-issues"></a>Har du problem med anslutningen?
För Windows-datorer kan du använda  *TestCloudConnectivity* -verktyget för att identifiera anslutnings problem. Verktyget installeras som standard med agenten i mappen *%systemroot%\Program Files\Microsoft Monitoring Agent\Agent*. Kör verktyget från en upphöjd kommando tolk. Det returnerar resultat och markerar var testet Miss lyckas. 

![TestCloudConnectivity-verktyg](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Mer agent fel sökning

I följande artiklar finns fel söknings problem med Log Analytics-agenten:

- [Felsöka problem med Log Analytics-agenten för Windows](../agents/agent-windows-troubleshoot.md)
- [Felsöka problem med Log Analytics-agenten för Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>Vyn prestanda saknar data
Om agenterna verkar installeras korrekt men du inte ser några data i vyn prestanda kan du läsa följande avsnitt för möjliga orsaker.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Har din Log Analytics-arbetsyta nått sin data gräns?
Kontrol lera [kapacitets reservationer och prissättningen för data inmatning](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Skickar den virtuella datorn logg-och prestanda data till Azure Monitor loggar?

Öppna Log Analytics från **loggar** i Azure Monitor-menyn i Azure Portal. Kör följande fråga för datorn:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Om du inte ser några data kan du ha problem med agenten. Se avsnittet ovan för information om agent fel sökning.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>Den virtuella datorn visas inte i Map-vyn

### <a name="is-the-dependency-agent-installed"></a>Är beroende agenten installerad?
 Använd informationen i avsnitten ovan för att avgöra om beroende agenten är installerad och fungerar korrekt.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Är du på den Log Analytics kostnads fria nivån?
Den [Log Analytics kostnads fria nivån](https://azure.microsoft.com/pricing/details/monitor/) är en äldre pris plan som tillåter upp till fem unika tjänstkarta datorer. Efterföljande datorer visas inte i Tjänstkarta, även om de fem föregående inte längre skickar data.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Skickar den virtuella datorn logg-och prestanda data till Azure Monitor loggar?
Använd logg frågan i [vyn prestanda det finns inga data](#performance-view-has-no-data) för att avgöra om data samlas in för den virtuella datorn. Om inte data samlas in använder du verktyget TestCloudConnectivity som beskrivs ovan för att avgöra om du har problem med anslutningen.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>Den virtuella datorn visas i Map-vyn men saknar data
Om den virtuella datorn finns i Map-vyn, installeras beroende agenten och körs, men kernel-drivrutinen har inte lästs in. Kontrol lera logg filen på följande platser:

| Operativsystem | Loggas | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

De sista raderna i filen anger varför kerneln inte har lästs in. Till exempel kanske din kernel inte stöds i Linux om du har uppdaterat den.
## <a name="next-steps"></a>Nästa steg

- Information om hur du registrerar VM Insights-agenter finns i [Aktivera översikt över VM](vminsights-enable-overview.md)-insikter.
