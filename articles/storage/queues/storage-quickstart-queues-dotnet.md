---
title: 'Snabbstart: Azure Queue Storage klientbibliotek v12 – .NET'
description: Lär dig hur du använder Azure Queue Storage-klientbiblioteket v12 för .NET för att skapa en kö och lägga till meddelanden i kön. Härnäst får du lära dig hur du läser och tar bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: f0b03f8a0a9e3aa277ec940c8e65064ef0411ccc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867627"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Snabbstart: Azure Queue Storage klientbibliotek v12 för .NET

Kom igång med Azure Queue Storage klientbibliotek version 12 för .NET. Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ de här stegen för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Använd Azure Queue Storage v12 för .NET för att:

- Skapa en kö
- Lägga till meddelanden i en kö
- Titta på meddelanden i en kö
- Uppdatera ett meddelande i en kö
- Ta emot meddelanden från en kö
- Ta bort meddelanden från en kö
- Ta bort en kö

Ytterligare resurser:

- [Referensdokumentation för API](/dotnet/api/azure.storage.queues)
- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
- [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
- [Exempel](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
- Azure Storage konto – skapa [ett lagringskonto](../common/storage-account-create.md)
- Aktuella [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativsystem. Se till att hämta SDK och inte körningen.

## <a name="setting-up"></a>Inrätta

Det här avsnittet går igenom hur du förbereder ett projekt för att arbeta Azure Queue Storage klientbiblioteket v12 för .NET.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett .NET Core-program med namnet `QueuesQuickstartV12` .

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en `dotnet new` ny konsolapp med namnet `QueuesQuickstartV12` . Det här kommandot skapar ett enkelt "hello world"-C#-projekt med en enda källfil med namnet `Program.cs` .

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Växla till den nyligen skapade `QueuesQuickstartV12` katalogen.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i programkatalogen installerar du Azure Queue Storage för .NET-paketet med hjälp av `dotnet add package` kommandot .

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

Från projektkatalogen:

1. Öppna filen `Program.cs` i redigeringsredigeraren
1. Ta bort `Console.WriteLine("Hello, World");` instruktionen
1. Lägga till `using` direktiv
1. Uppdatera `Main` metoddeklarationen för att [stödja asynkron kod](/dotnet/csharp/whats-new/csharp-7#async-main)

Här är koden:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 kB stort. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning för arbete som ska bearbetas asynkront. Queue Storage erbjuder tre typer av resurser:

- Lagringskontot
- En kö i lagringskontot
- Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över arkitekturen för Queue Storage](./media/storage-queues-introduction/queue1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

- [`QueueServiceClient`](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` gör att du kan hantera alla köer i ditt lagringskonto.
- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient): Med `QueueClient` klassen kan du hantera och ändra en enskild kö och dess meddelanden.
- [`QueueMessage`](/dotnet/api/azure.storage.queues.models.queuemessage): Klassen `QueueMessage` representerar de enskilda objekt som returneras vid anrop till en [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) kö.

## <a name="code-examples"></a>Kodexempel

Dessa exempelkodfragment visar hur du utför följande åtgärder med Azure Queue Storage klientbibliotek för .NET:

- [Hämta anslutningssträngen](#get-the-connection-string)
- [Skapa en kö](#create-a-queue)
- [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
- [Titta på meddelanden i en kö](#peek-at-messages-in-a-queue)
- [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
- [Ta emot meddelanden från en kö](#receive-messages-from-a-queue)
- [Ta bort meddelanden från en kö](#delete-messages-from-a-queue)
- [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Följande kod hämtar anslutningssträngen för lagringskontot. Anslutningssträngen lagras i miljövariabeln som skapades i [avsnittet Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den här koden i `Main` metoden :

```csharp
Console.WriteLine("Azure Queue Storage client library v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Följande kod lägger till ett GUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information finns i [Namngivning av köer och metadata.](/rest/api/storageservices/naming-queues-and-metadata)

Skapa en instans av [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) klassen . Anropa sedan metoden [`CreateAsync`](/dotnet/api/azure.storage.queues.queueclient.createasync) för att skapa kön i ditt lagringskonto.

Lägg till den här koden i slutet av `Main` metoden:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodfragment lägger asynkront till meddelanden i kö genom att anropa [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) metoden . Den sparar också ett som [`SendReceipt`](/dotnet/api/azure.storage.queues.models.sendreceipt) returneras från ett `SendMessageAsync` anrop. Kvittot används för att uppdatera meddelandet senare i programmet.

Lägg till den här koden i slutet av `Main` metoden:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Granska meddelandena i kön genom att anropa [`PeekMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) metoden . Den här metoden hämtar ett eller flera meddelanden från kön, men ändrar inte meddelandets synlighet.

Lägg till den här koden i slutet av `Main` metoden:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa [`UpdateMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) metoden . Den här metoden kan ändra tidsgränsen och innehållet för ett meddelandes synlighet. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB stor. Tillsammans med det nya innehållet för meddelandet skickar du in värdena från `SendReceipt` som sparades tidigare i koden. Värdena `SendReceipt` identifierar vilket meddelande som ska uppdateras.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Ladda ned tidigare tillagda meddelanden genom att anropa [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) metoden .

Lägg till den här koden i slutet av `Main` metoden:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Ta bort meddelanden från en kö

Ta bort meddelanden från kön när de har bearbetats. I det här fallet visar bearbetningen bara meddelandet i konsolen.

Appen pausar för användarindata genom att `Console.ReadLine` anropa innan den bearbetar och tar bort meddelandena. Kontrollera i [Azure Portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Meddelanden som inte uttryckligen tas bort blir så småningom synliga i kön igen för en annan chans att bearbeta dem.

Lägg till den här koden i slutet av `Main` metoden:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen skapade genom att ta bort kön med hjälp av [`DeleteAsync`](/dotnet/api/azure.storage.queues.queueclient.deleteasync) metoden .

Lägg till den här koden i slutet av `Main` metoden:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden listar meddelandena i kön och hämtar och tar sedan bort dem innan de slutligen tar bort kön.

I konsolfönstret navigerar du till programkatalogen och skapar och kör sedan programmet.

```console
dotnet build
```

```console
dotnet run
```

Appens utdata liknar följande exempel:

```output
Azure Queue Storage client library v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

När appen pausar innan du tar emot meddelanden kontrollerar du ditt lagringskonto i [Azure Portal](https://portal.azure.com). Kontrollera att meddelandena finns i kön.

Tryck på `Enter` tangenten för att ta emot och ta bort meddelandena. När du uppmanas till det trycker `Enter` du på tangenten igen för att ta bort kön och avsluta demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en kö och lägger till meddelanden i den med hjälp av asynkron .NET-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen lärde du dig hur du tar bort en meddelandekö.

Självstudier, exempel, snabbstarter och annan dokumentation finns här:

> [!div class="nextstepaction"]
> [Azure för .NET- och .NET Core-utvecklare](/dotnet/azure/)

- Mer information finns i Azure Storage [för .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
- Mer information Azure Queue Storage exempelappar finns i [Azure Queue Storage för .NET-exempel.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)
- Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
