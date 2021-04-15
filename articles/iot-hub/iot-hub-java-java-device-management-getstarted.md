---
title: Kom igång med Azure IoT Hub enhetshantering (Java) | Microsoft Docs
description: Så här använder du Azure IoT Hub för att initiera en omstart av en fjärransluten enhet. Du använder Sdk för Azure IoT-enheter för Java för att implementera en simulerad enhetsapp som innehåller en direktmetod och Azure IoT-tjänst-SDK för Java för att implementera en tjänstapp som anropar direktmetoden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 797c795b6c936e2f117a96fed4b4a483876f1c47
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478088"
---
# <a name="get-started-with-device-management-java"></a>Kom igång med enhetshantering (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure Portal för att skapa en IoT Hub och skapa en enhetsidentitet i din IoT-hubb.

* Skapa en simulerad enhetsapp som implementerar en direktmetod för att starta om enheten. Direktmetoder anropas från molnet.

* Skapa en app som anropar direktmetoden för omstart i den simulerade enhetsappen via din IoT-hubb. Den här appen övervakar sedan de rapporterade egenskaperna från enheten för att se när omstarten har slutförts.

I slutet av den här självstudien har du två Java-konsolappar:

**simulated-device**. Den här appen:

* Ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare.

* Tar emot ett direkt metod-anrop för omstart.

* Simulerar en fysisk omstart.

* Rapporterar tiden för den senaste omstarten via en rapporterad egenskap.

**trigger-reboot**. Den här appen:

* Anropar en direktmetod i den simulerade enhetsappen.

* Visar svaret på det direkta metod-anropet som skickas av den simulerade enheten.

* Visar de uppdaterade rapporterade egenskaperna.

> [!NOTE]
> Information om de SDK:er som du kan använda för att skapa program som ska köras på enheter och i lösningens backend-del finns i [Azure IoT-SDK:er.](iot-hub-devguide-sdks.md)

## <a name="prerequisites"></a>Förutsättningar

* [Java SE Development Kit 8](/java/azure/jdk/). Se till att du **väljer Java 8** under **Långsiktigt stöd för** att hämta till nedladdningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här artikeln använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta IoT Hub-anslutningssträngen

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärrstart på enheten med en direktmetod

I det här avsnittet skapar du en Java-konsolapp som:

1. Anropar direktmetoden för omstart i den simulerade enhetsappen.

2. Visar svaret.

3. Avsöker de rapporterade egenskaperna som skickas från enheten för att avgöra när omstarten är klar.

Den här konsolappen ansluter till IoT Hub för att anropa direktmetoden och läsa de rapporterade egenskaperna.

1. Skapa en tom mapp med **namnet dm-get-started**.

2. I mappen **dm-get-started** skapar du ett Maven-projekt med namnet **trigger-reboot** med följande kommando i kommandotolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Navigera till mappen **trigger-reboot** i kommandotolken.

4. Använd en textredigerare och öppna **pom.xml** i mappen **trigger-reboot** och lägg till följande beroende till **noden beroenden.** Med det här beroendet kan du använda iot-service-client-paketet i din app för att kommunicera med din IoT Hub:

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

5. Lägg till följande **build-nod** efter **noden beroenden.** Den här konfigurationen instruerar Maven att använda Java 1.8 för att skapa appen:

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

6. Spara och stäng **pom.xml** filen.

