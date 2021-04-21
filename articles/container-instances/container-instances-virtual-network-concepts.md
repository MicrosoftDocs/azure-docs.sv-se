---
title: Scenarier för att använda ett virtuellt nätverk
description: Scenarier, resurser och begränsningar för att distribuera containergrupper till ett virtuellt Azure-nätverk.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 6de99c68c3f05e4734dd46a579d28a6f1a3b824e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763783"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scenarier och resurser för virtuella nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) tillhandahåller säkra, privata nätverk för dina Azure-resurser och lokala resurser. Genom att distribuera containergrupper till ett virtuellt Azure-nätverk kan dina containrar kommunicera säkert med andra resurser i det virtuella nätverket. 

Den här artikeln innehåller bakgrundsinformation om scenarier, begränsningar och resurser för virtuella nätverk. Distributionsexempel med Hjälp av Azure CLI finns i [Distribuera containerinstanser till ett virtuellt Azure-nätverk.](container-instances-vnet.md)

> [!IMPORTANT]
> Distribution av containergrupper till ett virtuellt nätverk är allmänt tillgängligt för Linux-containrar, i de flesta regioner där Azure Container Instances är tillgänglig. Mer information finns i [Regioner och resurstillgänglighet.](container-instances-region-availability.md) 

## <a name="scenarios"></a>Scenarier

Containergrupper som distribueras till ett virtuellt Azure-nätverk möjliggör scenarier som:

* Direkt kommunikation mellan containergrupper i samma undernät
* Skicka [uppgiftsbaserade](container-instances-restart-policy.md) arbetsbelastningsutdata från containerinstanser till en databas i det virtuella nätverket
* Hämta innehåll för containerinstanser från en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) i det virtuella nätverket
* Aktivera containerkommunikation med lokala resurser via en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integrera med [Azure Firewall](../firewall/overview.md) för att identifiera utgående trafik från containern 
* Matcha namn via den interna Azure DNS för kommunikation med Azure-resurser i det virtuella nätverket, till exempel virtuella datorer
* Använda NSG-regler för att styra containeråtkomsten till undernät eller andra nätverksresurser

## <a name="unsupported-networking-scenarios"></a>Nätverksscenarier som inte stöds 

* **Azure Load Balancer** – Att placera Azure Load Balancer framför containerinstanser i en nätverkscontainergrupp stöds inte
* **Global peering för virtuella nätverk** – Global peering (ansluta virtuella nätverk mellan Azure-regioner) stöds inte
* **Offentlig IP- eller DNS-etikett** – Containergrupper som distribueras till ett virtuellt nätverk stöder för närvarande inte att exponera containrar direkt till Internet med en offentlig IP-adress eller ett fullständigt kvalificerat domännamn
* **Virtual Network NAT** – Containergrupper som distribueras till ett virtuellt nätverk stöder för närvarande inte användning av en NAT-gatewayresurs för utgående Internetanslutning.

## <a name="other-limitations"></a>Andra begränsningar

* För närvarande stöds endast Linux-containrar i en containergrupp som distribueras till ett virtuellt nätverk.
* Om du vill distribuera containergrupper till ett undernät får undernätet inte innehålla andra resurstyper. Ta bort alla befintliga resurser från ett befintligt undernät innan du distribuerar containergrupper till det eller skapa ett nytt undernät.
* Du kan inte använda en [hanterad identitet i](container-instances-managed-identity.md) en containergrupp som distribuerats till ett virtuellt nätverk.
* Du kan inte aktivera en [liveavsökning eller beredskapsavsökning](container-instances-liveness-probe.md) i en containergrupp som distribueras till ett virtuellt nätverk. [](container-instances-readiness-probe.md)
* På grund av de ytterligare nätverksresurser som ingår går distributionen till ett virtuellt nätverk vanligtvis långsammare än när du distribuerar en standardcontainerinstans.
* Om du ansluter din containergrupp till ett Azure Storage-konto måste du lägga till en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) för resursen.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Nödvändiga nätverksresurser

Det finns tre Azure Virtual Network-resurser som krävs för att distribuera [](#virtual-network) containergrupper till ett virtuellt nätverk: själva det virtuella nätverket, ett delegerat [undernät](#subnet-delegated) i det virtuella nätverket och en [nätverksprofil](#network-profile). 

### <a name="virtual-network"></a>Virtuellt nätverk

Ett virtuellt nätverk definierar det adressutrymme där du skapar ett eller flera undernät. Sedan distribuerar du Azure-resurser (t.ex. containergrupper) till undernäten i ditt virtuella nätverk.

### <a name="subnet-delegated"></a>Undernät (delegerat)

Undernät segmenterar det virtuella nätverket i separata adressutrymmen som kan användas av de Azure-resurser som du placerar i dem. Du skapar ett eller flera undernät i ett virtuellt nätverk.

Det undernät som du använder för containergrupper får endast innehålla containergrupper. När du först distribuerar en containergrupp till ett undernät delegerar Azure det undernätet till Azure Container Instances. När det har delegerats kan undernätet endast användas för containergrupper. Om du försöker distribuera andra resurser än containergrupper till ett delegerat undernät misslyckas åtgärden.

### <a name="network-profile"></a>Nätverksprofil

En nätverksprofil är en mall för nätverkskonfiguration för Azure-resurser. Den anger vissa nätverksegenskaper för resursen, till exempel undernätet som den ska distribueras till. När du först använder [kommandot az container create][az-container-create] för att distribuera en containergrupp till ett undernät (och därmed ett virtuellt nätverk) skapar Azure en nätverksprofil åt dig. Du kan sedan använda den nätverksprofilen för framtida distributioner till undernätet. 

Om du vill använda en Resource Manager-mall, YAML-fil eller en programmatisk metod för att distribuera en containergrupp till ett undernät måste du ange det fullständiga Resource Manager resurs-ID:t för en nätverksprofil. Du kan använda en profil som skapats tidigare med [az container create][az-container-create], eller skapa en profil med en Resource Manager mall (se [mallexempel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) och [referens](/azure/templates/microsoft.network/networkprofiles)). Om du vill hämta ID:t för en tidigare skapad profil använder du [kommandot az network profile list.][az-network-profile-list] 

I följande diagram har flera containergrupper distribuerats till ett undernät som delegerats till Azure Container Instances. När du har distribuerat en containergrupp till ett undernät kan du distribuera ytterligare containergrupper till den genom att ange samma nätverksprofil.

![Containergrupper i ett virtuellt nätverk][aci-vnet-01]

## <a name="next-steps"></a>Nästa steg

* Distributionsexempel med Azure CLI finns i Distribuera [containerinstanser till ett virtuellt Azure-nätverk.](container-instances-vnet.md)
* Information om hur du distribuerar ett nytt virtuellt nätverk, undernät, nätverksprofil och containergrupp med hjälp av en Resource Manager mall finns i Skapa en [Azure-containergrupp med VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).
* När du [använder Azure Portal](container-instances-quickstart-portal.md) för att skapa en containerinstans kan du även ange inställningar för ett nytt eller virtuellt nätverk på **fliken** Nätverk.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
