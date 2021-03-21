---
title: Privata IP-adresser i Azure
titlesuffix: Azure Virtual Network
description: Lär dig mer om privata IP-adresser i Azure.
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
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223269"
---
# <a name="private-ip-addresses"></a>Privata IP-adresser
Privata IP-adresser tillåter kommunikation mellan resurser i Azure. 

Resurser kan vara:
* Azure-tjänster som:
    * Nätverksgränssnitt för virtuella datorer
    * Interna lastbalanserare (ILB)
    * Programgatewayer
* I ett [virtuellt nätverk](virtual-networks-overview.md).
* Lokalt nätverk via en VPN-gateway eller ExpressRoute-krets.

Privata IP-adresser tillåter kommunikation till dessa resurser utan att använda en offentlig IP-adress.

## <a name="allocation-method"></a>Allokeringsmetod

Azure tilldelar privata IP-adresser till resurser från adress intervallet för det virtuella nätverks under nätet där resursen finns.

Azure reserverar de fyra första adresserna i varje under näts adress intervall. Adresserna kan inte tilldelas till resurser. Om under nätets adress intervall till exempel är 10.0.0.0/16, är adresserna 10.0.0.0-10.0.0.3 och 10.0.255.255 inte tillgängliga. IP-adresser inom undernätets adressintervall kan endast tilldelas en resurs åt gången. 

Det finns två metoder för att få en privat IP-adress:

- **Dynamisk**: Azure tilldelar nästa tillgängliga otilldelade eller oreserverade IP-adress i undernätets adressintervall. Azure tilldelar exempelvis 10.0.0.10 till en ny resurs om adresserna 10.0.0.4-10.0.0.9 redan är tilldelade andra resurser. 

    Dynamisk är standardinställningen för allokering. När de har tilldelats frigörs dynamiska IP-adresser om ett nätverks gränssnitt är:
    
    * Borttagen
    * Omtilldelad till ett annat undernät inom samma virtuella nätverk.
    * Tilldelnings metoden ändras till statisk och en annan IP-adress har angetts. 
    
    Som standard tilldelar Azure den tidigare dynamiskt tilldelade adressen som statisk adress när du ändrar allokeringsmetod från dynamisk till statisk.

- **Statisk**: Du väljer och tilldelar en otilldelad eller oreserverad IP-adress i undernätets adressintervall. 

    Ett undernäts adress intervall är till exempel 10.0.0.0/16 och adresser 10.0.0.4-10.0.0.9 tilldelas till andra resurser. Du kan tilldela valfri adress mellan 10.0.0.10-10.0.255.254. Statiska adresser släpps endast om ett nätverksgränssnitt tas bort. 
    
    Azure tilldelar den statiska IP-adressen som den dynamiska IP-adressen när tilldelnings metoden ändras. Omtilldelningen sker även om adressen inte är nästa tillgängliga i under nätet. Adressen ändras när nätverks gränssnittet tilldelas till ett annat undernät.
    
    Om du vill tilldela nätverks gränssnittet till ett annat undernät ändrar du tilldelnings metoden från statisk till dynamisk. Tilldela nätverks gränssnittet till ett annat undernät och ändra sedan metoden tillbaka till statisk. Tilldela en IP-adress från det nya under nätets adress intervall.
    
## <a name="virtual-machines"></a>Virtuella datorer

En eller flera privata IP-adresser tilldelas till ett eller flera **nätverks gränssnitt**. Nätverks gränssnitten tilldelas till en virtuell [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator. Du kan ange allokeringsmetoden som dynamisk eller statisk för varje privat IP-adress.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Intern DNS-värdnamnsmatchning (för virtuella datorer)

Virtuella Azure-datorer konfigureras med [Azure-hanterade DNS-servrar](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) som standard. Du kan uttryckligen konfigurera anpassade DNS-servrar. Dessa DNS-servrar tillhandahåller intern namn matchning för virtuella datorer i samma virtuella nätverk.

En mappning för värd namnet till en virtuell dators privata IP-adress läggs till i Azure-hanterade DNS-servrar. 

Ett värdnamn mappas till den primära IP-adressen för huvud nätverks gränssnittet när en virtuell dator har:

* Flera nätverksgränssnitt
* Flera IP-adresser
* Båda

Virtuella datorer som kon figurer ATS med Azure-hanterad DNS matcha värd namnen i samma virtuella nätverk. Använd en anpassad DNS-server för att matcha värddator namn för virtuella datorer i anslutna virtuella nätverk.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Interna lastbalanserare (ILB) och programgateways

Du kan tilldela en privat IP-adress till **klient delens** konfiguration:

* [Intern Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Azure Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Den här privata IP-adressen fungerar som en intern slut punkt. Den interna slut punkten är bara tillgänglig för resurserna i det virtuella nätverket och de fjärranslutna nätverken som är anslutna till den. Du kan tilldela en dynamisk eller statisk IP-adress.

## <a name="at-a-glance"></a>En snabb översikt
I följande tabell visas den egenskap genom vilken en privat IP-adress kan kopplas till en resurs. 

De möjliga fördelnings metoder som kan användas visas också:

* Dynamisk
* Statisk

| Resurs på den översta nivån | IP-adressassociation | Dynamisk | Statisk |
| --- | --- | --- | --- |
| Virtuell dator |Nätverksgränssnitt |Ja |Ja |
| Lastbalanserare |Konfiguration på klientsidan |Ja |Ja |
| Programgateway |Konfiguration på klientsidan |Ja |Ja |

## <a name="limits"></a>Gränser
Gränserna för IP-adressering finns i den fullständiga uppsättningen [begränsningar för nätverk](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) i Azure. Gränserna anges per region och per prenumeration. [Kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill öka standard gränserna upp till de maximala gränserna utifrån dina affärs behov.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [offentliga IP-adresser i Azure](public-ip-addresses.md)
* [Distribuera en virtuell dator med en statisk privat IP-adress med hjälp av Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)