---
title: Nätverks data flöde för virtuella Azure-datorer | Microsoft Docs
description: Lär dig mer om data flöde för virtuella Azure-datorer, inklusive hur bandbredden allokeras till en virtuell dator.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 280b3cbef8307691b0d50c4a26f6dca18b7fb65b
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233873"
---
# <a name="virtual-machine-network-bandwidth"></a>Nätverksbandbredd för virtuell dator

Azure erbjuder en mängd olika storlekar och typer av virtuella datorer, var och en med olika prestanda funktioner. En funktion är nätverks data flöde (eller bandbredd), mätt i megabit per sekund (Mbit/s). Eftersom virtuella datorer finns på delad maskin vara, måste nätverks kapaciteten delas relativt mellan de virtuella datorerna som delar samma maskin vara. Större virtuella datorer tilldelas relativt mer bandbredd än mindre virtuella datorer.
 
Nätverks bandbredden som allokeras till varje virtuell dator mäts på utgående trafik (utgående trafik) från den virtuella datorn. All nätverks trafik som lämnar den virtuella datorn räknas till den tilldelade gränsen, oavsett destination. Om till exempel en virtuell dator har en gräns på 1 000 Mbit/s, gäller den gränsen om den utgående trafiken är avsedd för en annan virtuell dator i samma virtuella nätverk eller utanför Azure.
 
Ingress är inte avgiftsbelagda eller begränsade direkt. Det finns dock andra faktorer, till exempel processor-och lagrings gränser, som kan påverka en virtuell dators möjlighet att bearbeta inkommande data.

Accelererat nätverk är en funktion som har utformats för att förbättra nätverks prestanda, inklusive svars tid, data flöde och processor belastning. Även om det accelererade nätverket kan förbättra en virtuell dators data flöde, kan det bara göra upp till den virtuella datorns allokerade bandbredd. Mer information om accelererat nätverk finns i accelererat nätverk för virtuella [Windows](create-vm-accelerated-networking-powershell.md) -eller [Linux](create-vm-accelerated-networking-cli.md) -datorer.
 
Virtuella Azure-datorer måste ha en, men kan ha flera nätverks gränssnitt anslutna till dem. Bandbredd som tilldelas en virtuell dator är summan av all utgående trafik över alla nätverks gränssnitt som är anslutna till en virtuell dator. Med andra ord är den allokerade bandbredden per virtuell dator, oavsett hur många nätverks gränssnitt som är kopplade till den virtuella datorn. Information om hur många olika nätverks gränssnitt som stöds av Azure VM-storlekar finns i storlekar för virtuella Azure [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -och [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -datorer. 

## <a name="expected-network-throughput"></a>Förväntat nätverks data flöde

Förväntat utgående data flöde och antalet nätverks gränssnitt som stöds av varje VM-storlek beskrivs i Azure [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -och [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar. Välj en typ, till exempel generell användning, och välj sedan en storleks serie på den resulterande sidan, till exempel Dv2-serien. Varje serie har en tabell med nätverks specifikationer i den sista kolumnen med rubriken, **högsta antal nätverkskort/förväntade nätverks prestanda (Mbit/s)**. 

Data flödes gränsen gäller för den virtuella datorn. Data flödet påverkas inte av följande faktorer:
- **Antal nätverks gränssnitt**: bandbredds gränsen är ackumulerad för all utgående trafik från den virtuella datorn.
- **Accelererat nätverk**: även om funktionen kan vara till hjälp när du uppnår den publicerade gränsen ändras inte gränsen.
- **Trafik mål**: alla destinationer räknas mot utgående gräns.
- **Protokoll**: all utgående trafik över alla protokoll räknas mot gränsen.

## <a name="network-flow-limits"></a>Nätverks flödes gränser

Utöver bandbredd kan antalet nätverks anslutningar som finns på en virtuell dator vid en bestämd tidpunkt påverka nätverkets prestanda. Azure Networking-stacken har tillstånd för varje riktning i en TCP/UDP-anslutning i data strukturer som kallas "flöden". En typisk TCP/UDP-anslutning har 2 flöden skapade, en för inkommande och en annan för den utgående riktningen. 

Data överföring mellan slut punkter kräver att flera flöden skapas förutom de som utför data överföringen. Några exempel är flöden som skapats för DNS-matchning och flöden som skapats för hälso avsökningar för belastningsutjämnare. Observera också att virtuella nätverks installationer (NVA), till exempel gatewayer, proxyservrar, brand väggar, kommer att se flöden som skapas för anslutningar som avslutas vid installationen och som har sitt ursprung av produkten. 

![Antal flöden för TCP-samtal via en vidarebefordrings enhet](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-active-connections-recommendations"></a>Rekommendationer för flödes gränser och aktiva anslutningar

Idag stöder Azure Networking stack totalt 1 miljon totalt antal flöden (500 000 inkommande och 500 000 utgående) för en virtuell dator. Det totala antalet aktiva anslutningar som kan hanteras av en virtuell dator i olika scenarier är följande.
- Virtuella datorer som tillhör VNET kan hantera 500 000 **_aktiva anslutningar_* _ för alla VM-storlekar med 500 000 _*_aktiva flöden i varje riktning_*_.  
- Virtuella datorer med virtuella nätverks installationer (NVA) som gateway, proxy, brand vägg kan hantera 250 000 _*_aktiva anslutningar_*_ med 500 000 _ *_aktiva flöden i varje riktning_** på grund av vidarebefordran och ytterligare ny flödes skapande i den nya anslutnings konfigurationen till nästa hopp som visas i diagrammet ovan. 

När den här gränsen har nåtts tas ytterligare anslutningar bort. Etablerings takten och termins taxan för anslutning kan också påverka nätverks prestanda som upprättande av anslutningar och avsluta delar CPU med paket bearbetnings rutiner. Vi rekommenderar att du benchmark-arbetsbelastningar mot förväntade trafik mönster och skalar ut arbets belastningar på lämpligt sätt för att matcha dina prestanda behov.

Mått är tillgängliga i [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) för att spåra antalet nätverks flöden och takten för skapande av flödet på den virtuella datorn eller VMSS-instanser.

![Skärm bild som visar sidan mått för Azure Monitor med ett linje diagram och summor för inkommande och utgående flöden.](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Etablerings takten och termins taxan för anslutning kan också påverka nätverks prestanda som upprättande av anslutningar och avsluta delar CPU med paket bearbetnings rutiner. Vi rekommenderar att du benchmark-arbetsbelastningar mot förväntade trafik mönster och skalar ut arbets belastningar på lämpligt sätt för att matcha dina prestanda behov. 

## <a name="next-steps"></a>Nästa steg

- [Optimera nätverkets dataflöde för en virtuell dators operativsystem](virtual-network-optimize-network-bandwidth.md)
- [Testa nätverks data flöde](virtual-network-bandwidth-testing.md) för en virtuell dator.