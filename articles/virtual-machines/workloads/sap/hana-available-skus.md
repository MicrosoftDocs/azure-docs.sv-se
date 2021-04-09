---
title: 'SKU: er för SAP HANA på Azure (stora instanser) | Microsoft Docs'
description: 'SKU: er för SAP HANA på Azure (stora instanser).'
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: 'HLI, HANA, SKU: er, S896, S224, S448, S672, Optane, SAP'
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25a11bf96ba680608e5bb22835becf80fadee4f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668924"
---
# <a name="available-skus-for-hana-large-instances"></a>Tillgängliga SKU: er för stora instanser av HANA

SAP HANA på Azure (stora instanser)-tjänsten baserat på revision 3-stämplar finns bara i flera konfigurationer i Azure-regionerna i:

- Australien, östra
- Australien, sydöstra
- Japan, östra
- Japan, västra

SAP HANA på Azure-tjänsten (stora instanser) som baseras på revision 4-stämplar finns i flera konfigurationer i Azure-regionerna i:

- USA, västra 2
- East US

BareMetal-infrastruktur (certifierad för SAP HANA arbets belastningar) som baseras på revision 4,2-stämplar. Den finns i flera konfigurationer i Azure-regionerna i:
- Europa, västra
- Europa, norra
- USA, östra 2
- USA, södra centrala




Listan över tillgängliga Azure Large-instanser som erbjuds listor som följande.

> [!IMPORTANT]
> Tänk på den första kolumnen som representerar statusen för HANA-certifiering för var och en av de stora instans typerna i listan. Kolumnen bör korreleras med [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) för de Azure-SKU: er som börjar med bokstaven **S**



| SAP HANA certifiering | Modell | Totalt minne | Minnes-DRAM | Optane för minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- | --- | --- |
| JA <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA på Azure-S96<br /> – 2 x Intel® Xeon®-processor E7-8890 v4 <br /> 48 CPU-kärnor och 96 CPU-trådar |  768 GB | 768 GB | --- | 3,0 TB | Tillgänglig |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA på Azure-S224<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar |  3,0 TB | 3,0 TB | --- | 6,3 TB | Tillgänglig |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA på Azure-S224m<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar |  6,0 TB | 6,0 TB | --- | 10,5 TB | Tillgänglig |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA på Azure-S224om<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Tillgänglig |
| NO | SAP HANA på Azure-S224oo<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Tillgänglig |
| NO | SAP HANA på Azure-S224ooo<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Tillgänglig |
| NO | SAP HANA på Azure-S224oom<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kärnor och 224 CPU-trådar | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Tillgänglig |
| JA <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA på Azure-S384<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  4,0 TB | 4,0 TB | --- | 16 TB | Tillgänglig |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA på Azure-S384m<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  6,0 TB | 6,0 TB | --- | 18 TB |  Tillgänglig  |
| JA <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA på Azure-S384xm<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  8,0 TB | 8,0 TB | --- | 22 TB | Tillgänglig |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA på Azure-S448<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Tillgängligt (endast rev 4) |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA på Azure-S448m<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S448oo<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S448om<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S448ooo<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S448oom<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kärnor och 448 CPU-trådar | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Tillgängligt (endast rev 4) |
| JA <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA på Azure-S576m<br /> – 12 x Intel® Xeon®-processor E7 – 8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  12,0 TB | 12,0 TB | --- | 28 TB | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S576xm<br /> – 12 x Intel® Xeon®-processor E7 – 8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  18,0 TB | 18.0 | --- |  41 TB | Tillgänglig |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA på Azure-S672<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Tillgängligt (endast rev 4) |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA på Azure-S672m<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S672oo<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S672om<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S672ooo<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S672oom<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kärnor och 672 CPU-trådar | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Tillgängligt (endast rev 4) |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA på Azure-S768m<br /> – 16 x Intel® Xeon®-processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  16,0 TB | 16,0 TB | -- | 36 TB | Tillgänglig |
| NO | SAP HANA på Azure-S768xm<br /> – 16 x Intel® Xeon®-processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  24,0 TB | 24,0 TB | --- | 56 TB | Tillgänglig |
|  JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA på Azure-S896<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Tillgängligt (endast rev 4) |
| JA <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA på Azure-S896m<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 24,0 TB | 24,0 TB | -- | 35,8 TB | Tillgänglig |
| NO | SAP HANA på Azure-S896oo<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S896om<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S896ooo<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Tillgängligt (endast rev 4) |
| NO | SAP HANA på Azure-S896oom<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kärnor och 896 CPU-trådar | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Tillgängligt (endast rev 4) |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA på Azure-S960m<br /> – 20 x Intel® Xeon®-processor E7-8890 v4<br /> 480 CPU-kärnor och 960 CPU-trådar |  20,0 TB | 20,0 TB | -- | 46 TB | Tillgängligt (endast rev 4) |


