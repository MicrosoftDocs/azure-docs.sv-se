---
title: Kom igång med Azure IoT Hub Device Ungarns (Java) | Microsoft Docs
description: Så här använder du Azure IoT Hub-enheten för att lägga till taggar och sedan använda en IoT Hub fråga. Du använder Azure IoT Device SDK för Java för att implementera Device-appen och Azure IoT service SDK för Java för att implementera en service app som lägger till taggarna och kör IoT Hubs frågan.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 53a82ba545fa1709d1b79f3ff6b18f1817736bb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217864"
---
# <a name="get-started-with-device-twins-java"></a>Kom igång med enhets dubbla (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I den här självstudien skapar du två Java-konsol program:

* **Lägg till taggar – fråga**, en Java-backend-app som lägger till taggar och frågar enheten med dubbla.
* **simulerad enhet**, en Java-app som ansluter till din IoT Hub och rapporterar sitt anslutnings tillstånd med hjälp av en rapporterad egenskap.

> [!NOTE]
> Artikeln [Azure IoT SDK](iot-hub-devguide-sdks.md) : er innehåller information om Azure IoT SDK: er som du kan använda för att bygga både enhets-och backend-appar.

## <a name="prerequisites"></a>Förutsättningar

* [Java se Development Kit 8](/java/azure/jdk/). Se till att du väljer **Java 8** under **långsiktigt stöd** för att hämta hämtningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänst appen

I det här avsnittet ska du skapa en Java-app som lägger till platsens metadata som en tagg på enheten i IoT Hub som är associerade med **myDeviceId**. Appen skickar först frågor till IoT Hub för enheter som finns i USA och sedan för enheter som rapporterar en mobil nätverks anslutning.

1. Skapa en tom mapp med namnet **IoT-Java-getstarted** på din utvecklings dator.

2. I mappen **IoT-Java-getstarted** , skapar du ett Maven-projekt med namnet **Add-Tags-Query** med hjälp av följande kommando i kommando tolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. I kommando tolken navigerar du till mappen **Add-Tags-Query** .

4. Använd en text redigerare och öppna **pom.xml** -filen i mappen **Add-Tagss-Query** och Lägg till följande beroende till noden **beroenden** . Detta beroende gör att du kan använda **IoT-service-client-** paketet i din app för att kommunicera med IoT-hubben:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Lägg till följande **build** -nod efter noden **beroenden** . Den här konfigurationen instruerar maven att använda Java 1,8 för att bygga appen.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Spara och Stäng **pom.xmls** filen.

7. Öppna **Add-Tags-query\src\main\java\com\mycompany\app\App.java** -filen med hjälp av en text redigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med IoT Hub-anslutningssträngen som du kopierade i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Uppdatera **main** -metoden signatur för att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Ersätt koden i **main** -metoden med följande kod för att skapa **DeviceTwin** -och **DeviceTwinDevice** -objekten. **DeviceTwin** -objektet hanterar kommunikationen med IoT Hub. **DeviceTwinDevice** -objektet representerar enheten med dess egenskaper och Taggar:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Lägg till följande `try/catch` block i **huvud** metoden:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Lägg till följande kod i blocket för att uppdatera **regionen** och **plantera** enheten med dubbla Taggar i enheten `try` :

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Om du vill fråga enheten dubbla i IoT Hub lägger du till följande kod i `try` blocket efter den kod som du lade till i föregående steg. Koden kör två frågor. Varje fråga returnerar högst 100 enheter.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Spara och Stäng **Add-Tags-query\src\main\java\com\mycompany\app\App.java** -filen

16. Skapa **Add-Tags-Query-** appen och korrigera eventuella fel. I kommando tolken navigerar du till mappen **Add-Tags-Query** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet skapar du en Java-konsol-app som anger ett rapporterat egenskaps värde som skickas till IoT Hub.

1. I mappen **IoT-Java-getstarted** , skapar du ett Maven-projekt med namnet **simulerad enhet** med hjälp av följande kommando i kommando tolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. I kommando tolken navigerar du till mappen **simulerad enhet** .

3. Använd en text redigerare och öppna **pom.xml** -filen i mappen **simulerad enhet** och Lägg till följande beroenden till noden **beroenden** . Med detta beroende kan du använda **IoT-Device-client-** paketet i din app för att kommunicera med IoT Hub.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Lägg till följande beroende till noden **beroenden** . Detta beroende konfigurerar en NOP för fasad för Apache [SLF4J](https://www.slf4j.org/) som används av enhets klientens SDK för att implementera loggning. Den här konfigurationen är valfri, men om du utelämnar den kan du se en varning i-konsolen när du kör appen. Mer information om loggning i enhets klientens SDK finns i avsnittet om [loggning](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) i *exemplen för Readme-filen för Azure IoT-enhetens SDK för Java* .

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Lägg till följande **build** -nod efter noden **beroenden** . Den här konfigurationen instruerar maven att använda Java 1,8 för att bygga appen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Spara och Stäng **pom.xmls** filen.

7. Öppna **Simulated-device\src\main\java\com\mycompany\app\App.java** -filen med hjälp av en text redigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med enhets anslutnings strängen som du kopierade i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt.

10. Lägg till följande metod i klassen **app** för att skriva ut information om dubbla uppdateringar:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Ersätt koden i **main** -metoden med följande kod för att:

    * Skapa en enhets klient för att kommunicera med IoT Hub.

    * Skapa ett **enhets** objekt för att lagra enhetens dubbla egenskaper.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. Lägg till följande kod i **main** -metoden för att skapa en **connectivityType** -rapporterad egenskap och skicka den till IoT Hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. Lägg till följande kod i slutet av **main** -metoden. Om du väntar på **RETUR** nyckeln kan IoT Hub rapportera statusen för enhetens dubbla åtgärder.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Ändra signaturen för **main**-metoden och ta med undantagen som visas nedan:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Spara och Stäng filen **Simulated-device\src\main\java\com\mycompany\app\App.java** .

16. Bygg den **simulerade Device-** appen och korrigera eventuella fel. I kommando tolken navigerar du till mappen **simulerad enhet** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra-konsol programmen.

1. Kör följande kommando i en kommando tolk i mappen **Add-Tags-Query** för att köra appen **Add-Taggar-Query** service:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Skärm bild som visar utdata från kommandot för att köra appen Lägg till taggar för frågekörning.](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Du kan se de **växter** -och **region** etiketter som har lagts till på enheten. Den första frågan returnerar enheten, men den andra inte.

2. I en kommando tolk i mappen **simulerad enhet** kör du följande kommando för att lägga till egenskapen **connectivityType** som rapporteras till enheten:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Enhets klienten lägger till den rapporterade egenskapen för anslutnings typen](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Kör följande kommando i en kommando tolk i mappen **Add-Tags-Query** och kör sedan följande kommando för att köra **Add-Tags-query service-** appen en andra gång:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub service-app för att uppdatera märkes värden och köra enhets frågor](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Nu när enheten har skickat egenskapen **connectivityType** till IoT Hub, returnerar den andra frågan enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till metadata för enheten som taggar från en backend-app och skrev en enhets app för att rapportera enhets anslutnings information på enheten. Du har också lärt dig hur du frågar enheten med dubbla uppgifter med hjälp av SQL-liknande IoT Hub frågespråk.

Använd följande resurser för att lära dig att:

* Skicka telemetri från enheter med självstudierna [Kom igång med IoT Hub](quickstart-send-telemetry-java.md) .

* Kontrol lera enheter interaktivt (t. ex. genom att aktivera en fläkt från en användardefinierad app) med självstudierna [Använd direkt metoder](quickstart-control-device-java.md) .