7. Öppna källfilen **trigger-reboot\src\main\java\com\mycompany\app\App.java** med en textredigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med den IoT Hub som du kopierade tidigare i Hämta [IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Om du vill implementera en tråd som läser de rapporterade egenskaperna från enhetstvillingen var 10:e sekund lägger du till följande kapslade klass i **klassen App:**

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Ändra signaturen för **main-metoden** för att skapa följande undantag:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Om du vill anropa direktmetoden för omstart på den simulerade enheten ersätter du koden i **main-metoden** med följande kod:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Starta tråden för att avssöka de rapporterade egenskaperna från den simulerade enheten genom att lägga till följande kod i **main-metoden:**

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Om du vill stoppa appen lägger du till följande kod i **main-metoden:**

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Spara och stäng **filen trigger-reboot\src\main\java\com\mycompany\app\App.java.**

16. Skapa **backend-appen** för utlösarstart och korrigera eventuella fel. Navigera till mappen **trigger-reboot i kommandotolken** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Java-konsolapp som simulerar en enhet. Appen lyssnar efter det direkta metodanropet för omstart från din IoT-hubb och svarar omedelbart på anropet. Appen försparar sig sedan en stund för att simulera omstartsprocessen innan den använder en rapporterad egenskap för att meddela **backend-appen** som utlöser omstarten att omstarten har slutförts.

1. I mappen **dm-get-started** skapar du ett Maven-projekt med namnet **simulated-device** med följande kommando i kommandotolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigera till mappen **simulated-device** i kommandotolken.

3. Öppna filenpom.xml **simulated-device** med hjälp av en textredigerare och lägg till följande beroende till **noden beroenden.**  Med det här beroendet kan du använda iot-service-client-paketet i din app för att kommunicera med din IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Lägg till följande beroende till **noden beroenden.** Det här beroendet konfigurerar en NOP för Apache SLF4J-loggningsfasaden, som används av enhetens klient-SDK för att implementera loggning. [](https://www.slf4j.org/) Den här konfigurationen är valfri, men om du utelämnar den kan du se en varning i konsolen när du kör appen. Mer information om hur du loggar [](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) in enhetens klient-SDK finns i Loggning i exempel för Readme-filen för *Azure IoT-enhets-SDK* för Java.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Lägg till följande **build-nod** efter **noden beroenden.** Den här konfigurationen instruerar Maven att använda Java 1.8 för att skapa appen:

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

6. Spara och stäng **pom.xml** fil.

7. Öppna källfilen **simulated-device\src\main\java\com\mycompany\app\App.java** med hjälp av en textredigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med enhetsanslutningssträngen som du [antecknade i avsnittet Registrera en ny enhet i IoT Hub:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Om du vill implementera en motringningshanterare för direktmetodstatushändelser lägger du till följande kapslade klass i **klassen App:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Om du vill implementera en motringningshanterare för statushändelser för enhetstvilling lägger du till följande kapslade klass i **klassen App:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Om du vill implementera en motringningshanterare för egenskapshändelser lägger du till följande kapslade klass i **klassen App:**

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Om du vill implementera en tråd för att simulera omstarten av enheten lägger du till följande kapslade klass i **klassen** App. Tråden för strömsparläge i fem sekunder och anger sedan den **rapporterade egenskapen lastReboot:**

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Om du vill implementera direktmetoden på enheten lägger du till följande kapslade klass i **klassen App.** När den simulerade appen tar  emot ett anrop till direktmetoden för omstart returnerar den en bekräftelse till anroparen och startar sedan en tråd för att bearbeta omstarten:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Ändra signaturen för **main-metoden** för att skapa följande undantag:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Om du vill **instansiera en DeviceClient** ersätter du koden i **main-metoden** med följande kod:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Börja lyssna efter direkta metodanrop genom att lägga till följande kod i **main-metoden:**

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Om du vill stänga av enhetssimulatorn lägger du till följande kod i **main-metoden:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Spara och stäng filen simulated-device\src\main\java\com\mycompany\app\App.java.

20. Skapa **appen för simulerade enheter** och korrigera eventuella fel. Navigera till mappen **simulated-device i kommandotolken** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Kör apparna

Nu är du redo att köra apparna.

1. Kör följande kommando i kommandotolken i mappen **simulated-device** för att börja lyssna efter omstartsmetodanrop från din IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub simulerad enhetsapp för att lyssna efter direkta metodsamtal för omstart](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Kör följande kommando i kommandotolken i mappen **trigger-reboot** för att anropa omstartsmetoden på den simulerade enheten från din IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-tjänstappen för att anropa direktmetoden för omstart](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Den simulerade enheten svarar på anropet av den direktinstartade metoden:

    ![Java IoT Hub simulerad enhetsapp svarar på direktmetods-anropet](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
