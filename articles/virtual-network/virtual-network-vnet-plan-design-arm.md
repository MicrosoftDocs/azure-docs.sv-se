---
title: Planera virtuella Azure-nätverk | Microsoft Docs
description: Lär dig att planera virtuella datornätverk som baseras på dina krav på isolering, anslutningar och plats.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 9ed060c1f3fc47aa7050c0f9b25c757953fa22fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220345"
---
# <a name="plan-virtual-networks"></a>Planera virtuella nätverk

Att skapa ett virtuellt nätverk för att experimentera med är tillräckligt, men det är möjligt att du distribuerar flera virtuella nätverk över tid för att stödja produktions behoven i din organisation. Med vissa planer kan du distribuera virtuella nätverk och ansluta resurserna som du behöver mer effektivt. Informationen i den här artikeln är mest användbar om du redan är van vid virtuella nätverk och har en del erfarenhet av att arbeta med dem. Om du inte är bekant med virtuella nätverk rekommenderar vi att du läser [Översikt över virtuella nätverk](virtual-networks-overview.md).

## <a name="naming"></a>Namngivning

Alla Azure-resurser har ett namn. Namnet måste vara unikt inom ett omfång, vilket kan variera för varje resurs typ. Namnet på ett virtuellt nätverk måste till exempel vara unikt inom en [resurs grupp](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), men kan dupliceras i en [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) eller Azure- [region](https://azure.microsoft.com/regions/#services). Att definiera en namngivnings konvention som du kan använda konsekvent när du namnger resurser är till hjälp när du hanterar flera nätverks resurser över tid. Förslag finns i [namngivnings konventioner](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Regioner

Alla Azure-resurser skapas i en Azure-region och prenumeration. En resurs kan bara skapas i ett virtuellt nätverk som finns i samma region och prenumeration som resursen. Du kan dock ansluta virtuella nätverk som finns i olika prenumerationer och regioner. Mer information finns i [anslutning](#connectivity). När du bestämmer vilka regioner som ska distribuera resurser i bör du tänka på var resurserna för resurserna är fysiskt placerade:

- Konsumenter av resurser vill vanligt vis ha den lägsta nätverks fördröjningen för resurserna. Information om relativa svars tider mellan en angiven plats och Azure-regioner finns i [Visa relativa fördröjningar](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Har du krav på data placering, suveränitet, efterlevnad eller återhämtning? I så fall, är det viktigt att välja den region som motsvarar kraven. Mer information finns i avsnittet om [Azure-geografiska](https://azure.microsoft.com/global-infrastructure/geographies/)områden.
- Behöver du återhämtning i Azure-tillgänglighetszoner inom samma Azure-region för de resurser som du distribuerar? Du kan distribuera resurser, till exempel virtuella datorer (VM) till olika tillgänglighets zoner inom samma virtuella nätverk. Alla Azure-regioner stöder dock inte tillgänglighets zoner. Läs mer om tillgänglighets zoner och de regioner som stöder dem i [tillgänglighets zoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Prenumerationer

Du kan distribuera så många virtuella nätverk som krävs inom varje prenumeration, upp till [gränsen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Vissa organisationer har olika prenumerationer för olika avdelningar, till exempel. Mer information och överväganden kring prenumerationer finns i [prenumerations styrning](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentering

Du kan skapa flera virtuella nätverk per prenumeration och per region. Du kan skapa flera undernät i varje virtuellt nätverk. De överväganden som följer hjälper dig att avgöra hur många virtuella nätverk och undernät du behöver:

### <a name="virtual-networks"></a>Virtuella nätverk

Ett virtuellt nätverk är en virtuell, isolerad del av det offentliga Azure-nätverket. Varje virtuellt nätverk är dedikerat för din prenumeration. Saker att tänka på när du bestämmer om du vill skapa ett virtuellt nätverk eller flera virtuella nätverk i en prenumeration:

- Finns det några organisatoriska säkerhets krav för att isolera trafik i separata virtuella nätverk? Du kan välja att ansluta virtuella nätverk eller inte. Om du ansluter virtuella nätverk kan du implementera en virtuell nätverks installation, till exempel en brand vägg, för att styra trafik flödet mellan de virtuella nätverken. Mer information finns i [säkerhet](#security) och [anslutning](#connectivity).
- Finns det några organisatoriska krav för att isolera virtuella nätverk i separata [prenumerationer](#subscriptions) eller [regioner](#regions)?
- Ett [nätverks gränssnitt](virtual-network-network-interface.md) gör det möjligt för en virtuell dator att kommunicera med andra resurser. Varje nätverks gränssnitt har tilldelats en eller flera privata IP-adresser. Hur många nätverks gränssnitt och [privata IP-adresser](./private-ip-addresses.md) behöver du i ett virtuellt nätverk? Det finns [begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) för antalet nätverks gränssnitt och privata IP-adresser som du kan ha i ett virtuellt nätverk.
- Vill du ansluta det virtuella nätverket till ett annat virtuellt nätverk eller lokalt nätverk? Du kan välja att ansluta några virtuella nätverk till varandra eller lokala nätverk, men inte andra. Mer information finns i [anslutning](#connectivity). Varje virtuellt nätverk som du ansluter till ett annat virtuellt nätverk eller lokalt nätverk måste ha ett unikt adress utrymme. Varje virtuellt nätverk har ett eller flera offentliga eller privata adress intervall tilldelade till sitt adress utrymme. Ett adress intervall anges i CIDR-format (Classless Internet Domain routing), till exempel 10.0.0.0/16. Lär dig mer om [adress intervall](manage-virtual-network.md#add-or-remove-an-address-range) för virtuella nätverk.
- Har du några organisations administrations krav för resurser i olika virtuella nätverk? I så fall kan du dela upp resurser i ett separat virtuellt nätverk för att förenkla [behörighets tilldelningen](#permissions) till enskilda användare i din organisation eller för att tilldela olika principer till olika virtuella nätverk.
- När du distribuerar vissa Azure-tjänsteresurser till ett virtuellt nätverk skapar de ett eget virtuellt nätverk. För att avgöra om en Azure-tjänst skapar ett eget virtuellt nätverk, se information för varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Undernät

Ett virtuellt nätverk kan segmenteras i ett eller flera undernät upp till [gränserna](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Saker att tänka på när du bestämmer om du vill skapa ett undernät eller flera virtuella nätverk i en prenumeration:

- Varje undernät måste ha ett unikt adress intervall, angivet i CIDR-format, inom det virtuella nätverkets adress utrymme. Adress intervallet får inte överlappa andra undernät i det virtuella nätverket.
- Om du planerar att distribuera vissa Azure-tjänsteresurser till ett virtuellt nätverk kan de behöva eller skapa ett eget undernät, så det måste finnas tillräckligt mycket ledigt utrymme för att de ska kunna göra det. För att avgöra om en Azure-tjänst skapar ett eget undernät, se information för varje [Azure-tjänst som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Om du till exempel ansluter ett virtuellt nätverk till ett lokalt nätverk med hjälp av ett Azure-VPN Gateway måste det virtuella nätverket ha ett dedikerat undernät för gatewayen. Läs mer om [Gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure dirigerar nätverks trafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan åsidosätta Azures standardroutning för att förhindra Azure-routning mellan undernät eller för att dirigera trafik mellan undernät via en virtuell nätverks installation, till exempel. Om du behöver trafik mellan resurser i samma virtuella nätverks flöde via en virtuell nätverks installation (NVA) distribuerar du resurserna till olika undernät. Läs mer i [säkerhet](#security).
- Du kan begränsa åtkomsten till Azure-resurser, till exempel ett Azure Storage-konto eller Azure SQL Database, till vissa undernät med en tjänst slut punkt för virtuella nätverk. Dessutom kan du neka åtkomst till resurserna från Internet. Du kan skapa flera undernät och aktivera en tjänst slut punkt för vissa undernät, men inte andra. Läs mer om [tjänst slut punkter](virtual-network-service-endpoints-overview.md)och de Azure-resurser som du kan aktivera dem för.
- Du kan koppla noll eller en nätverks säkerhets grupp till varje undernät i ett virtuellt nätverk. Du kan associera samma eller en annan nätverks säkerhets grupp till varje undernät. Varje nätverks säkerhets grupp innehåller regler som tillåter eller nekar trafik till och från källor och mål. Läs mer om [nätverks säkerhets grupper](#traffic-filtering).

## <a name="security"></a>Säkerhet

Du kan filtrera nätverks trafik till och från resurser i ett virtuellt nätverk med hjälp av nätverks säkerhets grupper och virtuella nätverks enheter. Du kan styra hur Azure dirigerar trafik från undernät. Du kan också begränsa vem i din organisation som kan arbeta med resurser i virtuella nätverk.

### <a name="traffic-filtering"></a>Trafikfiltrering

- Du kan filtrera nätverks trafik mellan resurser i ett virtuellt nätverk med hjälp av en nätverks säkerhets grupp, en NVA som filtrerar nätverks trafik eller både och. Om du vill distribuera en NVA, till exempel en brand vägg, för att filtrera nätverks trafik, se [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). När du använder en NVA kan du också skapa anpassade vägar för att dirigera trafik från undernät till NVA. Läs mer om [trafik dirigering](#traffic-routing).
- En nätverks säkerhets grupp innehåller flera standard säkerhets regler som tillåter eller nekar trafik till och från resurser. En nätverks säkerhets grupp kan kopplas till ett nätverks gränssnitt, under nätet som nätverks gränssnittet finns i eller både och. För att förenkla hanteringen av säkerhets regler rekommenderar vi att du kopplar en nätverks säkerhets grupp till enskilda undernät, i stället för enskilda nätverks gränssnitt inom under nätet, närhelst det är möjligt.
- Om olika virtuella datorer i ett undernät behöver olika säkerhets regler, kan du associera nätverks gränssnittet i den virtuella datorn med en eller flera program säkerhets grupper. En säkerhets regel kan ange en program säkerhets grupp i dess källa, mål eller både och. Regeln gäller sedan bara för de nätverks gränssnitt som är medlemmar i program säkerhets gruppen. Lär dig mer om [nätverks säkerhets grupper](./network-security-groups-overview.md) och [program säkerhets grupper](./network-security-groups-overview.md#application-security-groups).
- Azure skapar flera standard säkerhets regler inom varje nätverks säkerhets grupp. Med en standard regel kan all trafik flöda mellan alla resurser i ett virtuellt nätverk. Om du vill åsidosätta det här beteendet använder du nätverks säkerhets grupper, anpassad routning för att dirigera trafik till en NVA eller både och. Vi rekommenderar att du bekantar dig med alla [Standard säkerhets regler](./network-security-groups-overview.md#default-security-rules) för Azure och förstår hur regler för nätverks säkerhets grupper tillämpas på en resurs.

Du kan visa exempel design för att implementera ett perimeternätverk (kallas även DMZ) mellan Azure och Internet med en [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Trafik dirigering

Azure skapar flera standard vägar för utgående trafik från ett undernät. Du kan åsidosätta Azures standardroutning genom att skapa en routningstabell och associera den till ett undernät. Vanliga orsaker till att åsidosätta Azures standardroutning är:
- Eftersom du vill att trafiken mellan undernät ska flöda genom en NVA. Mer information om hur du [konfigurerar routningstabeller för att tvinga trafik genom en NVA](tutorial-create-route-table-portal.md).
- Eftersom du vill tvinga all Internet-baserad trafik via en NVA, eller lokalt, via en Azure VPN-gateway. Att tvinga Internet trafik lokalt för inspektion och loggning kallas ofta för Tvingad tunnel trafik. Läs mer om hur du konfigurerar [Tvingad tunnel trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Om du behöver implementera anpassad routning rekommenderar vi att du bekantar dig med [routning i Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Anslutning

Du kan ansluta ett virtuellt nätverk till andra virtuella nätverk med hjälp av peering för virtuella nätverk eller till ditt lokala nätverk med hjälp av en Azure VPN-gateway.

### <a name="peering"></a>Peering

När du använder [peering av virtuella nätverk](virtual-network-peering-overview.md)kan de virtuella nätverken vara i samma, eller olika, Azure-regioner som stöds. De virtuella nätverken kan finnas i samma eller olika Azure-prenumerationer (även prenumerationer som tillhör olika Azure Active Directory klienter). Innan du skapar en peering rekommenderar vi att du bekantar dig med alla peering- [krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints). Bandbredd mellan resurser i virtuella nätverk som är peer-kopplat i samma region är samma som om resurserna fanns i samma virtuella nätverk.

### <a name="vpn-gateway"></a>VPN gateway

Du kan använda en Azure- [VPN gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att ansluta ett virtuellt nätverk till ditt lokala nätverk med en [plats-till-plats-VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), eller genom att använda en särskild anslutning med Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Du kan kombinera peering och en VPN-gateway för att skapa [Hubbs-och eker-nätverk](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), där ekrar virtuella nätverk ansluter till ett virtuellt hubb nätverk, och hubben ansluter till ett lokalt nätverk, till exempel.

### <a name="name-resolution"></a>Namnmatchning

Resurser i ett virtuellt nätverk kan inte matcha namnen på resurserna i ett peer-kopplat virtuellt nätverk med hjälp av Azures [inbyggda DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Om du vill matcha namn i ett peer-kopplat virtuellt nätverk [distribuerar du din egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)eller använder Azure DNS [privata domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Att matcha namn mellan resurser i ett virtuellt nätverk och lokala nätverk kräver också att du distribuerar din egen DNS-server.

## <a name="permissions"></a>Behörigheter

Azure använder [Azures rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för resurser. Behörigheter tilldelas en [omfattning](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) i följande hierarki: hanterings grupp, prenumeration, resurs grupp och enskild resurs. Mer information om hierarkin finns i [ordna dina resurser](../governance/management-groups/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill arbeta med virtuella Azure-nätverk och alla tillhör ande funktioner, till exempel peering, nätverks säkerhets grupper, tjänst slut punkter och routningstabeller, kan du tilldela medlemmar i din organisation till den inbyggda rollen [ägare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)eller [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och sedan tilldela rollen till lämplig omfattning. Om du vill tilldela specifika behörigheter för en delmängd av funktioner för virtuella nätverk skapar du en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och tilldelar de specifika behörigheter som krävs för [virtuella nätverk](manage-virtual-network.md#permissions), [undernät och tjänst slut punkter](virtual-network-manage-subnet.md#permissions), [nätverks gränssnitt](virtual-network-network-interface.md#permissions), [peering](virtual-network-manage-peering.md#permissions), [nätverks-och program säkerhets grupper](manage-network-security-group.md#permissions)eller [väg tabeller](manage-route-table.md#permissions) till rollen.

## <a name="policy"></a>Policy

Med Azure Policy kan du skapa, tilldela och hantera princip definitioner. Princip definitioner tillämpar olika regler för dina resurser, så att resurserna förblir kompatibla med organisationens standarder och service avtal. Azure Policy kör en utvärdering av dina resurser och söker efter resurser som inte är kompatibla med de princip definitioner som du har. Du kan till exempel definiera och tillämpa en princip som gör det möjligt att skapa virtuella nätverk i endast en speciell resurs grupp eller region. En annan princip kan kräva att varje undernät har en nätverks säkerhets grupp som är kopplad till den. Principerna utvärderas sedan när du skapar och uppdaterar resurser.

Principer tillämpas på följande hierarki: hanterings grupp, prenumeration och resurs grupp. Läs mer om hur du [Azure policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller distribuerar några [Azure policy definitioner](./policy-reference.md)för virtuella nätverk.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om alla aktiviteter, inställningar och alternativ för ett [virtuellt nätverk](manage-virtual-network.md), [undernät och tjänst slut punkt](virtual-network-manage-subnet.md), [nätverks gränssnitt](virtual-network-network-interface.md), [peering](virtual-network-manage-peering.md),  [nätverks-och program säkerhets grupp](manage-network-security-group.md)eller [routningstabell](manage-route-table.md).
