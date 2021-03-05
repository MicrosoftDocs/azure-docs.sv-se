---
title: Anpassa användardefinierade vägar (UDR) i Azure Kubernetes service (AKS)
description: Lär dig hur du definierar en anpassad utgående väg i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: 72ba90510afb00ee001c97612e88f452039f53a4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182136"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Anpassa utgående kluster med ett User-Defined väg

Utgående från ett AKS-kluster kan anpassas så att de passar vissa scenarier. Som standard tillhandahåller AKS en standard-SKU Load Balancer som ska ställas in och användas för utgående trafik. Men standard installationen kanske inte uppfyller kraven i alla scenarier om offentliga IP-adresser inte tillåts eller om ytterligare hopp krävs för utgående trafik.

Den här artikeln beskriver hur du anpassar ett klusters utgående väg för att stödja anpassade nätverks scenarier, till exempel sådana som inte tillåter offentliga IP-adresser och kräver att klustret placeras bakom en virtuell nätverks installation (NVA).

## <a name="prerequisites"></a>Förutsättningar
* Azure CLI-version 2.0.81 eller senare
* API-version av `2020-01-01` eller större


## <a name="limitations"></a>Begränsningar
* OutboundType kan bara definieras i klustrets skapande tid och kan inte uppdateras efteråt.
* Inställningen `outboundType` kräver AKS-kluster med `vm-set-type` `VirtualMachineScaleSets` och `load-balancer-sku` `Standard` .
* Inställningen `outboundType` till värdet `UDR` kräver en användardefinierad väg med giltig utgående anslutning för klustret.
* Inställningen `outboundType` till värdet innebär att `UDR` den inkommande käll-IP-vägen till belastningsutjämnaren **inte matchar** klustrets utgående mål adress för utgående trafik.

## <a name="overview-of-outbound-types-in-aks"></a>Översikt över utgående typer i AKS

Ett AKS-kluster kan anpassas med en unik `outboundType` typ `loadBalancer` eller `userDefinedRouting` .

> [!IMPORTANT]
> Utgående typ påverkar bara utgående trafik i klustret. Mer information finns i Konfigurera ingångs [styrenheter](ingress-basic.md).

> [!NOTE]
> Du kan använda en egen [routningstabell][byo-route-table] med UDR och Kubernetes-nätverk. Se till att kluster identiteten (tjänstens huvud namn eller hanterad identitet) har deltagar behörighet till den anpassade väg tabellen.

### <a name="outbound-type-of-loadbalancer"></a>Utgående typ av loadBalancer

Om `loadBalancer` är inställt, slutför AKS följande konfiguration automatiskt. Belastningsutjämnaren används för utgående trafik genom en AKS tilldelad offentlig IP. En utgående typ som `loadBalancer` stöder Kubernetes-tjänster av typen `loadBalancer` , som förväntar sig utgående från belastningsutjämnaren som skapats av AKS-resurs leverantören.

Följande konfiguration görs av AKS.
   * En offentlig IP-adress har allokerats för utgående kluster.
   * Den offentliga IP-adressen är tilldelad till belastnings Utjämnings resursen.
   * Backend-pooler för belastningsutjämnaren konfigureras för agent-noder i klustret.

Nedan är en nätverkstopologi som distribueras i AKS-kluster som standard, som använder `outboundType` sig av `loadBalancer` .

![Diagrammet visar ingångs-och utgångs uppgifter i p, där ingången I P dirigerar trafik till en belastningsutjämnare som dirigerar trafik till och från ett internt kluster och annan trafik till utgående I P, som dirigerar trafik till Internet, M C R, Azure-tjänster som krävs och ett K S kontroll plan.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Utgående typ av userDefinedRouting

> [!NOTE]
> Använd utgående typ är ett avancerat nätverks scenario och kräver rätt nätverks konfiguration.

Om `userDefinedRouting` är inställt konfigurerar AKS inte utgående sökvägar automatiskt. Den utgående installationen måste göras av dig.

AKS-klustret måste distribueras till ett befintligt virtuellt nätverk med ett undernät som tidigare har kon figurer ATS på grund av att det inte går att använda en SLB-arkitektur (standard Load Balancer). Därför kräver den här arkitekturen explicit sändning av utgående trafik till en enhet som en brand vägg, Gateway, proxy eller för att tillåta NAT (Network Address Translation) att utföras av en offentlig IP-adress som tilldelas till standard belastnings utjämningen eller enheten.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Skapa belastningsutjämnare med userDefinedRouting

AKS-kluster med en utgående typ av UDR får en standard Load Balancer (SLB) endast när den första Kubernetes-tjänsten av typen loadBalancer har distribuerats. Belastningsutjämnaren konfigureras med en offentlig IP-adress för *inkommande* begär Anden och en backend-pool för *inkommande* begär Anden. Inkommande regler konfigureras av Azure Cloud Provider, men **ingen utgående offentlig IP-adress eller utgående regler** har kon figurer ATS som ett resultat av en utgående typ av UDR. Din UDR är fortfarande den enda källan för utgående trafik.

Azure Load Balancer debiteras [inte förrän en regel har placerats](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuera ett kluster med utgående typ av UDR och Azure-brandvägg

För att illustrera programmet för ett kluster med utgående typ med en användardefinierad väg, kan ett kluster konfigureras i ett virtuellt nätverk med en Azure-brandvägg i sitt eget undernät. Se det här exemplet på [exemplet begränsa utgående trafik med Azure Firewall](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> Den utgående typen av UDR kräver att det finns en väg för 0.0.0.0/0 och nästa hopp mål för NVA (virtuell nätverks installation) i routningstabellen.
> Routningstabellen har redan ett standardvärde 0.0.0.0/0 till Internet, utan en offentlig IP-adress till SNAT som du inte kommer att ange för att lägga till den här vägen. AKS validerar att du inte skapar en router med 0.0.0.0/0 som pekar på Internet utan i stället för NVA eller gateway osv. När du använder en utgående typ av UDR, skapas ingen offentlig IP- **adress för belastningsutjämnaren om inte** en tjänst av typen *Loadbalancer* har kon figurer ATS. En offentlig IP-adress för **utgående begär Anden** skapas aldrig av AKS om en utgående typ av UDR har angetts.

## <a name="next-steps"></a>Nästa steg

Se [Översikt över Azure Networking-UDR](../virtual-network/virtual-networks-udr-overview.md).

Se [hur du skapar, ändrar eller tar bort en](../virtual-network/manage-route-table.md)routningstabell.

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
