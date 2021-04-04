---
title: Översikt över underhålls kontroll för uppgraderingar av OS-avbildningar i skalnings uppsättningar för virtuella Azure-datorer
description: Lär dig hur du styr när automatiska uppgraderingar av operativ system avbildningar distribueras till dina skalnings uppsättningar för virtuella Azure-datorer med hjälp av underhålls kontroll.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532705"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>För hands version: underhålls kontroll för skalnings uppsättningar för virtuella Azure-datorer 

Hantera [automatiska uppgradering av operativ system avbildningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) för skalnings uppsättningar för virtuella datorer med underhålls kontroll.

Med underhålls kontrollen kan du bestämma när du ska tillämpa uppdateringar på OS-diskar i skalnings uppsättningar för virtuella datorer via en enklare och mer förutsägbar upplevelse. 

Underhålls konfigurationer fungerar mellan prenumerationer och resurs grupper.

Hela arbetsflödet består av följande övergripande steg: 
- Skapa en underhålls konfiguration.
- Koppla en skalnings uppsättning för virtuell dator till en underhålls konfiguration.
- Aktivera automatiska uppgraderingar av operativ systemet.

> [!IMPORTANT]
> Underhålls kontroll för uppgraderingar av OS-avbildningar på virtuella Azure-dators skalnings uppsättningar finns för närvarande i offentlig för hands
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Begränsningar

- Virtuella datorer måste finnas i en skalnings uppsättning.
- Användaren måste ha åtkomst till **resurs deltagare** .
- Underhålls tiden måste vara 5 timmar eller längre i underhålls konfigurationen.
- Upprepning av underhåll måste anges till Day i underhålls konfigurationen


## <a name="management-options"></a>Hanterings alternativ

Du kan skapa och hantera underhålls konfigurationer med något av följande alternativ:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du styr när underhåll tillämpas på dina skalnings uppsättningar för virtuella Azure-datorer med hjälp av underhålls kontroll och PowerShell.

> [!div class="nextstepaction"]
> [Underhålls kontroll av skalnings uppsättning för virtuell dator med hjälp av PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
