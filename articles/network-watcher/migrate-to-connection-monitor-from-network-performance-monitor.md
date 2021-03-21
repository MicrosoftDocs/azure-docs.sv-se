---
title: Migrera till anslutnings övervakaren från Övervakare av nätverksprestanda
titleSuffix: Azure Network Watcher
description: Lär dig hur du migrerar till anslutnings övervakaren från Övervakare av nätverksprestanda.
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
ms.openlocfilehash: 998b0cb04d465f675423e2472a7ca8c6441b1fed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010413"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrera till anslutnings övervakaren från Övervakare av nätverksprestanda

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta med Övervakare av nätverksprestanda. Du kan fortsätta att använda testerna som skapats före den 1 juli 2021. Migrera dina tester från Övervakare av nätverksprestanda till den nya anslutnings övervakaren i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Du kan migrera tester från Övervakare av nätverksprestanda (NPM) till ny, förbättrad anslutnings övervakare med ett enda klick och med noll stillestånds tid. Läs mer om fördelarna i [anslutnings övervakaren](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Viktiga punkter att Observera

Migreringen hjälper till att producera följande resultat:

* Lokala agenter och brand Väggs inställningar fungerar som de är. Inga ändringar krävs. Log Analytics agenter som är installerade på virtuella Azure-datorer måste ersättas med Network Watcher- [tillägget](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).
* Befintliga tester mappas till anslutnings övervakaren > test grupp > test format. Genom att välja **Redigera** kan du Visa och ändra egenskaperna för den nya anslutnings övervakaren, hämta en mall för att göra ändringar i den och skicka mallen via Azure Resource Manager.
* Agenter skickar data till både arbets ytan Log Analytics och måtten.
* Data övervakning:
   * **Data i Log Analytics**: innan migreringen finns data kvar i arbets ytan där NPM har kon figurer ATS i NetworkMonitoring-tabellen. Efter migreringen går data till NetworkMonitoring-tabellen, NWConnectionMonitorTestResult-tabellen och NWConnectionMonitorPathResult-tabellen i samma arbets yta. När testerna har inaktiverats i NPM lagras data endast i tabellen NWConnectionMonitorTestResult och NWConnectionMonitorPathResult.
   * **Loggbaserade aviseringar, instrument paneler och integreringar**: du måste redigera frågorna manuellt baserat på den nya tabellen NWConnectionMonitorTestResult och tabellen NWConnectionMonitorPathResult. Information om hur du återskapar aviseringarna i mått finns i [övervakning av nätverks anslutning med anslutnings övervakaren](./connection-monitor-overview.md#metrics-in-azure-monitor).
* För ExpressRoute-övervakning:
    * **Slut punkt till slut punkt och svars tid**: anslutnings övervakaren kommer att sätta detta och det blir enklare än NPM eftersom användarna inte behöver konfigurera vilka kretsar och peering som ska övervakas. Kretsar i sökvägen identifieras automatiskt. data kommer att vara tillgängliga i mått (snabbare än LA där NPM lagrade resultaten). Topologin fungerar som den också.
    * **Bandbredds mått**: vid start av bandbredds mått är NPM för den Log Analytics-baserade metoden inte effektiv i bandbredds övervakning för ExpressRoute-kunder. Den här funktionen är nu inte tillgänglig i anslutnings övervakaren.
    
## <a name="prerequisites"></a>Förutsättningar

* Se till att Network Watcher är aktive rad i din prenumeration och regionen för arbets ytan Log Analytics. 
* Om den virtuella Azure-datorn tillhör en annan region/prenumeration än den Log Analytics arbets ytan används som en slut punkt, kontrollerar du att Network Watcher har Aktiver ATS för den prenumerationen och regionen.   
* Virtuella Azure-datorer med Log Analytics installerade agenter måste vara aktiverade med Network Watcher-tillägget.

## <a name="migrate-the-tests"></a>Migrera testerna

Om du vill migrera testerna från Övervakare av nätverksprestanda till anslutnings övervakaren gör du följande:

1. I Network Watcher väljer du **anslutnings övervakare** och väljer sedan fliken **MIGRERA tester från NPM** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrera tester från Övervakare av nätverksprestanda till anslutnings övervakaren" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. I list rutorna väljer du din prenumeration och arbets yta och väljer sedan den NPM-funktion som du vill migrera. 
1. Klicka på **Importera** för att migrera testerna.

När migreringen har påbörjats sker följande ändringar: 
* En ny anslutnings övervaknings resurs skapas.
   * En anslutnings övervakare per region och prenumeration skapas. För tester med lokala agenter formateras namnet för den nya anslutnings övervakaren som `<workspaceName>_"workspace_region_name"` . För tester med Azure-agenter formateras namnet för den nya anslutnings övervakaren som `<workspaceName>_<Azure_region_name>` .
   * Övervaknings data lagras nu i samma Log Analytics arbets yta där NPM är aktiverat, i nya tabeller som heter NWConnectionMonitorTestResult Table och NWConnectionMonitorPathResult Table. 
   * Test namnet överförs som test gruppens namn. Test beskrivningen har inte migrerats.
   * Käll-och mål slut punkter skapas och används i den nya test gruppen. För lokala agenter formateras slut punkterna som `<workspaceName>_<FQDN of on-premises machine>` . Agent beskrivningen migreras inte.
   * Mål porten och söknings intervallet flyttas till en test konfiguration som kallas `TC_<protocol>_<port>` och `TC_<protocol>_<port>_AppThresholds` . Protokollet anges baserat på port värden. För ICMP heter test konfigurationerna som `TC_<protocol>` och `TC_<protocol>_AppThresholds` . Tröskelvärden för lyckade och andra valfria egenskaper om inställningen migreras lämnas annars tomt.
   * Om de migrerande testerna innehåller agenter som inte körs måste du aktivera agenterna och migrera igen.
* NPM är inte inaktiverat, så de migrerade testerna kan fortsätta att skicka data till NetworkMonitoring-tabellen, NWConnectionMonitorTestResult Table och NWConnectionMonitorPathResult-tabellen. Den här metoden säkerställer att befintliga loggbaserade aviseringar och integreringar inte påverkas.
* Den nyligen skapade anslutnings övervakaren visas i anslutnings övervakaren.

Efter migreringen, se till att:
* Inaktivera testerna manuellt i NPM. Innan du gör det kan du fortsätta att betala för dem. 
* När du inaktiverar NPM återskapar du aviseringarna på NWConnectionMonitorTestResult-och NWConnectionMonitorPathResult-tabellerna eller använder mått. 
* Migrera alla externa integreringar till tabellerna NWConnectionMonitorTestResult och NWConnectionMonitorPathResult. Exempel på externa integreringar är instrument paneler i Power BI-och Grafana, och integreringar med SIEM-system (Security information and Event Management).


## <a name="next-steps"></a>Nästa steg

Mer information om anslutnings övervakaren finns i:
* [Migrera från anslutnings övervakaren (klassisk) till anslutnings övervakaren](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Skapa anslutnings övervakare med hjälp av Azure Portal](./connection-monitor-create-using-portal.md)
