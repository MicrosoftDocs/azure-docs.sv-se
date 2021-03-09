---
title: Översikt över tillgänglighets uppsättningar
description: Lär dig mer om tillgänglighets uppsättningar i Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c0bbc218723ea75064d9eb6f9c5f8f8c0d18a10b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510591"
---
# <a name="availability-sets-overview"></a>Översikt över tillgänglighets uppsättningar

Den här artikeln ger en översikt över tillgänglighets funktionerna i Azure Virtual Machines (VM).

## <a name="what-is-an-availability-set"></a>Vad är en tillgänglighetsuppsättning? 

En tillgänglighetsuppsättning är en logisk gruppering av virtuella datorer som gör att Azure kan förstå hur ett program är utformat och kan tillhandahålla redundans och tillgänglighet. Vi rekommenderar att två eller flera virtuella datorer skapas i en tillgänglighets uppsättning för att tillhandahålla ett program med hög tillgänglighet och uppfylla [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För själva tillgänglighets uppsättningen kostar du bara att betala för varje VM-instans som du skapar.

## <a name="how-do-availability-sets-work"></a>Hur fungerar tillgänglighets uppsättningar?
Varje virtuell dator i din tillgänglighets uppsättning tilldelas en **uppdaterings domän** och en **feldomän** av den underliggande Azure-plattformen. För en viss tillgänglighetsuppsättning tilldelas som standard fem uppdateringsdomäner, som inte kan konfigureras av användare, (Resource Manager-distributioner kan utökas till 20 uppdateringsdomäner) för att ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Om fler än fem virtuella datorer har konfigurerats i en enskild tillgänglighetsuppsättning placeras den sjätte virtuella datorn i samma uppdateringsdomän som den första virtuella datorn. Den sjunde placeras i samma uppdateringsdomän som den andra virtuella datorn och så vidare. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om. En omstartad uppdateringsdomän får 30 minuter på sig för återställning innan underhållet initieras i en annan uppdateringsdomän.

Feldomäner definierar den grupp av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard är de virtuella datorer som kon figurer ATS i din tillgänglighets uppsättning åtskilda över upp till tre fel domäner för Resource Manager-distributioner. Att placera de virtuella datorerna i en tillgänglighetsuppsättning skyddar inte ditt program mot operativsystemfel eller programspecifika fel, men det begränsar påverkan av potentiella fel på fysisk maskinvara, problem med nätverket och strömavbrott.

![Tillgänglighetsuppsättningar](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


För virtuella datorer som använder [Azure Managed Disks](./faq-for-disks.md) justeras de virtuella datorerna efter feldomänerna för hanterade diskar när en hanterad tillgänglighetsuppsättning används. På så sätt säkerställs att alla hanterade diskar som är kopplade till en virtuell dator finns i samma feldomän. 

Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region. 

![Hanterad tillgänglighets uppsättning](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="next-steps"></a>Nästa steg
Nu kan du börja använda dessa tillgänglighets- och redundansfunktioner till att bygga din egen Azure-miljö. Metodtips hittar du i [Metodtips för Azure-tillgänglighet](/azure/architecture/checklist/resiliency-per-service).

