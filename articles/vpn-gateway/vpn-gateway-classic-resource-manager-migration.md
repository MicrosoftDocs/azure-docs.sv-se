---
title: VPN Gateway klassisk till Resource Manager-migrering | Microsoft Docs
description: Den här sidan innehåller en översikt över migreringen av VPN Gateway klassisk till Resource Manager.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96488212"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway klassisk till Resource Manager-migrering
VPN-gatewayer kan nu migreras från den klassiska distributions modellen till Resource Manager. Du kan läsa mer om Azure Resource Manager [funktioner och förmåner](../azure-resource-manager/management/overview.md). I den här artikeln beskriver vi hur du migrerar från klassiska distributioner till en nyare Resource Manager-baserad modell. 

VPN-gatewayer migreras som en del av VNet-migreringen från klassisk till Resource Manager. Den här migreringen görs ett VNet i taget. Det finns inga ytterligare krav vad gäller verktyg eller förutsättningar för migrering. Migrerings stegen är identiska med befintlig VNet-migrering och dokumenteras på [IaaS Resources-sidan](../virtual-machines/migration-classic-resource-manager-ps.md). Det finns ingen data Sök vägs nedtid under migreringen och därmed fortsätter befintliga arbets belastningar utan förlust av lokal anslutning under migreringen. Den offentliga IP-adress som är associerad med VPN-gatewayen ändras inte under migreringsprocessen. Detta innebär att du inte behöver konfigurera om den lokala routern när migreringen är klar.  

Modellen i Resource Manager skiljer sig från den klassiska modellen och består av virtuella nätverks-gatewayer, lokala nätverks-gatewayer och anslutnings resurser. Dessa representerar själva VPN-gatewayen, den lokala platsen som representerar lokalt adress utrymme och anslutningar mellan båda. När migreringen är klar är dina gatewayer inte tillgängliga i den klassiska modellen och alla hanterings åtgärder på virtuella nätverks-gatewayer, lokala nätverksgateway och anslutnings objekt måste utföras med hjälp av Resource Manager-modellen.

## <a name="supported-scenarios"></a>Scenarier som stöds
De flesta vanliga scenarier för VPN-anslutning täcks av klassisk till Resource Manager-migrering. De scenarier som stöds är-

* Peka på plats anslutning
* Plats-till-plats-anslutning med VPN Gateway ansluten till lokal plats
* VNet till VNet-anslutning mellan två virtuella nätverk med VPN-gatewayer
* Flera virtuella nätverk anslutna till samma plats på plats
* Anslutning till flera platser
* Tvingad tunnel aktive rad virtuella nätverk

Scenarier som inte stöds är-  

* VNet med både ExpressRoute-gateway och VPN Gateway stöds inte för närvarande.
* Överförings scenarier där VM-tillägg är anslutna till lokala servrar. Begränsningar för överföring av VPN-anslutning beskrivs nedan.

> [!NOTE]
> CIDR-verifiering i Resource Manager-modellen är mer strikt än den som finns i den klassiska modellen. Innan du migrerar ser du till att de klassiska adress intervallen uppfyller giltigt CIDR-format innan du påbörjar migreringen. CIDR kan verifieras med hjälp av vanliga CIDR-verifierare. VNet eller lokala platser med ogiltiga CIDR-intervall vid migrering skulle resultera i felaktigt tillstånd.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migrering av VNet till VNet-anslutning
VNet till VNet-anslutning i klassiskt uppnås genom att skapa en lokal plats representation av det anslutna VNet. Kunder behövde skapa två lokala platser som representerade de två virtuella nätverk som behövde anslutas tillsammans. De anslöt sedan till motsvarande virtuella nätverk med IPsec-tunneln för att upprätta anslutningar mellan de två virtuella nätverk. Den här modellen har hanterings utmaningar eftersom eventuella adress intervall ändringar i ett VNet också måste upprätthållas i motsvarande representation av den lokala platsen. Den här lösningen behövs inte längre i Resource Manager-modellen. Anslutningen mellan de två virtuella nätverk kan uppnås direkt med anslutnings typen "Vnet2Vnet" i anslutnings resursen. 

![Skärm bild av VNet till VNet-migrering.](./media/vpn-gateway-migration/migration1.png)

Under VNet-migrering upptäcker vi att den anslutna entiteten till det aktuella virtuella nätverkets VPN-gateway är ett annat VNet och ser till att när migreringen av båda virtuella nätverk har slutförts visas inte längre två lokala platser som representerar det andra virtuella nätverket. Den klassiska modellen av två VPN-gatewayer, två lokala platser och två anslutningar mellan dem omvandlas till Resource Manager-modellen med två VPN-gatewayer och två anslutningar av typen Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Överföring av VPN-anslutning
Du kan konfigurera VPN-gatewayer i en topologi så att lokal anslutning för ett VNet uppnås genom att ansluta till ett annat VNet som är direkt anslutet till den lokala platsen. Detta är en överföring av VPN-anslutning där instanser i det första virtuella nätverket är anslutna till lokala resurser via överföring till VPN-gatewayen i ett anslutet VNet som är direkt ansluten till en lokal plats. För att uppnå den här konfigurationen i den klassiska distributions modellen måste du skapa en lokal plats som har aggregerade prefix som representerar både det anslutna VNet och det lokala adress utrymmet. Den här omklassificerings lokala platsen ansluts sedan till det virtuella nätverket för att uppnå överförings anslutningen. Den här klassiska modellen har också liknande hanterings utmaningar eftersom alla ändringar i det lokala adress intervallet också måste upprätthållas på den lokala platsen som representerar mängden VNet och lokalt. Introduktionen av BGP-stöd i gatewayer som stöds i Resource Manager fören klar hanteringen eftersom anslutna gatewayer kan lära sig vägar från lokala platser utan manuell ändring av prefix.

![Skärm bild av scenariot för transit routning.](./media/vpn-gateway-migration/migration2.png)

Eftersom vi transformerar VNet till VNet-anslutning utan att behöva lokala platser förlorar överförings scenariot lokal anslutning för VNet som är indirekt ansluten till lokalt. Förlust av anslutning kan begränsas på följande två sätt efter att migreringen har slutförts – 

* Aktivera BGP på VPN-gatewayer som är anslutna till varandra och lokalt. Aktivering av BGP återställer anslutningar utan andra konfigurations ändringar eftersom vägar har lärts och annonser ATS mellan VNet-gatewayer. Observera att BGP-alternativet endast är tillgängligt på standard-och högre SKU: er.
* Upprätta en explicit anslutning från det virtuella nätverket till den lokala Nätverksgatewayen som representerar lokal plats. Detta kräver också att du ändrar konfigurationen på den lokala routern för att skapa och konfigurera IPsec-tunneln.

## <a name="next-steps"></a>Nästa steg
När du har lärt dig om stödet för migrering av VPN gateway går du till [plattforms stöd för migrering av IaaS-resurser från klassisk till Resource Manager](../virtual-machines/migration-classic-resource-manager-ps.md) för att komma igång.