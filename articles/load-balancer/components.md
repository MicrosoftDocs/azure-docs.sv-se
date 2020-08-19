---
title: Azure Load Balancer-komponenter
description: Översikt över Azure Load Balancer-komponenter
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: ccc6611f14903e47a76de938994552378bb3bc24
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589715"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer-komponenter

Azure Load Balancer innehåller några viktiga komponenter. Dessa komponenter kan konfigureras i din prenumeration via:

* Azure Portal
* Azure CLI
* Azure PowerShell
* Resource Manager-mallar

## <a name="frontend-ip-configuration"></a>IP-konfiguration för klient del <a name = "frontend-ip-configurations"></a>

IP-adressen för din Azure Load Balancer. Det är kontakt punkten för-klienter. Följande IP-adresser kan vara antingen:

- **Offentlig IP-adress**
- **Privat IP-adress**

IP-adressens typ avgör vilken **typ** av belastningsutjämnare som skapats. Val av privat IP-adress skapar en intern belastningsutjämnare. Val av offentlig IP-adress skapar en offentlig belastningsutjämnare.

|  | Offentlig lastbalanserare  | Intern lastbalanserare |
| ---------- | ---------- | ---------- |
| **IP-konfiguration för klient del**| Offentlig IP-adress | Privat IP-adress|
| **Beskrivning** | En offentlig belastningsutjämnare mappar den offentliga IP-adressen och porten för inkommande trafik till den privata IP-adressen och porten för den virtuella datorn. Belastnings utjämning mappar trafik på det andra sättet för svars trafiken från den virtuella datorn. Du kan distribuera vissa typer av trafik över flera virtuella datorer eller tjänster genom att använda regler för belastnings utjämning. Du kan till exempel sprida belastningen av trafik för webbegäranden på flera webbservrar.| En intern belastningsutjämnare distribuerar trafik till resurser som finns i ett virtuellt nätverk. Azure begränsar åtkomsten till klient delens IP-adresser för ett virtuellt nätverk som är belastningsutjämnad. IP-adresser och virtuella nätverk på klient sidan exponeras aldrig direkt till en Internet-slutpunkt. Interna verksamhetsspecifika appar körs i Azure och nås i Azure eller via lokala resurser. |
| **SKU: er som stöds** | Basic, standard | Basic, standard |

![Exempel på nivå för belastnings utjämning](./media/load-balancer-overview/load-balancer.png)

Load Balancer kan ha flera IP-adresser för klient delen. Läs mer om [flera klient](load-balancer-multivip-overview.md)delar.

## <a name="backend-pool"></a>Serverdelspool

Gruppen med virtuella datorer eller instanser i en skalnings uppsättning för virtuella datorer som hanterar den inkommande begäran. Om du vill skala kostnads effektivt för att möta stora volymer inkommande trafik rekommenderar vi att du lägger till fler instanser i Server dels poolen.

Belastnings utjämning konfigurerar genast om sig själv via automatisk omkonfiguration när du skalar upp eller ned instanser. Om du lägger till eller tar bort virtuella datorer från backend-poolen omkonfigureras belastningsutjämnaren utan ytterligare åtgärder. Omfånget för backend-poolen är en virtuell dator i det virtuella nätverket.

När du överväger att utforma en backend-pool bör du utforma för det minsta antalet enskilda resurser för Server dels poolen för att optimera längden på hanterings åtgärder. Det finns ingen skillnad i prestanda eller skalning för data planet.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En hälso avsökning används för att fastställa hälso status för instanserna i backend-poolen. Under skapandet av belastningsutjämnare konfigurerar du en hälso avsökning för belastningsutjämnaren som ska användas.  Den här hälso avsökningen avgör om en instans är felfri och kan ta emot trafik.

Du kan definiera tröskelvärdet för hälso avsökningar. När en avsökning inte svarar, Load Balancer sluta att skicka nya anslutningar till felaktiga instanser. Ett avsöknings haveri påverkar inte befintliga anslutningar. Anslutningen fortsätter tills programmet:

- Avslutar flödet
- Tids gräns för inaktivitet inträffar
- Den virtuella datorn stängs av

Load Balancer innehåller olika typer av hälso avsökningar för slut punkter: TCP, HTTP och HTTPS. [Läs mer om att Load Balancer hälso avsökningar](load-balancer-custom-probe-overview.md).

