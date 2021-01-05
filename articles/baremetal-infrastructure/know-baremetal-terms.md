---
title: Förstå villkoren i Azure BareMetal-infrastrukturen
description: Förstå villkoren i Azure BareMetal-infrastrukturen.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829218"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Känna till villkoren för BareMetal-infrastrukturen

I den här artikeln kommer vi att gå igenom viktiga BareMetal-termer.

- **Revision**: det finns två olika stämplar revisioner för BareMetal-instanser. Varje version skiljer sig i arkitekturen och närhet till värdar för virtuella Azure-datorer:
    - **Revision 3** (rev 3): är den ursprungliga designen.
    - **Revision 4** (rev 4): är en ny design som ger närmare närhet av virtuella Azure-värdar (VM) och minskar svars tiden mellan virtuella Azure-datorer och BareMetal-instans enheter. 
    - **Revision 4,2** (rev 4,2): är den senaste omanpassada BareMetal-infrastrukturen som använder den befintliga rev 4-arkitekturen. Du kan komma åt och hantera dina BareMetal-instanser via Azure Portal.  

- **Stamp**: definierar den interna distributions storleken för Microsoft BareMetal-instanser. Innan instans enheter kan distribueras måste en BareMetal-instans som består av beräknings-, nätverks-och lagrings rack distribueras på en data Center plats. En sådan distribution kallas för en BareMetal-instans eller från revision 4,2.

- **Klient organisation**: en kund som distribueras i BareMetal instance-stämplar isoleras i en *klient.* En klient är isolerad i nätverk, lagring och beräknings lager från andra klienter. Lagrings-och beräknings enheter som är tilldelade till olika klienter kan inte se varandra eller kommunicera med varandra på BareMetal-instansens Stamp-nivå. En kund kan välja att distribuera olika klienter. Det finns inte heller någon kommunikation mellan klienter på BareMetal-instansens Stamp-nivå.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du identifierar och interagerar med BareMetal-instans enheter via [Azure Portal](workloads/sap/baremetal-infrastructure-portal.md).


 