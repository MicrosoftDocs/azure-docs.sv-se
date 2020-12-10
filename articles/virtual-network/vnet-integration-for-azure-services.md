---
title: Integrering av virtuella nätverk för Azure-tjänster för nätverks isolering
titlesuffix: Azure Virtual Network
description: I den här artikeln beskrivs olika metoder för att integrera en Azure-tjänst till ett virtuellt nätverk som gör det möjligt att på ett säkert sätt få åtkomst till Azure-tjänsten.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 814158ad68e63760c1f2a1bce3eeff812de3c13e
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938740"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrera Azure-tjänster med virtuella nätverk för nätverks isolering

Virtual Network-integration (VNet) för en Azure-tjänst gör det möjligt att låsa åtkomsten till tjänsten enbart till din virtuella nätverks infrastruktur. VNet-infrastrukturen inkluderar även peer-baserade virtuella nätverk och lokala nätverk.

VNet-integrering ger Azure-tjänster fördelarna med nätverks isolering och kan utföras av en eller flera av följande metoder:
- [Distribuera dedikerade instanser av tjänsten till ett virtuellt nätverk](virtual-network-service-endpoints-overview.md). Tjänsterna kan sedan nås privat i det virtuella nätverket och från lokala nätverk.
- Använd [privat slut punkt](../private-link/private-endpoint-overview.md) som ansluter dig privat och säkert till en tjänst som drivs av en [privat Azure-länk](../private-link/private-link-overview.md). Privat slut punkt använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten i det virtuella nätverket.
- Åtkomst till tjänsten med offentliga slut punkter genom att utöka ett virtuellt nätverk till tjänsten via [tjänstens slut punkter](virtual-network-service-endpoints-overview.md). Med tjänst slut punkter kan tjänst resurser skyddas till det virtuella nätverket.
- Använda [service märken](service-tags-overview.md) för att tillåta eller neka trafik till dina Azure-resurser till och från offentliga IP-slutpunkter.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Distribuera dedikerade Azure-tjänster i virtuella nätverk

När du distribuerar dedikerade Azure-tjänster i ett virtuellt nätverk kan du kommunicera med tjänst resurserna privat via privata IP-adresser.

![Distribuera dedikerade Azure-tjänster i virtuella nätverk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Det finns följande funktioner för att distribuera en dedikerad Azure-tjänst i det virtuella nätverket:
- Resurser i det virtuella nätverket kan kommunicera med varandra privat via privata IP-adresser. Exempel: direkt överföring av data mellan HDInsight och SQL Server som körs på en virtuell dator i det virtuella nätverket.
- Lokala resurser kan komma åt resurser i ett virtuellt nätverk med hjälp av privata IP-adresser via en plats-till-plats-VPN (VPN Gateway) eller ExpressRoute.
- Virtuella nätverk kan peer-kopplas för att göra det möjligt för resurser i de virtuella nätverken att kommunicera med varandra, med hjälp av privata IP-adresser.
- Tjänst instanser i ett virtuellt nätverk hanteras vanligt vis helt av Azure-tjänsten. Detta omfattar övervakning av hälso tillståndet för resurser och skalning med belastningen.
- Tjänst instanser distribueras till ett undernät i ett virtuellt nätverk. Inkommande och utgående nätverks åtkomst för under nätet måste öppnas via nätverks säkerhets grupper, enligt de rikt linjer som tillhandahålls av tjänsten.
- Vissa tjänster har också begränsningar för det undernät som de distribueras i, vilket begränsar tillämpningen av principer, vägar eller kombinerar virtuella datorer och tjänst resurser i samma undernät. Sök efter varje tjänst enligt de begränsningar som de kan ändra med tiden. Exempel på sådana tjänster är Azure NetApp Files, dedikerad HSM, Azure Container Instances App Service.
- Om du vill kan tjänsterna kräva ett delegerat undernät som en explicit identifierare som ett undernät kan vara värd för en viss tjänst. Genom att delegera, får tjänster explicita behörigheter att skapa tjänstespecifika resurser i det delegerade under nätet.
- Se ett exempel på ett REST API-svar i ett virtuellt nätverk med ett delegerat undernät. En omfattande lista över tjänster som använder den delegerade under nät modellen kan hämtas via tillgängliga Delegerings-API: er.

En lista över tjänster som kan distribueras till ett virtuellt nätverk finns i [distribuera dedikerade Azure-tjänster i virtuella nätverk](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Privat länk och privata slut punkter

Du kan använda privata slut punkter för att tillåta ingress av händelser direkt från ditt virtuella nätverk till Azure-resurs säkert över en privat länk utan att gå via det offentliga Internet. En privat slut punkt är ett särskilt nätverks gränssnitt för en Azure-tjänst i det virtuella nätverket. När du skapar en privat slut punkt för din Azure-resurs ger den säker anslutning mellan klienter i ditt virtuella nätverk och Azure-resursen. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och Azure-tjänsten använder en säker privat länk.

I följande exempel visas privat åtkomst till en Event Grid resurs privat slut punkt som ger säker anslutning mellan klienter i ett virtuellt nätverk och Event Grid resurs.

![Privat åtkomst för SQL DB-resurs med privat slut punkt](./media/network-isolation/architecture-diagram.png)

Mer information om privata länkar och en lista över Azure-tjänster som stöds finns i [Vad är en privat länk?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Tjänstslutpunkter
VNet-tjänstens slut punkt ger säker och direkt anslutning till Azure-tjänster via en optimerad väg över Azure stamnät nätverket. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Med tjänstens slut punkter kan privata IP-adresser i VNet komma åt slut punkten för en Azure-tjänst utan att behöva en offentlig IP-adress på det virtuella nätverket.

![Skydda Azure-tjänster i virtuella nätverk](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Mer information finns i [tjänst slut punkter för virtuella nätverk](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>Tjänsttaggar

En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Med hjälp av service märken kan du definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) eller [Azure-brandvägg](https://docs.microsoft.com/azure/firewall/service-tags). Genom att ange service tag-namnet (till exempel AzureEventGrid) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst.

![Tillåta eller neka trafik med hjälp av service märken](./media/network-isolation/service-tags.png)

Du kan använda service märken för att uppnå nätverks isolering och skydda dina Azure-resurser från det allmänna Internet samtidigt som du får åtkomst till Azure-tjänster som har offentliga slut punkter. Skapa regler för inkommande/utgående nätverks säkerhets grupper för att neka trafik till/från **Internet** och tillåta trafik till/från **AzureCloud** eller andra [tillgängliga Service märken](service-tags-overview.md#available-service-tags) för vissa Azure-tjänster.

Mer information om service märken och Azure-tjänster som stöder dem finns i [Översikt över tjänst Taggar](service-tags-overview.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du integrerar din app med ett Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).
- Lär dig hur du [begränsar åtkomsten till resurser med hjälp av service märken](tutorial-restrict-network-access-to-resources.md).
- Lär dig hur du [ansluter privat till ett Azure Cosmos-konto med hjälp av en privat Azure-länk](../private-link/create-private-endpoint-cosmosdb-portal.md).