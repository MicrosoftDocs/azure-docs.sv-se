---
title: Skapa aviseringar med SQL-insikter (förhandsversion)
description: Skapa aviseringar med SQL-insikter i Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726870"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Skapa aviseringar med SQL-insikter (förhandsversion)
SQL-insikter innehåller en uppsättning aviseringsregelmallar som du kan använda för att skapa [aviseringsregler i Azure Monitor](../alert/../alerts/alerts-overview.md) för vanliga SQL-problem. Aviseringsreglerna i SQL Insights är loggvarningsregler baserat på prestandadata som lagras i *tabellen InsightsMetrics* i Azure Monitor Logs.  

> [!NOTE]
> Information om hur du skapar en avisering för SQL-insikter med hjälp av en Resource Manager-mall finns [i Resource Manager för SQL-insikter.](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights)


> [!NOTE]
> Om du har begäranden om fler aviseringsregelmallar för SQL-insikter skickar du feedback med hjälp av länken längst ned på den här sidan eller via länken för SQL Insights-feedback i Azure Portal.

## <a name="enable-alert-rules"></a>Aktivera aviseringsregler 
Använd följande steg för att aktivera aviseringarna i Azure Monitor från Azure Portal.Aviseringsreglerna som skapas är begränsade till alla SQL-resurser som övervakas under den valda övervakningsprofilen.  När en aviseringsregel utlöses utlöses den på den specifika SQL-instansen eller databasen.

> [!NOTE]
> Du kan också skapa anpassade [loggvarningsregler](../alerts/alerts-log.md) genom att köra frågor på datauppsättningarna i *tabellen InsightsMetrics* och sedan spara dessa frågor som en aviseringsregel. 

Välj **SQL (förhandsversion)** i **avsnittet Insikter** på Azure Monitor på Azure Portal. Klicka på **Aviseringar**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Knappen Aviseringar":::

Fönstret **Aviseringar** öppnas till höger på sidan. Som standard visas utställda aviseringar för SQL-resurser i den valda övervakningsprofilen baserat på de aviseringsregler som du redan har skapat. Välj **Aviseringsmallar,** som visar listan över tillgängliga mallar som du kan använda för att skapa en aviseringsregel.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Aviseringsmallar":::

På sidan **Skapa aviseringsregel** granskar du standardinställningarna för regeln och redigerar dem efter behov. Du kan också välja en [åtgärdsgrupp för](../alerts/action-groups.md) att skapa meddelanden och åtgärder när aviseringsregeln utlöses. Klicka **på Aktivera aviseringsregel** för att skapa aviseringsregeln när du har verifierat alla dess egenskaper.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Sidan Aviseringsregler":::

Om du vill distribuera aviseringsregeln direkt klickar du **på Distribuera aviseringsregel**. Klicka **på Visa** mall om du vill visa regelmallen innan du distribuerar den.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Distribuera aviseringsregel":::

Om du väljer att visa mallarna väljer du **Distribuera på** mallsidan för att skapa aviseringsregeln.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Distribuera från vymallen":::


## <a name="next-steps"></a>Nästa steg

Läs mer om [aviseringar i Azure Monitor](../alerts/alerts-overview.md).

