---
title: Kom igång med Azure IoT Hub Device Management (Java) | Microsoft Docs
description: Använda Azure-IoT Hub enhets hantering för att starta en fjärrstyrd enhet. Du använder Azure IoT-enhetens SDK för Java för att implementera en simulerad enhets app som innehåller en direkt metod och Azure IoT service SDK för Java för att implementera en tjänst-app som anropar den direkta metoden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: f05e1a458bc83fe4042c4b6cf35d9aa2095868ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217966"
---
# <a name="get-started-with-device-management-java"></a>Kom igång med enhets hantering (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure Portal för att skapa en IoT Hub och skapa en enhets identitet i din IoT-hubb.

* Skapa en simulerad enhets app som implementerar en direkt metod för att starta om enheten. Direkta metoder anropas från molnet.

* Skapa en app som anropar metoden starta om direkt i den simulerade Device-appen via din IoT Hub. Den här appen övervakar sedan de rapporterade egenskaperna från enheten för att se när åtgärden starta om har slutförts.

I slutet av den här självstudien har du två Java-konsol program:

**simulerad enhet**. Den här appen:

* Ansluter till din IoT Hub med enhets identiteten som skapades tidigare.

* Tar emot ett direkt metod anrop för omstart.

* Simulerar en fysisk omstart.

* Rapporterar tiden för den senaste omstarten via en rapporterad egenskap.

**Utlös – omstart**. Den här appen:

* Anropar en direkt metod i den simulerade Device-appen.

* Visar svaret på det direkta metod anropet som skickas av den simulerade enheten.

* Visar uppdaterade rapporterade egenskaper.

> [!NOTE]
> Information om SDK: er som du kan använda för att skapa program som ska köras på enheter och Server delen av lösningen finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

## <a name="prerequisites"></a>Förutsättningar

* [Java se Development Kit 8](/java/azure/jdk/). Se till att du väljer **Java 8** under **långsiktigt stöd** för att hämta hämtningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärran sluten omstart på enheten med en direkt metod

I det här avsnittet ska du skapa en Java-konsol-app som:

1. Anropar metoden starta om direkt i den simulerade Device-appen.

2. Visar svaret.

3. Avsöker de rapporterade egenskaper som skickas från enheten för att fastställa när omstarten är klar.

Den här konsolen ansluter till IoT Hub för att anropa den direkta metoden och läsa de rapporterade egenskaperna.

1. Skapa en tom mapp med namnet **DM-get-started**.

2. I mappen **DM-get-started** skapar du ett Maven-projekt med namnet **trigger-reboot** med hjälp av följande kommando i kommando tolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. I kommando tolken navigerar du till mappen **trigger-reboot** .

4. Använd en text redigerare och öppna **pom.xml** -filen i mappen **trigger-reboot** och Lägg till följande beroende till noden **beroenden** . Detta beroende gör att du kan använda IoT-service-client-paketet i din app för att kommunicera med IoT-hubben:

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

7. Öppna käll filen **trigger-reboot\src\main\java\com\mycompany\app\App.java** i en text redigerare.

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

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med IoT Hub anslutnings sträng som du tidigare kopierade i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Om du vill implementera en tråd som läser de rapporterade egenskaperna från enheten, måste du lägga till följande kapslade klass i klassen **app** :

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

11. Ändra signaturen för **main** -metoden för att utlösa följande undantag:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Om du vill anropa metoden reboot Direct på den simulerade enheten ersätter du koden i **main** -metoden med följande kod:

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

13. Om du vill starta tråden för att avsöka de rapporterade egenskaperna från den simulerade enheten lägger du till följande kod i **main** -metoden:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Du kan stoppa appen genom att lägga till följande kod i **main** -metoden:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Spara och Stäng filen **trigger-reboot\src\main\java\com\mycompany\app\App.java** .

16. Bygg in server för **utlösare och starta om** Server delen och korrigera eventuella fel. I kommando tolken navigerar du till mappen **trigger-reboot** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet ska du skapa en Java-konsol som simulerar en enhet. Appen lyssnar efter anropet för att starta om direkt från IoT-hubben och svarar omedelbart på det anropet. Appen försätts sedan i vilo läge för en stund att simulera omstarten innan den använder en rapporterad egenskap för att meddela **utlösaren-starta** om backend-appen att omstarten har slutförts.

1. I mappen **DM-get-started** skapar du ett Maven-projekt med namnet **simulerad enhet** med hjälp av följande kommando i kommando tolken:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. I kommando tolken navigerar du till mappen **simulerad enhet** .

3. Använd en text redigerare och öppna **pom.xml** -filen i mappen **simulerad enhet** och Lägg till följande beroende till noden **beroenden** . Detta beroende gör att du kan använda IoT-service-client-paketet i din app för att kommunicera med IoT-hubben:

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

7. Öppna käll filen **Simulated-device\src\main\java\com\mycompany\app\App.java** i en text redigerare.

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

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med den enhets anslutnings sträng som du antecknade i avsnittet [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Om du vill implementera en callback-hanterare för status händelser för direkta metoder lägger du till följande kapslade klass i klassen **app** :

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Om du vill implementera en callback-hanterare för enhetens dubbla status händelser lägger du till följande kapslade klass i klassen **app** :

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Om du vill implementera en callback-hanterare för egenskaps händelser lägger du till följande kapslade klass i klassen **app** :

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

13. Om du vill implementera en tråd för att simulera omstart av enheten lägger du till följande kapslade klass i klassen **app** . Tråden är i vilo läge i fem sekunder och anger sedan **lastReboot** -rapporterad egenskap:

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

14. Om du vill implementera den direkta metoden på enheten lägger du till följande kapslade klass i klassen **app** . När den simulerade appen tar emot ett anrop till den direkta metoden för **omstart** , returnerar den en bekräftelse till anroparen och startar sedan en tråd för att bearbeta omstarten:

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

15. Ändra signaturen för **main** -metoden för att utlösa följande undantag:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Om du vill instansiera en **DeviceClient** ersätter du koden i **main** -metoden med följande kod:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Du börjar lyssna efter direkta metod anrop genom att lägga till följande kod i **main** -metoden:

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

18. Om du vill stänga av enhets simulatorn lägger du till följande kod i **main** -metoden:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Spara och Stäng filen simulated-device\src\main\java\com\mycompany\app\App.java.

20. Bygg den **simulerade Device-** appen och korrigera eventuella fel. I kommando tolken navigerar du till mappen **simulerad enhet** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. I en kommando tolk i mappen **simulerad enhet** kör du följande kommando för att börja lyssna efter anrop för metoden starta om från IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java-IoT Hub simulerade Device-appar för att lyssna efter omstart Direct-metod anrop](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Kör följande kommando i kommando tolken i mappen **trigger-reboot** och anropa metoden reboot på den simulerade enheten från din IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub service-app för att anropa Direct-metoden för omstart](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Den simulerade enheten svarar på anropet till Direct-metoden för omstart:

    ![Java IoT Hub simulerade enhets appar svarar på direkt metod anrop](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]