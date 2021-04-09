---
title: Skicka Azure Service Health-aviseringar med OpsGenie med webhookar
description: Få personligt anpassade meddelanden om service Health-händelser till din OpsGenie-instans.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: d8867d442d0c7fe563f6429fc1ff4edb212737c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594593"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Skicka Azure Service Health-aviseringar med OpsGenie med webhookar

Den här artikeln visar hur du konfigurerar Azure Service Health-aviseringar med OpsGenie med hjälp av en webhook. Genom att använda [OpsGenie](https://www.opsgenie.com/)Azure Service Health-integrering kan du vidarebefordra Azure Service Health aviseringar till OpsGenie. OpsGenie kan bestämma vilka personer som ska meddelas baserat på jour scheman, använda e-post, SMS (SMS), telefonsamtal, iOS & Android push-meddelanden och eskalera aviseringar tills aviseringen har bekräftats eller stängts.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Skapa en URL för tjänste hälso integrering i OpsGenie
1.  Kontrol lera att du har registrerat dig för och är inloggad på ditt [OpsGenie](https://www.opsgenie.com/) -konto.

1.  Navigera till avsnittet **integreringar** i OpsGenie.

    ![Avsnittet "integreringar" i OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Välj knappen **Azure Service Health** integrering.

    ![Knappen Azure Service Health i OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Ge** aviseringen ett namn och ange fältet **tilldelad till Team** .

1.  Fyll i de andra fälten, t. ex. **mottagare**, **aktiverade** och **Ignorera meddelanden**.

1.  Kopiera och spara **integrations-URL**: en, som redan ska innehålla din `apiKey` bifogad fil till slutet.

    !["Integrations-URL" i OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Välj **Spara integrering**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Skapa en avisering med OpsGenie i Azure Portal
### <a name="for-a-new-action-group"></a>För en ny åtgärds grupp:
1. Följ steg 1 till 8 i [skapa en avisering om ett tjänst hälso meddelande för en ny åtgärds grupp med hjälp av Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. Definiera i listan med **åtgärder**:

    a. **Åtgärds typ:** *webhook*

    b. **Information:** OpsGenie- **integreringens URL** som du sparade tidigare.

    c. **Namn:** Webhookens namn, alias eller identifierare.

1. Välj **Spara** när du är färdig för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärds grupp:
1. I [Azure Portal](https://portal.azure.com/)väljer du **övervaka**.

1. I avsnittet **Inställningar** väljer du **Åtgärds grupper**.

1. Sök efter och välj den åtgärds grupp som du vill redigera.

1. Lägg till i listan med **åtgärder**:

    a. **Åtgärds typ:** *webhook*

    b. **Information:** OpsGenie- **integreringens URL** som du sparade tidigare.

    c. **Namn:** Webhookens namn, alias eller identifierare.

1. Välj **Spara** när du är färdig om du vill uppdatera åtgärds gruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa den tjänst hälso nytto last som du vill skicka. Du hittar ett exempel på en service Health webhook-nyttolast vid [Webhooks för Azure aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör få ett `200 OK` svar med meddelandet status "lyckades".

1. Gå till [OpsGenie](https://www.opsgenie.com/) för att bekräfta att din integrering har kon figurer ATS korrekt.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md).
- Granska [aktivitets logg aviseringens webhook-schema](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Läs mer om [meddelanden om tjänst hälsa](./service-notifications.md).
- Läs mer om [Åtgärds grupper](../azure-monitor/alerts/action-groups.md).
