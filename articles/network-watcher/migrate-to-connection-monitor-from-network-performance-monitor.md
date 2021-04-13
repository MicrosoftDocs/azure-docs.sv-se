---
title: Migrera till Anslutningsövervakare från Övervakare av nätverksprestanda
titleSuffix: Azure Network Watcher
description: Lär dig hur du migrerar till Anslutningsövervakare från Övervakare av nätverksprestanda.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366359"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrera till Anslutningsövervakare från Övervakare av nätverksprestanda

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester i en befintlig arbetsyta eller aktivera en ny arbetsyta med Övervakare av nätverksprestanda. Du kan fortsätta att använda testerna som skapats före den 1 juli 2021. För att minimera avbrott i tjänsten för dina aktuella arbetsbelastningar migrerar du dina tester från Övervakare av nätverksprestanda till den nya Anslutningsövervakare i Azure Network Watcher före den 29 februari 2024.

Du kan migrera tester från Övervakare av nätverksprestanda (NPM) till nya, förbättrade Anslutningsövervakare med ett enda klick och utan avbrott. Mer information om fördelarna finns i [Anslutningsövervakare](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Viktiga punkter att notera

Migreringen hjälper till att skapa följande resultat:

* Lokala agenter och brandväggsinställningar fungerar som de är. Inga ändringar krävs. Log Analytics-agenter som är installerade på virtuella Azure-datorer måste ersättas med [Network Watcher tillägget](../virtual-machines/extensions/network-watcher-windows.md).
* Befintliga tester mappas till Anslutningsövervakare > Test Group > Test format. Genom att **välja Redigera** kan du visa och ändra egenskaperna för den nya Anslutningsövervakare, ladda ned en mall för att göra ändringar i den och skicka mallen via Azure Resource Manager.
* Agenter skickar data till både Log Analytics-arbetsytan och måtten.
* Dataövervakning:
   * **Data i Log Analytics:** Före migreringen finns data kvar på arbetsytan där NPM har konfigurerats i tabellen NetworkMonitoring. Efter migreringen går data till tabellen NetworkMonitoring, tabellen NWConnectionMonitorTestResult och tabellen NWConnectionMonitorPathResult på samma arbetsyta. När testerna har inaktiverats i NPM lagras data endast i tabellen NWConnectionMonitorTestResult och tabellen NWConnectionMonitorPathResult.
   * **Loggbaserade aviseringar,** instrumentpaneler och integreringar: Du måste manuellt redigera frågorna baserat på den nya NWConnectionMonitorTestResult-tabellen och NWConnectionMonitorPathResult-tabellen. Om du vill skapa aviseringarna i måtten på nytt kan du [gå till Övervakning av nätverksanslutning med Anslutningsövervakare](./connection-monitor-overview.md#metrics-in-azure-monitor).
* För ExpressRoute-övervakning:
    * **Slut-till-slut-förlust** och svarstid: Anslutningsövervakare driver detta, och det blir enklare än NPM eftersom användarna inte behöver konfigurera vilka kretsar och peerings som ska övervakas. Kretsar i sökvägen identifieras automatiskt , data är tillgängliga i mått (snabbare än LA, där NPM lagrade resultaten). Topologin fungerar också som den är.
    * **Bandbreddsmått:** Med lanseringen av bandbreddsrelaterade mått var NPM:s log analytics-baserade metod inte effektiv vid bandbreddsövervakning för ExpressRoute-kunder. Den här funktionen är nu inte tillgänglig i Anslutningsövervakare.
    
## <a name="prerequisites"></a>Förutsättningar

* Se till Network Watcher är aktiverat i din prenumeration och regionen för Log Analytics-arbetsytan. Om du inte gör det visas ett felmeddelande om att "Innan du försöker migrera aktiverar du Network Watcher-tillägget i urvalsprenumerationen och platsen där LA-arbetsytan har valts".
* Om en virtuell Azure-dator som tillhör en annan region/prenumeration än Log Analytics-arbetsytan används som slutpunkt kontrollerar du att Network Watcher är aktiverat för prenumerationen och regionen.   
* Virtuella Azure-datorer med Log Analytics-agenter installerade måste vara aktiverade Network Watcher tillägget.

## <a name="migrate-the-tests"></a>Migrera testerna

Om du vill migrera testerna Övervakare av nätverksprestanda till Anslutningsövervakare gör du följande:

1. I Network Watcher väljer **du Anslutningsövervakare** och sedan fliken Migrera tester **från NPM.** 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrera tester från Övervakare av nätverksprestanda till Anslutningsövervakare" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. I listrutan väljer du din prenumeration och arbetsyta och sedan den NPM-funktion som du vill migrera. 
1. Välj **Importera** för att migrera testerna.
* Om NPM inte är aktiverat på arbetsytan visas ett felmeddelande om att "Ingen giltig NPM-konfiguration hittades". 
* Om det inte finns några tester i funktionen som du valde i steg 2 visas ett felmeddelande om att arbetsytan som valts inte <feature> har någon konfiguration.
* Om det inte finns några giltiga tester visas ett felmeddelande om att arbetsytan som valts inte har giltiga tester
* Dina tester kan innehålla agenter som inte längre är aktiva, men som kan ha varit aktiva tidigare. Du ser ett felmeddelande om att "Få tester innehåller agenter som inte längre är aktiva. Lista över inaktiva agenter – {0} . Dessa agenter kan köras tidigare men stängs av/körs inte längre. Aktivera agenter och migrera till Anslutningsövervakare. Klicka på Fortsätt för att migrera testerna som inte innehåller agenter som inte är aktiva."

När migreringen har startat sker följande ändringar: 
* En ny anslutningsövervakarresurs skapas.
   * En anslutningsövervakare per region och prenumeration skapas. För tester med lokala agenter formateras det nya anslutningsövervakarnamnet som `<workspaceName>_"workspace_region_name"` . För tester med Azure-agenter formateras det nya anslutningsövervakarnamnet som `<workspaceName>_<Azure_region_name>` .
   * Övervakningsdata lagras nu på samma Log Analytics-arbetsyta där NPM är aktiverat, i nya tabeller med namnet NWConnectionMonitorTestResult-tabellen och NWConnectionMonitorPathResult-tabellen. 
   * Testnamnet vidarebefordras som testgruppens namn. Testbeskrivningen migreras inte.
   * Käll- och målslutpunkter skapas och används i den nya testgruppen. För lokala agenter formateras slutpunkterna som `<workspaceName>_<FQDN of on-premises machine>` . Agentbeskrivningen migreras inte.
   * Målporten och avsökningsintervallet flyttas till en testkonfiguration med namnet `TC_<protocol>_<port>` och `TC_<protocol>_<port>_AppThresholds` . Protokollet anges baserat på portvärdena. För ICMP namnges testkonfigurationerna som `TC_<protocol>` och `TC_<protocol>_AppThresholds` . Tröskelvärden för lyckade och andra valfria egenskaper om uppsättningen migreras lämnas annars tomma.
   * Om migreringstesterna innehåller agenter som inte körs måste du aktivera agenterna och migrera igen.
* NPM är inte inaktiverat, så de migrerade testerna kan fortsätta att skicka data till tabellen NetworkMonitoring, NWConnectionMonitorTestResult-tabellen och NWConnectionMonitorPathResult-tabellen. Den här metoden säkerställer att befintliga loggbaserade aviseringar och integreringar inte påverkas.
* Den nyligen skapade anslutningsövervakaren visas i Anslutningsövervakare.

Efter migreringen ska du se till att:
* Inaktivera testerna i NPM manuellt. Tills du gör det fortsätter du att debiteras för dem. 
* När du inaktiverar NPM skapar du aviseringarna på nytt i tabellerna NWConnectionMonitorTestResult och NWConnectionMonitorPathResult eller använder mått. 
* Migrera eventuella externa integreringar till tabellerna NWConnectionMonitorTestResult och NWConnectionMonitorPathResult. Exempel på externa integreringar är instrumentpaneler i Power BI grafana och integreringar med SIEM-system (Säkerhetsinformation och händelsehantering).


## <a name="next-steps"></a>Nästa steg

Mer information om Anslutningsövervakare finns i:
* [Migrera från Anslutningsövervakare (klassisk) till Anslutningsövervakare](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Skapa Anslutningsövervakare med hjälp av Azure Portal](./connection-monitor-create-using-portal.md)
