---
title: Översikt över tillgänglighetsuppsättningar
description: Läs mer om tillgänglighetsuppsättningar i Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530574"
---
# <a name="availability-sets-overview"></a>Översikt över tillgänglighetsuppsättningar

Den här artikeln innehåller en översikt över tillgänglighetsfunktionerna i virtuella Azure-datorer (VM).

## <a name="what-is-an-availability-set"></a>Vad är en tillgänglighetsuppsättning? 

En tillgänglighetsuppsättning är en logisk gruppering av virtuella datorer som gör att Azure kan förstå hur ett program är utformat och kan tillhandahålla redundans och tillgänglighet. Vi rekommenderar att två eller flera virtuella datorer skapas i en tillgänglighetsuppsättning för att tillhandahålla ett program med hög tillgänglighet och för att uppfylla [99,95 % Serviceavtal för Azure.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Det kostar inget för själva tillgänglighetsuppsättningen, du betalar bara för varje VM-instans som du skapar.

## <a name="how-do-availability-sets-work"></a>Hur fungerar tillgänglighetsuppsättningar?
Varje virtuell dator i tillgänglighetsuppsättningen tilldelas en **uppdateringsdomän** och en **feldomän av** den underliggande Azure-plattformen. Varje tillgänglighetsuppsättning kan konfigureras med upp till tre feldomäner och tjugo uppdateringsdomäner. Uppdateringsdomäner anger grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Om fler än fem virtuella datorer har konfigurerats i en enskild tillgänglighetsuppsättning placeras den sjätte virtuella datorn i samma uppdateringsdomän som den första virtuella datorn. Den sjunde placeras i samma uppdateringsdomän som den andra virtuella datorn och så vidare. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om. En omstartad uppdateringsdomän får 30 minuter på sig för återställning innan underhållet initieras i en annan uppdateringsdomän.

Feldomäner definierar den grupp av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard är de virtuella datorer som konfigurerats i tillgänglighetsuppsättningen åtskilda mellan upp till tre feldomäner. Att placera de virtuella datorerna i en tillgänglighetsuppsättning skyddar inte ditt program mot operativsystemfel eller programspecifika fel, men det begränsar påverkan av potentiella fel på fysisk maskinvara, problem med nätverket och strömavbrott.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagram som visar olika beräkningskluster uppdelade i feldomäner och inom dessa feldomäner har vi flera uppdateringsdomäner":::

Virtuella datorer är också anpassade till diskfeldomäner. Den här justeringen säkerställer att alla hanterade diskar som är anslutna till en virtuell dator finns inom samma feldomäner. 

Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagram som visar hur feldomänerna för diskar och virtuella datorer är justerade.":::

## <a name="next-steps"></a>Nästa steg
Nu kan du börja använda dessa tillgänglighets- och redundansfunktioner till att bygga din egen Azure-miljö. Metodtips hittar du i [Metodtips för Azure-tillgänglighet](/azure/architecture/checklist/resiliency-per-service).

