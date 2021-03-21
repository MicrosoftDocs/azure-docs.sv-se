---
title: Konfigurera mått varningar – Azure Portal – Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till mått varningar för Azure Database for MySQL från Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5b9e7938f404a37672fe8d13725b1e64d665bf18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576268"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Använd Azure Portal för att konfigurera aviseringar för mått för Azure Database for MySQL 

Den här artikeln visar hur du konfigurerar Azure Database for MySQL aviseringar med hjälp av Azure Portal. Du kan få en avisering utifrån övervaknings måtten för dina Azure-tjänster.

Aviseringen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen utlöses både när villkoret först uppfylls och därefter när villkoret inte längre uppfylls. 

Du kan konfigurera en avisering för att utföra följande åtgärder när den utlöser:
* Skicka e-postaviseringar till tjänst administratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webbhook

Du kan konfigurera och hämta information om aviserings regler med hjälp av:
* [Azure-portalen](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [REST-API:et för Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel för ett mått från Azure-portalen
1. I [Azure Portal](https://portal.azure.com/)väljer du den Azure Database for MySQLs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **aviseringar** som visas:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Välj aviserings regler":::

3. Välj **Lägg till mått avisering** (+ ikon).

4. Sidan **Skapa regel** öppnas som visas nedan. Fyll i nödvändig information:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Lägg till mått aviserings formulär":::

5. I avsnittet **villkor** väljer du **Lägg till villkor**.

6. Välj ett mått i listan över signaler att bli aviserad om. I det här exemplet väljer du "lagrings procent".
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Välj mått":::

7. Konfigurera aviserings logiken inklusive **villkoret** (t. ex. "Större än"), **tröskel** (t. ex. 85 procent), **tids agg regering** **, tids mängd som mått** regeln måste vara nöjd innan aviserings utlösare (t. ex. "Under de senaste 30 minuterna") och **frekvens**.
   
   Välj **klar** när du är klar.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Välj mått 2":::

8. I avsnittet **Åtgärds grupper** väljer du **Skapa nytt** för att skapa en ny grupp för att ta emot meddelanden på aviseringen.

9. Fyll i formuläret "Lägg till åtgärds grupp" med ett namn, ett kort namn, en prenumeration och en resurs grupp.

10. Konfigurera en **e-post/SMS/push/röst** -åtgärds typ.
    
    Välj "e-Azure Resource Manager roll" om du vill välja prenumerations ägare, deltagare och läsare för att ta emot meddelanden.
   
    Du kan också ange en giltig URI i fältet **webhook** om du vill att det ska anropas när aviseringen utlöses.

    Välj **OK** när du är klar.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Åtgärdsgrupp":::

11. Ange ett namn, en beskrivning och en allvarlighets grad för varnings regeln.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Åtgärds grupp 2"::: 

12. Välj **skapa aviserings regel** för att skapa aviseringen.

    Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du välja den och utföra följande åtgärder:

* Visa ett diagram som visar mått tröskelvärdet och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varnings regeln.
* **Inaktivera** eller **Aktivera** aviseringen om du tillfälligt vill stoppa eller återuppta mottagning av meddelanden.


## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [konfigurerar Webhooks i aviseringar](../azure-monitor/alerts/alerts-webhooks.md).
* Få en [Översikt över mått samlingen](../azure-monitor/data-platform.md) för att se till att tjänsten är tillgänglig och svarar.