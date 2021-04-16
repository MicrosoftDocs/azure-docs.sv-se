---
title: IoT-fjärrövervakning och meddelanden med Azure Logic Apps | Microsoft Docs
description: Använd Azure Logic Apps IoT-temperaturövervakning på din IoT-hubb och skicka automatiskt e-postmeddelanden till din postlåda om eventuella avvikelser har identifierats.
author: robinsh
keywords: ioT-övervakning, ioT-meddelanden, ioT-temperaturövervakning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 74724357dea9cd6c8c89a11a9eeb3d1b2933b790
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564951"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT-fjärrövervakning och meddelanden med Azure Logic Apps ansluter din IoT-hubb och postlåda

![Diagram från end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) kan hjälpa dig att samordna arbetsflöden mellan lokala tjänster och molntjänster, ett eller flera företag och över olika protokoll. En logikapp börjar med en utlösare som sedan följs av en eller flera åtgärder som kan sekvenseras med hjälp av inbyggda kontroller, till exempel villkor och iteratorer. Den här flexibiliteten gör Logic Apps en perfekt IoT-lösning för IoT-övervakningsscenarier. Till exempel kan inkommande telemetridata från en enhet vid en IoT Hub-slutpunkt initiera logikapparbetsflöden för att lagra data i en Azure Storage-blob, skicka e-postaviseringar för att varna om dataavvikelser, schemalägga ett reparatörsbesök om en enhet rapporterar ett fel och så vidare.

I den här artikeln får du lära dig hur du skapar en logikapp som ansluter din IoT-hubb och din postlåda för temperaturövervakning och meddelanden. Klientkoden som körs på enheten anger en programegenskap, , för varje `temperatureAlert` telemetrimeddelande som skickas till din IoT-hubb. När klientkoden identifierar en temperatur över 30 C anger den här egenskapen till . Annars anges `true` egenskapen till `false` .

Meddelanden som kommer till din IoT-hubb ser ut ungefär så här: telemetridata i brödtexten och egenskapen i programegenskaperna `temperatureAlert` (systemegenskaperna visas inte):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Mer information om hur IoT Hub-format finns i [Skapa och läsa IoT Hub meddelanden.](iot-hub-devguide-messages-construct.md)

I det här avsnittet ställer du in routning på din IoT-hubb för att skicka meddelanden där egenskapen är till `temperatureAlert` `true` en Service Bus slutpunkt. Sedan ställer du in en logikapp som utlöser meddelanden som kommer till Service Bus slutpunkten och skickar dig ett e-postmeddelande.

## <a name="prerequisites"></a>Förutsättningar

