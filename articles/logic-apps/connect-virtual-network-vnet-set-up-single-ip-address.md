---
title: Konfigurera en offentlig utgående IP-adress för ISE:er
description: Lär dig hur du ställer in en enda offentlig utgående IP-adress för integrationstjänstmiljöer (ISE) i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874197"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Konfigurera en enskild IP-adress för en eller flera integreringstjänstmiljöer i Azure Logic Apps

När du arbetar med Azure Logic Apps kan du konfigurera en [ *Integration Service Environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) för att vara värd för logikappar som behöver åtkomst till resurser i ett [virtuellt Azure-nätverk.](../virtual-network/virtual-networks-overview.md) Om du har flera ISE-instanser som behöver åtkomst till andra slutpunkter som [](../virtual-network/virtual-networks-overview.md#filter-network-traffic) har IP-begränsningar distribuerar du en [Azure Firewall](../firewall/overview.md) eller en virtuell nätverksinstallation till ditt virtuella nätverk och dirigerar utgående trafik genom brandväggen eller den virtuella nätverksinstallationen. Du kan sedan få alla ISE-instanser i ditt virtuella nätverk att använda en enda, offentlig, statisk och förutsägbar IP-adress för att kommunicera med de målsystem som du vill använda. På så sätt behöver du inte konfigurera ytterligare brandväggsöppningar i målsystemen för varje ISE.

Det här avsnittet visar hur du dirigerar utgående trafik via en Azure Firewall, men du kan tillämpa liknande begrepp för en virtuell nätverksinstallation, till exempel en brandvägg från tredje part från Azure Marketplace. Det här avsnittet fokuserar på konfiguration för flera ISE-instanser, men du kan också använda den här metoden för en enda ISE när ditt scenario kräver att du begränsar antalet IP-adresser som behöver åtkomst. Överväg om de ytterligare kostnaderna för brandväggen eller den virtuella nätverksinstallationen är meningsfulla för ditt scenario. Läs mer om [Azure Firewall prissättning.](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-brandvägg som körs i samma virtuella nätverk som din ISE. Om du inte har en brandvägg lägger du först [till ett undernät](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) med namnet `AzureFirewallSubnet` i det virtuella nätverket. Du kan sedan [skapa och distribuera en brandvägg](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) i ditt virtuella nätverk.

* En [Azure-vägtabell](../virtual-network/manage-route-table.md). Om du inte har någon skapar du först [en vägtabell](../virtual-network/manage-route-table.md#create-a-route-table). Mer information om routning finns i [Trafikdirigering i virtuella nätverk.](../virtual-network/virtual-networks-udr-overview.md)

## <a name="set-up-route-table"></a>Konfigurera vägtabell

1. I [Azure Portal](https://portal.azure.com)väljer du vägtabellen, till exempel:

   ![Välj vägtabell med regel för att dirigera utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Om [du vill lägga till en ny](../virtual-network/manage-route-table.md#create-a-route)väg går du till menyn för vägtabellen och väljer Vägar **Lägg**  >  **till**.

   ![Lägg till väg för att dirigera utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. I fönstret **Lägg till** väg ställer [du](../virtual-network/manage-route-table.md#create-a-route) in den nya vägen med en regel som anger att all utgående trafik till målsystemet följer det här beteendet:

   * Använder [**den virtuella installationen**](../virtual-network/virtual-networks-udr-overview.md#user-defined) som nästa hopptyp.

   * Går till den privata IP-adressen för brandväggsinstansen som nästa hopp-adress.

     Om du vill hitta den här IP-adressen går du till **brandväggsmenyn och** väljer Översikt och hittar adressen under **Privat IP-adress,** till exempel:

     ![Hitta brandväggens privata IP-adress](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Här är ett exempel som visar hur en sådan regel kan se ut:

   ![Konfigurera regel för att dirigera utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Vägnamn** | <*unique-route-name*> | Ett unikt namn för vägen i vägtabellen |
   | **Adressprefix** | <*måladress*> | Adressprefixet för målsystemet där du vill att utgående trafik ska gå. Se till att du använder [CIDR-notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) för den här adressen. I det här exemplet är det här adressprefixet för en SFTP-server, som beskrivs i [avsnittet Konfigurera nätverksregel](#set-up-network-rule). |
   | **Nästa hopptyp** | **Virtuell installation** | Hopptypen [som](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) används av utgående trafik |
   | **Nexthop-adress** | <*firewall-private-IP-address*> | Den privata IP-adressen för brandväggen |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Konfigurera nätverksregel

1. I Azure Portal du och väljer brandväggen. På brandväggsmenyn går du **till Inställningar** och väljer **Regler.** I regelfönstret väljer du **Nätverksregelsamling Lägg**  >  **till nätverksregelsamling**.

   ![Lägga till nätverksregelsamling i brandväggen](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. I samlingen lägger du till en regel som tillåter trafik till målsystemet.

   Anta till exempel att du har en logikapp som körs i en ISE och behöver kommunicera med en SFTP-server. Du skapar en nätverksregelsamling med namnet `LogicApp_ISE_SFTP_Outbound` , som innehåller en nätverksregel med namnet `ISE_SFTP_Outbound` . Den här regeln tillåter trafik från IP-adressen för alla undernät där din ISE körs i ditt virtuella nätverk till SFTP-målservern med hjälp av brandväggens privata IP-adress.

   ![Konfigurera nätverksregel för brandvägg](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Egenskaper för nätverksregelsamling**

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*network-rule-collection-name*> | Namnet på din nätverksregelsamling |
   | **Priority** | <*prioritetsnivå*> | Prioritetsordning som ska användas för att köra regelsamlingen. Mer information finns i Vad [är några Azure Firewall begrepp?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) |
   | **Åtgärd** | **Tillåt** | Den åtgärdstyp som ska utföras för den här regeln |
   |||

   **Egenskaper för nätverksregel**

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*network-rule-name*> | Namnet på nätverksregeln |
   | **Protokoll** | <*anslutningsprotokoll*> | De anslutningsprotokoll som ska användas. Om du till exempel använder NSG-regler väljer du både **TCP** och **UDP**, inte bara **TCP**. |
   | **Källadresser** | <*ISE-undernätsadresser*> | Undernätets IP-adresser där ISE körs och varifrån trafik från logikappen kommer |
   | **Måladresser** | <*mål-IP-adress*> | IP-adressen för målsystemet där du vill att utgående trafik ska gå. I det här exemplet är den här IP-adressen för SFTP-servern. |
   | **Målportar** | <*målportar*> | Alla portar som målsystemet använder för inkommande kommunikation |
   |||

   Mer information om nätverksregler finns i följande artiklar:

   * [Konfigurera en nätverksregel](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Regelbearbetningslogik för Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Vanliga frågor och svar om Azure Firewall](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az network firewall network-rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
