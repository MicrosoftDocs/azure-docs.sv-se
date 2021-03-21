---
title: Borttagning av ITSM-anslutaren och åtgärden som är kopplad till den
description: Den här artikeln innehåller en förklaring av hur du tar bort ITSM-anslutningen och de åtgärds grupper som är kopplade till den.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: e73510b6c61c58f6f0b2b8067a240214ee35a46c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036494"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Borttagning av oanvända ITSM-kopplingar

Processen att ta bort oanvänd koppling innehåller 2 faser:

1. Borttagning av associerade åtgärder: alla åtgärder som är associerade med ITSM-anslutningsprogrammet bör tas bort. Detta bör göras för att inte ha åtgärder utan anslutning som kan orsaka fel i din prenumeration.

2. Borttagning av den oanvända ITSM-anslutningen.

## <a name="deletion-of-the-associated-actions"></a>Borttagning av associerade åtgärder

1. För att hitta åtgärds gruppen ska du gå till skärm bilden "övervaka"  ![ för att övervaka val.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Välj "aviseringar"  ![ skärm bild av aviserings urvalet.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Välj  ![ skärm bild av alternativet hantera åtgärder.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Välj alla ITSM-kopplingar som är anslutna till Cherwell  ![ skärm bild av ITSM-kopplingar som är anslutna till Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Ta bort skärm bilden åtgärds grupp  ![ för borttagning av åtgärds grupp.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Borttagning av den oanvända ITSM-anslutningen

1. Du bör söka och välja "ServiceDesk"-LA i den övre delen av Sök fältet i  ![ Sök och välja "servicedesk" La.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Välj "ITSM Connections" och välj  ![ skärm bilden Cherwell Connector för CHERWELL ITSM-kopplingar.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Välj Ta bort  ![ skärm bild av ITSM Connector-borttagning.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)
