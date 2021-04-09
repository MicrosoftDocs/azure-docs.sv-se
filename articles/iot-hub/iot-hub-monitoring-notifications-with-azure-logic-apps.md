---
title: Övervakning och aviseringar för IoT-fjärrhantering med Azure Logic Apps | Microsoft Docs
description: Använd Azure Logic Apps för övervakning av IoT-temperaturer på din IoT-hubb och skicka e-postmeddelanden automatiskt till din post låda för eventuella avvikelser som upptäckts.
author: robinsh
keywords: IoT-övervakning, IoT-aviseringar, IoT-temperatur övervakning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: cd14ff0688f4230aeedac748ca4b32609bdd2938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490330"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Övervakning och aviseringar för IoT-fjärrhantering med Azure Logic Apps du ansluter din IoT Hub och post lådan

![Diagram från slut punkt till slut punkt](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) kan hjälpa dig att dirigera arbets flöden över lokala tjänster och moln tjänster, ett eller flera företag och mellan olika protokoll. En Logic app börjar med en utlösare som sedan följs av en eller flera åtgärder som kan sekvenseras med hjälp av inbyggda kontroller, till exempel villkor och iteratorer. Den här flexibiliteten gör Logic Apps en idealisk IoT-lösning för IoT Monitoring-scenarier. Till exempel kan mottagning av telemetridata från en enhet på en IoT Hub slut punkt initiera Logic app-arbetsflöden till lagret data i en Azure Storage-BLOB, skicka e-postaviseringar till en varning om data avvikelser, schemalägga en tekniker besök om en enhet rapporterar ett fel och så vidare.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du får lära dig hur du skapar en Logi Kap par som ansluter IoT-hubben och din post låda för temperatur övervakning och meddelanden.

Klient koden som körs på enheten anger en program egenskap, `temperatureAlert` vid varje telemetri som skickas till din IoT-hubb. När klient koden identifierar en temperatur över 30 C, anges denna egenskap till. `true` i annat fall anges egenskapen till `false` .

Meddelanden som kommer till din IoT-hubb ser ut ungefär så här, med telemetri-data som finns i texten och `temperatureAlert` egenskapen i program egenskaperna (system egenskaper visas inte):

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

Mer information om IoT Hub meddelande format finns i [skapa och läsa IoT Hub meddelanden](iot-hub-devguide-messages-construct.md).

I det här avsnittet ställer du in routning i IoT Hub för att skicka meddelanden där `temperatureAlert` egenskapen är `true` till en Service Bus slut punkt. Sedan kan du ställa in en Logi Kap par som utlöser de meddelanden som kommer till Service Bus slut punkten och skickar ett e-postmeddelande till dig.

## <a name="what-you-do"></a>Vad du gör

* Skapa ett Service Bus-namnområde och Lägg till en Service Bus kö i den.
* Lägg till en anpassad slut punkt och en regel för routning i IoT Hub för att dirigera meddelanden som innehåller en temperatur avisering till Service Bus kön.
* Skapa, konfigurera och testa en Logic-app för att använda meddelanden från din Service Bus kö och skicka e-postmeddelanden till en önskad mottagare.

## <a name="what-you-need"></a>Detta behöver du

