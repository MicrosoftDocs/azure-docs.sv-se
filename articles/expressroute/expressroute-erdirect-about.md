---
title: Om Azure ExpressRoute Direct
description: 'Lär dig mer om viktiga funktioner i Azure ExpressRoute Direct och information som behövs för att publicera till ExpressRoute Direct, till exempel tillgängliga SKU: er och tekniska krav.'
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 4b129a218f0fe90f632adef1325288b3f8d97d16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585969"
---
# <a name="about-expressroute-direct"></a>Om ExpressRoute Direct

ExpressRoute Direct ger dig möjlighet att ansluta direkt till Microsofts globala nätverk vid peering-platser som distribueras strategiskt över hela världen. ExpressRoute Direct tillhandahåller dubbla 100 Gbit/s-anslutningar med 10 Gbit/s som stöder aktiv/aktiv anslutning i skala.

Viktiga funktioner som ExpressRoute Direct tillhandahåller inkluderar, men är inte begränsade till:

* Stora datainmatningar till tjänster som Storage och Cosmos DB
* Fysisk isolering för branscher som är reglerade och kräver dedikerad och isolerad anslutning som: Bank, myndigheter och detalj handel
* Detaljerad kontroll över kretsfördelning utifrån affärsenheter

## <a name="onboard-to-expressroute-direct"></a>Publicera till ExpressRoute Direct

Innan du använder ExpressRoute Direct måste du först registrera din prenumeration. Registrera genom att köra följande kommandon med hjälp av Azure PowerShell:

1.  Logga in på Azure och välj den prenumeration som du vill registrera.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registrera din prenumeration för offentlig för hands version med följande kommando:
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

När du har registrerat dig kontrollerar du att **Microsoft. Network** Resource provider har registrerats i din prenumeration. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören.

1. Få åtkomst till dina prenumerations inställningar enligt beskrivningen i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

1. Kontrol lera att **Microsoft. Network** Provider visar en **registrerad** status för **resurs leverantörer** i din prenumeration. Om Microsoft. Network Resource-providern inte finns i listan över registrerade providers lägger du till den.

Om du börjar använda ExpressRoute Direct och ser att det inte finns några tillgängliga portar på din valda peering-plats, e-post ExpressRouteDirect@microsoft.com för att begära mer inventering.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute med en tjänst leverantör och ExpressRoute Direct

| **ExpressRoute med en tjänst leverantör** | **ExpressRoute Direct** | 
| --- | --- |
| Använder tjänst leverantörer för att aktivera snabb onboarding och anslutning till befintlig infrastruktur | Kräver en infrastruktur på 100 Gbit/s/10 Gbit/s och fullständig hantering av alla skikt
| Integrerar med hundratals providers, inklusive Ethernet och MPLS | Direkt/dedikerad kapacitet för reglerade branscher och enorm data inmatning |
| Kretsar SKU: er från 50 Mbit/s till 10 Gbit/s | Kunden kan välja en kombination av följande krets-SKU: er på 100 Gbit/s ExpressRoute Direct: <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> Kunden kan välja en kombination av följande krets-SKU: er på 10 Gbit/s ExpressRoute Direct:<ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>
| Optimerad för enskild klient | Optimerad för en enskild klient med flera affär senheter och flera arbets miljöer

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct-kretsar

Med Microsoft Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en privat anslutning som gjorts enklare av en anslutnings leverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts moln tjänster, till exempel Microsoft Azure och Microsoft 365.

Varje peering-plats har till gång till Microsofts globala nätverk och kan komma åt valfri region i en regional politisk zon som standard. Du kan komma åt alla globala regioner med en Premium-krets.  

Funktionerna i de flesta scenarier är likvärdiga med kretsar som använder en ExpressRoute tjänst leverantör för att fungera. För att ge stöd till ytterligare granularitet och nya funktioner som erbjuds med ExpressRoute Direct finns det vissa viktiga funktioner som finns på ExpressRoute Direct-kretsar.

## <a name="circuit-skus"></a>Krets-SKU: er

ExpressRoute Direct stöder enorma data inmatnings scenarier i Azure Storage och andra Big Data Services. ExpressRoute-kretsar på 100 Gbit/s ExpressRoute Direct stöder nu även **40 Gbit/** s och * * 100-Gbit/s krets SKU: er. De fysiska portarna är **100 Gbit/s eller 10 Gbit/s** och kan ha flera virtuella kretsar. Krets storlekar:

| **100 Gbit/s ExpressRoute Direct** | **10 Gbit/s ExpressRoute Direct** | 
| --- | --- |
| **Prenumererad bandbredd**: 200 Gbit/s | **Prenumererad bandbredd**: 20 Gbit/s |
| <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> | <ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>

## <a name="technical-requirements"></a>Tekniska krav

* Microsoft MSEE: N-gränssnitt (Enterprise Edge router):
    * Dubbla 10 Gigabit-eller 100-Gigabit Ethernet-portar endast över router-par
    * LR fiber anslutning med enkel läge
    * IPv4 och IPv6
    * IP-MTU 1500 byte

* Switch/router Layer 2/Layer tre-anslutning:
    * Måste ha stöd för 1 802.1 Q (Dot1Q)-tagg eller två tag 802.1 Q (QinQ) tagg-inkapsling
    * Ethertype = 0x8100
    * Måste lägga till den yttre VLAN-taggen (STAG) baserat på det VLAN-ID som anges av Microsoft- *endast tillgängligt på QinQ*
    * Måste ha stöd för flera BGP-sessioner (VLAN) per port och enhet
    * IPv4-och IPv6-anslutning. *Inget extra under gränssnitt kommer att skapas för IPv6. IPv6-adressen kommer att läggas till i det befintliga under gränssnittet*. 
    * Valfritt: stöd för [BFD-stöd (dubbelriktad vidarebefordring)](./expressroute-bfd.md) som är konfigurerat som standard för alla privata peer-kopplingar på ExpressRoute-kretsar

## <a name="vlan-tagging"></a>VLAN-taggning

ExpressRoute Direct stöder både QinQ-och Dot1Q VLAN-taggning.

* **QINQ VLAN-taggning** tillåter isolerade routningsdomäner per ExpressRoute-krets. Azure ger dynamiskt en S-tagg när en krets skapas och kan inte ändras. Varje peering på kretsen (privat och Microsoft) kommer att använda en unik C-tagg som VLAN. C-taggen behöver inte vara unik för kretsar i ExpressRoute Direct-portarna.

* **DOT1Q VLAN-taggning** tillåter ett enskilt taggat VLAN på ett per ExpressRoute direkt port par. En C-tagg som används på en peering måste vara unik för alla kretsar och peering i ExpressRoute Direct-port paret.

## <a name="workflow"></a>Arbetsflöde

[![arbets flöde](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct tillhandahåller samma service avtal i företags klass med aktiva/aktiva redundanta anslutningar till Microsofts globala nätverk. ExpressRoute-infrastrukturen är redundant och anslutning till Microsofts globala nätverk är redundant och skild och skalar korrekt med kund krav. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera ExpressRoute Direct](expressroute-howto-erdirect.md)
