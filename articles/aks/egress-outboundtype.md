---
title: Anpassa användardefinierade vägar (UDR) i Azure Kubernetes Service (AKS)
description: Lär dig hur du definierar en anpassad utgående väg i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: e9433978c8ee855ec66901c7692e4d2b59261fd3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773053"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Anpassa utgående kluster med en User-Defined väg

Utgående från ett AKS-kluster kan anpassas för att passa specifika scenarier. Som standard etablerar AKS en standard-SKU Load Balancer konfigureras och användas för utgående. Standardinstallationen kanske dock inte uppfyller kraven för alla scenarier om offentliga IP-adresser är otillåtna eller ytterligare hopp krävs för utgående.

Den här artikeln går igenom hur du anpassar ett klusters utgående väg för att stödja anpassade nätverksscenarier, till exempel sådana som inte tillåtar offentliga IP-adresser och kräver att klustret finns bakom en virtuell nätverksinstallation (NVA).

## <a name="prerequisites"></a>Förutsättningar
* Azure CLI version 2.0.81 eller senare
* API-version `2020-01-01` av eller senare


## <a name="limitations"></a>Begränsningar
* OutboundType kan bara definieras när klustret skapas och kan inte uppdateras efteråt.
* Inställningen `outboundType` kräver AKS-kluster med a `vm-set-type` för och `VirtualMachineScaleSets` `load-balancer-sku` `Standard` .
* Inställningen `outboundType` till värdet kräver en `UDR` användardefinierad väg med giltig utgående anslutning för klustret.
* Om du anger värdet för innebär det att ip-adressen för ingresskällan som dirigeras till lastbalanseraren kanske inte matchar klustrets utgående `outboundType` `UDR` utgående måladress. 

## <a name="overview-of-outbound-types-in-aks"></a>Översikt över utgående typer i AKS

Ett AKS-kluster kan anpassas med en unik `outboundType` typ `loadBalancer` eller `userDefinedRouting` .

> [!IMPORTANT]
> Utgående typ påverkar endast utgående trafik i klustret. Mer information finns i [Konfigurera ingress-kontrollanter.](ingress-basic.md)

> [!NOTE]
> Du kan använda din egen [vägtabell med][byo-route-table] UDR och kubenet-nätverk. Kontrollera att klusteridentiteten (tjänstens huvudnamn eller hanterade identitet) har deltagarbehörighet till den anpassade vägtabellen.

### <a name="outbound-type-of-loadbalancer"></a>Utgående typ av loadBalancer

Om `loadBalancer` har angetts slutför AKS följande konfiguration automatiskt. Lastbalanseraren används för utgående data via en AKS-tilldelad offentlig IP-adress. En utgående typ av stöder Kubernetes-tjänster av typen , som förväntar sig utgående trafik från lastbalanseraren som skapas av `loadBalancer` `loadBalancer` AKS-resursprovidern.

Följande konfiguration utförs av AKS.
   * En offentlig IP-adress etableras för utgående kluster.
   * Den offentliga IP-adressen tilldelas till lastbalanseringsresursen.
   * Serverpooler för lastbalanseraren konfigureras för agentnoder i klustret.

Nedan visas en nätverkstopologi som distribueras i AKS-kluster som standard, som använder `outboundType` en av `loadBalancer` .

![Diagram som visar ingående I P och utgående I P, där ingress-I P dirigerar trafik till en lastbalanserare som dirigerar trafik till och från ett internt kluster och annan trafik till utgående I P, som dirigerar trafik till Internet, M C R, azure-tjänster som krävs och A K S-kontrollplanet.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Utgående typ av användareDefinedRouting

> [!NOTE]
> Användning av utgående typ är ett avancerat nätverksscenario och kräver korrekt nätverkskonfiguration.

Om `userDefinedRouting` har angetts konfigurerar inte AKS utgående sökvägar automatiskt. Konfigurationen av utgående måste göras av dig.

AKS-klustret måste distribueras till ett befintligt virtuellt nätverk med ett undernät som har konfigurerats tidigare eftersom du måste upprätta explicit utgående data när du inte använder standardarkitekturen för lastbalanserare (SLB). Därför kräver den här arkitekturen att utgående trafik uttryckligen skickas till en apparat som en brandvägg, gateway, proxy eller att NAT (Network Address Translation) kan utföras av en offentlig IP-adress som tilldelats till standardlastbalanserare eller installation.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Skapa lastbalanserare med userDefinedRouting

AKS-kluster med en utgående typ av UDR får endast en standardlastbalanserare (SLB) när den första Kubernetes-tjänsten av typen "loadBalancer" distribueras. Lastbalanseraren konfigureras med en offentlig IP-adress för *inkommande* begäranden och en backend-pool för *inkommande* begäranden. Regler för inkommande trafik konfigureras av Azure-molnleverantören, men inga utgående offentliga **IP-adresser** eller regler för utgående trafik konfigureras på grund av en utgående typ av UDR. Din UDR är fortfarande den enda källan för utgående trafik.

Azure-lastbalanserare [debiteras inte förrän en regel har placerats.](https://azure.microsoft.com/pricing/details/load-balancer/)

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuera ett kluster med utgående typ av UDR och Azure Firewall

För att illustrera tillämpningen av ett kluster med utgående typ med hjälp av en användardefinierad väg kan ett kluster konfigureras i ett virtuellt nätverk med en Azure Firewall på ett eget undernät. Se det här exemplet på [begränsa utgående trafik med Azure Firewall-exempel](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> Utgående typ av UDR kräver att det finns en väg för 0.0.0.0/0 och nästa hoppmål för NVA (Network Virtual Appliance) i vägtabellen.
> Vägtabellen har redan standardvärdet 0.0.0.0/0 till Internet, utan en offentlig IP-adress till SNAT som bara lägger till den här vägen kommer inte att ge dig utgående. AKS verifierar att du inte skapar en 0.0.0.0/0-väg som pekar på Internet, utan istället till NVA eller gateway osv. När du använder en utgående typ av UDR skapas inte en offentlig IP-adress för lastbalanserare för **inkommande** begäranden om inte en tjänst av typen *lastbalanserare* har konfigurerats. En offentlig IP-adress **för utgående begäranden** skapas aldrig av AKS om en utgående typ av UDR har angetts.

## <a name="next-steps"></a>Nästa steg

Se [Översikt över UDR för Azure-nätverk.](../virtual-network/virtual-networks-udr-overview.md)

Se [hur du skapar, ändrar eller tar bort en vägtabell.](../virtual-network/manage-route-table.md)

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
