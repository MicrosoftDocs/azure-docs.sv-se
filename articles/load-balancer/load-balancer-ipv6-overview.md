---
title: Översikt över IPv6 – Azure Load Balancer
description: Med den här utbildnings vägen kommer du igång med IPv6-stöd för Azure Load Balancer och belastningsutjämnade virtuella datorer.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, dubbel stack, offentlig IP, inbyggd IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 4061a3dbf4dc92d6d412528115d46edc36d20d5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94700699"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Översikt över IPv6 för Azure Load Balancer


>[!NOTE] 
>Det här innehållet har ersatts av [IPv6 för Azure VNet-översikt](../virtual-network/ipv6-overview.md). Azure rekommenderar nya IPv6-distributioner med hjälp av de nya funktionerna för IPv6 för virtuella Azure-nätverk.

>[!NOTE]
>Azures Load Balancer stöder två typer: grundläggande och standard. Den här artikeln beskriver den grundläggande lastbalanseraren. Mer information om Standard Load Balancer finns i [standard Load Balancer översikt](./load-balancer-overview.md).

Grundläggande SKU: er för Internet-belastnings utjämning kan distribueras med en IPv6-adress. Förutom IPv4-anslutning möjliggör detta följande funktioner:

* Inbyggd IPv6-anslutning från slut punkt till slut punkt mellan offentliga Internet-klienter och Azure-Virtual Machines (VM) via belastningsutjämnaren.
* Inbyggd IPv6 utgående anslutning från slut punkt till slut punkt mellan virtuella datorer och offentliga Internet IPv6-aktiverade klienter.

Följande bild illustrerar IPv6-funktionerna för Azure Load Balancer.

![Azure Load Balancer med IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

När en IPv4-eller IPv6-aktiverad Internet-klient har distribuerats kan den kommunicera med offentliga IPv4-eller IPv6-adresser (eller värdnamn) för Azure Internet-riktade Load Balancer. Belastningsutjämnaren dirigerar IPv6-paketen till de privata IPv6-adresserna för de virtuella datorerna med hjälp av Network Address Translation (NAT). IPv6-Internet klienten kan inte kommunicera direkt med den virtuella datorns IPv6-adress.

## <a name="features&quot;></a>Funktioner

Inbyggt IPv6-stöd för virtuella datorer som distribueras via Azure Resource Manager ger:

1. Belastningsutjämnade IPv6-tjänster för IPv6-klienter på Internet
2. Inbyggda IPv6-och IPv4-slutpunkter på virtuella datorer (&quot;dubbla staplade")
3. Inkommande och utgående initierade interna IPv6-anslutningar
4. Protokoll som stöds som TCP, UDP och HTTP (S) aktiverar en fullständig uppsättning tjänst arkitekturer

## <a name="benefits"></a>Fördelar

Den här funktionen möjliggör följande viktiga fördelar:

* Uppfylla myndighets bestämmelser som kräver att nya program är tillgängliga för endast IPv6-klienter
* Gör det möjligt för utvecklare av mobil-och Sakernas Internet (IOT) att använda Dual-Stack (IPv4 + IPv6) Azure Virtual Machines för att hantera de växande mobil & IOT-marknaderna

## <a name="details-and-limitations"></a>Information och begränsningar

Information

* Tjänsten Azure DNS innehåller både IPv4 A-och IPv6 AAAA-namn och svarar med båda posterna för belastningsutjämnaren. Klienten väljer vilken adress (IPv4 eller IPv6) som ska kommunicera med.
* När en virtuell dator initierar en anslutning till en offentlig Internet IPv6-ansluten enhet, är den virtuella datorns käll-IPv6-adress översatt (NAT) till den offentliga IPv6-adressen för belastningsutjämnaren.
* Virtuella datorer som kör Linux-operativsystemet måste konfigureras för att ta emot en IPv6 IP-adress via DHCP. Många av Linux-avbildningarna i Azure-galleriet har redan kon figurer ATS för att stödja IPv6 utan modifiering. Mer information finns i [Konfigurera DHCPv6 för virtuella Linux-datorer](load-balancer-ipv6-for-linux.md)
* Om du väljer att använda en hälso avsökning med belastningsutjämnaren skapar du en IPv4-avsökning och använder den med både IPv4-och IPv6-slutpunkter. Om tjänsten på den virtuella datorn slutar fungera tas både IPv4-och IPv6-slutpunkterna bort från rotationen.

Begränsningar

* Det går inte att lägga till regler för IPv6-belastnings utjämning i Azure Portal. Reglerna kan bara skapas via mallen, CLI, PowerShell.
* Du kan inte uppgradera befintliga virtuella datorer för att använda IPv6-adresser. Du måste distribuera nya virtuella datorer.
* En enda IPv6-adress kan tilldelas till ett enda nätverks gränssnitt i varje virtuell dator.
* Det går inte att tilldela offentliga IPv6-adresser till en virtuell dator. De kan bara tilldelas till en belastningsutjämnare.
* Det går inte att konfigurera omvänd DNS-sökning för dina offentliga IPv6-adresser.
* De virtuella datorerna med IPv6-adresser kan inte vara medlemmar i en Azure-moln tjänst. De kan anslutas till ett Azure-Virtual Network (VNet) och kommunicera med varandra över sina IPv4-adresser.
* Privata IPv6-adresser kan distribueras på enskilda virtuella datorer i en resurs grupp, men kan inte distribueras till en resurs grupp via skalnings uppsättningar.
* Virtuella Azure-datorer kan inte ansluta via IPv6 till andra virtuella datorer, andra Azure-tjänster eller lokala enheter. De kan bara kommunicera med Azure Load Balancer via IPv6. De kan dock kommunicera med de här andra resurserna med hjälp av IPv4.
* Skydd för nätverks säkerhets gruppen (NSG) för IPv4 stöds i distributioner med dubbla stackar (IPv4 + IPv6). NSG: er gäller inte för IPv6-slutpunkterna.
* IPv6-slutpunkten på den virtuella datorn exponeras inte direkt för Internet. Det ligger bakom en belastningsutjämnare. Endast de portar som anges i belastnings Utjämnings reglerna är tillgängliga via IPv6.
* Det finns **för närvarande inte stöd** för att ändra idleTimeout-parametern för IPv6. Standardvärdet är fyra minuter.
* Det finns **för närvarande inte stöd** för att ändra parametern LoadDistributionMethod för IPv6.
* IPv6 för grundläggande Load Balancer är låst till en **dynamisk** SKU.  IPv6 för en Standard Load Balancer är låst till en **statisk** SKU.
* NAT64 (översättning av IPv6 till IPv4) stöds inte.
* Det finns **för närvarande inte stöd för** att ansluta ett sekundärt nätverkskort som refererar till ett IPv6-undernät till en backend-pool.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en belastningsutjämnare med IPv6.

* [Tillgänglighet för IPv6 efter region](https://go.microsoft.com/fwlink/?linkid=828357)
* [Distribuera en belastningsutjämnare med IPv6 med hjälp av en mall](load-balancer-ipv6-internet-template.md)
* [Distribuera en belastningsutjämnare med IPv6 med hjälp av Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Distribuera en belastningsutjämnare med IPv6 med Azure CLI](load-balancer-ipv6-internet-cli.md)