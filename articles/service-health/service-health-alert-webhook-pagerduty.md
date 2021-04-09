---
title: Skicka Azure Service Health-aviseringar med PagerDuty
description: Få personligt anpassade meddelanden om service Health-händelser till din PagerDuty-instans.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7ae5141f33ebf4ec6e32029625af3a199a04d67b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594528"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Skicka Azure Service Health-aviseringar med PagerDuty med webhookar

Den här artikeln visar hur du konfigurerar Azure Service Health-aviseringar via PagerDuty med en webhook. Genom att använda [PagerDuty](https://www.pagerduty.com/)anpassade Microsoft Azure integrations typ kan du smidigt lägga till service Health aviseringar till dina nya eller befintliga PagerDuty-tjänster.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Skapa en URL för tjänste hälso integrering i PagerDuty
1.  Kontrol lera att du har registrerat dig för och är inloggad på ditt [PagerDuty](https://www.pagerduty.com/) -konto.

1.  Gå till avsnittet **tjänster** i PagerDuty.

    ![Avsnittet "tjänster" i PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Välj **Lägg till ny tjänst** eller öppna en befintlig tjänst som du har konfigurerat.

1.  I **integrations inställningarna** väljer du följande:

    a. **Integrations typ**: Microsoft Azure

    b. **Integrations namn**: \<Name\>

    !["Integrations inställningar" i PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Fyll i alla andra obligatoriska fält och välj **Lägg till**.

1.  Öppna den här nya integrationen och kopiera och spara **integrations-URL:** en.

    !["Integrations-URL" i PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Skapa en avisering med PagerDuty i Azure Portal
### <a name="for-a-new-action-group"></a>För en ny åtgärds grupp:
1. Följ steg 1 till 8 i [skapa en avisering om ett tjänst hälso meddelande för en ny åtgärds grupp med hjälp av Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. Definiera i listan med **åtgärder**:

    a. **Åtgärds typ:** *webhook*

    b. **Information:** PagerDuty- **integreringens URL** som du sparade tidigare.

    c. **Namn:** Webhookens namn, alias eller identifierare.

1. Välj **Spara** när du är färdig för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärds grupp:
1. I [Azure Portal](https://portal.azure.com/)väljer du **övervaka**.

1. I avsnittet **Inställningar** väljer du **Åtgärds grupper**.

1. Sök efter och välj den åtgärds grupp som du vill redigera.

1. Lägg till i listan med **åtgärder**:

    a. **Åtgärds typ:** *webhook*

    b. **Information:** PagerDuty- **integreringens URL** som du sparade tidigare.

    c. **Namn:** Webhookens namn, alias eller identifierare.

1. Välj **Spara** när du är färdig om du vill uppdatera åtgärds gruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa den tjänst hälso nytto last som du vill skicka. Du hittar ett exempel på en service Health webhook-nyttolast vid [Webhooks för Azure aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör få ett `202 Accepted` meddelande som innehåller ditt "händelse-ID".

1. Gå till [PagerDuty](https://www.pagerduty.com/) för att bekräfta att din integrering har kon figurer ATS korrekt.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md).
- Granska [aktivitets logg aviseringens webhook-schema](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Läs mer om [meddelanden om tjänst hälsa](./service-notifications.md).
- Läs mer om [Åtgärds grupper](../azure-monitor/alerts/action-groups.md).
