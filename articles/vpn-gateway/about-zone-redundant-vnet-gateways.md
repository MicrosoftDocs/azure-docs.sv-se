---
title: Om zoner – redundanta virtuella nätverksgateway i Azure-tillgänglighetszoner
description: Distribuera VPN-och ExpressRoute-gatewayer i Azure-tillgänglighetszoner för att få återhämtning, skalbarhet och högre tillgänglighet till virtuella nätverksgateway.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2d0e6464e81c0b0d04b9a0f483bcd14f075fa399
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065170"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Om zoner – redundanta virtuella nätverksgateway i Azure-tillgänglighetszoner

Du kan distribuera VPN-och ExpressRoute-gatewayer i [Azure-tillgänglighetszoner](../availability-zones/az-overview.md). Det ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribution av gatewayer i Azure-tillgänglighetszoner skiljer gatewayerna åt fysiskt och logiskt i en region, samtidigt som din lokala nätverksanslutning till Azure skyddas mot fel på zonnivå.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zonredundant gateway

Om du vill distribuera dina virtuella nätverksgateway automatiskt mellan tillgänglighets zoner kan du använda zoner som är redundanta virtuella nätverk. Med Zone-redundanta gatewayer kan du dra nytta av zon återhämtning för att få åtkomst till dina verksamhets kritiska, skalbara tjänster på Azure.

<br>
<br>

![zon – redundanta gateways-bilder](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Zonindelade-gatewayer

Om du vill distribuera gateways i en speciell zon kan du använda zonindelade-gatewayer. När du distribuerar en zonindelade-Gateway distribueras alla instanser av gatewayen i samma tillgänglighets zon.

<br>
<br>

![bild av zonindelade-gatewayer](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Gateway-SKU:er

Zoner-redundanta och zonindelade-gatewayer är tillgängliga som nya gateway SKU: er. Vi har lagt till nya virtuella nätverksgateway-SKU: er i Azure AZ-regioner. Dessa SKU: er liknar motsvarande befintliga SKU: er för ExpressRoute och VPN Gateway, förutom att de är relaterade till zoner-redundanta och zonindelade-gatewayer. Du kan identifiera dessa SKU: er med "AZ" i SKU-namnet.

Information om Gateway-SKU: er finns i [VPN gateway SKU: er](vpn-gateway-about-vpngateways.md#gwsku) och [ExpressRoute Gateway SKU: er](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>Offentliga IP-SKU: er

Zone-redundanta gatewayer och zonindelade-gatewayer båda förlitar sig på Azures offentliga IP Resource *standard* SKU. Konfigurationen av den offentliga Azure-IP-resursen avgör om den gateway som du distribuerar är en zon-redundant eller zonindelade. Om du skapar en offentlig IP-resurs med en *grundläggande* SKU, kommer gatewayen inte att ha någon zon redundans och gateway-resurserna kommer att vara regionala.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zonredundant gateway

När du skapar en offentlig IP-adress med hjälp av **standard** -offentliga IP SKU utan att ange en zon, varierar beteendet beroende på om gatewayen är en VPN-gateway eller en ExpressRoute-Gateway. 

* För en VPN-gateway distribueras de två Gateway-instanserna i valfri 2 av dessa tre zoner för att ge zon-redundans. 
* För en ExpressRoute-Gateway, eftersom det kan finnas fler än två instanser, kan gatewayen sträcka sig över alla tre zoner.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Zonindelade-gatewayer

När du skapar en offentlig IP-adress med hjälp av **standard** -offentliga IP-SKU: n och anger zonen (1, 2 eller 3), kommer alla gateway-instanser att distribueras i samma zon.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionala gatewayer

När du skapar en offentlig IP-adress med hjälp av den **grundläggande** offentliga IP-SKU: n distribueras gatewayen som en regional gateway och har ingen zon-redundans inbyggd i gatewayen.

## <a name="faq"></a><a name="faq"></a>ASSURANCE

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Vad kommer att ändras när jag distribuerar dessa nya SKU: er?

I ditt perspektiv kan du distribuera dina gateways med zon-redundans. Det innebär att alla instanser av gatewayerna distribueras över Azure-tillgänglighetszoner och att varje tillgänglighets zon är ett annat fel och en annan uppdaterings domän. Detta gör dina gatewayer mer pålitliga, tillgängliga och elastiska för zon haverier.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure Portal?

Ja, du kan använda Azure Portal för att distribuera de nya SKU: erna. Du kommer dock bara att se dessa nya SKU: er i de Azure-regioner som har Azure-tillgänglighetszoner.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Vilka regioner är tillgängliga för mig att använda de nya SKU: erna?

De nya SKU: erna är tillgängliga i Azure-regioner som har Azure-tillgänglighetszoner centrala USA, centrala Frankrike, Nord Europa, Västeuropa och västra USA 2 regioner, östra USA, östra USA 2, Sydostasien, Östra Japan, Storbritannien, södra. Nu kommer vi att göra zoner-redundanta gatewayer tillgängliga i andra offentliga Azure-regioner.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra/migrera/uppgradera mina befintliga virtuella nätverks-gatewayer till zoner-redundanta eller zonindelade-gatewayer?

Det finns för närvarande inte stöd för migrering av befintliga virtuella nätverksgateway till zoner-redundanta eller zonindelade-gatewayer. Du kan dock ta bort din befintliga gateway och återskapa en zon-redundant eller zonindelade Gateway.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera både VPN-och Express-flödesnoder i samma virtuella nätverk?

Samtidig användning av både VPN-och Express-flödesnoder i samma virtuella nätverk stöds. Du bör dock reservera ett/27 IP-adressintervall för gateway-undernätet.

## <a name="next-steps"></a>Nästa steg

[Skapa en zonredundant virtuell nätverksgateway](create-zone-redundant-vnet-gateway.md)
