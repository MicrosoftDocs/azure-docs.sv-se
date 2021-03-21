---
title: Visa och skapa frågor för Logic Apps i Azure Monitor loggar
description: Visa och skapa frågor i Azure Monitor loggar för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574556"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Visa och skapa frågor för att övervaka och spåra Azure Monitor loggar för Azure Logic Apps

Du kan visa de underliggande frågorna som producerar resultatet från [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md) och skapa frågor som filtrerar resultaten baserat på dina kriterier. Du kan till exempel hitta meddelanden baserat på ett speciellt utbytes kontroll nummer. Frågor använder [Kusto-frågespråket](/azure/data-explorer/kusto/query/), som du kan redigera om du vill visa olika resultat. Mer information finns i [Azure Monitor logg frågor](/azure/data-explorer/kusto/query/).

## <a name="prerequisites"></a>Förutsättningar

* En Log Analytics-arbetsyta. Om du inte har en Log Analytics arbets yta, lär du dig [hur du skapar en Log Analytics arbets yta](../azure-monitor/logs/quick-create-workspace.md).

* En Logic-app som har kon figurer ATS med Azure Monitor loggning och skickar informationen till en Log Analytics arbets yta. Lär dig [hur du konfigurerar Azure Monitor loggar för din Logic app](../logic-apps/monitor-logic-apps.md).

* Om du använder ett integrations konto kontrollerar du att du har konfigurerat kontot med Azure Monitor loggning för att skicka informationen till en Log Analytics arbets yta. Lär dig hur du [konfigurerar Azure Monitor loggning för ditt integrations konto](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Visa frågor bakom resultat

Följ dessa steg om du vill visa eller redigera frågan som genererar resultatet i arbets ytans Sammanfattning:

1. Välj **Visa alla** på alla resultat sidor längst ned.

   ![Visa alla resultat](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Sidan loggar öppnas och visar frågan bakom föregående resultat sida.

   ![Sidan loggar – frågevy](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. På sidan **loggar** kan du välja följande alternativ:

   * Om du vill visa frågeresultaten som en tabell går du till Frågeredigeraren och väljer **tabell**.

   * Om du vill ändra frågan uppdaterar du frågesträngen och väljer **Kör** för att visa resultaten i tabellen.

## <a name="create-your-own-query"></a>Skapa en egen fråga

Om du vill hitta eller filtrera resultat baserat på specifika egenskaper eller värden kan du skapa en egen fråga genom att starta från en tom fråga eller använda en befintlig fråga. Mer information finns i [Kom igång med logg frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md).

1. Leta upp och välj arbets ytan Log Analytics i [Azure Portal](https://portal.azure.com).

1. På arbets ytans meny väljer du **loggar** under **Allmänt**.

1. Börja med en tom fråga eller en tillgänglig befintlig fråga.

   * Om du vill kontrol lera om några befintliga frågor är tillgängliga går du till fråga-verktygsfältet och väljer antingen **exempel**  >  på fråge **Historik**, som visar frågor från föregående fråga körs, eller väljer **query Explorer**, som visar fördefinierade frågor.

     Logic Apps B2B-lösningen innehåller till exempel följande fördefinierade frågor:

     ![Börja med "Logic Apps B2B"-lösning för inbyggda frågor](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Börja med en tom fråga i Frågeredigeraren genom att skriva [Kusto-frågespråket](/azure/data-explorer/kusto/query/) för frågan.

     ![Starta med en tom fråga](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Nästa steg

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade spårnings scheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)