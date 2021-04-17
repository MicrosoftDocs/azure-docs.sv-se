---
title: Känna till villkoren i Azure BareMetal-infrastrukturen
description: Känna till villkoren i Azure BareMetal-infrastrukturen.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: aa7d9693b3417ff0bb6c6a61800aee72cd416c48
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536773"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Känna till termerna för BareMetal-infrastruktur

I den här artikeln tar vi upp några viktiga termer relaterade till BareMetal-infrastrukturen.

- **Revision:** Det finns en ursprunglig stämpelrevision som kallas Revision 3 (Rev 3) och två ytterligare stämpelrevisioner för BareMetal-instansstämplar. Varje stämpel skiljer sig åt vad gäller arkitektur och närhet till virtuella Azure-datorvärdar:
    - **Revision 4** (Rev 4): En nyare design som ger närmare anslutning till de virtuella Azure-värdarna (VM) och sänker svarstiden mellan virtuella Azure-datorer och SAP HANA instanser. 
    - **Revision 4.2** (Rev 4.2): Den senaste omdöpta BareMetal-infrastrukturen med den befintliga Rev 4-arkitekturen. Rev 4 ger närmare till de virtuella Azure-värdarna (VM). Det har betydande förbättringar i nätverksfördröjningen mellan virtuella Azure-datorer och BareMetal-instanser som distribuerats i Rev 4-stämplar eller rader. Du kan komma åt och hantera dina BareMetal-instanser via Azure Portal.    

- **Stämpel:** Definierar Microsofts interna distributionsstorlek för BareMetal-instanser. Innan instanser kan distribueras måste en BareMetal-instansstämpel som består av beräknings-, nätverks- och lagringsrack distribueras på en datacenterplats. En sådan distribution kallas för en BareMetal-instansstämpel.

- **Klientorganisation:** En kund som distribuerar en BareMetal-instansstämpel isoleras som en *klientorganisation.* En klientorganisation är isolerad i nätverks-, lagrings- och beräkningslagret från andra klienter. Lagrings- och beräkningsenheter som är tilldelade till de olika klienterna kan inte se varandra eller kommunicera med varandra på stämpelnivån för BareMetal-instansen. En kund kan välja att ha distributioner till olika klienter. Inte ens då finns det någon kommunikation mellan klienter på stämpelnivån BareMetal-instans.

## <a name="next-steps"></a>Nästa steg

Nu när du har introducerats för viktig terminologi för BareMetal-infrastrukturen kanske du vill lära dig mer om:
- Mer information om [BareMetal-infrastrukturen](concepts-baremetal-infrastructure-overview.md).
- Ansluta [BareMetal-infrastrukturinstanser i Azure](connect-baremetal-infrastructure.md).

