---
title: SKU:er för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: SKU:er SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKU:er, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017, references_regions
ms.openlocfilehash: 3ecbbe4d477f3e6c3c6606528c51b934b6cf534a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718745"
---
# <a name="available-skus-for-hana-large-instances"></a>Tillgängliga SKU:er för hana stora instanser

BareMetal Infrastructure-tjänsten (certifierad SAP HANA arbetsbelastningar) som baseras på Rev 4.2* är tillgänglig i följande regioner:
- Europa, västra
- Europa, norra
- Stöd för Tyskland, västra centrala med zoner
- USA, östra med stöd för zoner
- USA, östra 2
- USA, södra centrala
- USA, västra 2 med stöd för zoner

BareMetal Infrastructure-tjänsten (certifierad SAP HANA arbetsbelastningar) som baseras på Rev 3* har begränsad tillgänglighet i följande regioner:
- USA, västra
- East US 
- Australien, östra 
- Australien, sydöstra
- Japan, östra


Följande är en lista över tillgängliga stora Azure-instanser.

> [!IMPORTANT]
> Tänk på den första kolumnen som representerar status för HANA-certifiering för var och en av typerna av stora instanser i listan. Kolumnen bör korrelera med [SAP HANA maskinvarukatalogen för](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) De Azure-SKU:er som börjar med bokstaven **S**.



| SAP HANA certifierad | Modell | Totalt minne | Minnes-DRAM | Minnesanvändning | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- | --- | --- |
| JA <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA på Azure S96<br /> – 2 x Intel® Xeon® Processor E7-8890 v4 <br /> 48 CPU-kärnor och 96 CPU-trådar |  768 GB | 768 GB | --- | 3,0 TB | Tillgängligt |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA på Azure S224<br /> – 4 x Intel® Xeon® × 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar |  3,0 TB | 3,0 TB | --- | 6,3 TB | Tillgängligt |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA på Azure S224 m<br /> – 4 x Intel® Xeon® × 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar |  6,0 TB | 6,0 TB | --- | 10,5 TB | Tillgängligt |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA på Azure S224om<br /> – 4 x Intel® Xeon® × 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Tillgängligt |
| NO | SAP HANA på Azure S224oo<br /> – 4 x Intel® Xeon® Amd 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Tillgängligt |
| NO | SAP HANA på Azure S224ooo<br /> – 4 x Intel® Xeon® Amd 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Tillgängligt |
| NO | SAP HANA på Azure S224oom<br /> – 4 x Intel® Xeon® Amd 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Tillgängligt |
| JA <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA på Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  4,0 TB | 4,0 TB | --- | 16 TB | Tillgängligt |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA på Azure S384 m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  6,0 TB | 6,0 TB | --- | 18 TB |  Tillgängligt  |
| JA <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA på Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  8,0 TB | 8,0 TB | --- | 22 TB | Tillgängligt |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA på Azure S448<br /> – 8 x Intel® Xeon® Amd 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Tillgänglig (endast Rev 4.2) |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA på Azure S448 m<br /> – 8 x Intel® Xeon® Amd 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S448oo<br /> – 8 x Intel® Xeon® Amd 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S448om<br /> – 8 x Intel® Xeon® × 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S448ooo<br /> – 8 x Intel® Xeon® × 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S448oom<br /> – 8 x Intel® Xeon® Amd 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Tillgänglig (endast Rev 4.2) |
| JA <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA på Azure S576 m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  12,0 TB | 12,0 TB | --- | 28 TB | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  18,0 TB | 18.0 | --- |  41 TB | Tillgängligt |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA på Azure S672<br /> – 12 x Intel® Xeon® × 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Tillgänglig (endast Rev 4.2) |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA på Azure S672 m<br /> – 12 x Intel® Xeon® × 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S672oo<br /> – 12 x Intel® Xeon® × 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S672om<br /> – 12 x Intel® Xeon® × 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S672ooo<br /> – 12 x Intel® Xeon® × 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S672oom<br /> – 12 x Intel® Xeon® Amd 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Tillgänglig (endast Rev 4.2) |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA på Azure S768 m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  16,0 TB | 16,0 TB | -- | 36 TB | Tillgängligt |
| NO | SAP HANA på Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  24,0 TB | 24,0 TB | --- | 56 TB | Tillgängligt |
|  JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA på Azure S896<br /> – 16 x Intel® Xeon® × 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Tillgänglig (endast Rev 4.2) |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA på Azure S896 m<br /> – 16 x Intel® Xeon® × 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 24,0 TB | 24,0 TB | -- | 35,8 TB | Tillgängligt |
| NO | SAP HANA på Azure S896oo<br /> – 16 x Intel® Xeon® × 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S896om<br /> – 16 x Intel® Xeon® Processor 8276 <br /> 448 CPU-kärnor och 896 CPU-trådar | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S896ooo<br /> – 16 x Intel® Xeon® Processor 8276 <br /> 448 CPU-kärnor och 896 CPU-trådar | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Tillgänglig (endast Rev 4.2) |
| NO | SAP HANA på Azure S896oom<br /> – 16 x Intel® Xeon® × 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Tillgänglig (endast Rev 4.2) |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA på Azure S960 m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 CPU-kärnor och 960 CPU-trådar |  20,0 TB | 20,0 TB | -- | 46 TB | Tillgänglig (endast Rev 4.2) |


