---
title: Konfigurera en offentlig utgående IP-adress för ISEs
description: Lär dig hur du konfigurerar en enskild offentlig IP-adress för integrerings tjänst miljöer (ISEs) i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e88c4bf05d88007a6e19b568f1bc1085e24b0325
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211064"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Konfigurera en enskild IP-adress för en eller flera integrerings tjänst miljöer i Azure Logic Apps

När du arbetar med Azure Logic Apps kan du konfigurera en [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) för att vara värd för logi Kap par som behöver åtkomst till resurser i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du har flera ISE-instanser som behöver åtkomst till andra slut punkter som har IP-begränsningar distribuerar du en [Azure-brandvägg](../firewall/overview.md) eller en [virtuell nätverks](../virtual-network/virtual-networks-overview.md#filter-network-traffic) installation till det virtuella nätverket och dirigerar utgående trafik via brand väggen eller den virtuella nätverks installationen. Du kan sedan ha alla ISE-instanser i det virtuella nätverket använda en enda, offentlig, statisk och förutsägbar IP-adress för att kommunicera med de mål system som du vill använda. På så sätt behöver du inte konfigurera ytterligare brand Väggs öppningar i mål systemen för varje ISE.

Det här avsnittet visar hur du dirigerar utgående trafik via en Azure-brandvägg, men du kan använda liknande begrepp för en virtuell nätverks installation, till exempel en brand vägg från en tredje part från Azure Marketplace. Det här avsnittet fokuserar på installations programmet för flera ISE-instanser, men du kan också använda den här metoden för en enskild ISE när ditt scenario kräver att du begränsar antalet IP-adresser som behöver åtkomst. Överväg om ytterligare kostnader för brand väggen eller den virtuella nätverks enheten passar ditt scenario. Läs mer om [priser för Azure-brandvägg](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-brandvägg som körs i samma virtuella nätverk som din ISE. Om du inte har en brand vägg lägger du först [till ett undernät](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) som heter `AzureFirewallSubnet` i det virtuella nätverket. Du kan sedan [skapa och distribuera en brand vägg](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) i det virtuella nätverket.

* En Azure [route-tabell](../virtual-network/manage-route-table.md). Om du inte har en sådan måste du först [skapa en](../virtual-network/manage-route-table.md#create-a-route-table)routningstabell. Mer information om routning finns i [trafik dirigering i virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Konfigurera routningstabell

1. I [Azure Portal](https://portal.azure.com)väljer du routningstabellen, till exempel:

   ![Välj routningstabell med regel för att dirigera utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Om du vill [lägga till en ny väg](../virtual-network/manage-route-table.md#create-a-route)går du till routningstabellen-menyn och väljer **vägar**  >  **Lägg till**.

   ![Lägg till väg för direkt utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. I fönstret **Lägg till väg** [ställer du in den nya vägen](../virtual-network/manage-route-table.md#create-a-route) med en regel som anger att all utgående trafik till mål systemet följer detta beteende:

   * Använder den [**virtuella enheten**](../virtual-network/virtual-networks-udr-overview.md#user-defined) som nästa hopp typ.

   * Går till den privata IP-adressen för brand Väggs instansen som nästa hopp adress.

     Du hittar den här IP-adressen genom att välja **Översikt** på brand Väggs menyn, hitta adressen under **privat IP-adress**, till exempel:

     ![Hitta privat IP-adress för brand vägg](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Här är ett exempel som visar hur en sådan regel kan se ut:

   ![Konfigurera regel för att dirigera utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Vägnamn** | <*unikt flöde-namn*> | Ett unikt namn för vägen i routningstabellen |
   | **Adressprefix** | <*mål adress*> | Adressprefixet för ditt mål system där du vill att utgående trafik ska gå. Se till att du använder [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) för den här adressen. I det här exemplet är detta adressprefix för en SFTP-server, som beskrivs i avsnittet [Konfigurera nätverks regel](#set-up-network-rule). |
   | **Nästa hopptyp** | **Virtuell installation** | [Hopp typen](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) som används av utgående trafik |
   | **Nexthop-adress** | <*brand vägg – privat IP-adress*> | Den privata IP-adressen för din brand vägg |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Konfigurera nätverks regel

1. I Azure Portal söker du efter och väljer brand väggen. I menyn brand vägg under **Inställningar** väljer du **regler**. I fönstret regler väljer du **regel insamling**  >  **Lägg till nätverks regel samling**.

   ![Lägg till nätverks regel samling i brand väggen](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. I samlingen lägger du till en regel som tillåter trafik till mål systemet.

   Anta till exempel att du har en Logic-app som körs i en ISE och som måste kommunicera med en SFTP-server. Du skapar en nätverks regel samling som har namnet `LogicApp_ISE_SFTP_Outbound` , som innehåller en nätverks regel med namnet `ISE_SFTP_Outbound` . Den här regeln tillåter trafik från IP-adressen för ett undernät där ISE körs i det virtuella nätverket till målets SFTP-server genom att använda brand väggens privata IP-adress.

   ![Konfigurera nätverks regel för brand vägg](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Egenskaper för nätverks regel samling**

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*nätverks regel-samlings namn*> | Namnet på din nätverks regel samling |
   | **Priority** | <*prioritets nivå*> | Prioritetsordningen som används för att köra regel samlingen. Mer information finns i [Vad är några Azure Firewall-koncept](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)? |
   | **Åtgärd** | **Tillåt** | Den åtgärds typ som ska utföras för den här regeln |
   |||

   **Egenskaper för nätverks regel**

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*nätverks regel-namn*> | Nätverks regelns namn |
   | **Protokoll** | <*anslutnings protokoll*> | Anslutnings protokollen som ska användas. Om du till exempel använder NSG-regler, väljer du både **TCP** och **UDP**, inte bara **TCP**. |
   | **Käll adresser** | <*ISE-undernät-adresser*> | IP-adresserna för under nätet där ISE körs och varifrån trafik från din Logic app kommer |
   | **Mål adresser** | <*mål-IP-adress*> | IP-adressen för ditt mål system där du vill att utgående trafik ska gå. I det här exemplet är den här IP-adressen för SFTP-servern. |
   | **Mål portar** | <*mål portar*> | Alla portar som mål systemet använder för inkommande kommunikation |
   |||

   Mer information om nätverks regler finns i följande artiklar:

   * [Konfigurera en nätverksregel](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Regelbearbetningslogik för Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Vanliga frågor och svar om Azure Firewall](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: AZ Network Firewall Network-Rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
