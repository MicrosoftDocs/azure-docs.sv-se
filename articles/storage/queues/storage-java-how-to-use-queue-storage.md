---
title: Använda Queue Storage från Java-Azure Storage
description: Lär dig hur du använder Queue Storage för att skapa och ta bort köer. Lär dig att infoga, granska, hämta och ta bort meddelanden med Azure Storage klient bibliotek för Java.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 997a37ac4252813abf1b35877cd34e192ec3e2ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585725"
---
# <a name="how-to-use-queue-storage-from-java"></a>Använda Queue Storage från Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Översikt

I den här guiden får du lära dig hur du kodar vanliga scenarier med hjälp av Azure Queue Storage-tjänsten. Exemplen är skrivna i Java och använder [Azure Storage SDK för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage). Scenarier innefattar att **Infoga**, **Granska**, **Hämta** och **ta bort** Kömeddelanden. Kod för att **skapa** och **ta bort** köer omfattas också. Mer information om köer finns i avsnittet [Nästa steg](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program

# <a name="java-v12"></a>[Java-V12](#tab/java)

Kontrol lera först att utvecklings systemet uppfyller de krav som anges i [Azure Queue Storage-V12 för klient bibliotek för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue).

Så här skapar du ett Java-program med namnet `queues-how-to-v12` :

1. I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du Maven för att skapa en ny konsol app med namnet `queues-how-to-v12` . Skriv följande `mvn` kommando för att skapa ett "Hello World"-Java-projekt.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

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
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created `queues-howto-v12` directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Installera paketet

Öppna `pom.xml` filen i text redigeraren. Lägg till följande beroende element i gruppen med beroenden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Kontrol lera först att utvecklings systemet uppfyller de krav som anges i [Azure Storage SDK för Java-V8](https://github.com/azure/azure-storage-java). Följ anvisningarna för att ladda ned och installera Azure Storage bibliotek för Java. Sedan kan du skapa ett Java-program med hjälp av exemplen i den här artikeln.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program till att få åtkomst till Queue Storage

Lägg till följande import-instruktioner överst i Java-filen där du vill använda Azure Storage-API: er för att komma åt köer:

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Azure Storage-anslutningssträng

En Azure Storage-klient använder en lagrings anslutnings sträng för att komma åt data hanterings tjänster. Hämta namnet och den primära åtkomst nyckeln för ditt lagrings konto som anges i [Azure Portal](https://portal.azure.com). Använd dem som- `AccountName` och- `AccountKey` värden i anslutnings strängen. Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Du kan lagra strängen i tjänstens konfigurations fil med namnet `ServiceConfiguration.cscfg` . För en app som körs i en Microsoft Azure roll får du åtkomst till anslutnings strängen genom att anropa `RoleEnvironment.getConfigurationSettings` . Här är ett exempel på hur du hämtar anslutnings strängen från ett `Setting` element med namnet `StorageConnectionString` :

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

Följande exempel förutsätter att du har ett `String` objekt som innehåller lagrings anslutnings strängen.

## <a name="how-to-create-a-queue"></a>Gör så här: skapa en kö

# <a name="java-v12"></a>[Java-V12](#tab/java)

Ett- `QueueClient` objekt innehåller åtgärder för att interagera med en kö. Följande kod skapar ett- `QueueClient` objekt. Använd `QueueClient` objektet för att skapa den kö som du vill använda.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

`CloudQueueClient`Med ett objekt kan du hämta referens objekt för köer. Följande kod skapar ett- `CloudQueueClient` objekt som innehåller en referens till den kö som du vill använda. Du kan skapa kön om den inte finns.

> [!NOTE]
> Det finns andra sätt att skapa `CloudStorageAccount` objekt. Mer information finns `CloudStorageAccount` i i [Azure Storage client SDK Reference](https://azure.github.io/azure-sdk-for-java/storage.html).)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>Gör så här: lägga till ett meddelande i en kö

# <a name="java-v12"></a>[Java-V12](#tab/java)

Anropa metoden om du vill infoga ett meddelande i en befintlig kö `sendMessage` . Ett meddelande kan vara antingen en sträng (i UTF-8-format) eller en byte mat ris. Här är kod som skickar ett sträng meddelande till kön.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Om du vill infoga ett meddelande i en befintlig kö börjar du med att skapa ett nytt `CloudQueueMessage` . Anropa sedan `addMessage` metoden. En `CloudQueueMessage` kan skapas från antingen en sträng (i UTF-8-format) eller en byte mat ris. Här är kod som skapar en kö (om den inte finns) och infogar meddelandet `Hello, World` .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>Gör så här: granska vid nästa meddelande

Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa `peekMessage` .

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här gör du: ändra innehållet i ett köat meddelande

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbets uppgift kan du använda den här funktionen för att uppdatera statusen. Följande kod uppdaterar ett Queue-meddelande med nytt innehåll och anger tids gränsen för visning för att utöka ytterligare 30 sekunder. Om du utökar Synlighets tids gränsen får klienten ytterligare 30 sekunder att fortsätta arbeta med meddelandet. Du kan också behålla antalet nya försök. Om meddelandet provas fler än *n* gånger skulle du ta bort det. Det här scenariot skyddar mot ett meddelande som utlöser ett program fel varje gången det bearbetas.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Följande kod exempel söker igenom kön med meddelanden, letar upp det första meddelande innehållet som matchar en Sök sträng, ändrar meddelandets innehåll och avslutar.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Följande kod exempel söker igenom kön med meddelanden, letar upp det första meddelande innehållet som matchar `Hello, world` , ändrar meddelandets innehåll och avslutar.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

Följande kod exempel uppdaterar bara det första synliga meddelandet i kön.

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>Så här gör du: Hämta Kölängd

Du kan hämta en uppskattning av antalet meddelanden i en kö.

# <a name="java-v12"></a>[Java-V12](#tab/java)

`getProperties`Metoden returnerar flera värden, inklusive antalet meddelanden som för närvarande finns i en kö. Antalet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort efter din begäran. `getApproximateMessageCount`Metoden returnerar det sista värdet som hämtades av anropet till `getProperties` , utan att anropa Queue Storage.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

`downloadAttributes`Metoden hämtar flera värden, inklusive antalet meddelanden som för närvarande finns i en kö. Antalet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort efter din begäran. `getApproximateMessageCount`Metoden returnerar det sista värdet som hämtades av anropet till `downloadAttributes` , utan att anropa Queue Storage.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>Gör så här: ta bort nästa meddelande i kö

# <a name="java-v12"></a>[Java-V12](#tab/java)

Din kod avstår ett meddelande från en kö i två steg. När du anropar `receiveMessage` får du nästa meddelande i en kö. Ett meddelande som returnerades från `receiveMessage` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa `deleteMessage` . Om din kod inte kan bearbeta ett meddelande, säkerställer den här processen att du får samma meddelande och försöker igen. Dina kod anrop `deleteMessage` direkt efter att meddelandet har bearbetats.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Din kod avstår ett meddelande från en kö i två steg. När du anropar `retrieveMessage` får du nästa meddelande i en kö. Ett meddelande som returnerades från `retrieveMessage` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa `deleteMessage` . Om din kod inte kan bearbeta ett meddelande, säkerställer den här processen att du får samma meddelande och försöker igen. Dina kod anrop `deleteMessage` direkt efter att meddelandet har bearbetats.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för demsmq-meddelanden

Det finns två sätt att anpassa meddelande hämtning från en kö. Börja med att hämta en batch med meddelanden (upp till 32). Sedan anger du en längre eller kortare tids gräns för insikter, vilket gör att koden får mer eller mindre tid att bearbeta varje meddelande fullständigt.

# <a name="java-v12"></a>[Java-V12](#tab/java)

I följande kod exempel används `receiveMessages` metoden för att få 20 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `for` loop. Den anger också timeout för insikter till fem minuter (300 sekunder) för varje meddelande. Tids gränsen startar för alla meddelanden samtidigt. När fem minuter har passerat sedan anropet till `receiveMessages` , kommer alla meddelanden som inte tas bort att bli synliga igen.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

I följande kod exempel används `retrieveMessages` metoden för att få 20 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `for` loop. Den anger också timeout för insikter till fem minuter (300 sekunder) för varje meddelande. Tids gränsen startar för alla meddelanden samtidigt. När fem minuter har passerat sedan anropet till `retrieveMessages` , kommer alla meddelanden som inte tas bort att bli synliga igen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>Gör så här: Visa en lista över köer

# <a name="java-v12"></a>[Java-V12](#tab/java)

Om du vill hämta en lista över aktuella köer anropar du `QueueServiceClient.listQueues()` metoden, som returnerar en samling `QueueItem` objekt.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Om du vill hämta en lista över aktuella köer anropar du `CloudQueueClient.listQueues()` metoden, som returnerar en samling `CloudQueue` objekt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>Så här gör du: ta bort en kö

# <a name="java-v12"></a>[Java-V12](#tab/java)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du `delete` metoden på `QueueClient` objektet.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java-V8](#tab/java8)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du `deleteIfExists` metoden på `CloudQueue` objektet.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

- [Azure Storage SDK för Java](https://github.com/Azure/Azure-SDK-for-Java)
- [Azure Storage SDK-referens för klient](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Azure Storage Services REST API](/rest/api/storageservices/)
- [Azure Storage teamets blogg](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
