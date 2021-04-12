---
title: Hantera start/stoppa VM v2 (för hands version)
description: Den här artikeln beskriver hur du övervakar status för dina virtuella Azure-datorer som hanteras av funktionen starta/stoppa virtuella datorer v2 (för hands version) och utför andra hanterings uppgifter.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112183"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Så här hanterar du starta/stoppa VM v2 (för hands version)

## <a name="azure-dashboard"></a>Azure-instrumentpanel

Starta/stoppa virtuella datorer v2 (för hands version) innehåller en [instrument panel](../../azure-monitor/visualizations.md#azure-dashboards) som hjälper dig att förstå hanterings omfånget och de senaste åtgärderna för dina virtuella datorer. Det är ett snabbt och enkelt sätt att kontrol lera status för varje åtgärd som utförs på dina virtuella Azure-datorer. Visualiseringen i varje panel baseras på en logg fråga och du kan se frågan genom att välja **bladet öppna i loggar** i det högra hörnet av panelen. Detta öppnar [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) -verktyget i Azure Portal och härifrån kan du utvärdera frågan och ändra för att stödja dina behov, t. ex. anpassade [logg aviseringar](../../azure-monitor/alerts/alerts-log.md), en anpassad [arbets bok](../../azure-monitor/visualize/workbooks-overview.md)osv.

Logg data varje panel på instrument panelen visas varje timme, med ett manuellt uppdaterings alternativ på begäran genom att klicka på **uppdaterings** ikonen i en specifik visualisering, eller genom att uppdatera hela instrument panelen.

Information om hur du arbetar med en logg baserad instrument panel finns i följande [självstudie](../../azure-monitor/visualize/tutorial-logs-dashboards.md).

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Om du vill ändra e-postaviseringar när du har distribuerat/stoppa VM v2 (för hands version) kan du ändra den åtgärds grupp som skapades under distributionen.

1. I Azure Portal går du till **övervaka**, sedan **aviseringar**. Välj **Hantera åtgärder**.

1. På sidan **Hantera åtgärder** väljer du den åtgärds grupp som heter **StartStopV2_VM_Notication**.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Skärm bild av sidan åtgärds grupper.":::

1. På sidan **StartStopV2_VM_Notification** kan du ändra meddelande alternativen e-post/SMS/push/Voice. Lägg till andra åtgärder eller uppdatera din befintliga konfiguration till den här åtgärds gruppen och klicka sedan på **OK** för att spara ändringarna.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Skärm bild av sidan e-post/SMS/push/Voice som visar ett exempel på en e-postadress som uppdaterats.":::

    Läs mer om åtgärds grupper i [Åtgärds grupper](../../azure-monitor/alerts/action-groups.md)

Följande skärm bild är ett exempel på ett e-postmeddelande som skickas när funktionen stänger av virtuella datorer.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Skärm bild av ett exempel på ett e-postmeddelande som skickas när funktionen stänger av virtuella datorer.":::

## <a name="next-steps"></a>Nästa steg

Information om hur du hanterar problem under hantering av virtuella datorer finns i Felsöka problem med [Starta/stoppa virtuella datorer v2 (för](troubleshoot.md) hands version).