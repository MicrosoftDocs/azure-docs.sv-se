---
title: Använd Azure Application Gateway med virtuella VMware-datorer
description: Beskriver hur du använder Azure Application Gateway för att hantera inkommande webb trafik för webb servrar som körs i VMware Virtual Machines Win CloudSimple Private Cloud Environment
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897951"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Använd Azure Application Gateway med virtuella VMware-datorer i CloudSimple-miljön för privat moln

Du kan använda Azure Application Gateway för att hantera inkommande webb trafik för dina webb servrar som körs på virtuella VMware-datorer i din CloudSimple privata moln miljö.

Genom att använda Azure Application Gateway i en offentlig privat hybrid distribution kan du hantera webb trafik till dina program, tillhandahålla en säker front-end och avlasta TLS-bearbetning för de tjänster som körs i VMware-miljön. Azure Application Gateway dirigerar inkommande webb trafik till instanser av backend-pooler som finns i VMware-miljöer enligt konfigurerade regler och hälso avsökningar.

Denna Azure Application Gateway-lösning kräver att du:

* Ha en Azure-prenumeration.
* Skapa och konfigurera ett virtuellt Azure-nätverk och ett undernät i det virtuella nätverket.
* Skapa och konfigurera NSG-regler och peer-nätverket med ExpressRoute till ditt CloudSimple-privata moln.
* Skapa & konfigurera ditt privata moln.
* Skapa & konfigurera din Azure Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Distributions scenario för Azure Application Gateway

I det här scenariot körs Azure Application Gateway i det virtuella Azure-nätverket. Det virtuella nätverket är anslutet till ditt privata moln via en ExpressRoute-krets. Alla undernät i det privata molnet är IP-adresser tillgängliga från de virtuella nätverkets undernät.

![Azure Load Balancer i Azure Virtual Network](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Så här distribuerar du lösningen

Distributions processen består av följande uppgifter:

1. [Kontrol lera att kraven är uppfyllda](#1-verify-prerequisites)
2. [Anslut din virtuella Azure-anslutning till det privata molnet](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Distribuera en Azure Application Gateway](#3-deploy-an-azure-application-gateway)
4. [Skapa och konfigurera VM-poolen för virtuella datorer i ditt privata moln](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. kontrol lera krav

Kontrol lera att följande krav är uppfyllda:

* En Azure Resource Manager och ett virtuellt nätverk har redan skapats.
* Ett dedikerat undernät (för Application Gateway) i ditt virtuella Azure-nätverk har redan skapats.
* Ett privat CloudSimple-moln har redan skapats.
* Det finns ingen IP-konflikt mellan IP-undernät i det virtuella nätverket och undernät i det privata molnet.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Anslut ditt virtuella Azure-nätverk till ditt privata moln

Följ den här processen om du vill ansluta ditt virtuella Azure-nätverk till ditt privata moln.

1. [Kopiera ExpressRoute-peering-informationen i CloudSimple-portalen](virtual-network-connection.md).

2. [Konfigurera en virtuell nätverksgateway för ditt virtuella Azure-nätverk](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Länka ditt virtuella nätverk till CloudSimple ExpressRoute-kretsen](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Använd peering-informationen som du kopierade för att länka ditt virtuella nätverk till ExpressRoute-kretsen](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. distribuera en Azure Application Gateway

Detaljerade instruktioner för detta finns i [skapa en Programgateway med Sök vägsbaserade regler för routning med hjälp av Azure Portal](../application-gateway/create-url-route-portal.md). Här är en sammanfattning av de steg som krävs:

1. Skapa ett virtuellt nätverk i din prenumeration och resurs grupp.
2. Skapa ett undernät (som ska användas som dedikerat undernät) i det virtuella nätverket.
3. Skapa en standard Application Gateway (om du vill aktivera WAF): på Start sidan Azure Portal klickar du på **resurs**  >  **nätverk**  >  **Application Gateway** på sidans övre vänstra sida. Välj standard-SKU och storlek och ange information om Azure-prenumeration, resurs grupp och plats. Om det behövs skapar du en ny offentlig IP-adress för den här programgatewayen och ger information om det virtuella nätverket och det dedikerade under nätet för programgatewayen.
4. Lägg till en backend-pool med virtuella datorer och Lägg till den i Application Gateway.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. skapa och konfigurera en VM-pool för en webb server i ditt privata moln

I vCenter skapar du virtuella datorer med det operativ system och den webb server du väljer (till exempel Windows/IIS eller Linux/Apache). Välj ett undernät/VLAN som är avsett för webb nivån i ditt privata moln. Kontrol lera att minst en vNIC av de virtuella webb servererna finns på webb nivå under nätet.
