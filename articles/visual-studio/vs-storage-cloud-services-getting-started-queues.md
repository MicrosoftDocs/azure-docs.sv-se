---
title: Kom igång med Queue Storage med hjälp av Visual Studio (Cloud Services)
description: Komma igång med Azure Queue Storage i ett moln tjänst projekt i Visual Studio efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545877"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure Queue Storage och Visual Studio Connected Services (Cloud Services-projekt)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Queue Storage i Visual Studio när du har skapat eller refererat till ett Azure Storage-konto i ett Cloud Services-projekt med hjälp av dialog rutan **Lägg till anslutna tjänster** i Visual Studio.

Vi visar dig hur du skapar en kö i kod. Vi visar också hur du utför grundläggande åtgärder för köer, till exempel att lägga till, ändra, läsa och ta bort Kömeddelanden. Exemplen är skrivna i C#-kod och använder [Microsoft Azure Storage klient bibliotek för .net](/previous-versions/azure/dn261237(v=azure.100)).

Åtgärden **Lägg till anslutna tjänster** installerar rätt NuGet-paket för att komma åt Azure Storage i projektet och lägger till anslutnings strängen för lagrings kontot i dina projekt konfigurationsfiler.

* Se [Kom igång med Azure Queue Storage med hjälp av .net](../storage/queues/storage-dotnet-how-to-use-queues.md) för mer information om hur du hanterar köer i kod.
* Se [lagrings dokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.
* Se [Cloud Services dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/) för allmän information om Azure Cloud Services.
* Mer information om programmering av ASP.NET-program finns i [ASP.net](https://www.asp.net) .

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

## <a name="access-queues-in-code"></a>Åtkomst till köer i kod
Om du vill komma åt köer i Visual Studio-Cloud Services-projekt måste du inkludera följande objekt i valfri C#-källfil som använder Azure Queue Storage.

1. Se till att namn områdes deklarationerna överst i C#-filen inkluderar dessa **using** -instruktioner.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Hämta ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänstekonfigurationen.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Hämta ett **CloudQueueClient** -objekt för att referera till köobjektet i ditt lagrings konto.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Hämta ett **CloudQueue** -objekt för att referera till en speciell kö.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**Obs:** Använd all ovanstående kod framför koden i följande exempel.

## <a name="create-a-queue-in-code"></a>Skapa en kö i kod
Om du vill skapa kön i kod lägger du bara till ett anrop till **CreateIfNotExists**.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö
Om du vill infoga ett meddelande i en befintlig kö, skapar du ett nytt **CloudQueueMessage** -objekt och anropar sedan **AddMessage** -metoden.

Ett **CloudQueueMessage** -objekt kan skapas antingen från en sträng (i UTF-8-format) eller en byte mat ris.

Här är ett exempel som infogar meddelandet "Hello, World".

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Läsa ett meddelande i en kö
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa metoden **PeekMessage**.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö
Din kod kan ta bort (ta bort kön) ett meddelande från en kö i två steg.

1. Anropa **GetMessage** för att hämta nästa meddelande i en kö. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
2. Om du vill slutföra borttagningen av meddelandet från kön anropar du **DeleteMessage**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Följande kod anropar **DeleteMessage** direkt efter att meddelandet har bearbetats.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Använd fler alternativ för att bearbeta och ta bort Kömeddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

* Du kan hämta en batch med meddelanden (upp till 32).
* Du kan ange en längre eller kortare tids gräns för insikter, vilket gör att koden får mer eller mindre tid att bearbeta varje meddelande fullständigt. I följande kodexempel används metoden **GetMessage** för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **foreach**-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, vilket betyder att när det här har gått fem minuter sedan anropet till **GetMessage** så blir alla meddelanden som inte har tagits bort synliga igen.

Här är ett exempel:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Hämta kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. **FetchAttributes**-metoden ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Egenskapen **ApproximateMethodCount** returnerar det sista värdet som hämtades av **FetchAttributes** -metoden, utan att kötjänst anropas.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Använd Async-Await mönster med vanliga Azure Queue-API: er
Det här exemplet visar hur du använder Async-Await-mönstret med vanliga Azure Queue-API: er. Exemplet anropar den asynkrona versionen av var och en av de metoder som anges. Detta kan ses av den **asynkrona** efter korrigeringen av varje metod. När en async-metod används pausar async-await-mönstret den lokala körningen tills anropet har slutförts. Med det här beteendet kan den aktuella tråden utföra annat arbete som hjälper till att undvika prestanda Flask halsar och förbättra programmets övergripande svars tid. Mer information om hur du använder Async-Await-mönstret i .NET finns i [Async och Await (C# och Visual Basic)](/previous-versions/hh191443(v=vs.140))

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Ta bort en kö
Om du vill ta bort en kö och alla meddelanden i den anropar du **Delete**-metoden för köobjektet.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]