---
title: Ta bort itsm-anslutningsappar som inte används
description: Den här artikeln innehåller en förklaring av hur du tar bort ITSM-anslutningsappar och de åtgärdsgrupper som är associerade med den.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387946"
---
# <a name="delete-unused-itsm-connectors"></a>Ta bort itsm-anslutningsappar som inte används

Processen att ta bort oanvända Hantering av IT-tjänster (ITSM)-anslutningsappar (ITSM) har två faser. Du tar bort alla åtgärder som är associerade med en ITSM-anslutning och sedan tar du bort själva anslutningsappen. Du tar bort åtgärderna först eftersom åtgärder utan anslutning kan orsaka fel i din prenumeration.

## <a name="delete-associated-actions"></a>Ta bort associerade åtgärder

1. I Azure Portal du **Övervaka.**
  
    ![Skärmbild av valet Övervaka.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Välj **Aviseringar.**
   
    ![Skärmbild av valet Aviseringar.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Välj **Hantera åtgärder.**
   
    ![Skärmbild av valet Hantera åtgärder.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Välj en åtgärdsgrupp som är associerad med ITSM-anslutningsprogrammet som du vill ta bort. I den här artikeln används ett exempel på en Cherwell-anslutningsapp.
   
    ![Skärmbild av åtgärder som är associerade med Cherwell-anslutningsappen.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Granska informationen och välj sedan Ta **bort åtgärdsgrupp.**

    ![Skärmbild av information om åtgärdsgrupp och knappen för att ta bort gruppen.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Ta bort anslutningsappen

1. I sökfältet söker du efter **servicedesk**. Välj sedan **ServiceDesk** i listan över resurser.

    ![Skärmbild av sökning efter och val av ServiceDesk.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Välj **ITSM-anslutningar** och sedan Cherwell-anslutningsappen.

    ![Skärmbild av Cherwell I T S M-anslutningsappen.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Välj **Ta bort**.

    ![Skärmbild av borttagningsknappen för I T S M-anslutningsappen.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem i en ITSM-anslutning](./itsmc-resync-servicenow.md)
