---
title: Virtuella Azure-datorer-storlekar – minne | Microsoft Docs
description: Visar en lista över de olika minnesoptimerade storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolering av virtuella datorer, isolerad virtuell dator, isolerad
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 08a14a1c97a1eae9c07c50334929fb16304e635a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556527"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Minnesoptimerade storlekar för virtuella datorer

Minnesoptimerade VM-storlekar erbjuder ett högt förhållande mellan minne och processor som är perfekt för Relations databas servrar, medel stora och stora cacheminnen och minnes intern analys. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för varje storlek i den här gruppen.

- [Dv2 och DSv2-serien](dv2-dsv2-series-memory.md), som är en uppföljning av den ursprungliga D-serien, har en mer kraftfull processor. Dv2-serien är cirka 35% snabbare än D-serien. Den körs på Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) eller Intel &reg; Xeon &reg; e5-2673 v4 2,3 GHz (Broadwell) eller Intel &reg; xeon &reg; E5-2673 v3 2,4 GHz-processorer (Haswell) och med Intel Turbo Boost-tekniken 2,0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

    Dv2 och DSv2-serien är idealiska för program som kräver snabbare virtuella processorer, bättre tillfälliga lagrings prestanda eller har högre minnes krav. De utgör en kraftfull kombination för många program i företagsklass.

- [Eav4 och Easv4-serien](eav4-easv4-series.md) använder AMD: s 2.35 GHz EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 MB L3-cache, vilket ökar alternativen för att köra de flesta minnesoptimerade arbets belastningar. Eav4-serien och Easv4-serien har samma minnes-och diskkonfigurationer som Ev3 & Esv3-serien.

- [Ev3-och Esv3-serien](ev3-esv3-series.md) Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) eller Intel &reg; xeon &reg; E5-2673 v4 2,3 GHz-processorn (Broadwell) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar och ger Ev3 till justering med de flesta virtuella datorerna i de flesta andra moln. Minnet har utökats (från 7 GiB/vCPU till 8 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras med flytten till Hyper-Threading. Ev3 är en uppföljning av de virtuella datorerna med hög minnes storlek för D/Dv2-familjer.

- [Ev4 och Esv4-serien](ev4-esv4-series.md) körs på andra generationen Intel &reg; Xeon &reg; platina-processorer (Cascade Lake) i en Hyper-threadd konfiguration, är idealiska för olika minnes intensiva företags program och funktioner upp till 504 GIB RAM-minne. Den har [Intel &reg; Turbo Boost-tekniken 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) och [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Ev4 och Esv4-serien inkluderar inte en lokal temporär disk. Mer information finns i storlekar för  [virtuella Azure-datorer utan en lokal temporär disk](azure-vms-no-temp-disk.md).

- [Edv4 och Edsv4-serien](edv4-edsv4-series.md) körs på andra generationens Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake), idealiska för extremt stora databaser eller andra program som drar nytta av höga vCPU-antal och stora mängder minne. Dessutom omfattar dessa VM-storlekar snabb, större lokal SSD-lagring för program som har nytta av låg latens, lokal lagring med hög hastighet. Den har en central Turbo klock hastighet på 3,4 GHz, [Intel &reg; Turbo Boost-teknik 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) och [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- [M-serien](m-series.md) erbjuder ett högt antal vCPU (upp till 128 virtuella processorer) och en stor mängd minne (upp till 3,8 TIB). Det är också idealiskt för extremt stora databaser eller andra program som drar nytta av höga vCPU-räkningar och stora mängder minne.

- [Mv2-serien](mv2-series.md) erbjuder det högsta antalet vCPU (upp till 416 virtuella processorer) och störst minne (upp till 11,4 TIB) för virtuella datorer i molnet. Det är idealiskt för extremt stora databaser eller andra program som drar nytta av höga vCPU-räkningar och stora mängder minne.

Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskin varu typ och som är dedikerad till en enda kund. Dessa storlekar på virtuella datorer lämpar sig bäst för arbets belastningar som kräver en hög grad av isolering från andra kunder för arbets belastningar som inbegriper element som efterlevnad och myndighets krav. Kunder kan också välja att ytterligare dela upp resurserna i de isolerade virtuella datorerna med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Se sidorna för virtuella dator familjer nedan för de isolerade VM-alternativen.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

Mer information om hur Azure namnger sina virtuella datorer finns i [namngivnings konventioner för virtuella datorer i Azure](./vm-naming-conventions.md).
