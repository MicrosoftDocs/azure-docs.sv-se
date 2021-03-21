---
title: Översikt över Azure Monitor Agent
description: Översikt över Azure Monitor Agent (AMA) som samlar in övervaknings data från gäst operativ systemet för virtuella datorer.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.custom: references_regions
ms.openlocfilehash: f1f1ea787406d900c8035c0462ef903b848d7e81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608222"
---
# <a name="azure-monitor-agent-overview-preview"></a>Översikt över Azure Monitor Agent (för hands version)
Azure Monitor agenten (AMA) samlar in övervaknings data från gäst operativ systemet på virtuella datorer och levererar det till Azure Monitor. Den här artikeln innehåller en översikt över Azure Monitor Agent, inklusive hur du installerar den och hur du konfigurerar data insamling.

## <a name="relationship-to-other-agents"></a>Förhållande till andra agenter
Azure Monitor Agent ersätter följande agenter som för närvarande används av Azure Monitor för att samla in gäst data från virtuella datorer:

- [Log Analytics agent](./log-analytics-agent.md) – skickar data till Log Analytics-arbetsytan och stöder lösningar för VM-insikter och övervakning.
- [Diagnostiskt tillägg](./diagnostics-extension-overview.md) – skickar data till Azure Monitor mått (endast Windows), Azure Event Hubs och Azure Storage.
- [Teleympkvistar-agenten](../essentials/collect-custom-metrics-linux-telegraf.md) – skickar data till Azure Monitor mått (endast Linux).

Förutom att konsolidera den här funktionen i en enda agent ger Azure Monitor-agenten följande fördelar jämfört med befintliga agenter:

- Övervaknings omfång. Konfigurera samling centralt för olika uppsättningar med data från olika uppsättningar av virtuella datorer.  
- Linux multi-värdar: skicka data från virtuella Linux-datorer till flera arbets ytor.
- Windows-händelse filtrering: Använd XPATH-frågor för att filtrera vilka Windows-händelser som samlas in.
- Förbättrad tillägg hantering: Azure Monitor Agent använder en ny metod för att hantera utöknings barhet som är mer transparent och lättare än hanterings paket och Linux-plugin-program i de aktuella Log Analyticss agenterna.

### <a name="changes-in-data-collection"></a>Ändringar i data insamling
Metoderna för att definiera data insamling för befintliga agenter skiljer sig tydligt från varandra och var och en har utmaningar som åtgärdas med Azure Monitor Agent.

- Log Analytics agent hämtar konfigurationen från en Log Analytics arbets yta. Detta är enkelt att konfigurera, men svårt att definiera oberoende definitioner för olika virtuella datorer. Den kan bara skicka data till en Log Analytics-arbetsyta.

- Diagnostiskt tillägg har en konfiguration för varje virtuell dator. Detta är enkelt att definiera oberoende definitioner för olika virtuella datorer men svårt att hantera centralt. Den kan bara skicka data till Azure Monitor mått, Azure Event Hubs eller Azure Storage. För Linux-agenter krävs det att du måste skicka data till Azure Monitor mått med öppen källkod.

