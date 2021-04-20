---
title: Känna till villkoren i Azure BareMetal-infrastrukturen
description: Känna till villkoren för Azure BareMetal-infrastrukturen.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725466"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Känna till termerna för BareMetal-infrastruktur

I den här artikeln tar vi upp några viktiga termer relaterade till BareMetal-infrastrukturen.

- **Revision:** Det finns två olika stämpelrevisioner för stämplar för BareMetal Infrastructure (HANA Large Instance). Dessa skiljer sig åt vad gäller arkitektur och närhet till virtuella Azure-värdar:
    - "Revision 3" (Rev 3): Den ursprungliga designen distribuerades i mitten av 2016.
    - "Revision 4.2" (Rev 4.2): En ny design som ger närmare närhet till virtuella Azure-datorvärdar, med extremt låg nätverksfördröjning mellan virtuella Azure-datorer och HANA– stora instanser. Resurser i Azure Portal kallas "BareMetal-infrastruktur" och kunder kan komma åt sina resurser som BareMetal-instanser från Azure Portal.

- **Stämpel:** Definierar Microsofts interna distributionsstorlek för BareMetal-instanser. Innan instanser kan distribueras måste en BareMetal-instansstämpel som består av beräknings-, nätverks- och lagringsrack distribueras på en datacenterplats. En sådan distribution kallas för en BareMetal-instansstämpel.

- **Klientorganisation:** En kund som distribuerar en BareMetal-instansstämpel isoleras som en *klientorganisation.* En klientorganisation är isolerad i nätverks-, lagrings- och beräkningslagret från andra klienter. Lagrings- och beräkningsenheter som är tilldelade till de olika klienterna kan inte se varandra eller kommunicera med varandra på stämpelnivån för BareMetal-instansen. En kund kan välja att ha distributioner till olika klienter. Inte ens då finns det någon kommunikation mellan klienterna på stämpelnivån BareMetal-instans.

## <a name="next-steps"></a>Nästa steg

Nu när du har introducerats för viktig terminologi för BareMetal-infrastrukturen kanske du vill lära dig mer om:
- Mer information om [BareMetal-infrastrukturen](concepts-baremetal-infrastructure-overview.md).
- Ansluta [BareMetal-infrastrukturinstanser i Azure](connect-baremetal-infrastructure.md).

