---
title: 'Azure-ExpressRoute: flytta en offentlig peering till Microsoft-peering'
description: Den här artikeln visar hur du flyttar din offentliga peering till Microsoft-peering på ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 608d6c87442821e904fde16c6b75841fe792e3f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206296"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Flytta offentlig peering till Microsoft-peering

Den här artikeln hjälper dig att flytta en offentlig peering-konfiguration till Microsoft-peering utan drift avbrott. ExpressRoute stöder användning av Microsoft-peering med flödesfilter för Azure PaaS-tjänster, till exempel Azure Storage och Azure SQL Database. Nu behöver du endast en routningsdomän för åtkomst till Microsoft PaaS- och SaaS-tjänster. Du kan använda flödesfilter för att selektivt presentera PaaS-tjänstprefixen för Azure-regioner du vill använda.

Offentlig Azure-peering har en NAT-IP-adress kopplad till varje BGP-session. Med Microsoft-peering kan du konfigurera dina egna NAT-allokeringar, samt använda väg filter för annonser av selektivt prefix. Offentlig peering är en enkelriktad tjänst som använder den anslutning som alltid initieras från WAN till Microsoft Azure-tjänster. Microsoft Azure Services kommer inte att kunna initiera anslutningar till nätverket via den här routningsdomänen.

När offentlig peering har Aktiver ATS kan du ansluta till alla Azure-tjänster. Vi tillåter inte att du selektivt väljer tjänster som vi annonserar vägar till. Även om Microsoft-peering är en dubbelriktad anslutning där anslutning kan initieras från Microsoft Azure-tjänsten tillsammans med ditt WAN. Mer information om routningsdomäner och peering finns i ExpressRoute- [kretsar och routningsdomäner](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

För att ansluta till Microsoft-peering måste du konfigurera och hantera NAT. Din anslutnings leverantör kan konfigurera och hantera NAT som en hanterad tjänst. Om du planerar att komma åt Azure-PaaS och Azure SaaS-tjänsterna på Microsoft-peering är det viktigt att du har rätt storlek på NAT IP-adresspoolen. Mer information om NAT för ExpressRoute finns i NAT- [krav för Microsoft-peering](expressroute-nat.md#nat-requirements-for-microsoft-peering). När du ansluter till Microsoft via Azure ExpressRoute (Microsoft-peering) har du flera länkar till Microsoft. En länk är din befintliga Internetanslutning och den andra är via ExpressRoute. En del trafik till Microsoft kan gå genom Internet men komma tillbaka via ExpressRoute eller tvärtom.

![Dubbelriktad anslutning](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> NAT:s IP-pool som annonseras till Microsoft får inte annonseras till Internet. Detta bryter anslutningen till andra Microsoft-tjänster.

Se [asymmetrisk routning med flera nätverks Sök vägar](./expressroute-asymmetric-routing.md) för varningar från asymmetrisk routning innan du konfigurerar Microsoft-peering.

* Om du använder offentlig peering och för närvarande har IP-nätverks regler för offentliga IP-adresser som används för att komma åt [Azure Storage](../storage/common/storage-network-security.md) eller [Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md), måste du se till att NAT IP-poolen som kon figurer ATS med Microsoft-peering ingår i listan över offentliga IP-adresser för Azure Storage-kontot eller Azure SQL-kontot.<br>
* Använd stegen i den här artikeln i den ordning som de presenteras för att flytta till Microsoft-peering utan drift avbrott.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. skapa Microsoft-peering

Om Microsoft-peering inte har skapats kan du använda någon av följande artiklar för att skapa Microsoft-peering. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be anslutnings leverantören att aktivera Microsoft-peering för din krets.

Om skikt 3 hanteras av dig krävs följande information innan du fortsätter:

* Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här under nätet tilldelar du den första användbara IP-adressen till din router eftersom Microsoft använder den andra användbara IP-adressen för sin router.<br>
* Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här under nätet tilldelar du den första användbara IP-adressen till din router eftersom Microsoft använder den andra användbara IP-adressen för sin router.<br>
* Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.<br>
* AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.<br>
* Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.<br>
* Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.

* **Valfritt** -kund-ASN: om du annonserar prefix som inte har registrerats för peering som ett nummer kan du ange det as-nummer som de är registrerade för.<br>
* **Valfritt** – en MD5-hash om du väljer att använda en.

Detaljerade anvisningar om hur du aktiverar Microsoft-peering finns i följande artiklar:

* [Skapa Microsoft-peering med Azure Portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Skapa Microsoft-peering med Azure PowerShell](expressroute-howto-routing-arm.md#msft)<br>
* [Skapa Microsoft-peering med Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Verifiera att Microsoft-peering har Aktiver ATS

Kontrol lera att Microsoft-peering är aktiverat och att de annonserade offentliga prefixen är i det konfigurerade läget.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Konfigurera och koppla ett flödes filter till kretsen

Som standard annonserar inte nya Microsoft-peering några prefix förrän ett flödes filter är kopplat till kretsen. När du skapar en regel för flödes filter kan du ange en lista över service grupper för Azure-regioner som du vill använda för Azure PaaS Services. Detta ger dig flexibiliteten att filtrera vägarna efter behov, som du ser på följande skärm bild:

![Slå samman offentlig peering](./media/how-to-move-peering/routefilter.jpg)

Konfigurera väg filter med någon av följande artiklar:

* [Konfigurera väg filter för Microsoft-peering med Azure Portal](how-to-routefilter-portal.md)<br>
* [Konfigurera väg filter för Microsoft-peering med Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurera väg filter för Microsoft-peering med Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. ta bort offentlig peering

När du har kontrollerat att Microsoft-peering har kon figurer ATS och de prefix som du vill använda annonseras korrekt på Microsoft-peering kan du ta bort den offentliga peer kopplingen. Använd någon av följande artiklar om du vill ta bort offentlig peering:

* [Ta bort offentlig Azure-peering med Azure PowerShell](about-public-peering.md#powershell)
* [Ta bort offentlig Azure-peering med CLI](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Visa peering
  
Du kan se en lista över alla ExpressRoute-kretsar och peering-kopplingar i Azure Portal. Mer information finns i [Visa information om Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).