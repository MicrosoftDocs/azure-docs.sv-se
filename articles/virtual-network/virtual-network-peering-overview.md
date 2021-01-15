---
title: Peering för virtuella nätverk i Azure
titlesuffix: Azure Virtual Network
description: Lär dig mer om peering i virtuella nätverk i Azure, inklusive hur du kan ansluta nätverk i Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: feea2d54edd8a93e6e0effbef03389ef895d5ffb
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216758"
---
# <a name="virtual-network-peering"></a>Peering för virtuella nätverk

Med peering för virtuella nätverk kan du sömlöst ansluta två eller fler [virtuella nätverk](virtual-networks-overview.md) i Azure. De virtuella nätverken visas som ett för anslutnings syfte. Trafiken mellan virtuella datorer i peer-kopplat virtuella nätverk använder Microsofts stamnät infrastruktur. Som trafik mellan virtuella datorer i samma nätverk dirigeras trafiken bara via Microsofts *privata* nätverk.

Azure har stöd för följande typer av peering:

* Virtuell nätverks-peering: Anslut virtuella nätverk inom samma Azure-region.
* Global peering för virtuellt nätverk: anslutning av virtuella nätverk i Azure-regioner.

Fördelar med att använda VNET-peering (avsett om den är lokal eller global):

* En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
* Möjligheten för resurser i ett virtuellt nätverk att kommunicera med resurser i ett annat virtuellt nätverk.
* Möjlighet att överföra data mellan virtuella nätverk över Azure-prenumerationer, Azure Active Directory klienter, distributions modeller och Azure-regioner.
* Möjligheten att koppla virtuella nätverk som skapats via Azure Resource Manager.
* Möjlighet att peer-koppla ett virtuellt nätverk som skapats via Resource Manager till ett som skapats via den klassiska distributions modellen. Läs mer i avsnittet [om Azures distributionsmodeller](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Inga driftstopp för resurser i de virtuella nätverken när du skapar peer-kopplingen eller när peer-kopplingen har skapats.

Nätverkstrafiken mellan peer-kopplade virtuella nätverk är privat. Trafiken mellan de virtuella nätverken finns i Microsoft-stamnätverket. Vid kommunikation mellan virtuella nätverk krävs inget offentligt Internet, inga gatewayer eller ingen kryptering.

## <a name="connectivity"></a>Anslutningar

För peer-kopplade virtuella nätverk kan resurser i antingen det virtuella nätverket ansluta direkt till resurser i det peer-kopplade virtuella nätverket.

Nätverksfördröjningen mellan virtuella datorer i peer-kopplade virtuella nätverk i samma region är densamma som svarstiden inom ett enda virtuellt nätverk. Nätverkets genomflöde baseras på den bandbredd som tillåts för den virtuella datorn i proportion till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden inom peerkopplingen.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras direkt genom Microsoft-stamnätsinfrastrukturen, inte via en gateway eller det offentliga Internet.

Du kan använda nätverks säkerhets grupper i virtuella nätverk för att blockera åtkomst till andra virtuella nätverk eller undernät.
När du konfigurerar peering för virtuella nätverk kan du antingen öppna eller stänga reglerna för nätverks säkerhets gruppen mellan de virtuella nätverken. Om du öppnar fullständig anslutning mellan peer-anslutna virtuella nätverk kan du använda nätverks säkerhets grupper för att blockera eller neka åtkomst. Fullständig anslutning är standard alternativet. Mer information om nätverks säkerhets grupper finns i [säkerhets grupper](./network-security-groups-overview.md).

## <a name="service-chaining"></a>Tjänstlänkning

Med tjänst länkning kan du dirigera trafik från ett virtuellt nätverk till en virtuell installation eller gateway i ett peer-kopplat nätverk genom användardefinierade vägar.

Konfigurera användardefinierade vägar som pekar på virtuella datorer i peer-kopplat virtuella nätverk som *nästa hopp* -IP-adress för att aktivera tjänst länkning. Användardefinierade vägar kan också peka på virtuella nätverksgateway för att aktivera Service kedjor.

Du kan distribuera nav-och-eker-nätverk, där hubbens virtuella nätverk är värd för infrastruktur komponenter, till exempel en virtuell nätverks installation eller VPN-gateway. Alla virtuella ekernätverk kan peer-kopplas till det virtuella navnätverket. Trafik flödar via virtuella nätverks apparater eller VPN-gatewayer i det virtuella hubb nätverket.

Peering för virtuellt nätverk gör att nästa hopp i den användardefinierade routningen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket eller en VPN-gateway. Du kan inte dirigera mellan virtuella nätverk med en användardefinierad väg som anger en Azure ExpressRoute-gateway som nästa hopp typ. Mer information om användardefinierade vägar finns i [översikten över användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Information om hur du skapar en nätverkstopologi med nav och ekrar finns i nätverkstopologi [i hubben i Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar

Varje virtuellt nätverk, inklusive ett peer-kopplat virtuellt nätverk, kan ha sin egen Gateway. Ett virtuellt nätverk kan använda sin Gateway för att ansluta till ett lokalt nätverk. Du kan också konfigurera [virtuella nätverk till virtuella nätverks anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) med hjälp av gatewayer, även för peer-baserade virtuella nätverk.

När du konfigurerar båda alternativen för anslutning mellan virtuella nätverk flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen. Trafiken använder Azure-stamnätet.

Du kan också konfigurera gatewayen i det peer-baserade virtuella nätverket som en överförings punkt till ett lokalt nätverk. I detta fall kan det virtuella nätverket som använder en fjärr-Gateway inte ha sin egen Gateway. Ett virtuellt nätverk har bara en gateway. Gatewayen är antingen en lokal gateway eller en fjärran sluten gateway i det peer-virtuella nätverket, som du ser i följande diagram:

![Överföring vid VNET-peering](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Både virtuell nätverks-peering och global virtuell nätverks-peering stöder Gateway-överföring.

Gateway-överföring mellan virtuella nätverk som skapats via olika distributions modeller stöds. Gatewayen måste finnas i det virtuella nätverket i Resource Manager-modellen. Mer information om hur du använder en gateway för överföring finns i [Configure a VPN gateway for transit in a virtual network peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Konfigurera en VPN-gateway för överföring i peer-kopplade virtuella nätverk).

När du använder virtuella peer-nätverk som delar en enda Azure ExpressRoute-anslutning går trafiken mellan dem genom peering-relationen. Trafiken använder Azure stamnät nätverket. Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Annars kan du använda en delad gateway och konfigurera överföring för lokal anslutning.

## <a name="troubleshoot"></a>Felsöka

För att bekräfta att virtuella nätverk är peer-kopplade kan du kontrol lera effektiva vägar. Kontrol lera vägar för ett nätverks gränssnitt i ett undernät i ett virtuellt nätverk. Om peer-koppling för virtuellt nätverk finns har alla undernät i det virtuella nätverket vägar med nästa hopp-typ *VNet-peering* för varje adressutrymme i varje peer-kopplat virtuellt nätverk. Mer information finns i [diagnostisera ett problem med en virtuell dator routning](diagnose-network-routing-problem.md).

Du kan också felsöka anslutningen till en virtuell dator i ett peer-kopplat virtuellt nätverk med hjälp av Azure Network Watcher. Med anslutnings kontrollen kan du se hur trafiken dirigeras från en virtuell käll dators nätverks gränssnitt till en virtuell mål dators nätverks gränssnitt. Mer information finns i [Felsöka anslutningar med Azure Network Watcher med hjälp av Azure Portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Du kan också prova [fel sökning av problem med virtuella nätverks peering](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Begränsningar för peer-kopplat virtuella nätverk<a name="requirements-and-constraints"></a>

Följande begränsningar gäller endast när virtuella nätverk peerkopplas globalt:

* Resurser i ett virtuellt nätverk kan inte kommunicera med klient delens IP-adress för en grundläggande intern Load Balancer (ILB) i ett globalt peer-kopplat virtuellt nätverk.
* Vissa tjänster som använder en grundläggande belastningsutjämnare fungerar inte över global peering för virtuella nätverk. Mer information finns i [Vad är begränsningar relaterade till globala VNet-peering och belastningsutjämnare?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Mer information finns i [krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints). Mer information om antalet peer-kopplingar som stöds finns i [nätverks begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Behörigheter

Information om vilka behörigheter som krävs för att skapa en virtuell nätverks-peering finns i [behörigheter](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Prissättning

Det finns en nominell avgift för ingångs-och utgående trafik som använder en peering-anslutning för virtuellt nätverk. Mer information finns i [Virtual Network prissättning](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway-överföring är en peering-egenskap som gör att ett virtuellt nätverk kan använda en VPN/ExpressRoute-gateway i ett peer-kopplat virtuellt nätverk. Gateway-överföring fungerar både mellan lokala nätverk och nätverks anslutningar. Trafik till gatewayen (ingångs-eller utgångs händelser) i det peer-baserade virtuella nätverket medför kostnader för peering av virtuella nätverk i eker VNet (eller icke-gatewayens VNet). Mer information finns i [VPN gateway priser](https://azure.microsoft.com/pricing/details/vpn-gateway/) för VPN gateway-avgifter och ExpressRoute Gateway-priser för ExpressRoute Gateway-avgifter.

>[!NOTE]
> En tidigare version av det här dokumentet angav att avgifter för virtuellt nätverk inte skulle gälla för det virtuella ekraret för ekrar (eller icke-gatewayens VNet) med Gateway-överföring. Det visar nu korrekt prissättning på sidan med priser.

## <a name="next-steps"></a>Nästa steg

* Du kan skapa en peering mellan två virtuella nätverk. Nätverken kan tillhöra samma prenumeration, olika distributions modeller i samma prenumeration eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

    |Azure-distributionsmodell             | Prenumeration  |
    |---------                          |---------|
    |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
    |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* Information om hur du skapar en nätverkstopologi med nav och ekrar finns i nätverkstopologi [i hubben i Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Information om alla peering-inställningar för virtuella nätverk finns i [skapa, ändra eller ta bort en virtuell nätverks-peering](virtual-network-manage-peering.md).
* Svar på vanliga peering-frågor för virtuella nätverk och globala peering-frågor för virtuella nätverk finns i [VNet-peering](virtual-networks-faq.md#vnet-peering).