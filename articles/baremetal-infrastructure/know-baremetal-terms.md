---
title: Förstå villkoren i Azure BareMetal-infrastrukturen
description: Förstå villkoren i Azure BareMetal-infrastrukturen.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: b22a6cecb2647df3878cb8fd4ade93d9a7d963fd
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770894"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Känna till villkoren för BareMetal-infrastrukturen

I den här artikeln kommer vi att gå igenom viktiga BareMetal-termer.

- **Revision**: det finns en ursprunglig Stamp-revision som kallas revision 3 (rev 3) och två olika stämplar revisioner för BareMetal-instanser. Varje stämpel skiljer sig i arkitekturen och närhet till virtuella Azure-värddatorer:
    - **Revision 4** (rev 4): en nyare design som ger närmare närhet av virtuella Azure-datorer (VM) och sänker fördröjningen mellan virtuella Azure-datorer och BareMetal-instans enheter. 
    - **Revision 4,2** (rev 4,2): den senaste anpassade BareMetal-infrastrukturen med den befintliga rev 4-arkitekturen. Rev 4 ger närmare närhet till Azures virtuella dator värdar. Den har betydande förbättringar i nätverks fördröjningen mellan virtuella Azure-datorer och BareMetal-instanser som distribueras i rev 4-stämplar eller rader. Du kan komma åt och hantera dina BareMetal-instanser via Azure Portal.    

- **Stamp**: definierar den interna distributions storleken för Microsoft BareMetal-instanser. Innan instans enheter kan distribueras måste en BareMetal-instans som består av beräknings-, nätverks-och lagrings rack distribueras på en plats i data centret. En sådan distribution kallas för en BareMetal-instans eller från revision 4,2.

- **Klient organisation**: en kund som distribueras i BareMetal instance-stämplar isoleras i en *klient.* En klient är isolerad i nätverk, lagring och beräknings lager från andra klienter. Lagrings-och beräknings enheter som är tilldelade till olika klienter kan inte se varandra eller kommunicera med varandra på BareMetal-instansens Stamp-nivå. En kund kan välja att distribuera olika klienter. Det finns inte heller någon kommunikation mellan klienter på BareMetal-instansens Stamp-nivå.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [BareMetal-infrastrukturen](concepts-baremetal-infrastructure-overview.md) eller hur du [identifierar och interagerar med BareMetal instans enheter](connect-baremetal-infrastructure.md). 