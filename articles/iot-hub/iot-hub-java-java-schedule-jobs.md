---
title: Schemalägga jobb med Azure IoT Hub (Java) | Microsoft Docs
description: Schemalägga ett Azure IoT Hub-jobb för att anropa en direkt metod och ange önskad egenskap på flera enheter. Du använder Azure IoT-enhetens SDK för Java för att implementera de simulerade apparna och Azure IoT service SDK för Java för att implementera en service app för att köra jobbet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 3e98cfc2d8c7fb8d40c8565a1c620f123ce171ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217847"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Schema-och sändnings jobb (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT Hub för att schemalägga och spåra jobb som uppdaterar miljon tals enheter. Använd jobb för att:

* Uppdatera önskade egenskaper
* Uppdatera Taggar
* Anropa direkt metoder

Ett jobb radbryter en av dessa åtgärder och spårar körningen mot en uppsättning enheter. En enhet med dubbla frågor definierar den uppsättning av enheter som jobbet körs mot. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod på 10 000 enheter som startar om enheterna. Du kan ange en uppsättning enheter med en enhet med dubbla frågor och schemalägga jobbet så att det körs vid ett senare tillfälle. Jobbet spårar förloppet när var och en av enheterna tar emot och kör metoden starta om Direct.

Mer information om var och en av dessa funktioner finns i:

* Enhetens dubbla och egenskaper: [Kom igång med enhets dubbla](iot-hub-java-java-twin-getstarted.md)

* Direkta metoder: [IoT Hub Developer Guide – direkta metoder](iot-hub-devguide-direct-methods.md) och [Självstudier: Använd direkta metoder](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en app för enheten som implementerar en direkt metod som kallas **lockDoor**. Enhets appen tar också emot önskade egenskaps ändringar från backend-appen.

* Skapa en backend-app som skapar ett jobb för att anropa metoden **lockDoor** Direct på flera enheter. Ett annat jobb skickar önskade egenskaps uppdateringar till flera enheter.

I slutet av den här självstudien har du en app för Java-konsolen och en Java-konsol backend-app:

**simulerad enhet** som ansluter till din IoT Hub, implementerar **lockDoor** Direct-metoden och hanterar önskade egenskaps ändringar.

**Schemalägg – jobb** som använder jobb för att anropa metoden **lockDoor** Direct och uppdatera enheten med dubbla önskade egenskaper på flera enheter.

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

Du kan också använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) -verktyget för att lägga till en enhet i IoT Hub.

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänst appen

I det här avsnittet skapar du en Java-konsol-app som använder jobb för att:

* Anropa metoden **lockDoor** Direct på flera enheter.

* Skicka önskade egenskaper till flera enheter.

Så här skapar du appen:

1. Skapa en tom mapp med namnet **IoT-Java-Schedule-Jobs** på din utvecklings dator.

2. I mappen **IoT-Java-Schedule-Jobs** skapar du ett Maven-projekt med namnet **schema-Jobs** med hjälp av följande kommando i kommando tolken. Observera att detta är ett enda långt kommando:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. I kommando tolken navigerar du till mappen **schema-Jobs** .

4. Använd en text redigerare och öppna **pom.xml** filen i mappen **schema-Jobs** och Lägg till följande beroende till noden **beroenden** . Detta beroende gör att du kan använda **IoT-service-client-** paketet i din app för att kommunicera med IoT-hubben:

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

7. Öppna **Schedule-jobs\src\main\java\com\mycompany\app\App.java** -filen med hjälp av en text redigerare.

8. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{youriothubconnectionstring}` med din IoT Hub-anslutningssträng som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Lägg till följande metod i klassen **app** för att schemalägga ett jobb för att uppdatera de önskade egenskaperna för **byggnad** och **golv** i enheten:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Om du vill schemalägga ett jobb för att anropa **lockDoor** -metoden lägger du till följande metod i klassen **app** :

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Om du vill övervaka ett jobb lägger du till följande metod i klassen **app** :

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Om du vill fråga efter information om de jobb som du körde lägger du till följande metod:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Uppdatera **main** -metoden signatur för att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Om du vill köra och övervaka två jobb sekventiellt ersätter du koden i **main** -metoden med följande kod:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Spara och Stäng **Schedule-jobs\src\main\java\com\mycompany\app\App.java** -filen

17. Bygg appen **schema-Jobs** och korrigera eventuella fel. I kommando tolken navigerar du till mappen **schema-Jobs** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Skapa en enhetsapp

I det här avsnittet ska du skapa en Java-konsol-app som hanterar önskade egenskaper som skickas från IoT Hub och implementerar direkt metod anropet.

1. I mappen **IoT-Java-Schedule-Jobs** skapar du ett Maven-projekt med namnet **simulerad enhet** med hjälp av följande kommando i kommando tolken. Observera att detta är ett enda långt kommando:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. I kommando tolken navigerar du till mappen **simulerad enhet** .

3. Använd en text redigerare och öppna **pom.xml** -filen i mappen **simulerad enhet** och Lägg till följande beroenden till noden **beroenden** . Det här beroendet gör att du kan använda **IoT-Device-client-** paketet i din app för att kommunicera med IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-device-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Lägg till följande beroende till noden **beroenden** . Detta beroende konfigurerar en NOP för fasad för Apache [SLF4J](https://www.slf4j.org/) som används av enhets klientens SDK för att implementera loggning. Den här konfigurationen är valfri, men om du utelämnar den kan du se en varning i-konsolen när du kör appen. Mer information om loggning i enhets klientens SDK finns i avsnittet om [loggning](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)i *exemplen för Readme-filen för Azure IoT-enhetens SDK för Java* .

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

9. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{yourdeviceconnectionstring}` med enhets anslutnings strängen som du kopierade tidigare i avsnittet [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Den här exempelappen använder variabeln **protocol** när den instantierar ett **DeviceClient**-objekt.

10. Om du vill skriva ut enhetens dubbla meddelanden till-konsolen lägger du till följande kapslade klass i klassen **app** :

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Om du vill skriva ut direkta metod meddelanden till-konsolen lägger du till följande kapslade klass i klassen **app** :

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Om du vill hantera direkta metod anrop från IoT Hub lägger du till följande kapslade klass i klassen **app** :

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. Uppdatera **main** -metoden signatur för att inkludera följande `throws` sats:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Ersätt koden i **main** -metoden med följande kod för att:
    * Skapa en enhets klient för att kommunicera med IoT Hub.
    * Skapa ett **enhets** objekt för att lagra enhetens dubbla egenskaper.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Starta enhets klient tjänsterna genom att lägga till följande kod i **main** -metoden:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Om du vill vänta tills användaren trycker på **RETUR** -tangenten innan du stänger av, lägger du till följande kod i slutet av **main** -metoden:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Spara och Stäng filen **Simulated-device\src\main\java\com\mycompany\app\App.java** .

18. Bygg den **simulerade Device-** appen och korrigera eventuella fel. I kommando tolken navigerar du till mappen **simulerad enhet** och kör följande kommando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra-konsol programmen.

1. Kör följande kommando i en kommando tolk i mappen **simulerad enhet** för att starta enhets appen som lyssnar efter önskade egenskaps ändringar och direkta metod anrop:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Enhets klienten startar](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Kör följande kommando i en kommando tolk i `schedule-jobs` mappen för att köra tjänsten **schema-Jobs** för att köra två jobb. Den första uppsättningen anger önskade egenskaps värden, den andra anropar den direkta metoden:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Java IoT Hub service-appen skapar två jobb](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Enhets appen hanterar önskad egenskaps ändring och det direkta metod anropet:

   ![Enhets klienten svarar på ändringarna](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetens egenskaper.

Använd följande resurser för att lära dig att:

* Skicka telemetri från enheter med självstudierna [Kom igång med IoT Hub](quickstart-send-telemetry-java.md) .

* Kontrol lera enheter interaktivt (t. ex. genom att aktivera en fläkt från en användardefinierad app) med självstudierna [Använd direkt metoder](quickstart-control-device-java.md) . s