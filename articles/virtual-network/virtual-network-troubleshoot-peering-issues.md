---
title: Felsöka problem med peering i virtuella nätverk
description: Steg för att lösa de flesta peering-problem med virtuella nätverk.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 608f3d72aecb0c6d53325c0d082b1779822e9c80
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200536"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Felsöka problem med peering i virtuella nätverk

Den här fel söknings guiden innehåller steg som hjälper dig att lösa de flesta problem med [peering i virtuella nätverk](virtual-network-peering-overview.md) .

![Diagram över peering för virtuellt nätverk](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Konfigurera virtuell nätverks-peering mellan två virtuella nätverk

Är de virtuella nätverken i samma prenumeration eller i olika prenumerationer?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>De virtuella nätverken finns i samma prenumeration

Använd metoderna i följande artiklar för att konfigurera peering för virtuella nätverk för de virtuella nätverk som finns i samma prenumeration:

* Om de virtuella nätverken finns i *samma region*, se [skapa en peering](./virtual-network-manage-peering.md#create-a-peering).
* Om de virtuella nätverken finns i *olika regioner*, se [peering för virtuella nätverk](./virtual-network-peering-overview.md). 

> [!Note]
> Anslutningen fungerar inte över global peering för virtuella nätverk för följande resurser: 
>
> * Virtual Machines (VM) bakom Basic Internal Load Balancer (ILB) SKU
> * Redis cache (använder Basic ILB SKU)
> * Application Gateway v1 (använder Basic ILB SKU)
> * Skalnings uppsättningar för virtuella datorer (använder Basic ILB SKU)
> * Azure Service Fabric-kluster (använder Basic ILB SKU)
> * SQL Server Always On (använder Basic ILB SKU)
> * Azure App Service Environment för PowerApps (använder Basic ILB SKU)
> * Azure API Management (använder Basic ILB SKU)
> * Azure Active Directory Domain Services (Azure AD DS) (använder Basic ILB SKU)

Mer information finns i [krav och begränsningar](./virtual-network-peering-overview.md#requirements-and-constraints) för global peering.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>De virtuella nätverken finns i olika prenumerationer eller Active Directory klienter

Om du vill konfigurera virtuell nätverks-peering för virtuella nätverk i olika prenumerationer eller Active Directory klienter, se [skapa peering i olika prenumerationer för Azure CLI](./create-peering-different-subscriptions.md#cli).

> [!Note]
> Om du vill konfigurera nätverks-peering måste du ha behörighet som **nätverks deltagare** i båda prenumerationerna. Mer information finns i [peering-behörigheter](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Konfigurera peering för virtuellt nätverk med nav-eker-topologi som använder lokala resurser

![Diagram över virtuell nätverks-peering med lokal eker](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>För en plats-till-plats-anslutning eller en ExpressRoute-anslutning

Följ stegen i: [Konfigurera VPN gateway-överföring för virtuell nätverks-peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>För punkt-till-plats-anslutningar

1. Följ stegen i: [Konfigurera VPN gateway-överföring för virtuell nätverks-peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. När peering av virtuella nätverk har upprättats eller ändrats, laddar du ned och installerar om punkt-till-plats-paketet så att punkt-till-plats-klienterna hämtar de uppdaterade vägarna till det virtuella nätverket eker.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Konfigurera peering för virtuellt nätverk med nav-eker-topologi för virtuella nätverk

![Diagram över virtuell nätverks-peering med en virtuell nätverks-eker](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>De virtuella nätverken finns i samma region


1. I det virtuella hubb nätverket konfigurerar du en virtuell nätverks installation (NVA).
1. I de eker-virtuella nätverken har användardefinierade vägar med nästa hopp typ "virtuell nätverks installation" tillämpats.

Mer information finns i [tjänst länkning](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Om du behöver hjälp med att konfigurera en NVA [kontaktar du NVA-leverantören](https://mskb.pkisolutions.com/kb/2984655).

Information om hur du felsöker NVA enhets konfiguration och routning finns i [problem med virtuella nätverks installationer i Azure](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>De virtuella nätverken finns i olika regioner

Överföring via global peering av virtuella nätverk stöds nu. Anslutningen fungerar inte över global peering för virtuella nätverk för följande resurser:

* Virtuella datorer bakom Basic ILB SKU
* Redis cache (använder Basic ILB SKU)
* Application Gateway (använder Basic ILB SKU)
* Skalnings uppsättningar (använder Basic ILB SKU)
* Service Fabric-kluster (använder Basic ILB SKU)
* SQL Server Always On (använder Basic ILB SKU)
* App Service-miljön (använder Basic ILB SKU)
* API Management (använder Basic ILB SKU)
* Azure AD DS (använder Basic ILB SKU)

Mer information om global peering krav och begränsningar finns i det [virtuella nätverkets peering](./virtual-network-peering-overview.md#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Felsöka ett anslutnings problem mellan två peer-anslutna virtuella nätverk

Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](virtual-network-manage-peering.md#permissions)som krävs. Välj det virtuella nätverket, Välj **peering** och kontrol lera sedan fältet **status** . Vad är statusen?

### <a name="the-peering-status-is-connected"></a>Peering-statusen är "ansluten"

Så här felsöker du problemet:

1. Kontrol lera flödena för nätverks trafik:

   Använd [anslutnings fel sökning](../network-watcher/network-watcher-connectivity-overview.md) och [IP-flöde verifiera](../network-watcher/network-watcher-ip-flow-verify-overview.md) från den virtuella käll datorn till den virtuella mål datorn för att avgöra om det finns en NSG eller UDR som orsakar störningar i trafikflöden.

   Om du använder en brand vägg eller en NVA: 
   1. Dokumentera UDR-parametrarna så att du kan återställa dem när det här steget har slutförts.
   2. Ta bort UDR från det virtuella käll-eller NIC-nätverket som pekar på NVA som nästa hopp. Verifiera anslutningen från den virtuella käll datorn direkt till målet som kringgår NVA. Om det här steget inte fungerar går du till [fel sökaren för NVA](./virtual-network-troubleshoot-nva.md).

2. Ta en nätverks spårning: 
   1. Starta en nätverks spårning på den virtuella mål datorn. För Windows kan du använda **netsh**. För Linux använder du **TCPDump**.
   2. Kör **TcpPing** eller **PsPing** från källan till målets IP-adress.

      Detta är ett exempel på ett **TcpPing** -kommando: `tcping64.exe -t <destination VM address> 3389`

   3. Stoppa nätverks spårningen på målet när **TcpPing** är klar.
   4. Om paketen kommer från källan finns det inga nätverks problem. Undersök både VM-brandväggen och programmet som lyssnar på den porten för att hitta konfigurations problemet.

   > [!Note]
   > Du kan inte ansluta till följande resurs typer över global peering för virtuella nätverk (virtuella nätverk i olika regioner):
   >
   > * Virtuella datorer bakom Basic ILB SKU
   > * Redis cache (använder Basic ILB SKU)
   > * Application Gateway (använder Basic ILB SKU)
   > * Skalnings uppsättningar (använder Basic ILB SKU)
   > * Service Fabric-kluster (använder Basic ILB SKU)
   > * SQL Server Always On (använder Basic ILB SKU)
   > * App Service-miljön (använder Basic ILB SKU)
   > * API Management (använder Basic ILB SKU)
   > * Azure AD DS (använder Basic ILB SKU)

Mer information finns i [krav och begränsningar](./virtual-network-peering-overview.md#requirements-and-constraints) för global peering.

### <a name="the-peering-status-is-disconnected"></a>Peering-statusen är "frånkopplad"

Lös problemet genom att ta bort peering från båda de virtuella nätverken och återskapa dem sedan.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Felsöka ett anslutnings problem mellan ett nav eker-virtuellt nätverk och en lokal resurs

Använder nätverket en NVA-eller VPN-gateway från tredje part?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Mitt nätverk använder en NVA-eller VPN-gateway från tredje part

Information om hur du felsöker anslutnings problem som påverkar en NVA eller VPN-gateway från tredje part finns i följande artiklar:

* [NVA-felsökare](./virtual-network-troubleshoot-nva.md)
* [Tjänstlänkning](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Mitt nätverk använder inte någon tredjeparts-NVA eller VPN-gateway

Har hubbens virtuella nätverk och det virtuella eker-nätverket en VPN-gateway?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Både det virtuella hubb nätverket och det virtuella eker-nätverket har en VPN-gateway

Det finns inte stöd för att använda en fjärr-Gateway.

Om det virtuella nätverket för ekrar redan har en VPN-gateway, stöds inte alternativet **Använd fjärr-Gateway** på det virtuella nätverket i ekrar. Detta beror på en begränsning för en virtuell nätverks-peering.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Både det virtuella hubb nätverket och det virtuella nätverket i eker har ingen VPN-gateway

För plats-till-plats-eller Azure ExpressRoute-anslutningar kontrollerar du följande primära orsaker till anslutnings problem till det virtuella fjärrnätverket från den lokala servern:

* På det virtuella nätverk som har en gateway kontrollerar du att kryss rutan **Tillåt vidarebefordrad trafik** är markerad.
* På det virtuella nätverk som inte har en gateway kontrollerar du att kryss rutan **Använd fjärr-Gateway** är markerad.
* Be nätverks administratören kontrol lera dina lokala enheter för att kontrol lera att alla har det fjärranslutna virtuella nätverkets adress utrymme tillagt.

För punkt-till-plats-anslutningar:

* På det virtuella nätverk som har en gateway kontrollerar du att kryss rutan **Tillåt vidarebefordrad trafik** är markerad.
* På det virtuella nätverk som inte har en gateway kontrollerar du att kryss rutan **Använd fjärr-Gateway** är markerad.
* Ladda ned och installera om punkt-till-plats-klient paketet. Virtuella nätverks vägar som nyligen peer-kopplas lägger inte automatiskt till vägar till punkt-till-plats-klienter.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Felsöka ett problem med nätverks anslutningen mellan ekrar och ekrar mellan ekrar och virtuella nätverk i samma region

Ett hubb nätverk måste innehålla en NVA. Konfigurera UDR i ekrar som har en NVA angiven som nästa hopp och aktivera **Tillåt vidarebefordrad trafik** i det virtuella hubb nätverket.

Mer information finns i [tjänst länkning](./virtual-network-peering-overview.md#service-chaining)och diskutera dessa krav med den [NVA-leverantör](https://mskb.pkisolutions.com/kb/2984655) som du väljer.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Felsöka ett problem med nätverks anslutningen mellan ekrar mellan ekrar och virtuella nätverk i olika regioner

Överföring via global peering av virtuella nätverk stöds nu. Anslutningen fungerar inte över global peering för virtuella nätverk för följande resurser:

* Virtuella datorer bakom Basic ILB SKU
* Redis cache (använder Basic ILB SKU)
* Application Gateway (använder Basic ILB SKU)
* Skalnings uppsättningar (använder Basic ILB SKU)
* Service Fabric-kluster (använder Basic ILB SKU)
* SQL Server Always On (använder Basic ILB SKU)
* App Service-miljön (använder Basic ILB SKU)
* API Management (använder Basic ILB SKU)
* Azure AD DS (använder Basic ILB SKU)

Mer information finns i [krav och begränsningar](./virtual-network-peering-overview.md#requirements-and-constraints) för global peering och [olika VPN-topologier](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Felsöka ett problem med nätverks anslutningen från hubben mellan en webbapp och det virtuella nätverket i eker

Så här felsöker du problemet:

1. Logga in på Azure-portalen. 
1. I webbappen väljer du **nätverk** och väljer sedan VNet- **integrering**.
1. Kontrol lera om du kan se det virtuella fjärrnätverket. Ange adress utrymmet för det virtuella fjärrnätverket manuellt (**Synkronisera nätverk** och **Lägg till vägar**).

Mer information finns i följande artiklar:

* [Integrera en app med ett virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md)
* [Om VPN-routning från punkt till plats](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Felsöka ett fel meddelande om konfiguration av virtuellt nätverk peering 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Den aktuella klienten har `<TENANT ID>` inte åtkomst behörighet till den länkade prenumerationen

Lös problemet genom att se [skapa peering – Azure CLI](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>Inte ansluten

Lös problemet genom att ta bort peering från båda de virtuella nätverken och återskapa dem sedan.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Det gick inte att peer-koppla ett Databricks virtuellt nätverk

Lös problemet genom att konfigurera det virtuella nätverkets peering under **Azure Databricks** och ange det virtuella mål nätverket med hjälp av **resurs-ID**. Mer information finns i [peer a Databricks Virtual Network to a Remote Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Det virtuella fjärrnätverket saknar Gateway

Det här problemet uppstår när du peer-koppla virtuella nätverk från olika klienter och senare vill konfigurera `Use Remote Gateways` . En begränsning av Azure Portal är att det inte går att verifiera förekomsten av en virtuell nätverksgateway i en annan innehavares virtuella nätverk.

Det finns två sätt att lösa problemet:

 * Ta bort peer kopplingarna och aktivera `Use Remote Gateways` alternativet när du skapar en ny peering.
 * Använd PowerShell eller CLI, i stället för Azure Portal, för att aktivera `Use Remote Gateways` .

## <a name="next-steps"></a>Nästa steg

* [Felsökning av anslutningsproblem mellan virtuella Azure-datorer](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