* Slutför själv studie kursen om [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av enhets självstudierna. till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Detta beskriver följande krav:

  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb under din prenumeration.
  * Ett klient program som körs på din enhet som skickar telemetri-meddelanden till Azure IoT Hub.

## <a name="create-service-bus-namespace-and-queue"></a>Skapa Service Bus namnrymd och kö

Skapa ett namnområde och en kö för Service Bus. Senare i det här avsnittet skapar du en regel för routning i IoT Hub för att dirigera meddelanden som innehåller en temperatur avisering till Service Bus kön, där de hämtas av en Logic app och utlöser den för att skicka ett e-postmeddelande.

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

1. På [Azure Portal](https://portal.azure.com/)väljer du **+ skapa en resurs**  >  **integrations**  >  **Service Bus**.

1. I fönstret **skapa namn område** anger du följande information:

   **Namn**: namnet på Service Bus-namnområdet. Namn området måste vara unikt i Azure.

   **Pris nivå**: Välj **Basic** i list rutan. Basic-nivån räcker för den här självstudien.

   **Resurs grupp**: Använd samma resurs grupp som din IoT Hub använder.

   **Plats**: Använd samma plats som din IoT Hub använder.

   ![Skapa ett Service Bus-namnområde i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Välj **Skapa**. Vänta tills distributionen har slutförts innan du går vidare till nästa steg.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Lägg till en Service Bus-kö i namn området

1. Öppna Service Bus namn området. Det enklaste sättet att komma till Service Bus namn området är att välja **resurs grupper** från resurs fönstret, markera din resurs grupp och välj sedan Service Bus namn området i listan över resurser.

1. I fönstret **Service Bus namnrymd** väljer du **+ kö**.

1. Ange ett namn för kön och välj sedan **skapa**. När kön har skapats stängs fönstret **skapa kö** .

   ![Lägg till en Service Bus-kö i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Gå tillbaka till rutan **Service Bus namnrymd** under **entiteter** och välj **köer**. Öppna kön Service Bus i listan och välj sedan **principer för delad åtkomst**  >  **+ Lägg till**.

1. Ange ett namn för principen, kontrol lera **Hantera** och välj sedan **skapa**.

   ![Lägg till en princip för Service Bus-kö i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Lägg till en anpassad slut punkt och routningsregler i IoT Hub

Lägg till en anpassad slut punkt för Service Bus kön i IoT-hubben och skapa en regel för att dirigera meddelanden som innehåller en temperatur avisering till den slut punkten, där de hämtas av din Logic app. Routningstjänsten använder en cirkulations fråga, `temperatureAlert = "true"` för att vidarebefordra meddelanden baserat på värdet för `temperatureAlert` egenskapen program som anges av den klient kod som körs på enheten. Mer information finns i [meddelande cirkulations fråga baserat på meddelande egenskaper](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Lägg till en anpassad slut punkt

1. Öppna din IoT Hub. Det enklaste sättet att komma till IoT Hub är att välja **resurs grupper** från resurs fönstret, välja din resurs grupp och sedan använda IoT Hub i listan över resurser.

1. Under **meddelanden** väljer du **meddelanderoutning.** **I fönstret meddelanderoutning** väljer du fliken **anpassade slut punkter** och väljer sedan **+ Lägg till**. I list rutan väljer du **Service Bus-kö**.

   ![Skärm bild som visar alternativet för Service Bus-kön.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. I fönstret **Lägg till en Service Bus-slutpunkt** anger du följande information:

   **Slut punkts namn**: namnet på slut punkten.

   **Service Bus-namnrymd**: Välj det namn område du skapade.

   **Service Bus-kö**: Välj den kö som du skapade.

   ![Lägg till en slut punkt i IoT Hub i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Välj **Skapa**. När slut punkten har skapats går du vidare till nästa steg.

### <a name="add-a-routing-rule"></a>Lägga till en hanteringsregel

1. Tillbaka **i fönstret meddelanderoutning** väljer du fliken **vägar** och väljer sedan **+ Lägg till**.

1. Ange följande information i fönstret **Lägg till en väg** :

   **Namn**: namnet på regeln för routning.

   **Slut punkt**: Välj den slut punkt som du skapade.

   **Data källa**: Välj **meddelanden om enhets telemetri**.

   **Cirkulations fråga**: ange `temperatureAlert = "true"` .

   ![Lägg till en regel för routning i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Välj **Spara**. Du kan **stänga fönstret meddelanderoutning** .

## <a name="create-and-configure-a-logic-app"></a>Skapa och konfigurera en Logic app

I föregående avsnitt ställer du in IoT-hubben för att dirigera meddelanden som innehåller en temperatur avisering till din Service Bus-kö. Nu kan du konfigurera en Logic app för att övervaka Service Bus kön och skicka ett e-postmeddelande när ett meddelande läggs till i kön.

### <a name="create-a-logic-app"></a>Skapa en logikapp

1. Välj **skapa en** Logic-app för resurs  >  **integrering**  >  .

1. Ange följande information:

   **Namn**: namnet på Logic-appen.

   **Resurs grupp**: Använd samma resurs grupp som din IoT Hub använder.

   **Plats**: Använd samma plats som din IoT Hub använder.

   ![Skapa en Logic-app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Välj **Skapa**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurera Logic app-utlösaren

1. Öppna Logic-appen. Det enklaste sättet att komma till Logic app är att välja **resurs grupper** från resurs fönstret, välja din resurs grupp och sedan din Logi Kap par i listan över resurser. När du väljer Logic-appen öppnas Logic Apps designer.

1. Rulla ned till **mallar** i Logic Apps designer och välj **Tom Logic app**.

   ![Börja med en tom Logic-app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Välj fliken **alla** och välj sedan **Service Bus**.

   ![Välj Service Bus för att börja skapa din Logic app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Under **utlösare** väljer du **när ett eller flera meddelanden anländer i en kö (komplettera automatiskt)**.

   ![Välj utlösaren för din Logic app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Skapa en Service Bus-anslutning.
   1. Ange ett namn på anslutningen och välj Service Bus namn området i listan. Nästa skärm öppnas.

      ![Skärm bild som visar när ett eller flera meddelanden tas emot i en kö (komplettera automatiskt).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Välj Service Bus-princip (RootManageSharedAccessKey). Välj sedan  **skapa**.

      ![Skapa en Service Bus-anslutning för din Logic app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. På den sista skärmen, för **Könamn** väljer du den kö som du skapade i list rutan. Ange `175` för **maximalt antal meddelanden**.

      ![Ange maximalt antal meddelanden för Service Bus-anslutningen i din Logic app](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Spara ändringarna genom att välja **Spara** på menyn längst upp i Logic Apps designer.

### <a name="configure-the-logic-app-action"></a>Konfigurera Logic app-åtgärden

1. Skapa en SMTP-tjänst anslutning.

   1. Välj **Nytt steg**. I **Välj en åtgärd** väljer du fliken **alla** .

   1. Skriv `smtp` i sökrutan, Välj **SMTP** -tjänsten i Sök resultatet och välj sedan **skicka e-post**.

      ![Skapa en SMTP-anslutning i din Logic app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Ange SMTP-information för din post låda och välj sedan **skapa**.

      ![Ange SMTP-anslutnings information i din Logic app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Hämta SMTP-information för [Hotmail/Outlook. com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en)och [Yahoo-e-post](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Du kan behöva inaktivera TLS/SSL för att upprätta anslutningen. Om så är fallet och du vill återaktivera TLS när anslutningen har upprättats, se det valfria steget i slutet av det här avsnittet.

   1. Från List rutan **Lägg till ny parameter** i steget för **att skicka e-post** väljer du **från**, **till**, **ämne** och **brödtext**. Klicka eller tryck var som helst på skärmen för att stänga markerings rutan.

      ![Välj SMTP-anslutningens e-fält](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Ange din e-postadress för **från** och **till**, samt `High temperature detected` för **ämne** och **brödtext**. Om dialog rutan **Lägg till dynamiskt innehåll från appar och anslutningar som används i det här flödet** öppnas väljer du **Dölj** för att stänga den. Du använder inte dynamiskt innehåll i den här självstudien.

      ![Fyll i e-fält för SMTP-anslutning](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Välj **Spara** för att spara SMTP-anslutningen.

1. Valfritt Om du har inaktiverat TLS för att upprätta en anslutning till e-postleverantören och vill återaktivera den, följer du dessa steg:

   1. I fönstret **Logic app** , under **utvecklingsverktyg**, väljer du **API-anslutningar**.

   1. Välj SMTP-anslutningen i listan över API-anslutningar.

   1. I fönstret **SMTP API-anslutning** under **Allmänt** väljer du **Redigera API-anslutning**.

   1. I fönstret **Redigera API-anslutning** väljer du **Aktivera SSL?**, anger lösen ordet för ditt e-postkonto igen och väljer **Spara**.

      ![Redigera SMTP API-anslutning i din Logic app i Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Din Logi Kap par är nu redo att bearbeta temperatur aviseringar från Service Bus kön och skicka meddelanden till ditt e-postkonto.

## <a name="test-the-logic-app"></a>Testa logikappen

1. Starta klient programmet på enheten.

1. Om du använder en fysisk enhet bör du noggrant ta en värme källa nära värme sensorn tills temperaturen överskrider 30 grader C. Om du använder online simulatorn kommer klient koden slumpmässigt att skicka telemetri om telemetri som överstiger 30 C.

1. Du bör börja ta emot e-postmeddelanden som skickas av Logic app.

   > [!NOTE]
   > Din e-postleverantör kan behöva verifiera avsändar identiteten för att kontrol lera att den är den som skickar e-postmeddelandet.

## <a name="next-steps"></a>Nästa steg

Du har skapat en Logic-app som ansluter din IoT-hubb och din post låda för temperatur övervakning och-meddelanden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]