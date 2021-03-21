---
title: Ändra analys i VM Insights
description: Integrering av VM Insights med integrering av program ändrings analys gör att du kan visa alla ändringar som gjorts på en virtuell dator som kan påverka IT-prestanda.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046779"
---
# <a name="change-analysis-in-vm-insights"></a>Ändra analys i VM Insights
Integrering av VM Insights med integrering av [program ändrings analys](../app/change-analysis.md) gör att du kan visa alla ändringar som gjorts på en virtuell dator som kan påverka IT-prestanda.

## <a name="overview"></a>Översikt
Anta att du har en virtuell dator som körs långsamt och vill undersöka om de senaste ändringarna i konfigurationen kan påverka prestandan. Du kan visa prestanda för den virtuella datorn med hjälp av VM-insikter och se att det finns en ökning av minnes användningen under den senaste timmen. Med ändrings analys kan du avgöra om eventuella konfigurations ändringar som gjorts under den här tiden är orsaken till den här ökningen.

Tjänsten för program ändrings analys samlar in ändringar från [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) och kapslade egenskaper, t. ex. nätverks säkerhets regler från Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Aktivera ändrings analys
Om du vill utföra en ändrings analys i VM-insikter måste du registrera Resource-providern för *Microsoft. ChangeAnalysis* . Första gången du startar VM-insikter eller program ändrings analys i Azure Portal registreras den här resurs leverantören automatiskt åt dig. Program ändrings analys är en kostnads fri tjänst som inte har resurser som är kostnads fria.

## <a name="view-change-analysis"></a>Visa ändrings analys
Ändrings analys är tillgängligt från fliken **prestanda** eller **karta** för VM-insikter genom att välja alternativet **ändra** . 

[![Undersök ändringar](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Klicka på knappen **Undersök ändringar** för att starta sidan för program ändrings analys som filtrerats för den virtuella datorn. Du kan granska ändringarna i listan för att se om det finns något som kan ha orsakat problemet. Om du är osäker på om du har en viss ändring kan du referera till kolumnen **ändra efter** för att avgöra vilken person som gjorde ändringen.

[![Ändra information](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [program ändrings analys](../app/change-analysis.md).
- Lär dig mer om [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

