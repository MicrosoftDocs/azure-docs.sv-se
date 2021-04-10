---
title: Förstå villkoren i Azure BareMetal-infrastrukturen
description: Förstå villkoren i Azure BareMetal-infrastrukturen.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 53a601cc4556198479d8ca5d7495942d4dc2762c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580128"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Känna till villkoren för BareMetal-infrastrukturen

I den här artikeln tar vi upp några viktiga villkor som rör BareMetal-infrastrukturen.

- **Revision**: det finns en ursprunglig Stamp-revision som kallas revision 3 (rev 3) och två ytterligare stämplar revisioner för BareMetal-instanser. Varje stämpel skiljer sig i arkitekturen och närhet till virtuella Azure-värddatorer:
    - **Revision 4** (rev 4): en nyare design som ger närmare närhet av virtuella Azure-datorer (VM) och sänker fördröjningen mellan virtuella Azure-datorer och SAP HANA instanser. 
    - **Revision 4,2** (rev 4,2): den senaste anpassade BareMetal-infrastrukturen med den befintliga rev 4-arkitekturen. Rev 4 ger närmare närhet till Azures virtuella dator värdar. Den har betydande förbättringar i nätverks fördröjningen mellan virtuella Azure-datorer och BareMetal-instanser som distribueras i rev 4-stämplar eller rader. Du kan komma åt och hantera dina BareMetal-instanser via Azure Portal.    

- **Stamp**: definierar den interna distributions storleken för Microsoft BareMetal-instanser. Innan instanser kan distribueras måste en BareMetal-instans som består av beräknings-, nätverks-och lagrings rack distribueras på en plats i data centret. En sådan distribution kallas en instans av en BareMetal.

- **Klient organisation**: en kund som distribuerar en BareMetal instance-stämpel isoleras som en *klient.* En klient är isolerad i nätverk, lagring och beräknings lager från andra klienter. Lagrings-och beräknings enheter som är tilldelade till olika klienter kan inte se varandra eller kommunicera med varandra på BareMetal-instansens Stamp-nivå. En kund kan välja att distribuera olika klienter. Det finns inte heller någon kommunikation mellan klienter på BareMetal-instansens Stamp-nivå.

## <a name="next-steps"></a>Nästa steg
Nu när du har introducerat den viktiga terminologin i BareMetal-infrastrukturen kanske du vill lära dig mer om:
- Mer information om [BareMetal-infrastrukturen](concepts-baremetal-infrastructure-overview.md).
- Så här [ansluter du BareMetal Infrastructure instances i Azure](connect-baremetal-infrastructure.md).