- PROCESSOR kärnor = summan av icke-processorers processor kärnor som summan av processorerna för server enheten har.
- PROCESSOR trådar = summan av de beräknings trådar som tillhandahålls av processor kärnor i Hyper-Threading av summan av processorerna för server enheten. De flesta enheter är konfigurerade som standard för att använda Hyper-Threading teknik.
- Baserat på leverantörs rekommendationer S768m, S768xm och S960m har inte kon figurer ATS för att använda Hyper-Threading för att köra SAP HANA.


> [!IMPORTANT]
> Följande SKU: er, trots att de fortfarande stöds, kan inte köpas längre: S72, S72m, S144, S144m, S192 och S192m 

De angivna konfigurationerna är beroende av arbets belastning, processor resurser och önskat minne. Det är möjligt för OLTP-arbetsbelastningen att använda de SKU: er som är optimerade för OLAP-arbetsbelastningen. 

Två olika klasser av maskin vara delar in SKU: er i:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 och S224m, S224oo, S224om, S224ooo, S224oom kallas "typ I-klass" för SKU: er.
- Alla andra SKU: er kallas "typ II-klass" för SKU: er.
- Om du är intresse rad av SKU: er som ännu inte finns med i katalogen SAP-maskinvara kan du kontakta Microsoft-konto-teamet för att få mer information. 


En fullständig HANA-stor instans stämpel är inte helt allokerad för en enskild kund&#39;s användning. Detta faktum gäller för rack och lagrings resurser som är anslutna via en nätverks infrastruktur som också distribueras i Azure. HANA stor instans infrastruktur, som Azure, distribuerar olika kund &quot; klienter &quot; som är isolerade från varandra på följande tre nivåer:

- **Nätverk**: isolera via virtuella nätverk inom den Hana-stora instans stämplingen.
- **Lagring**: isolering genom virtuella lagrings datorer som har lagrings volymer tilldelade och isolerar lagrings volymer mellan klienter.
- **Compute**: dedikerad tilldelning av Server enheter till en enda klient. Ingen hård disk eller mjuk partitionering av Server enheter. Ingen delning av en enskild server eller värd enhet mellan klienter. 

Distributioner av HANA-stora instans enheter mellan olika klienter är inte synliga för varandra. HANA-stora instans enheter som distribueras i olika klienter kan inte kommunicera direkt med varandra på nivån HANA stor instans Stamp. Endast HANA stora instans enheter inom en klient organisation kan kommunicera med varandra på nivån HANA-stor instans stämpel.

En distribuerad klient i den stora instans stämpeln tilldelas till en Azure-prenumeration för fakturerings syfte. För ett nätverk kan det nås från virtuella nätverk av andra Azure-prenumerationer inom samma Azure-registrering. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region, kan du också välja att be om en kommaavgränsad instans av HANA-stor instans.

Det finns betydande skillnader mellan att köra SAP HANA på HANA stor instans och SAP HANA som körs på virtuella datorer som distribueras i Azure:

- Det finns inget Virtualization-lager för SAP HANA på Azure (stora instanser). Du får prestanda för den underliggande maskin varan utan operativ system.
- Till skillnad från Azure är SAP HANA på Azure-servern (stora instanser) dedikerad till en specifik kund. Det finns ingen risk för att en server enhet eller värd är hårt partitionerad. Det innebär att en HANA-stor instans enhet används som tilldelad som helhet till en klient organisation och med det till dig. En omstart eller avstängning av servern leder inte automatiskt till operativ systemet och SAP HANA distribueras på en annan server. (För typ I-klass-SKU: er är det enda undantaget om en server stöter på problem och omdistribution måste utföras på en annan server.)
- Till skillnad från Azure, där värd processor typer väljs för bästa pris/prestanda-kvot, är de processor typer som väljs för SAP HANA på Azure (stora instanser) den högsta utförandet av processor linjen för Intel E7v3 och E7v4.

**Nästa steg**
- Referera [HLI storlek](hana-sizing.md)
