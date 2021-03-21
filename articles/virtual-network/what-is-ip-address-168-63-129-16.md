---
title: Vad är IP-168.63.129.16? | Microsoft Docs
description: Lär dig mer om IP-168.63.129.16, särskilt att den används för att under lätta en kommunikations kanal till Azures plattforms resurser.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 1e304bc30a48c92fdff576723dae6af1e26ef3da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222589"
---
# <a name="what-is-ip-address-1686312916"></a>Vad är IP-168.63.129.16?

IP-168.63.129.16 är en virtuell offentlig IP-adress som används för att under lätta en kommunikations kanal till Azures plattforms resurser. Kunder kan definiera valfritt adress utrymme för sitt privata virtuella nätverk i Azure. Därför måste Azures plattforms resurser presenteras som en unik offentlig IP-adress. Den här virtuella offentliga IP-adressen underlättar följande saker:

- Gör att VM-agenten kan kommunicera med Azure-plattformen för att signalera att den är i ett klart läge.
- Möjliggör kommunikation med den virtuella DNS-servern för att tillhandahålla filtrerad namn matchning till de resurser (t. ex. VM) som inte har en anpassad DNS-server. Den här filtreringen ser till att kunderna bara kan matcha sina resursers värdnamn.
- Aktiverar [hälso avsökningar från Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) för att fastställa hälso tillståndet för virtuella datorer.
- Gör det möjligt för den virtuella datorn att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.
- Aktiverar pulsslags meddelanden för gäst agenten för PaaS-rollen.

> [!NOTE]
> I ett icke-virtuellt nätverks scenario (klassisk) används en privat IP-adress i stället för 168.63.129.16. Den här privata IP-adressen identifieras dynamiskt via DHCP. Brand Väggs regler som är speciella för 168.63.129.16 måste justeras efter behov.

## <a name="scope-of-ip-address-1686312916"></a>Omfånget för IP-168.63.129.16

Den offentliga IP-168.63.129.16 används i alla regioner och i alla nationella moln. Den här särskilda offentliga IP-adressen ägs av Microsoft och kommer inte att ändras. Vi rekommenderar att du tillåter den här IP-adressen lokalt (i den virtuella datorns) brand Väggs principer (utgående riktning). Kommunikationen mellan den här särskilda IP-adressen och resurserna är säker eftersom endast den interna Azure-plattformen kan källa ett meddelande från den här IP-adressen. Om den här adressen är blockerad kan ett oväntat beteende uppstå i olika scenarier. 168.63.129.16 är en [virtuell IP-adress för noden värd](./network-security-groups-overview.md#azure-platform-considerations) , vilket innebär att den inte omfattas av användardefinierade vägar.

- Den virtuella dator agenten kräver utgående kommunikation över portarna 80/TCP och 32526/TCP med WireServer (168.63.129.16). Dessa bör vara öppna i den lokala brand väggen på den virtuella datorn. Kommunikationen på dessa portar med 168.63.129.16 omfattas inte av de konfigurerade nätverks säkerhets grupperna.

- 168.63.129.16 kan tillhandahålla DNS-tjänster till den virtuella datorn. Om detta inte är önskat kan utgående trafik till 168.63.129.16-portarna 53/UDP och 53/TCP blockeras i den lokala brand väggen på den virtuella datorn.

  Som standard är DNS-kommunikation inte beroende av de konfigurerade nätverks säkerhets grupperna, om de inte särskilt riktar sig mot [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) -tjänst tag gen. Om du vill blockera DNS-trafik till Azure DNS via NSG skapar du en utgående regel som nekar trafik till [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)och anger "*" som "mål Port intervall" och "any" som protokoll.

- När den virtuella datorn är en del av en belastningsutjämnare för belastnings utjämning, bör [hälso avsöknings](../load-balancer/load-balancer-custom-probe-overview.md) kommunikationen ha sitt ursprung i 168.63.129.16. Standard konfigurationen för nätverks säkerhets gruppen har en regel som tillåter den här kommunikationen. Den här regeln utnyttjar [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) -tjänst tag gen. Om du vill att den här trafiken ska blockeras genom att konfigurera nätverks säkerhets gruppen, kommer detta att resultera i avsökningar som Miss lyckas.

## <a name="next-steps"></a>Nästa steg

- [Säkerhetsgrupper](./network-security-groups-overview.md)
- [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](manage-network-security-group.md)