* Slutför [självstudien om Raspberry Pi-onlinesimulatorn](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av självstudierna för enheten. Du kan till exempel gå till [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md) eller till någon av snabbstarterna för att skicka [telemetri.](quickstart-send-telemetry-dotnet.md) De här artiklarna omfattar följande krav:

  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb under din prenumeration.
  * Ett klientprogram som körs på enheten och som skickar telemetrimeddelanden till Din Azure IoT-hubb.

## <a name="create-service-bus-namespace-and-queue"></a>Skapa Service Bus namnområde och kö

Skapa ett namnområde och en kö för Service Bus. Senare i det här avsnittet skapar du en routningsregel i din IoT-hubb för att dirigera meddelanden som innehåller en temperaturavisering till Service Bus-kön, där de hämtas av en logikapp och utlöser den för att skicka ett e-postmeddelande.

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

1. På [Azure Portal](https://portal.azure.com/)väljer du **+ Skapa en resursintegrering**  >    >  **Service Bus**.

1. Ange **följande information i** fönstret Skapa namnområde:

   **Namn:** Namnet på Service Bus-namnområdet. Namnområdet måste vara unikt i Azure.

   **Prisnivå:** Välj **Basic** i listrutan. Basic-nivån räcker för den här självstudien.

   **Resursgrupp:** Använd samma resursgrupp som din IoT Hub använder.

   **Plats:** Använd samma plats som IoT-hubben använder.

   ![Skapa ett Service Bus-namnområde i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Välj **Skapa**. Vänta tills distributionen har slutförts innan du går vidare till nästa steg.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Lägga till Service Bus en kö i namnområdet

1. Öppna Service Bus namnområdet. Det enklaste sättet att komma Service Bus-namnområdet  är att välja Resursgrupper i resursfönstret, välja din resursgrupp och sedan Service Bus namnområdet i listan över resurser.

1. I fönstret **Service Bus namnområde** väljer du **+ Kö**.

1. Ange ett namn för kön och välj sedan **Skapa.** När kön har skapats stängs **fönstret Skapa** kö.

   ![Lägga till en Service Bus-kö i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. I fönstret **Service Bus namnområde** väljer **du** Köer under **Entiteter.** Öppna kön Service Bus listan och välj sedan Principer för **delad åtkomst +** Lägg  >  **till**.

1. Ange ett namn för principen, markera **Hantera** och välj sedan **Skapa.**

   ![Lägga till en Service Bus-köprincip i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Lägga till en anpassad slutpunkt och routningsregel i din IoT-hubb

Lägg till en anpassad slutpunkt för Service Bus-kön i IoT-hubben och skapa en meddelanderoutningsregel för att dirigera meddelanden som innehåller en temperaturavisering till slutpunkten, där de hämtas av logikappen. Routningsregeln använder en routningsfråga, , för att vidarebefordra meddelanden baserat på värdet för programegenskapen som angetts av `temperatureAlert = "true"` `temperatureAlert` klientkoden som körs på enheten. Mer information finns i [Meddelanderoutningsfråga baserat på meddelandeegenskaper.](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)

### <a name="add-a-custom-endpoint"></a>Lägga till en anpassad slutpunkt

1. Öppna din IoT-hubb. Det enklaste sättet att komma till IoT-hubben är att välja Resursgrupper i resursfönstret, välja din resursgrupp och sedan välja din IoT-hubb i listan över resurser. 

1. Under **Meddelanden** väljer du **Meddelanderoutning.** I fönstret **Meddelanderoutning** väljer du **fliken Anpassade slutpunkter** och sedan **+ Lägg till**. Välj Service Bus-kö i **listrutan.**

   ![Skärmbild som visar alternativet Service Bus-kö.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. I fönstret **Lägg till en Service Bus-slutpunkt** anger du följande information:

   **Slutpunktens** namn: Namnet på slutpunkten.

   **Service Bus-namnområde:** Välj det namnområde som du skapade.

   **Service Bus-kö:** Välj den kö som du skapade.

   ![Lägg till en slutpunkt i IoT-hubben i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Välj **Skapa**. När slutpunkten har skapats går du vidare till nästa steg.

### <a name="add-a-routing-rule"></a>Lägga till en hanteringsregel

1. När du är tillbaka **i meddelanderoutningsfönstret** väljer du **fliken** Vägar och sedan + Lägg **till**.

1. I fönstret **Lägg till en** väg anger du följande information:

   **Namn:** Namnet på routningsregeln.

   **Slutpunkt:** Välj den slutpunkt som du skapade.

   **Datakälla:** Välj **Enhettelemetrimeddelanden**.

   **Routningsfråga:** Ange `temperatureAlert = "true"` .

   ![Lägga till en routningsregel i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Välj **Spara**. Du kan stänga **meddelanderoutningsfönstret.**

## <a name="create-and-configure-a-logic-app"></a>Skapa och konfigurera en logikapp

I föregående avsnitt konfigurerade du din IoT-hubb för att dirigera meddelanden som innehåller en temperaturavisering till din Service Bus kö. Nu ska du konfigurera en logikapp för att övervaka Service Bus kön och skicka ett e-postmeddelande när ett meddelande läggs till i kön.

### <a name="create-a-logic-app"></a>Skapa en logikapp

1. Välj **Create a resource** Integration Logic App (Skapa en  >    >  **logikapp för resursintegrering).**

1. Ange följande information:

   **Namn:** Namnet på logikappen.

   **Resursgrupp:** Använd samma resursgrupp som din IoT Hub använder.

   **Plats:** Använd samma plats som IoT-hubben använder.

   ![Skapa en logikapp i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Välj **Skapa**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurera logikappens utlösare

1. Öppna logikappen. Det enklaste sättet att komma åt  logikappen är att välja Resursgrupper i resursfönstret, välja din resursgrupp och sedan välja din logikapp i listan över resurser. När du väljer logikappen öppnas Logic Apps Designer.

1. I Logic Apps Designer bläddrar du ned till **Mallar och** väljer **Tom logikapp.**

   ![Börja med en tom logikapp i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Välj fliken **Alla** och välj sedan **Service Bus**.

   ![Välj Service Bus för att börja skapa logikappen i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Under **Utlösare** väljer du **När ett eller flera meddelanden tas emot i en kö (komplettera automatiskt).**

   ![Välj utlösaren för logikappen i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Skapa en Service Bus-anslutning.
   1. Ange ett anslutningsnamn och välj Service Bus namnområdet i listan. Nästa skärm öppnas.

      ![Skärmbild som visar alternativet När ett eller flera meddelanden tas emot i en kö (komplettera automatiskt).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Välj Service Bus-principen (RootManageSharedAccessKey). Välj sedan **Skapa.**

      ![Skapa en Service Bus-anslutning för logikappen i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. På den sista skärmen går du **till Könamn** och väljer den kö som du skapade från listrutan. Ange `175` för **Maximalt antal meddelanden.**

      ![Ange det maximala antalet meddelanden för Service Bus-anslutningen i logikappen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Välj **Spara** på menyn längst upp i Logic Apps Designer för att spara ändringarna.

### <a name="configure-the-logic-app-action"></a>Konfigurera logikappåtgärden

1. Skapa en SMTP-tjänstanslutning.

   1. Välj **Nytt steg**. I **Välj en åtgärd** väljer du **fliken** Alla.

   1. Skriv `smtp` i sökrutan, välj **SMTP-tjänsten** i sökresultatet och välj sedan Skicka **e-post.**

      ![Skapa en SMTP-anslutning i logikappen i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Ange SMTP-informationen för postlådan och välj sedan **Skapa.**

      ![Ange SMTP-anslutningsinformation i logikappen i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Hämta SMTP-informationen för [Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)och [Yahoo Mail.](https://help.yahoo.com/kb/SLN4075.html)

      > [!NOTE]
      > Du kan behöva inaktivera TLS/SSL för att upprätta anslutningen. Om så är fallet och du vill återaktivera TLS när anslutningen har upprättats kan du gå till det valfria steget i slutet av det här avsnittet.

   1. I **listrutan Lägg till ny parameter** i steget **Skicka** e-post väljer du **Från**, **Till**, **Ämne** och **Brödtext.** Klicka eller tryck var som helst på skärmen för att stänga markeringsrutan.

      ![Välj e-postfält för SMTP-anslutning](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Ange din e-postadress **för Från** **och Till** och för `High temperature detected` Ämne **och** **Brödtext.** Om dialogrutan **Lägg till dynamiskt innehåll från appar och anslutningsappar som används i det här flödet** öppnas väljer du **Dölj** för att stänga det. Du använder inte dynamiskt innehåll i den här självstudien.

      ![Fyll i e-postfält för SMTP-anslutning](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Välj **Spara** för att spara SMTP-anslutningen.

1. (Valfritt) Om du var tvungen att inaktivera TLS för att upprätta en anslutning till din e-postleverantör och vill återaktivera den följer du dessa steg:

   1. I fönstret **Logikapp** går du till **Utvecklingsverktyg och** väljer **API-anslutningar.**

   1. I listan över API-anslutningar väljer du SMTP-anslutningen.

   1. I fönstret **smtp API-anslutning** går du till **Allmänt och** väljer **Redigera API-anslutning.**

   1. I fönstret **Redigera API-anslutning** väljer du **Aktivera SSL?**, anger lösenordet för ditt e-postkonto på nytt och väljer **Spara**.

      ![Redigera SMTP API-anslutningen i logikappen i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Logikappen är nu redo att bearbeta temperaturaviseringar från kön Service Bus skicka meddelanden till ditt e-postkonto.

## <a name="test-the-logic-app"></a>Testa logikappen

1. Starta klientprogrammet på enheten.

1. Om du använder en fysisk enhet ska du noggrant ta en värmekälla nära den värmesensorn tills temperaturen överstiger 30 grader C. Om du använder onlinesimulatorn matar klientkoden slumpmässigt ut telemetrimeddelanden som överstiger 30 C.

1. Du bör börja ta emot e-postaviseringar som skickas av logikappen.

   > [!NOTE]
   > Din e-posttjänstleverantör kan behöva verifiera avsändaridentiteten för att se till att det är du som skickar e-postmeddelandet.

## <a name="next-steps"></a>Nästa steg

Du har skapat en logikapp som ansluter din IoT-hubb och din postlåda för temperaturövervakning och meddelanden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]