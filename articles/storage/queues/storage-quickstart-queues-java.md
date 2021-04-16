---
title: 'Snabbstart: Azure Queue Storage klientbibliotek v12 – Java'
description: Lär dig hur du använder Azure Queue Storage-klientbiblioteket v12 för Java för att skapa en kö och lägga till meddelanden i den. Lär dig sedan hur du läser och tar bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: twooley
ms.author: twooley
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: f4e33cac6ba00be56b0f63cf5a10b2dce32e1be7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534456"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Snabbstart: Azure Queue Storage klientbibliotek v12 för Java

Kom igång med Azure Queue Storage-klientbiblioteket v12 för Java. Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ de här stegen för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Använd Azure Queue Storage v12 för Java för att:

- Skapa en kö
- Lägga till meddelanden i en kö
- Granska meddelanden i en kö
- Uppdatera ett meddelande i en kö
- Ta emot och ta bort meddelanden från en kö
- Ta bort en kö

Ytterligare resurser:

- [Referensdokumentation för API](/java/api/overview/azure/storage-queue-readme)
- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Paket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Exempel](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Förutsättningar

- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
- Azure Storage konto – skapa [ett lagringskonto](../common/storage-account-create.md)

## <a name="setting-up"></a>Inrätta

Det här avsnittet går igenom hur du förbereder ett projekt för att arbeta Azure Queue Storage klientbiblioteket v12 för Java.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Java-program med namnet `queues-quickstart-v12` .

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du Maven för att skapa en ny konsolapp med namnet `queues-quickstart-v12` . Skriv följande kommando `mvn` för att skapa ett "hello world"-Java-projekt.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. Utdata från att generera projektet bör se ut ungefär så här:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Växla till den nyligen skapade `queues-quickstart-v12` katalogen.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Installera paketet

Öppna filen `pom.xml` i textredigeraren. Lägg till följande beroendeelement i gruppen med beroenden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

Från projektkatalogen:

1. Navigera till `/src/main/java/com/queues/quickstart` katalogen
1. Öppna filen `App.java` i redigeringsredigeraren
1. Ta bort `System.out.println("Hello, world");` instruktionen
1. Lägga till `import` direktiv

Här är koden:

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 KB stort. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning för arbete som ska bearbetas asynkront. Queue Storage erbjuder tre typer av resurser:

- Lagringskontot
- En kö i lagringskontot
- Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över arkitekturen för Queue Storage](./media/storage-queues-introduction/queue1.png)

Använd följande Java-klasser för att interagera med dessa resurser:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): Klassen `QueueClientBuilder` konfigurerar och instansierar ett `QueueClient` -objekt.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): `QueueServiceClient` gör att du kan hantera alla köer i ditt lagringskonto.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): Med `QueueClient` klassen kan du hantera och ändra en enskild kö och dess meddelanden.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): Klassen `QueueMessageItem` representerar de enskilda objekt som returneras vid anrop till en [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) kö.

## <a name="code-examples"></a>Kodexempel

De här exempelkodfragmenten visar hur du gör följande med Azure Queue Storage klientbiblioteket för Java:

- [Hämta anslutningssträngen](#get-the-connection-string)
- [Skapa en kö](#create-a-queue)
- [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
- [Granska meddelanden i en kö](#peek-at-messages-in-a-queue)
- [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
- [Ta emot och ta bort meddelanden från en kö](#receive-and-delete-messages-from-a-queue)
- [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Följande kod hämtar anslutningssträngen för lagringskontot. Anslutningssträngen lagras miljövariabeln som skapades i [avsnittet Konfigurera anslutningssträngen för](#configure-your-storage-connection-string) lagring.

Lägg till den här koden i `main` metoden :

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Följande kod lägger till ett GUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information om namngivning av köer finns i [Namngivning av köer och metadata.](/rest/api/storageservices/naming-queues-and-metadata)

Skapa en instans av [`QueueClient`](/java/api/com.azure.storage.queue.queueclient) klassen . Anropa sedan metoden [`Create`](/java/api/com.azure.storage.queue.queueclient.create) för att skapa kön i ditt lagringskonto.

Lägg till den här koden i slutet av `main` metoden:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodfragment lägger till meddelanden i kön genom att anropa [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) metoden . Den sparar också ett som [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) returneras från ett `sendMessage` anrop. Resultatet används för att uppdatera meddelandet senare i programmet.

Lägg till den här koden i slutet av `main` metoden:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Granska meddelandena i kön genom att anropa [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) metoden . Den här metoden hämtar ett eller flera meddelanden från kön, men ändrar inte meddelandets synlighet.

Lägg till den här koden i slutet av `main` metoden:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) metoden . Den här metoden kan ändra tidsgränsen och innehållet för ett meddelandes synlighet. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB stor. Tillsammans med nytt innehåll för meddelandet skickar du meddelande-ID:t och popup-kvittot med hjälp av `SendMessageResult` som sparades tidigare i koden. Meddelande-ID:t och popup-kvittot identifierar vilket meddelande som ska uppdateras.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Ta emot och ta bort meddelanden från en kö

Ladda ned tidigare tillagda meddelanden genom att anropa [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) metoden . Exempelkoden tar även bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet visar bearbetningen bara meddelandet i konsolen.

Appen pausar för användarindata genom att anropa `System.console().readLine();` innan den tar emot och tar bort meddelanden. Kontrollera i [Azure Portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Meddelanden som inte uttryckligen tas bort blir så småningom synliga i kön igen för en annan möjlighet att bearbeta dem.

Lägg till den här koden i slutet av `main` metoden:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen skapade genom att ta bort kön med hjälp av [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) metoden .

Lägg till den här koden i slutet av `main` metoden:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden listar meddelandena i kön och hämtar och tar sedan bort dem innan de slutligen tar bort kön.

I konsolfönstret navigerar du till programkatalogen och skapar och kör sedan programmet.

```console
mvn compile
```

Skapa sedan paketet.

```console
mvn package
```

Använd följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Appens utdata liknar följande exempel:

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

När appen pausar innan du tar emot meddelanden kontrollerar du ditt lagringskonto i [Azure Portal](https://portal.azure.com). Kontrollera att meddelandena finns i kön.

Tryck på `Enter` tangenten för att ta emot och ta bort meddelandena. När du uppmanas till det trycker `Enter` du på tangenten igen för att ta bort kön och avsluta demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en kö och lägger till meddelanden i den med hjälp av Java-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen lärde du dig hur du tar bort en meddelandekö.

Självstudier, exempel, snabbstarter och annan dokumentation finns här:

> [!div class="nextstepaction"]
> [Azure för Java-molnutvecklare](/azure/developer/java/)

- Mer information Azure Queue Storage exempelappar finns i [Azure Queue Storage-klientbiblioteket v12 för Java – exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
