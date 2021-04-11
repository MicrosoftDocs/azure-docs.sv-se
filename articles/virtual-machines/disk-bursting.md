---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och virtuella Azure-datorer.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9758b026ef205e6608f7fc4110219dc5f267369e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568723"
---
# <a name="managed-disk-bursting"></a>Hanterad disk-bursting
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Azure [Premium-SSD](disks-types.md#premium-ssd) erbjuder två modeller av bursting:

- En överförings modell på begäran (för hands version), där disken burst-överför när dess behov överskrider den aktuella kapaciteten. Den här modellen debiteras ytterligare avgifter när diskarna är i gång. Ej kredit överföring är bara tillgängligt på diskar som är större än 512 GiB.
- En kredit baserad modell där disken endast får burst om den har ackumulerade burst-krediter i kredit Bucket. Den här modellen debiterar inte ytterligare avgifter när diskarna är i burst-överföring. Kredit-baserad burst-överföring är bara tillgängligt på diskar som är 512 GiB och mindre.

Dessutom [kan prestanda nivån för hanterade diskar ändras](disks-change-performance.md), vilket kan vara idealiskt om din arbets belastning annars skulle kunna köras i burst.

|  |Kredit baserad burst-överföring  |Burst-överföring på begäran  |Ändra prestanda nivå  |
|---------|---------|---------|---------|
| **Scenarier**|Idealisk för kortsiktig skalning (högst 30 minuter).|Idealisk för kortsiktig skalning (inte begränsad tid).|Idealisk om din arbets belastning annars skulle kunna köras kontinuerligt i burst.|
|**Kostnad**     |Kostnadsfri         |Kostnad är variabel, se [fakturerings](#billing) avsnittet för mer information.        |Kostnaden för varje prestanda nivå är fast, se [Managed disks priser](https://azure.microsoft.com/pricing/details/managed-disks/) för mer information.         |
|**Tillgänglighet**     |Endast tillgängligt för Premium SSD 512-GiB och mindre.         |Endast tillgängligt för Premium-SSD som är större än 512 GiB.         |Tillgängligt för alla Premium SSD-storlekar.         |
|**Aktivering**     |Aktive rad som standard på berättigade diskar.         |Måste aktive ras av användaren.         |Användaren måste ändra sin nivå manuellt.         |

## <a name="common-scenarios"></a>Vanliga scenarier
Följande scenarier kan dra nytta av burst-överföring:
- **Förbättra start tiderna**  – med burst-överföring kommer instansen att starta med en betydligt snabbare hastighet. Till exempel är standard operativ system disken för Premium-aktiverade virtuella datorer P4-disken, som är en etablerad prestanda på upp till 120 IOPS och 25 MB/s. Med burst-överföring kan P4 gå upp till 3500 IOPS och 170 MB/s för att starta snabbare med upp till 6X.
- **Hantera batch-jobb** – vissa program arbets belastningar är cykliska. De kräver en bas linje prestanda i största delen av tiden och högre prestanda under korta tids perioder. Ett exempel på detta är ett bokförings program som bearbetar dagliga transaktioner som kräver liten mängd disk trafik. I slutet av månaden Slutför programmet avstämnings rapporter som behöver en mycket större disk trafik.
- **Trafik** toppar – webb servrar och deras program kan uppleva trafik toppar när som helst. Om webb servern backas upp av virtuella datorer eller diskar som använder burst-överföring, är servrarna bättre utrustade för att hantera trafik toppar. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar burst på begäran finns i [Aktivera burst-överföring på begäran](disks-enable-bursting.md).
Information om hur du får inblick i dina burst-resurser finns i [mått för disk burst](disks-metrics.md).
