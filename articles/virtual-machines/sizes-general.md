---
title: Storlekar för virtuella Azure-datorer – generell användning | Microsoft Docs
description: Visar en lista över de olika generella syftes storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 46a9b7e630001161fbf2790cfb9be474379864b6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557632"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Generella storlekar för virtuella datorer

Generella storlekar för virtuella datorer ger balanserade förhållandet mellan processor och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. Den här artikeln innehåller information om erbjudanden för generell användning av ändamål.

- De virtuella datorerna i [AV2-serien](av2-series.md) kan distribueras på olika typer av maskin vara och processorer. Virtuella datorer i A-serien har CPU-prestanda och minneskonfigurationer som är bäst lämpade för arbets belastningar på ingångs nivå som utveckling och testning. Storleken begränsas, baserat på maskinvaran, för att erbjuda enhetliga processorprestanda på instansen som körs, oavsett vilken maskinvara instansen har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn. Exempel på användnings områden är utvecklings-och test servrar, webb servrar med låg trafik, små till medel stora databaser, koncept koncept och kod databaser.

  > [!NOTE]
  > De virtuella A8-, A9-och A10 A11-datorerna planeras för pensionering den 3/2021. Mer information finns i [Guide för HPC-migrering](https://azure.microsoft.com/resources/hpc-migration-guide/). De här VM-storlekarna finns i den ursprungliga A_v1-serien, inte "v2".

- [B-seriens burst-](sizes-b-series-burstable.md) överföring Virtuella datorer är idealiska för arbets belastningar som inte behöver PROCESSORns fulla prestanda kontinuerligt, till exempel webb servrar, små databaser och utvecklings-och test miljöer. Dessa arbets belastningar har vanligt vis höga prestanda krav. B-serien ger kunderna möjlighet att köpa en VM-storlek med en pris medveten bas linje prestanda som gör det möjligt för den virtuella dator instansen att bygga krediter när den virtuella datorn använder mindre än dess grundläggande prestanda. När den virtuella datorn har ackumulerat kredit kan den virtuella datorn överföras över den virtuella datorns bas linje med upp till 100% av CPU: n när programmet kräver högre CPU-prestanda.

- [Dav4 och Dasv4-serien](dav4-dasv4-series.md) är nya storlekar som använder AMD: s 2.35 GHz-EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 MB L3-cache dedikera 8 MB av den L3-cachen till varje 8 kärnor som ökar kund alternativen för att köra sina generella arbets belastningar. Dav4-serien och Dasv4-serien har samma minnes-och diskkonfigurationer som D & Dsv3-serien.

- [DV4 och Dsv4-serien](dv4-dsv4-series.md) DV4 och Dsv4-serien körs på Intel® Xeon-® platina 8272CL-processorer (Cascade Lake) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar. Den har en central Turbo klock hastighet på 3,4 GHz.

- [Ddv4 och Ddsv4-serien](ddv4-ddsv4-series.md) Ddv4 och Ddsv4-serien körs på Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar. Den har en central Turbo klock hastighet på 3,4 GHz, [Intel &reg; Turbo Boost-teknik 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) och [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). De stöder också [Intel &reg; djup inlärnings förstärkning](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Dessa nya VM-storlekar kommer att ha 50% större lokal lagring, samt bättre IOPS för lokala diskar för både läsning och skrivning jämfört med [dv3-/Dsv3-](./dv3-dsv3-series.md) storlekarna med [Gen2-VM](./generation-2.md): ar.

- [Dv3 och Dsv3-serien](dv3-dsv3-series.md) De virtuella datorerna körs på den andra generationen Intel® Xeon® platina 8272CL (överlappar sjö), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) i en Hyper-Threading-konfiguration, vilket ger ett bättre värde för de flesta Minnet har utökats (från ~ 3,5 GiB/vCPU till 4 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras efter flytta till hyperthreading. Dv3-serien har inte längre hög minnes storlek för virtuella datorer i D/Dv2-serien, de har flyttats till den minnesoptimerade [Ev3 och Esv3-serien](ev3-esv3-series.md).

- [Dv2 och Dsv2-serien](dv2-dsv2-series.md) Virtuella datorer, en uppföljning till den ursprungliga D-serien, har en kraftfullare processor och optimal konfiguration av processor till minne som gör dem lämpliga för de flesta produktions arbets belastningar. Dv2-serien är cirka 35% snabbare än D-serien. Dv2-serien körs på den andra generationen Intel® Xeon® platina 8272CL (överlappande sjö), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

- [DCv2-serien](dcv2-series.md) kan hjälpa till att skydda konfidentialiteten och integriteten hos dina data och kod när de bearbetas i det offentliga molnet. De här datorerna backas upp av den senaste generationen Intel XEON E-2288G-processor med SGX-teknik. Med Intel Turbo Boost-tekniken kan de här datorerna gå upp till 5,0 GHz. DCv2 Series-instanser gör det möjligt för kunder att skapa säkra enklaven-baserade program för att skydda sin kod och sina data medan den används.

## <a name="other-sizes"></a>Andra storlekar

- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

Mer information om hur Azure namnger sina virtuella datorer finns i [namngivnings konventioner för virtuella datorer i Azure](./vm-naming-conventions.md).
