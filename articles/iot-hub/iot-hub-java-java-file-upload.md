---
title: Ladda upp filer från enheter till Azure IoT Hub med Java | Microsoft Docs
description: Ladda upp filer från en enhet till molnet med Azure IoT-enhets-SDK för Java. Överförda filer lagras i en BLOB-behållare för Azure Storage.
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
ms.openlocfilehash: 3529361cacf0890b7c4752bbd745a9240020b4f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217830"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Ladda upp filer från enheten till molnet med IoT Hub (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-java-java-c2d.md) själv studie kurs som visar hur du använder [fil överförings funktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till [Azure Blob Storage](../storage/index.yml). Självstudien visar hur du:

* Tillhandahålla en enhet på ett säkert sätt en Azure Blob-URI för att ladda upp en fil.

* Använd IoT Hub fil överförings meddelanden för att utlösa bearbetning av filen i Server delen av din app.

[Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-java.md) snabb start och [skicka meddelanden från molnet till enheten med IoT Hub](iot-hub-java-java-c2d.md) själv studie kursen Visa de grundläggande meddelande funktionerna från enhet till moln och meddelanden från moln till enhet i IoT Hub. I självstudien [Konfigurera meddelanderoutning med IoT Hub](tutorial-routing.md) beskrivs ett tillförlitligt sätt att lagra meddelanden från enheten till molnet i Azure Blob Storage. I vissa fall kan du dock inte enkelt mappa de data som enheterna skickar till de relativt små enhets-till-moln-meddelanden som IoT Hub accepterar. Exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Exempel på vibrations data med hög frekvens
* Någon form av förbearbetade data.

De här filerna bearbetas vanligt vis i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) -stacken. När du behöver använda filer från en enhet kan du fortfarande använda säkerhet och tillförlitlighet för IoT Hub.

I slutet av den här självstudien kör du två Java-konsol program:

* **simulerad enhet**, en modifierad version av appen som skapats i själv studie kursen [skicka meddelanden från moln till enhet med IoT Hub]. Den här appen laddar upp en fil till lagring med hjälp av en SAS-URI från IoT Hub.

* **Read-File-Upload-Notification**, som tar emot fil överförings meddelanden från IoT Hub.

> [!NOTE]
> IoT Hub stöder många enhets plattformar och språk (inklusive C, .NET och Java Script) via SDK: er för Azure IoT-enheter. I [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) hittar du stegvisa instruktioner för hur du ansluter enheten till Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Java se Development Kit 8](/java/azure/jdk/). Se till att du väljer **Java 8** under **långsiktigt stöd** för att hämta hämtningar för JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhets app

I det här avsnittet ändrar du den enhets app som du skapade i [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-java-java-c2d.md) att ladda upp en fil till IoT Hub.

1. Kopiera en avbildnings fil till `simulated-device` mappen och Byt namn på den `myimage.png` .

2. Öppna filen med en text redigerare `simulated-device\src\main\java\com\mycompany\app\App.java` .

3. Lägg till variabel deklarationen i klassen **app** :

    ```java
    private static String fileName = "myimage.png";
    ```

4. Om du vill bearbeta återställnings meddelanden för fil överföring lägger du till följande kapslade klass i klassen **app** :

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Om du vill ladda upp avbildningar till IoT Hub lägger du till följande metod i klassen **app** för att ladda upp avbildningar till IoT Hub:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Ändra **main** -metoden för att anropa **UploadFile** -metoden som visas i följande kodfragment:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. Använd följande kommando för att bygga den **simulerade Device-** appen och söka efter fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att ta emot meddelanden om fil överföring från IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md). Om du vill ta emot meddelanden om fil överföring behöver tjänsten **tjänst anslutnings** behörighet. Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om fil uppladdning

I det här avsnittet ska du skapa en Java-konsol-app som tar emot meddelanden om fil överförings meddelanden från IoT Hub.

1. Skapa ett Maven-projekt med namnet **Read-File-Upload-Notification** med följande kommando i kommando tolken. Observera att det här kommandot är ett enda, långt kommando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. I kommando tolken navigerar du till den nya `read-file-upload-notification` mappen.

3. Använd en text redigerare `pom.xml` och öppna filen i `read-file-upload-notification` mappen och Lägg till följande beroende till noden **beroenden** . Genom att lägga till beroendet kan du använda **iothub-Java-service-client-** paketet i ditt program för att kommunicera med tjänsten IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Du kan söka efter den senaste versionen av **iot-service-client** med [Maven-sökning](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Spara och Stäng `pom.xml` filen.

5. Öppna filen med en text redigerare `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` .

6. Lägg till följande **Import**-instruktioner i filen:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Lägg till följande variabler på klassnivå till klassen **App**. Ersätt `{Your IoT Hub connection string}` placeholder-värdet med IoT Hub-anslutningssträngen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string):

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Om du vill skriva ut information om fil uppladdning till-konsolen lägger du till följande kapslade klass i klassen **app** :

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Om du vill starta tråden som lyssnar efter meddelanden om fil uppladdning lägger du till följande kod i **main** -metoden:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. Spara och Stäng `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` filen.

11. Använd följande kommando för att bygga appen **Read-File-Upload-Notification** och söka efter fel:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

Kör följande kommando i kommando tolken i `read-file-upload-notification` mappen:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Kör följande kommando i kommando tolken i `simulated-device` mappen:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Följande skärm bild visar utdata från den **simulerade Device-** appen:

![Utdata från den simulerade enhets appen](media/iot-hub-java-java-upload/simulated-device.png)

Följande skärm bild visar utdata från appen **Read-File-Upload-Notification** :

![Utdata från Read-File-Upload-Notification-appen](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den överförda filen i den lagrings behållare som du konfigurerade:

![Överförd fil](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder fil överförings funktionerna i IoT Hub för att förenkla fil överföringar från enheter. Du kan fortsätta att utforska funktionerna och scenarierna i IoT Hub med följande artiklar:

* [Skapa en IoT Hub program mässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Simulera en enhet med IoT Edge](../iot-edge/quickstart-linux.md)