Azure Monitor Agent använder sig av [data insamlings regler (DCR)](data-collection-rule-overview.md) för att konfigurera data som ska samlas in från varje agent. Data insamlings regler möjliggör hantering av samlings inställningar i skala samtidigt som unika, begränsade konfigurationer för del mängder av datorer aktive ras. De är oberoende av arbets ytan och oberoende av den virtuella datorn, vilket gör att de kan definieras en gång och återanvändas på olika datorer och miljöer. Se [Konfigurera data insamling för Azure Monitor agenten (för hands version)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Bör jag byta till Azure Monitor-agenten?
Azure Monitor Agent samexisterar med de [allmänt tillgängliga agenterna för Azure Monitor](agents-overview.md), men du kan överväga att överföra dina virtuella datorer från aktuella agenter under den Azure Monitor agentens offentliga för hands versions period. Tänk på följande faktorer när du gör den här beräkningen.

- **Miljö krav.** Azure Monitor Agent har en begränsad uppsättning operativ system, miljöer och nätverks krav som stöds än de aktuella agenterna. Framtida miljö support, till exempel nya operativ system versioner och olika typer av nätverks krav, finns förmodligen bara i Azure Monitor-agenten. Du bör kontrol lera om din miljö stöds av Azure Monitor-agenten. Annars måste du hålla den aktuella agenten. Om Azure Monitor Agent stöder din aktuella miljö bör du överväga att gå över till den.
- **Offentlig för hands versions risk tolerans.** Även om Azure Monitor-agenten har testats grundligt för de scenarier som stöds för närvarande, är agenten fortfarande i offentlig för hands version. Förbättringar av versions uppdateringar och funktioner sker ofta och kan leda till buggar. Du bör bedöma risken för en bugg i agenten på dina virtuella datorer som kan stoppa data insamlingen. Om ett mellanrum i data insamlingen inte kommer att ha en betydande inverkan på dina tjänster kan du fortsätta med Azure Monitor agenten. Om du har låg tolerans för eventuell instabilitet bör du vara kvar med de allmänt tillgängliga agenterna tills Azure Monitor Agent når denna status.
- **Aktuella och nya funktions krav.** Azure Monitor agenten innehåller flera nya funktioner, till exempel filtrering, omfattning och flera värdar, men inte vid paritet än med de aktuella agenterna för andra funktioner, till exempel anpassad logg insamling och integrering med lösningar. De flesta nya funktioner i Azure Monitor görs bara tillgängliga med Azure Monitor Agent, så över tiden är fler funktioner bara tillgängliga i den nya agenten. Du bör fundera över om Azure Monitor Agent har de funktioner som du behöver och om det finns några funktioner som du kan utföra tillfälligt utan att hämta andra viktiga funktioner i den nya agenten. Om Azure Monitor Agent har alla kärn funktioner som du behöver kan du överväga att gå över till den. Om det finns viktiga funktioner som du behöver kan du fortsätta med den aktuella agenten tills Azure Monitor Agent når paritet.
- **Tolerans för omarbete.** Om du konfigurerar en ny miljö med resurser som distributions skript och onboarding-mallar bör du fundera över om du kommer att kunna återanvända dem när Azure Monitor Agent blir allmänt tillgänglig. Om ansträngningen för den här återworken är minimal, stannar du med de aktuella agenterna för tillfället. Om det kommer att ta en stor del av arbetet bör du överväga att konfigurera din nya miljö med den nya agenten. Azure Monitor Agent förväntas bli allmänt tillgänglig och ett utfasnings datum som publicerats för Log Analytics agenterna i 2021. De aktuella agenterna kommer att stödjas i flera år när utfasningen börjar.



## <a name="current-limitations"></a>Aktuella begränsningar
Följande begränsningar gäller vid en offentlig för hands version av Azure Monitor agenten:

- Azure Monitor agenten har inte stöd för lösningar och insikter som VM Insights och Azure Security Center. Det enda scenario som stöds för närvarande är att samla in data med de data insamlings regler som du konfigurerar. 
- Data insamlings regler måste skapas i samma region som en Log Analytics arbets yta som används som mål.
- Virtuella Azure-datorer, skalnings uppsättningar för virtuella datorer och Azure Arc-aktiverade servrar stöds för närvarande. Azure Kubernetes-tjänsten och andra beräknings resurs typer stöds inte för närvarande.
- Den virtuella datorn måste ha åtkomst till följande HTTPS-slutpunkter:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="supported-regions"></a>Regioner som stöds
Azure Monitor-agenten stöder för närvarande resurser i följande regioner:

- Asien, östra
- Sydostasien
- Australien, centrala
- Australien, östra
- Australien, sydöstra
- Kanada, centrala
- Europa, norra
- Europa, västra
- Frankrike, centrala
- Tyskland, västra centrala
- Indien, centrala
- Japan, östra
- Sydkorea, centrala
- Sydafrika, norra
- Schweiz, norra
- Storbritannien, södra
- Storbritannien, västra
- Central US
- East US
- USA, östra 2
- USA, norra centrala
- USA, södra centrala
- USA, västra
- USA, västra 2
- USA, västra centrala

## <a name="coexistence-with-other-agents"></a>Samexistens med andra agenter
Azure Monitor agenten kan samverka med befintliga agenter så att du kan fortsätta att använda sina befintliga funktioner under utvärderingen eller migreringen. Detta är särskilt viktigt på grund av begränsningarna i offentlig för hands version i stöd för befintliga lösningar. Du bör vara försiktig när du samlar in dubblettdata eftersom detta kan skeva frågeresultat och leda till ytterligare avgifter för data inmatning och kvarhållning.

Till exempel använder VM Insights Log Analytics-agenten för att skicka prestanda data till en Log Analytics arbets yta. Du kan också ha konfigurerat arbets ytan för att samla in Windows-händelser och Syslog-händelser från agenter. Om du installerar Azure Monitor agenten och skapar en data insamlings regel för dessa händelser och prestanda data, resulterar det i dubbla data.


## <a name="costs"></a>Kostnader
Det kostar inget att Azure Monitor Agent, men du kan debiteras avgifter för inmatade data. Se [Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/) för information om Log Analytics data insamling och kvarhållning och för kund mått.

## <a name="data-sources-and-destinations"></a>Data källor och mål
I följande tabell visas de typer av data som du kan samla in med Azure Monitor-agenten med hjälp av regler för data insamling och var du kan skicka dessa data. Se [vad som övervakas av Azure Monitor?](../monitor-reference.md) för en lista med insikter, lösningar och andra lösningar som använder Azure Monitor-agenten för att samla in andra typer av data.


Azure Monitor Agent skickar data till Azure Monitor mått eller en Log Analytics arbets yta som stöder Azure Monitor loggar.

| Datakälla | Mål | Description |
|:---|:---|:---|
| Prestanda        | Azure Monitor-statistik<br>Log Analytics-arbetsyta | Numeriska värden mäter prestanda för olika aspekter av operativ system och arbets belastningar. |
| Händelse loggar i Windows | Log Analytics-arbetsyta | Information som skickas till händelse loggnings systemet i Windows. |
| Syslog             | Log Analytics-arbetsyta | Information som skickas till händelse loggnings systemet i Linux. |


## <a name="supported-operating-systems"></a>Operativsystem som stöds
Se [operativ system som stöds](agents-overview.md#supported-operating-systems) för en lista över Windows-och Linux-operativsystem som för närvarande stöds av den Azure Monitor agenten.



## <a name="security"></a>Säkerhet
Den Azure Monitor agenten behöver inte några nycklar utan kräver istället en [systemtilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Du måste ha en systemtilldelad hanterad identitet aktive rad på varje virtuell dator innan du distribuerar agenten.

## <a name="networking"></a>Nätverk
Azure Monitor Agent har stöd för Azure Service-Taggar (både AzureMonitor-och AzureResourceManager-Taggar krävs) men fungerar ännu inte med Azure Monitor privata länk omfattningar eller direkta proxyservrar.


## <a name="next-steps"></a>Nästa steg

- [Installera Azure Monitor Agent](azure-monitor-agent-install.md) på virtuella Windows-och Linux-datorer.
- [Skapa en data insamlings regel](data-collection-rule-azure-monitor-agent.md) för att samla in data från agenten och skicka den till Azure Monitor.