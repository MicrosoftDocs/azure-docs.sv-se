---
title: Meddelanden från moln till enhet med Azure IoT Hub (Java) | Microsoft Docs
description: 'Skicka meddelanden från moln till enhet till en enhet från en Azure IoT Hub med Azure IoT SDK: er för Java. Du ändrar en simulerad enhets app för att ta emot meddelanden från molnet till enheten och ändra en backend-app för att skicka meddelanden från molnet till enheten.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 5ae1850add94d83278b0fe1905dfa6e53c71fc8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217898"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Skicka meddelanden från moln till enhet med IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen. Snabb starten [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-java.md) visar hur du skapar en IoT-hubb, etablerar en enhets identitet i den och kodar en simulerad enhets app som skickar enhet till molnet-meddelanden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md). Det visar hur du gör följande:

* Skicka meddelanden från moln till enhet från Server delen av lösningen till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från Server delen av lösningen kan du begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om [moln-till-enhet-meddelanden i guiden för IoT Hub utvecklare](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två Java-konsol program:

* **simulerad enhet**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **send-C2D-** Messages, som skickar ett meddelande från molnet till enheten till den simulerade appen via IoT Hub och sedan får leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhets plattformar och språk (inklusive C, Java, python och Java Script) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner för hur du ansluter din enhet till den här själv studie kursen och i allmänhet till Azure IoT Hub finns i [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Förutsättningar

* En komplett fungerande version av funktionen [Skicka telemetri från en enhet till en snabb start för IoT Hub](quickstart-send-telemetry-java.md) eller den [konfigurera meddelanderoutning med IoT Hub](tutorial-routing.md) själv studie kursen.

* [Java se Development Kit 8](/java/azure/jdk/). Se till att du väljer **Java 8** under **långsiktigt stöd** för att hämta hämtningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade Device-appen

I det här avsnittet ändrar du den simulerade Device-app som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md) för att ta emot meddelanden från molnet till enheten från IoT Hub.

1. Öppna filen simulated-device\src\main\java\com\mycompany\app\App.java i en textredigerare.

2. Lägg till följande **MessageCallback** -klass som en kapslad klass inuti klassen **app** . Metoden **execute** anropas när enheten tar emot ett meddelande från IoT Hub. I det här exemplet meddelar enheten alltid IoT-hubben att den har slutfört meddelandet:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Ändra **main** -metoden för att skapa en **AppMessageCallback** -instans och anropa **setMessageCallback** -metoden innan den öppnar klienten på följande sätt:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

`execute`Metoden i `AppMessageCallback` klassen returnerar `IotHubMessageResult.COMPLETE` . Detta meddelar IoT Hub att meddelandet har bearbetats och att meddelandet kan tas bort från enhets kön på ett säkert sätt. Enheten ska returnera det här värdet när dess bearbetning slutförs, oavsett vilket protokoll det använder.

Med AMQP och HTTPS, men inte MQTT, kan enheten också:

* Överge ett meddelande som resulterar i att IoT Hub behåller meddelandet i enhets kön för framtida konsumtion.
* Avvisa ett meddelande som permanent tar bort meddelandet från enhets kön.

Om något händer som hindrar enheten från att kunna slutföra, avbryta eller avvisa meddelandet, kommer IoT Hub efter en fast tids gräns, köa meddelandet för leverans igen. Därför måste meddelande bearbetnings logiken i enhets appen vara *idempotenta*, så att samma meddelande får flera gånger samma resultat.

Mer detaljerad information om hur IoT Hub bearbetar meddelanden från molnet till enheten, inklusive information om livs cykeln för moln-till-enhet-meddelanden, finns i [skicka meddelanden från moln till enhet från en IoT-hubb](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Om du använder HTTPS i stället för MQTT eller AMQP som transport söker **DeviceClient** -instansen efter meddelanden från IoT Hub sällan (minst var 25: e minut). Mer information om skillnaderna mellan MQTT, AMQP och HTTPS-stöd finns i avsnittet [om kommunikation mellan moln och enheter](iot-hub-devguide-c2d-guidance.md) och [Välj ett kommunikations protokoll](iot-hub-devguide-protocols.md).

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md). För att skicka meddelanden från molnet till enheten måste tjänsten ha behörighet för **tjänst anslutning** . Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från moln till enhet

I det här avsnittet ska du skapa en Java-konsol-app som skickar meddelanden från molnet till enheten till den simulerade Device-appen. Du behöver enhets-ID för enheten som du har lagt till i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-java.md) -snabb start. Du behöver också den IoT Hub-anslutningssträng som du kopierade tidigare i [Hämta anslutnings strängen för IoT Hub](#get-the-iot-hub-connection-string).

1. Skapa ett Maven-projekt med namnet **send-C2D-Messages** med hjälp av följande kommando i kommando tolken. Observera att det här kommandot är ett enda, långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå till den nya mappen Send-C2D-Messages i kommando tolken.

3. Öppna pom.xml-filen i mappen Send-C2D-Messages med hjälp av en text redigerare och Lägg till följande beroende till noden **beroenden** . Genom att lägga till beroendet kan du använda **iothub-Java-service-client-** paketet i ditt program för att kommunicera med tjänsten IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Spara och stäng filen pom.xml.

5. Öppna send-c2d-messages\src\main\java\com\mycompany\app\App.java-filen med hjälp av en text redigerare.

6. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Lägg till följande variabler på klass nivå i klassen **app** och Ersätt **{yourhubconnectionstring}** och **{yourdeviceid}** med de värden som du antecknade tidigare:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Ersätt **main** -metoden med följande kod. Den här koden ansluter till din IoT-hubb, skickar ett meddelande till din enhet och väntar sedan på en bekräftelse på att enheten har tagit emot och bearbetat meddelandet:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktions koden bör du implementera principer för omförsök (till exempel exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

9. Skapa appen **simulated-device** med hjälp av Maven genom att köra följande kommando i Kommandotolken i mappen simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando i en kommando tolk i mappen simulerad enhet för att börja skicka telemetri till din IoT-hubb och lyssna efter meddelanden från moln till enhet som skickas från hubben:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kör den simulerade Device-appen](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Kör följande kommando i en kommando tolk i mappen Send-C2D-Messages för att skicka ett meddelande från moln till enhet och vänta på bekräftelse av feedback:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kör kommandot för att skicka meddelanden från molnet till enheten](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skickar och tar emot meddelanden från molnet till enheten.

Om du vill se exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub, se [Azure IoT Solution-acceleratorer](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).