Basic Load Balancer stöder inte HTTPS-avsökningar. Grundläggande Load Balancer stänger alla TCP-anslutningar (inklusive etablerade anslutningar).

## <a name="load-balancing-rules"></a>Belastnings Utjämnings regler

En Load Balancer regel används för att definiera hur inkommande trafik distribueras till **alla** instanser i backend-poolen. En belastnings Utjämnings regel mappar en bestämd IP-konfiguration och port för klient delen till flera Server dels IP-adresser och portar.

Använd till exempel en belastnings Utjämnings regel för port 80 för att dirigera trafik från klient delens IP-adress till port 80 för Server dels instanserna.

<p align="center">
  <img src="./media/load-balancer-components/lbrules.svg" alt= "Figure depicts how Azure Load Balancer directs frontend port 80 to three instances of backend port 80." width="512" title="Belastnings Utjämnings regler">
</p>

*Bild: belastnings Utjämnings regler*

## <a name="high-availability-ports"></a>Portar med hög tillgänglighet

En belastnings Utjämnings regel som kon figurer ATS med **protokollet-all och Port-0**. 

Med den här regeln kan en enskild regel belastningsutjämna alla TCP-och UDP-flöden som tas emot på alla portar i en intern Standard Load Balancer. 

Belastnings Utjämnings beslutet görs per flöde. Den här åtgärden baseras på följande fem-tupel-anslutning: 

1. 
    källans IP-adress
  
2. källporten
3. mål-IP-adressen
4. målporten
5. protokollhanterare

Belastnings Utjämnings reglerna för HA-portar hjälper dig med kritiska scenarier, till exempel hög tillgänglighet och skalning för virtuella nätverks installationer (NVA) i virtuella nätverk. Funktionen kan hjälpa dig när ett stort antal portar måste vara belastningsutjämnade.

<p align="center">
  <img src="./media/load-balancer-components/harules.svg" alt="Figure depicts how Azure Load Balancer directs all frontend ports to three instances of all backend ports" width="512" title="Regler för HA-portar">
</p>

*Bild: regler för HA-portar*

Läs mer om [ha-portar](load-balancer-ha-ports-overview.md).

## <a name="inbound-nat-rules"></a>Ingående NAT-regler

En inkommande NAT-regel vidarebefordrar inkommande trafik som skickas till klient delens IP-adress och port kombination. Trafiken skickas till en **speciell** virtuell dator eller instans i backend-poolen. Vidarebefordran av portar utförs av samma hash-baserade distribution som belastnings utjämning.

Till exempel, om du vill att Remote Desktop Protocol (RDP) eller Secure Shell-sessioner (SSH) ska separera VM-instanser i en backend-pool. Flera interna slut punkter kan mappas till portar på samma IP-adress för klient delen. Klient delens IP-adresser kan användas för att fjärradministrera dina virtuella datorer utan någon ytterligare hopp ruta.

<p align="center">
  <img src="./media/load-balancer-components/inboundnatrules.svg" alt="Figure depicts how Azure Load Balancer directs frontend ports 3389, 443, and 80 to backend ports with the same values on separate servers." width="512" title="Ingående NAT-regler">
</p>

*Bild: inkommande NAT-regler*

Inkommande NAT-regler i kontexten för Virtual Machine Scale Sets är inkommande NAT-pooler. Läs mer om [Load Balancer-komponenter och skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Regler för utgående trafik

En utgående regel konfigurerar utgående NAT (Network Address Translation) för alla virtuella datorer eller instanser som identifieras av backend-poolen. Den här regeln gör det möjligt för instanser i Server delen att kommunicera (utgående) till Internet eller andra slut punkter.

Läs mer om [utgående anslutningar och regler](load-balancer-outbound-connections.md).

Den grundläggande belastningsutjämnaren stöder inte utgående regler.

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Lär dig mer om den [offentliga IP-adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Läs mer om [privat IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Lär dig mer om [standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md).
- Läs mer om [TCP-återställning vid inaktivitet](load-balancer-tcp-reset.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Läs mer om [gränser för belastnings utjämning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Lär dig mer om att använda [vidarebefordran av portar](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
