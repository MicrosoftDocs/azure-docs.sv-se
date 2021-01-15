---
title: Virtuellt nätverk för Azure-tjänster
titlesuffix: Azure Virtual Network
description: Lär dig hur du distribuerar dedikerade Azure-tjänster i ett virtuellt nätverk och lär dig mer om de funktioner som distributionerna tillhandahåller.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: e4fdab5a4bcf73af2be453367ef2dea5dd8929dc
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221127"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Distribuera dedikerade Azure-tjänster i virtuella nätverk

När du distribuerar dedikerade Azure-tjänster i ett [virtuellt nätverk](virtual-networks-overview.md)kan du kommunicera med tjänst resurserna privat via privata IP-adresser.

![Tjänster som distribueras i ett virtuellt nätverk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Det finns följande funktioner för att distribuera tjänster i ett virtuellt nätverk:

- Resurser i det virtuella nätverket kan kommunicera med varandra privat via privata IP-adresser. Exempel: direkt överföring av data mellan HDInsight och SQL Server som körs på en virtuell dator i det virtuella nätverket.
- Lokala resurser kan komma åt resurser i ett virtuellt nätverk med hjälp av privata IP-adresser via en [plats-till-plats-VPN (VPN gateway)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) eller [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverk kan [peer](virtual-network-peering-overview.md) -kopplas för att göra det möjligt för resurser i de virtuella nätverken att kommunicera med varandra, med hjälp av privata IP-adresser.
- Tjänst instanser i ett virtuellt nätverk hanteras vanligt vis helt av Azure-tjänsten. Detta omfattar övervakning av hälso tillståndet för resurser och skalning med belastningen.
- Tjänst instanser distribueras till ett undernät i ett virtuellt nätverk. Inkommande och utgående nätverks åtkomst för under nätet måste öppnas via [nätverks säkerhets grupper](./network-security-groups-overview.md#network-security-groups), enligt de rikt linjer som tillhandahålls av tjänsten.
- Vissa tjänster har också begränsningar för det undernät som de distribueras i, vilket begränsar tillämpningen av principer, vägar eller kombinerar virtuella datorer och tjänst resurser i samma undernät. Sök efter varje tjänst enligt de begränsningar som de kan ändra med tiden. Exempel på sådana tjänster är Azure NetApp Files, dedikerad HSM, Azure Container Instances App Service. 
- Om du vill kan tjänsterna kräva ett [delegerat undernät](virtual-network-manage-subnet.md#add-a-subnet) som en explicit identifierare som ett undernät kan vara värd för en viss tjänst. Genom att delegera, får tjänster explicita behörigheter att skapa tjänstespecifika resurser i det delegerade under nätet.
- Se ett exempel på ett REST API-svar i ett [virtuellt nätverk med ett delegerat undernät](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). En omfattande lista över tjänster som använder den delegerade under nät modellen kan hämtas via [tillgängliga Delegerings](/rest/api/virtualnetwork/availabledelegations/list) -API: er.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Tjänster som kan distribueras till ett virtuellt nätverk

|Kategori|Tjänst| Dedikerat<sup>1</sup> undernät
|-|-|-|
| Compute | Virtuella datorer: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Moln tjänst](/previous-versions/azure/reference/jj156091(v=azure.100)): endast virtuellt nätverk (klassiskt)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nej <br/> Nej <br/> Nej <br/> Nr<sup>2</sup>
| Nätverk | [Application Gateway-WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Virtuella nätverks enheter](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| Ja <br/> Ja <br/> Ja <br/> Ja <br/> Nej
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Hanterad Azure SQL-instans](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analys | [Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nr<sup>2</sup> <br/> Nr<sup>2</sup> <br/> 
| Identitet | [Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service motor](https://github.com/Azure/acs-engine) med [plugin-programmet](https://github.com/Azure/acs-engine/tree/master/examples/vnet) Azure Virtual Network cni<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Nr<sup>2</sup><br/> Ja <br/><br/> Nej <br/> Ja
| Webben | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja <br/> Ja
| Värdbaserad | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| Azure Spring Cloud | [Distribuera i Azure Virtual Network (VNet-insprutning)](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| Yes <br/>
| | |

<sup>1</sup> dedikerat innebär att endast tjänstespecifika resurser kan distribueras i det här under nätet och inte kan kombineras med Customer VM/VMSSs <br/> 
<sup>2</sup> vi rekommenderar att du har dessa tjänster i ett dedikerat undernät, men inte ett obligatoriskt krav som ställs av tjänsten.