- CPU-kärnor = summan av icke-flertrådade CPU-kärnor av summan av processorerna i serverenheten.
- CPU-trådar = summan av beräkningstrådar som tillhandahålls av flertrådade CPU-kärnor av summan av processorerna i serverenheten. De flesta enheter är konfigurerade som standard att använda Hyper-Threading Technology.
- Baserat på leverantörsrekommendationerna är S768m, S768xm och S960m inte konfigurerade för att använda Hyper-Threading för att köra SAP HANA.


> [!IMPORTANT]
> Följande SKU:er kan dock fortfarande inte köpas längre: S72, S72m, S144, S144m, S192 och S192 m 

De specifika konfigurationer som väljs är beroende av arbetsbelastning, CPU-resurser och önskat minne. Oltp-arbetsbelastningen kan använda SKU:er som är optimerade för OLAP-arbetsbelastningen. 

Två olika maskinvaruklasser delar in SKU:erna i:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 och S224m, S224oo, S224om, S224ooo, S224oom kallas "typ I-klass" för SKU:er.
- Alla andra SKU:er kallas "Type II class" för SKU:er.
- Om du är intresserad av SKU:er som ännu inte finns i SAP-maskinvarukatalogen kontaktar du ditt Microsoft-konto team för att få mer information. 


En fullständig stämpel för en stor HANA-instans allokeras inte exklusivt för en enskild&#39;användning. Detta gäller även för rack för beräknings- och lagringsresurser som är anslutna via en nätverks fabric som distribueras i Azure. Infrastruktur för stora HANA-instanser, som Azure, distribuerar olika kundklienter som är isolerade från varandra &quot; &quot; på följande tre nivåer:

- **Nätverk:** Isolering via virtuella nätverk inom stämpeln för HANA Large Instance.
- **Lagring:** Isolering via virtuella lagringsdatorer som har tilldelade lagringsvolymer och isolerar lagringsvolymer mellan klienter.
- **Compute**: Dedikerad tilldelning av serverenheter till en enda klientorganisation. Ingen hård eller mjuk partitionering av serverenheter. Ingen delning av en enskild server eller värdenhet mellan klienter. 

Distributionerna av hana large instance-enheter mellan olika klienter är inte synliga för varandra. Stora HANA-instansenheter som distribueras i olika klienter kan inte kommunicera direkt med varandra på stämpelnivån HANA Large Instance. Endast hana stora instansenheter inom en klientorganisation kan kommunicera med varandra på stämpelnivån HANA Large Instance.

En distribuerad klientorganisation i stämpeln För stor instans tilldelas en Azure-prenumeration i faktureringssyfte. För ett nätverk kan det nås från virtuella nätverk med andra Azure-prenumerationer inom samma Azure-registrering. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region kan du även välja att fråga efter en separerad stor HANA-instansklient.

Det finns betydande skillnader mellan att köra SAP HANA på stora HANA-instanser och SAP HANA på virtuella datorer som distribueras i Azure:

- Det finns inget virtualiseringslager för SAP HANA på Azure (stora instanser). Du får prestanda för den underliggande bare metal-maskinvaran.
- Till skillnad från Azure är SAP HANA på Azure-servern (stora instanser) dedikerad till en specifik kund. Det är inte möjligt att en serverenhet eller värd är hård eller mjuk partitionerad. Därför används en enhet för en stor HANA-instans som tilldelad som helhet till en klientorganisation och med den till dig. En omstart eller avstängning av servern leder inte automatiskt till operativsystemet och SAP HANA distribueras på en annan server. (För typ I-klass-SKU:er är det enda undantaget om en server stöter på problem och omdistribution måste utföras på en annan server.)
- Till skillnad från Azure, där värdprocessortyper väljs för bästa pris/prestanda-förhållande, är de processortyper som valts för SAP HANA på Azure (stora instanser) de som presterar bäst av Intel E7v3- och E7v4-processorlinjen.

## <a name="next-steps"></a>Nästa steg
- Se [HLI-storlek](hana-sizing.md).
