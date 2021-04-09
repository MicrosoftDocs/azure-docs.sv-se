---
title: 'Snabb start: Azure Queue Storage klient bibliotek V12-Java'
description: Lär dig hur du använder Azure Queue Storage-V12 för klient bibliotek för Java för att skapa en kö och lägga till meddelanden till den. Lär dig sedan att läsa och ta bort meddelanden från kön. Du lär dig också hur du tar bort en kö.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 814531adc9dafa524797d0c2674a1e600e407bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588224"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Snabb start: Azure Queue Storage V12 för klient bibliotek för Java

Kom igång med Azure-Queue Storage klient bibliotek V12 för Java. Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

Använd Azure Queue Storage-V12 för klient bibliotek för Java för att:

- Skapa en kö
- Lägga till meddelanden i en kö
- Granska meddelanden i en kö
- Uppdatera ett meddelande i en kö
- Ta emot och ta bort meddelanden från en kö
- Ta bort en kö

Ytterligare resurser:

- [Referensdokumentation för API](/java/api/overview/azure/storage-queue-readme)
- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Paket (maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Exempel](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Förutsättningar

- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare
- [Apache maven](https://maven.apache.org/download.cgi)
- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- Azure Storage konto – [skapa ett lagrings konto](../common/storage-account-create.md)

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt för att arbeta med Azure Queue Storage-V12 för Java.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Java-program med namnet `queues-quickstart-v12` .

1. I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du Maven för att skapa en ny konsol app med namnet `queues-quickstart-v12` . Skriv följande `mvn` kommando för att skapa ett "Hello World"-Java-projekt.

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

1. Resultatet från att generera projektet bör se ut ungefär så här:

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

Öppna `pom.xml` filen i text redigeraren. Lägg till följande beroende element i gruppen med beroenden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Navigera till `/src/main/java/com/queues/quickstart` katalogen
1. Öppna `App.java` filen i redigerings programmet
1. Ta bort `System.out.println("Hello, world");` instruktionen
1. Lägg till `import` direktiv

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

## <a name="object-model"></a>Objekt modell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett Queue-meddelande kan vara upp till 64 KB stort. En kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront. Queue Storage erbjuder tre typer av resurser:

- Lagrings kontot
- En kö i lagrings kontot
- Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över kö Storage-arkitektur](./media/storage-queues-introduction/queue1.png)

Använd följande Java-klasser för att interagera med dessa resurser:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): `QueueClientBuilder` Klassen konfigurerar och instansierar ett `QueueClient` objekt.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): Med `QueueServiceClient` kan du hantera alla köer i ditt lagrings konto.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): `QueueClient` Klassen låter dig hantera och ändra en enskild kö och dess meddelanden.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): `QueueMessageItem` Klassen representerar de enskilda objekt som returneras vid anrop [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) i en kö.

## <a name="code-examples"></a>Kodexempel

I de här exempel kods tycken visar vi hur du utför följande åtgärder med Azure Queue Storage-klient biblioteket för java:

- [Hämta anslutningssträngen](#get-the-connection-string)
- [Skapa en kö](#create-a-queue)
- [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
- [Granska meddelanden i en kö](#peek-at-messages-in-a-queue)
- [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
- [Ta emot och ta bort meddelanden från en kö](#receive-and-delete-messages-from-a-queue)
- [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Följande kod hämtar anslutnings strängen för lagrings kontot. Anslutnings strängen lagras i den miljö variabel som skapades i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden i- `main` metoden:

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

Välj ett namn för den nya kön. Följande kod lägger till ett GUID-värde till könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemena bokstäver, siffror och bindestreck, och måste börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste vara mellan 3 och 63 tecken långt. Mer information om namngivning av köer finns i [namnge köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).

Skapa en instans av [`QueueClient`](/java/api/com.azure.storage.queue.queueclient) klassen. Anropa sedan [`Create`](/java/api/com.azure.storage.queue.queueclient.create) metoden för att skapa kön i ditt lagrings konto.

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

Följande kodfragment lägger till meddelanden i kön genom att anropa- [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) metoden. Det sparar också en [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) returnerad från ett `sendMessage` samtal. Resultatet används för att uppdatera meddelandet senare i programmet.

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

Titta på meddelandena i kön genom att anropa- [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) metoden. Den här metoden hämtar ett eller flera meddelanden från början av kön, men ändrar inte synligheten för meddelandet.

Lägg till den här koden i slutet av `main` metoden:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa- [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) metoden. Den här metoden kan ändra ett meddelandes Synlighets-timeout och innehåll. Meddelande innehållet måste vara en kodad UTF-8-sträng som är upp till 64 KB stor. Tillsammans med nytt innehåll för meddelandet skickar du meddelande-ID och pop-kvitto genom att använda det `SendMessageResult` som sparades tidigare i koden. Meddelande-ID och pop-kvitto identifierar vilket meddelande som ska uppdateras.

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

Hämta tidigare tillagda meddelanden genom att anropa- [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) metoden. Exempel koden tar också bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet visar bearbetningen bara meddelandet i-konsolen.

Appen pausar indata från användaren genom att anropa `System.console().readLine();` innan den tar emot och tar bort meddelandena. Kontrol lera i [Azure Portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Eventuella meddelanden som inte tas bort kommer att bli synliga i kön igen för en annan chans att bearbeta dem.

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

Följande kod rensar resurserna som skapas av appen genom att ta bort kön med hjälp av- [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) metoden.

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

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. I koden visas meddelanden i kön, sedan hämtas och tas de bort innan kön tas bort.

I konsol fönstret navigerar du till program katalogen och skapar och kör sedan programmet.

```console
mvn compile
```

Sedan skapar du paketet.

```console
mvn package
```

Använd följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Utdata från appen liknar följande exempel:

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

När appen pausar innan meddelanden tas emot kontrollerar du ditt lagrings konto i [Azure Portal](https://portal.azure.com). Kontrol lera att meddelandena finns i kön.

Tryck på knappen `Enter` för att ta emot och ta bort meddelandena. När du uppmanas till det trycker du på `Enter` nyckeln igen för att ta bort kön och avsluta demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en kö och lägger till meddelanden i den med hjälp av Java Code. Sedan har du lärt dig att granska, hämta och ta bort meddelanden. Slutligen har du lärt dig hur du tar bort en meddelandekö.

För självstudier, exempel, snabb starter och annan dokumentation går du till:

> [!div class="nextstepaction"]
> [Azure för Java-molnutvecklare](/azure/developer/java/)

- Mer information om Azure Queue Storage Sample-appar finns i [azure Queue Storage-klient bibliotek V12 för Java-exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
