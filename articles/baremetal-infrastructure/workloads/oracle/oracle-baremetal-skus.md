---
title: BareMetal-SKU:er för Oracle-arbetsbelastningar
description: Lär dig mer om SKU:er för Oracle BareMetal Infrastructure-arbetsbelastningar.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588861"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>BareMetal-SKU:er för Oracle-arbetsbelastningar

I den här artikeln tar vi en titt på specialiserade BareMetal Infrastructure-SKU:er för Oracle-arbetsbelastningar.

BareMetal Infrastructure för Oracle-SKU:er sträcker sig från två sockets upp till fyra sockets. Du kan också välja mellan olika processorkärnor och minnesstorlekar för att uppfylla kraven för din arbetsbelastning. Här är en tabell med funktioner för tillgängliga SKU:er.
 
| **Oracle-certifierad**  **maskinvara** | **Modell** | **Totalt minne** | **Storage** | **Tillgänglighet** |
| --- | --- | --- | --- | --- |
| JA | SAP HANA på Azure S32 m– 2 x Intel® Xeon® I6234 Processor 16 CPU-kärnor och 32 CPU-trådar | 1,5 TB | --- | Tillgängligt |
| JA | SAP HANA på Azure S64m– 4 x Intel® Xeon® I6234 Processor 32 CPU-kärnor och 64 CPU-trådar | 3,0 TB | --- | Tillgängligt |
| JA | SAP HANA på Azure S96 – 2 x Intel® Xeon® E7-8890 v4 Processor 48 CPU-kärnor och 96 CPU-trådar | 768 GB | 3,0 TB | Tillgängligt |
| JA | SAP HANA på Azure S224 – 4 x Intel® Xeon® × 8276-processor med 112 processorkärnor och 224 CPU-trådar | 3,0 TB | 6,3 TB | Tillgängligt |
| JA | SAP HANA på Azure S224m – 4 x Intel® Xeon® × 8276-processor med 112 processorkärnor och 224 CPU-trådar | 6,0 TB | 10,5 TB | Tillgängligt |

- CPU-kärnor = summan av icke-hypertrådade CPU-kärnor (det totala antalet fysiska processorer) för serverenheten. 
- CPU-trådar = summan av beräkningstrådar som tillhandahålls av flertrådade CPU-kärnor (det totala antalet logiska processorer) för serverenheten. De flesta enheter är konfigurerade som standard att använda Hyper-Threading Technology.
- Servrar är dedikerade till kunder.
- Kunden har rotåtkomst (ingen hypervisor).
- Servrar är inte direkt i virtuella Azure-nätverk.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om lagring som erbjuds av BareMetal Infrastructure för Oracle.

> [!div class="nextstepaction"]
> [Lagring på BareMetal för Oracle-arbetsbelastningar](oracle-baremetal-storage.md)
