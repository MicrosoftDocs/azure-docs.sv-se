---
title: Scenarier för att använda ett virtuellt nätverk
description: Scenarier, resurser och begränsningar för att distribuera behållar grupper till ett virtuellt Azure-nätverk.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 2fb468a6a860bcc076043c30606922f43fbdb1ad
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882440"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scenarier och resurser för virtuella nätverk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) ger säker, privat nätverk för dina Azure-resurser och lokala resurser. Genom att distribuera behållar grupper till ett virtuellt Azure-nätverk kan dina behållare kommunicera säkert med andra resurser i det virtuella nätverket. 

Den här artikeln innehåller bakgrunds information om virtuella nätverks scenarier, begränsningar och resurser. Exempel på distributions exempel med Azure CLI finns i [distribuera behållar instanser i ett virtuellt Azure-nätverk](container-instances-vnet.md).

## <a name="scenarios"></a>Scenarier

Behållar grupper som distribueras till ett virtuellt Azure-nätverk möjliggör scenarier som:

* Direkt kommunikation mellan behållar grupper i samma undernät
* Skicka [uppgiftsbaserade](container-instances-restart-policy.md) arbets belastnings resultat från container instances till en databas i det virtuella nätverket
* Hämta innehåll för behållar instanser från en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) i det virtuella nätverket
* Aktivera container kommunikation med lokala resurser via en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integrera med [Azure-brandväggen](../firewall/overview.md) för att identifiera utgående trafik som härstammar från behållaren 
* Matcha namn via interna Azure DNS för kommunikation med Azure-resurser i det virtuella nätverket, till exempel virtuella datorer
* Använd NSG-regler för att styra container åtkomst till undernät eller andra nätverks resurser

## <a name="unsupported-networking-scenarios"></a>Nätverks scenarier som inte stöds 

* **Azure Load Balancer** -att placera ett Azure Load Balancer framför container instances i en nätverksansluten behållar grupp stöds inte
* Global peering för **virtuella nätverk** – global peering (anslutning av virtuella nätverk i Azure-regioner) stöds inte
* **Offentliga IP-eller DNS-etiketter** – behållar grupper som distribueras till ett virtuellt nätverk har för närvarande inte stöd för att exponera behållare direkt till Internet med en offentlig IP-adress eller ett fullständigt kvalificerat domän namn

## <a name="other-limitations"></a>Andra begränsningar

* För närvarande stöds endast Linux-behållare i en behållar grupp som distribueras till ett virtuellt nätverk.
* Under nätet kan inte innehålla andra resurs typer för att distribuera behållar grupper till ett undernät. Ta bort alla befintliga resurser från ett befintligt undernät innan du distribuerar behållar grupper till den, eller skapa ett nytt undernät.
* Du kan inte använda en [hanterad identitet](container-instances-managed-identity.md) i en behållar grupp som distribuerats till ett virtuellt nätverk.
* Du kan inte aktivera en [Direktmigrering](container-instances-liveness-probe.md) eller en [beredskaps avsökning](container-instances-readiness-probe.md) i en behållar grupp som distribueras till ett virtuellt nätverk.
* På grund av de ytterligare nätverks resurser som berörs är distributioner till ett virtuellt nätverk normalt långsammare än att distribuera en standard behållar instans.
* Om du ansluter din behållar grupp till ett Azure Storage konto måste du lägga till en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) till resursen.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="where-to-deploy"></a>Distributions plats

Följande regioner och maximalt antal resurser är tillgängliga för att distribuera en behållar grupp i ett virtuellt Azure-nätverk.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="required-network-resources"></a>Nödvändiga nätverks resurser

Det finns tre Azure Virtual Network-resurser som krävs för att distribuera behållar grupper till ett virtuellt nätverk: det [virtuella nätverket](#virtual-network) , ett [delegerat undernät](#subnet-delegated) i det virtuella nätverket och en [nätverks profil](#network-profile). 

### <a name="virtual-network"></a>Virtuellt nätverk

Ett virtuellt nätverk definierar det adress utrymme som du skapar ett eller flera undernät i. Sedan distribuerar du Azure-resurser (t. ex. behållar grupper) till under näten i det virtuella nätverket.

### <a name="subnet-delegated"></a>Undernät (delegerat)

Undernät segmenterar det virtuella nätverket i separata adress utrymmen som kan användas av de Azure-resurser som du placerar i dem. Du skapar ett eller flera undernät i ett virtuellt nätverk.

Det undernät som du använder för container grupper får bara innehålla behållar grupper. När du först distribuerar en behållar grupp till ett undernät delegerar Azure det under nätet till Azure Container Instances. Under nätet kan endast användas för container grupper när det har delegerats. Om du försöker distribuera andra resurser än container grupper till ett delegerat undernät, Miss lyckas åtgärden.

### <a name="network-profile"></a>Nätverksprofil

En nätverks profil är en mall för nätverks konfiguration för Azure-resurser. Den anger vissa nätverks egenskaper för resursen, till exempel det undernät som den ska distribueras till. När du först använder kommandot [AZ container Create][az-container-create] för att distribuera en behållar grupp till ett undernät (och därför ett virtuellt nätverk), skapar Azure en nätverks profil åt dig. Du kan sedan använda nätverks profilen för framtida distributioner till under nätet. 

Om du vill använda en Resource Manager-mall, YAML-fil eller en programmerings metod för att distribuera en behållar grupp till ett undernät måste du ange det fullständiga Resource Manager-resurs-ID: t för en nätverks profil. Du kan använda en profil som tidigare skapats med [AZ container Create][az-container-create]eller skapa en profil med hjälp av en Resource Manager-mall (se [mall-exempel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) och [referens](/azure/templates/microsoft.network/networkprofiles)). Om du vill hämta ID för en profil som skapats tidigare använder du kommandot [AZ Network Profile List][az-network-profile-list] . 

I följande diagram har flera behållar grupper distribuerats till ett undernät delegerade till Azure Container Instances. När du har distribuerat en behållar grupp till ett undernät kan du distribuera ytterligare behållar grupper till den genom att ange samma nätverks profil.

![Behållar grupper inom ett virtuellt nätverk][aci-vnet-01]

## <a name="next-steps"></a>Nästa steg

* Exempel på distributions exempel med Azure CLI finns i [distribuera behållar instanser i ett virtuellt Azure-nätverk](container-instances-vnet.md).
* Om du vill distribuera ett nytt virtuellt nätverk, undernät, nätverks profil och behållar grupp med hjälp av en Resource Manager-mall, se [skapa en Azure Container Group med VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).
* När du använder [Azure Portal](container-instances-quickstart-portal.md) för att skapa en behållar instans kan du också ange inställningar för ett nytt eller exsting virtuellt nätverk på fliken **nätverk** .


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
