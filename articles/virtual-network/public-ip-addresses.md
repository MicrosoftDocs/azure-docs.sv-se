---
title: Offentliga IP-adresser i Azure
titlesuffix: Azure Virtual Network
description: Läs mer om offentliga och privata IP-adresser i Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: ffdd673cc8a357a7156fb3b3e932c524c831db15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103418070"
---
# <a name="public-ip-addresses"></a>Offentliga IP-adresser

Offentliga IP-adresser tillåter att Internet-resurser kommunicerar inkommande till Azure-resurser. Offentliga IP-adresser gör att Azure-resurser kan kommunicera med Internet och offentliga Azure-tjänster. Adressen är dedikerad till resursen tills den har tilldelats av dig. En resurs utan en offentlig IP-adress kan kommunicera utgående. Azure tilldelar dynamiskt en tillgänglig IP-adress som inte är dedikerad till resursen. Mer information om utgående anslutningar i Azure finns i [Förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

I Azure Resource Manager är en [offentlig IP-adress](virtual-network-public-ip-address.md) en resurs som har sina egna egenskaper. Några av resurserna du kan associera en offentlig IP-adressresurs med:

* Nätverksgränssnitt för virtuella datorer
* Internetuppkopplade lastbalanserare
* VPN-gateways
* Programgatewayer
* Azure Firewall

## <a name="ip-address-version"></a>IP-adressversion

Offentliga IP-adresser skapas med en IPv4- eller IPv6-adress. 

## <a name="sku"></a>SKU

Mer information om SKU-uppgradering finns i [offentlig IP-uppgradering](../virtual-network/virtual-network-public-ip-address-upgrade.md).

Offentliga IP-adresser skapas med någon av följande SKU:er:

>[!IMPORTANT]
> Matchande SKU: er krävs för belastnings utjämning och offentliga IP-resurser. Du kan inte blanda grundläggande SKU-resurser och standard-SKU-resurser. Du kan inte bifoga fristående virtuella datorer, virtuella datorer i en tillgänglighetsuppsättningsresurs eller en virtuell dators skalningsuppsättningsresurser till båda SKU:erna samtidigt.  Ny design bör överväga att använda standard-SKU-resurser.  Mer information finns i [Standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="standard"></a>Standard

Offentliga IP-adresser för standard-SKU:

- Använd alltid den statiska allokeringsmetoden.
- Har en justerbar inkommande tidsgräns för inaktivitet i flöde på 4–30 minuter, med ett standardvärde på 4 minuter, och en fast utgående tidsgräns för inaktivitet i flöde på 4 minuter.
- Skydda som standard och stängas till inkommande trafik. Tillåt en lista över inkommande trafik med en [nätverks säkerhets grupp](./network-security-groups-overview.md#network-security-groups).
- Tilldelad till nätverks gränssnitt, offentliga standard belastningsutjämnare eller programgatewayer. Mer information om standard Load Balancer finns i [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Kan vara Zone-redundant (annonserad från alla tre zoner), zonindelade (garanterat i en viss förvald tillgänglighets zon) eller ingen zon (som inte är associerad med en viss förvald tillgänglighets zon). Om du vill veta mer om tillgänglighetszoner kan du läsa [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Standard Load Balancer och tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Zon redundanta IP-adresser kan bara skapas i [regioner där 3 tillgänglighets zoner](../availability-zones/az-region.md) är Live.** IP-adresser som skapats innan zoner är Live är inte zonens redundanta.
- Kan användas som anycast-klient-IP [-adresser för belastningsutjämnare mellan regioner](../load-balancer/cross-region-overview.md) (för hands versions funktioner).
 
> [!NOTE]
> Inkommande kommunikation med en resurs med standard-SKU misslyckas tills du har skapat och kopplat en [nätverkssäkerhetsgrupp](./network-security-groups-overview.md#network-security-groups) och uttryckligen tillåtit önskad inkommande trafik.

> [!NOTE]
> Endast offentliga IP-adresser med Basic SKU är tillgängliga när du använder [IMDS för instans-metadata](../virtual-machines/windows/instance-metadata-service.md). Standard-SKU stöds inte.

> [!NOTE]
> Diagnostikinställningar visas inte under bladet om att använda en offentlig IP-adress för standard-SKU. Om du vill aktivera loggning på din offentliga standard-IP-adressresurs navigerar du till diagnostikinställningar under Azure Monitor bladet och väljer din IP-adressresurs.

### <a name="basic"></a>Grundläggande

Alla offentliga IP-adresser som skapades innan SKU:erna infördes är offentliga IP-adresser på nivån Basic. 

I introduktionen av SKU: er anger du vilket SKU som du vill att den offentliga IP-adressen ska vara. 

Grundläggande SKU-adresser:

- Tilldelas med en statisk eller dynamisk allokeringsmetod.
- Har en justerbar inkommande tidsgräns för inaktivitet i flöde på 4–30 minuter, med ett standardvärde på 4 minuter, och en fast utgående tidsgräns för inaktivitet i flöde på 4 minuter.
- Är öppna som standard.  Nätverkssäkerhetsgrupper rekommenderas för att begränsa inkommande och utgående datatrafik, men det är valfritt att använda dem.
- Tilldelad till alla Azure-resurser som kan tilldelas en offentlig IP-adress, till exempel:
    * Nätverksgränssnitt
    * VPN-gatewayer
    * Programgateways
    * Publika belastningsutjämnare
- Stöder inte scenarier för tillgänglighets zoner. Använd standard-SKU offentlig IP för tillgänglighets zon scenarier. Om du vill veta mer om tillgänglighetszoner kan du läsa [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Standard Load Balancer och tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Allokeringsmetod

Standard-och offentliga IP-adresser stöder **statisk** tilldelning.  Resursen tilldelas en IP-adress när den skapas. IP-adressen släpps när resursen tas bort.

Grundläggande SKU offentliga IP-adresser stöder en **dynamisk** tilldelning. Dynamisk är standard tilldelnings metoden. Den här IP-adressen har **inte** tilldelats resursen vid tidpunkten för skapandet när du väljer dynamisk.

IP-adressen tilldelas när du associerar den offentliga IP-adressresursen med en:

* Virtuell dator 
* Den första virtuella datorn är kopplad till backend-poolen för en belastningsutjämnare.

IP-adressen frisläpps när du stoppar (eller tar bort) resursen.  

Till exempel släpps en offentlig IP-resurs från en resurs med namnet **Resource a**. **Resurs A** tar emot en annan IP-adress vid start om den offentliga IP-resursen tilldelas om.

IP-adressen släpps när tilldelnings metoden ändras från **statisk** till **dynamisk**. Om du vill se till att IP-adressen för den tillhör ande resursen är densamma, ställer du in tilldelnings metoden explicit på **statisk**. En statisk IP-adress tilldelas direkt.

> [!NOTE]
> Även om du anger allokeringsmetoden till **statisk** kan du inte ange själva IP-adressen som tilldelas till den offentliga IP-adressresursen. Azure tilldelar i stället IP-adressen från en pool med tillgängliga IP-adresser på den Azure-plats där resursen skapas.
>

Statiska offentliga IP-adresser används ofta i följande scenarier:

* När du måste uppdatera brandväggsregler för att kommunicera med dina Azure-resurser.
* DNS-namnmatchning, där en ändring i IP-adressen kräver uppdatering av A-poster.
* Dina Azure-resurser kommunicerar med andra appar eller tjänster som använder en IP-adressbaserad säkerhetsmodell.
* Du använder TLS/SSL-certifikat som är länkade till en IP-adress.

> [!NOTE]
> Azure allokerar offentliga IP-adresser från ett intervall som är unikt för varje region i varje Azure-moln. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).
>

## <a name="dns-hostname-resolution"></a>Matchning av DNS-värdnamn

Välj alternativet för att ange en DNS-domän namn etikett för en offentlig IP-resurs. 

Det här alternativet skapar en mappning för **domainnamelabel**. **location**. cloudapp.Azure.com till den offentliga IP-adressen i Azure-hanterade DNS. 

Till exempel skapas en offentlig IP-adress med:

* **contoso** som en **domainnamelabel**
* **Västra USA** Azure- **plats**

Det fullständigt kvalificerade domän namnet (FQDN) **contoso.westus.cloudapp.Azure.com** matchar resursens offentliga IP-adress.

> [!IMPORTANT]
> Varje domännamnsetikett som skapas måste vara unik inom dess Azure-plats.  
>

## <a name="dns-recommendations"></a>DNS-rekommendationer

Om du behöver flytta en region kan du inte migrera FQDN för din offentliga IP-adress. Använd FQDN för att skapa en anpassad CNAME-post som pekar på den offentliga IP-adressen. 

Om det krävs en flytt till en annan offentlig IP-adress uppdaterar du CNAME-posten i stället för att uppdatera FQDN.

Du kan använda [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) eller en extern DNS-Provider för din DNS-post. 

## <a name="virtual-machines"></a>Virtuella datorer

Du kan associera en offentlig IP-adress med en [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- eller [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-baserad virtuell dator genom att tilldela den till dess **nätverksgränssnitt**. 

Välj **dynamisk** eller **statisk** för den offentliga IP-adressen. Lär dig mer om att [tilldela IP-adresser till nätverksgränssnitt](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Internetuppkopplade lastbalanserare

Du kan associera en offentlig IP-adress för antingen [SKU: n](#sku) med en [Azure Load Balancer](../load-balancer/load-balancer-overview.md), genom att tilldela den till konfiguration av belastningsutjämnarens **klient** del. Den offentliga IP-adressen fungerar som en belastningsutjämnad IP-adress. 

Du kan tilldela antingen en dynamisk eller en statisk offentlig IP-adress till klientsidan för en lastbalanserare. Du kan tilldela flera offentliga IP-adresser till en belastnings Utjämnings klient del. Den här konfigurationen möjliggör [multi-VIP-](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) scenarier som en miljö med flera klienter med TLS-baserade webbplatser. 

Mer information om SKU:er för lastbalanserare i Azure finns i [Standard-SKU för Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="vpn-gateways"></a>VPN-gateways

[Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ansluter ett virtuellt Azure-nätverk till:

* Virtuella Azure-nätverk
* Lokala nätverk (n). 

En offentlig IP-adress tilldelas till VPN Gateway för att möjliggöra kommunikation med fjärrnätverket. 

* Tilldela en **dynamisk** grundläggande offentlig IP-adress till en VPNGw 1-5-SKU front-end-konfiguration.
* Tilldela en **statisk** standard-IP-adress till en VPNGwAZ 1-5-SKU front-end-konfiguration.

## <a name="application-gateways"></a>Programgatewayer

Du kan associera en offentlig IP-adress med en Azure [Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genom att tilldela den till gatewayens konfiguration på **klientsidan**. 

* Tilldela en **dynamisk** grundläggande offentlig IP-adress till en Application Gateway v1-konfiguration för klient delen. 
* Tilldela en **statisk** offentlig IP-adress till en v2-konfiguration för klient delen.

## <a name="azure-firewall"></a>Azure Firewall

Med [Azure-brandväggen](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kan du skapa, tillämpa och logga program-och nätverks anslutnings principer över prenumerationer och virtuella nätverk.

Du kan bara koppla **statiska** offentliga IP-adresser med en brand vägg. Detta gör det möjligt för externa brand väggar att identifiera trafik från det virtuella nätverket. 


## <a name="at-a-glance"></a>En snabb översikt

I följande tabell visas den egenskap som kan användas för att koppla en offentlig IP-adress till en resurs på den översta nivån och möjliga fördelnings metoder.

| Resurs på den översta nivån | IP-adressassociation | Dynamisk | Statisk |
| --- | --- | --- | --- |
| Virtuell dator |Nätverksgränssnitt |Ja |Ja |
| Internetuppkopplad lastbalanserare |Konfiguration på klientsidan |Ja |Ja |
| VPN gateway |IP-konfiguration för gateway |Ja |Inga |
| Programgateway |Konfiguration på klientsidan |Ja (endast V1) |Ja (endast V2) |
| Azure Firewall | Konfiguration på klientsidan | Inga | Ja|

## <a name="limits"></a>Gränser

Begränsningarna för IP-adresser anges i den fullständiga uppsättningen [nätverks gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) i Azure. 

Gränserna anges per region och per prenumeration. [Kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill öka standard gränserna upp till de maximala gränserna utifrån dina affärs behov.

## <a name="pricing"></a>Priser

Offentliga IP-adresser kan medföra en nominell avgift. Mer information om priserna för IP-adresser i Azure finns på sidan med [priser för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [privata IP-adresser i Azure](private-ip-addresses.md)
* [Distribuera en virtuell dator med en statisk offentlig IP-adress i Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
