---
title: Vilka arkitektur alternativ för Azure Firewall Manager?
description: Jämför och kontrast med hubb virtuellt nätverk eller säkra arkitekturer för virtuella hubbar med Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563156"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Vilka arkitektur alternativ för Azure Firewall Manager?

Azure Firewall Manager kan tillhandahålla säkerhets hantering för två typer av nätverks arkitektur:

- **skyddad virtuell hubb**

   En [virtuell WAN-hubb i Azure](../virtual-wan/virtual-wan-about.md#resources) är en Microsoft-hanterad resurs som gör att du enkelt kan skapa Hubbs-och eker-arkitekturer. När säkerhets-och routningsprinciperna är associerade med en sådan hubb kallas den för en *[säker virtuell hubb](secured-virtual-hub.md)*. 
- **hubb virtuellt nätverk**

   Det här är ett standard Azure Virtual Network som du skapar och hanterar själv. När säkerhets principer är associerade med en sådan hubb kallas det ett *nav virtuellt nätverk*. För närvarande stöds endast Azure Firewall-principen. Du kan använda peer-ekrar för virtuella nätverk som innehåller dina arbets belastnings servrar och-tjänster. Du kan också hantera brand väggar i fristående virtuella nätverk som inte är peer-kopplade till ekrar.

## <a name="comparison"></a>Jämförelse

I följande tabell jämförs dessa två arkitektur alternativ och kan hjälpa dig att avgöra vilken som passar din organisations säkerhets krav:


|  |**Virtuellt hub-nätverk**|**Skyddad virtuell hubb**  |
|---------|---------|---------|
|**Underliggande resurs**     |Virtuellt nätverk|Virtuell WAN-hubb|
|**Hubb & eker**     |Använder peering för virtuella nätverk|Automatiserad användning av hubb för virtuell nätverks anslutning|
|**Lokal anslutning**     |VPN Gateway upp till 10 Gbit/s och 30 S2S-anslutningar; ExpressRoute|Mer skalbar VPN Gateway upp till 20 Gbit/s och 1000 S2S-anslutningar; Express Route|
|**Automatisk gren anslutning med SDWAN**      |Stöds inte|Stöds|
|**Hubbar per region**     |Flera virtuella nätverk per region|Enskild virtuell hubb per region. Flera hubbar kan användas med flera virtuella WAN|
|**Azure-brandvägg – flera offentliga IP-adresser**      |Kunden tillhandahöll|Automatiskt genererad|
|**Azure Firewall-Tillgänglighetszoner**     |Stöds|Ännu inte tillgängligt|
|**Avancerad Internet säkerhet med säkerhet från tredje part som tjänst partner**     |Kund etablerade och hanterade VPN-anslutningar till partner tjänsten som du väljer|Automatiserad via säkerhets partner leverantörs flöde och partner hanterings upplevelse|
|**Centraliserad flödes hantering för att dirigera trafik till hubben**     |Kundhanterad användardefinierad väg|Stöds med BGP|
|**Stöd för flera säkerhetsproviders**|Stöds med manuellt konfigurerad Tvingad tunnel trafik till brand väggar från tredje part|Automatiskt stöd för två säkerhets leverantörer: Azure-brandvägg för privat trafik filtrering och tredje part för Internet filtrering|
|**Brandvägg för webbaserade program på Application Gateway** |Stöds i Virtual Network|Stöds för närvarande i eker-nätverk|
|**Virtuell nätverks installation**|Stöds i Virtual Network|Stöds för närvarande i eker-nätverk|
|**Azure DDoS Protection standard support**|Ja|Nej|

## <a name="next-steps"></a>Nästa steg

- Läs [Översikt över Azure Firewall Manager-distribution](deployment-overview.md)
- Läs mer om [skyddade virtuella nav](secured-virtual-hub.md).