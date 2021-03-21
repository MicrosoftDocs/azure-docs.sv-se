---
title: Skapa aviseringar med SQL Insights (förhands granskning)
description: Skapa aviseringar med SQL Insights i Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610161"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Skapa aviseringar med SQL Insights (förhands granskning)
SQL Insights innehåller en uppsättning mallar för aviserings regler som du kan använda för att skapa [aviserings regler i Azure Monitor](../alert/../alerts/alerts-overview.md) för vanliga SQL-problem. Varnings reglerna i SQL Insights är logg aviserings regler baserade på prestanda data som lagras i tabellen *InsightsMetrics* i Azure Monitor loggar.  

> [!NOTE]
> Om du har begär Anden om fler SQL Insights-aviseringar kan du skicka feedback med hjälp av länken längst ned på den här sidan eller använda länken SQL Insights feedback i Azure Portal.

## <a name="enable-alert-rules"></a>Aktivera varnings regler 
Använd följande steg för att aktivera aviseringarna i Azure Monitor från Azure Portal.De aviserings regler som skapas är begränsade till alla SQL-resurser som övervakas under den valda övervaknings profilen.  När en varnings regel utlöses utlöses den på den aktuella SQL-instansen eller databasen.

> [!NOTE]
> Du kan också skapa anpassade [varnings regler](../alerts/alerts-log.md) genom att köra frågor på data uppsättningarna i tabellen *InsightsMetrics* och sedan spara frågorna som en varnings regel. 

Välj **SQL (för hands version)** i avsnittet **insikter** på Azure Monitor-menyn i Azure Portal. Klicka på **aviseringar**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Knappen aviseringar":::

Fönstret **aviseringar** öppnas till höger på sidan. Som standard visas utlösta aviseringar för SQL-resurser i den valda övervaknings profilen baserat på de aviserings regler som du redan har skapat. Välj **Alert templates**, som visar en lista över tillgängliga mallar som du kan använda för att skapa en aviserings regel.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Alert-mallar":::

På sidan **skapa aviserings regel** granskar du standardinställningarna för regeln och redigerar dem efter behov. Du kan också välja en [Åtgärds grupp](../alerts/action-groups.md) för att skapa meddelanden och åtgärder när aviserings regeln utlöses. Klicka på **Aktivera aviserings regel** om du vill skapa en varnings regel när du har verifierat alla egenskaper.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Sidan aviserings regler":::

Om du vill distribuera aviserings regeln omedelbart klickar du på **distribuera aviserings regel**. Klicka på **Visa mall** om du vill visa regel mal len innan du distribuerar den.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Distribuera aviserings regel":::

Om du väljer att Visa mallarna väljer du **distribuera** från sidan mall för att skapa aviserings regeln.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Distribuera från Visa mall":::


## <a name="next-steps"></a>Nästa steg

Lär dig mer om [aviseringar i Azure Monitor](../alerts/alerts-overview.md).

