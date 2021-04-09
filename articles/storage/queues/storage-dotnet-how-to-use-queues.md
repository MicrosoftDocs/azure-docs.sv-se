---
title: Kom igång med Azure Queue Storage med .NET-Azure Storage
description: Azure Queue Storage tillhandahåller Reliable, asynkrona meddelanden mellan program komponenter. Med hjälp av molnmeddelanden kan programkomponenter skalas separat.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: d54b8f15c90aa8f6ffcc04453fee0349e501f47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585759"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Komma igång med Azure Queue Storage med hjälp av .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Översikt

Azure Queue Storage tillhandahåller moln meddelanden mellan program komponenter. Vid utformning av program för skalning är program komponenterna ofta fristående så att de kan skalas oberoende av varandra. Queue Storage levererar asynkrona meddelanden mellan program komponenter, oavsett om de körs i molnet, på Skriv bordet, på en lokal server eller på en mobil enhet. Queue Storage också stöd för hantering av asynkrona uppgifter och skapande av process arbets flöden.

### <a name="about-this-tutorial"></a>Om den här självstudiekursen

Den här självstudien visar hur du skriver .NET-kod för några vanliga scenarier med hjälp av Azure Queue Storage. Du lär dig bland annat hur du skapar och tar bort köer och hur du lägger till, läser och tar bort kömeddelanden.

**Uppskattad tidsåtgång:** 45 minuter

### <a name="prerequisites"></a>Förutsättningar

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- Ett [Azure Storage konto](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Konfigurera sedan din utvecklingsmiljö i Visual Studio så att du är redo att testa kodexemplen i den här guiden.

### <a name="create-a-windows-console-application-project"></a>Skapa ett Windows-konsolprogramprojekt

Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsol program i Visual Studio 2019. Stegen är ungefär som i andra versioner av Visual Studio.

1. Välj **Arkiv**  >  **nytt**  >  **projekt**
2. Välj **plattforms**  >  **fönster**
3. Välj **konsol program (.NET Framework)**
4. Välj **Nästa**
5. Ange ett namn för ditt program i fältet **projekt namn**
6. Välj **Skapa**

Alla kodexempel i den här självstudiekursen kan läggas till i `Main()`-metoden i konsolprogrammets `Program.cs`-fil.

Du kan använda Azure Storage klient bibliotek i vilken typ av .NET-program som helst, inklusive en Azure-moln tjänst eller webbapp, samt Skriv bords-och mobil program. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

### <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Du måste referera till följande fyra paket i projektet för att slutföra den här självstudien:

- [Azure. Core-bibliotek för .net](https://www.nuget.org/packages/azure.core/): det här paketet ger delade primitiver, abstraktioner och hjälp för moderna .net Azure SDK-klientcertifikat.
- [Azure. Storage. common-klient bibliotek för .net](https://www.nuget.org/packages/azure.storage.common/): det här paketet innehåller en infrastruktur som delas av andra Azure Storage klient bibliotek.
- [Azure. Storage. Queues-klient bibliotek för .net](https://www.nuget.org/packages/azure.storage.queues/): det här paketet gör det möjligt att arbeta med Azure-Queue Storage för att lagra meddelanden som kan kommas åt av en klient.
- [System.Configuration.ConfigurationManager-bibliotek för .net](https://www.nuget.org/packages/system.configuration.configurationmanager/): det här paketet ger till gång till konfigurationsfiler för klient program.

Du kan använda NuGet för att hämta paketen. Följ de här stegen:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.
1. Välj **Bläddra**
1. Sök online efter `Azure.Storage.Queues` och välj **Installera** för att installera Azure Storage klient bibliotek och dess beroenden. Detta kommer också att installera Azure. Storage. common och Azure. Core-bibliotek, som är beroenden av kösystemet.
1. Sök online efter `System.Configuration.ConfigurationManager` och välj **Installera** för att installera Configuration Manager.

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Du måste referera till följande tre paket i projektet för att slutföra den här självstudien:

- [Microsoft. Azure. Storage. common-klient bibliotek för .net](https://www.nuget.org/packages/microsoft.azure.storage.common/): det här paketet ger programmatisk åtkomst till data resurser i ditt lagrings konto.
- [Microsoft Azure Queue Storage klient bibliotek för .net](https://www.nuget.org/packages/microsoft.azure.storage.queue/): det här klient biblioteket gör det möjligt att arbeta med Azure-Queue Storage för att lagra meddelanden som kan nås av en klient.
- [Microsoft Azure Configuration Manager-biblioteket för .NET](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): det här paketet tillhandahåller en klass för parsning av en anslutningssträng i en konfigurationsfil, oavsett var ditt program körs.

Du kan använda NuGet för att hämta paketen. Följ de här stegen:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.
1. Välj **Bläddra**
1. Sök online efter `Microsoft.Azure.Storage.Queue` och välj **Installera** för att installera Azure Storage klient bibliotek och dess beroenden. Detta kommer också att installera Microsoft. Azure. Storage. common-biblioteket, vilket är ett beroende av Queue-biblioteket.
1. Sök online efter `Microsoft.Azure.ConfigurationManager` och välj **Installera** för att installera Azure-Configuration Manager.

---

### <a name="determine-your-target-environment"></a>Fastställ målmiljön

Du har två miljöalternativ för att köra exemplen i den här guiden:

- Du kan köra din kod mot ett Azure Storage-konto i molnet.
- Du kan köra din kod mot Azurite Storage-emulatorn. Azurite är en lokal miljö som emulerar ett Azure Storage-konto i molnet. Azurite är ett kostnads fritt alternativ för att testa och felsöka koden när ditt program är under utveckling. Emulatorn använder sig av ett välkänt konto och nyckel. Mer information finns i [använda Azurite-emulatorn för lokal Azure Storage utveckling och testning](../common/storage-use-azurite.md).

> [!NOTE]
> Du kan använda lagringsemulatorn för att undvika kostnader associerade med Azure Storage. Men om du väljer att rikta in dig på ett Azure Storage konto i molnet blir kostnaderna för att utföra den här kursen försumbara.

## <a name="get-your-storage-connection-string"></a>Hämta anslutnings strängen för lagring

Azure Storage klient bibliotek för .NET-stöd med en lagrings anslutnings sträng för att konfigurera slut punkter och autentiseringsuppgifter för åtkomst till lagrings tjänster. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelkoden måste autentisera åtkomsten till lagringskontot. När du vill autentisera tillhandahåller du autentiseringsuppgifterna för ditt lagringskonto i form av en anslutningssträng i programmet. Visa autentiseringsuppgifterna för lagringskontot:

1. Navigera till [Azure Portal](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**. Du lägger till strängvärdet för anslutningen till en miljövariabel i nästa steg.

    ![Skärmbild som visar hur man kopierar en anslutningssträng från Azure-portalen](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Mer information om anslutningssträngar finns i [Konfigurera en anslutningssträng för Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Var alltid noga med att skydda din lagringskontonyckel. Undvik att dela ut den till andra användare, hårdkoda den eller spara den i en oformaterad textfil som andra har åtkomst till. Återskapa din nyckel med hjälp av Azure Portal om du misstänker att den komprometterats.

Det bästa sättet att underhålla anslutningssträngen för lagring är i en konfigurationsfil. För att konfigurera din anslutningssträng öppnar du `app.config`-filen i Solutions Explorer i Visual Studio. Lägg till innehållet i det `<appSettings>` element som visas här. Ersätt `connection-string` med värdet som du kopierade från ditt lagrings konto i portalen:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Din konfigurationsinställning kan till exempel se ut så här:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Om du vill rikta Azurite Storage-emulatorn kan du använda en genväg som mappar till det välkända konto namnet och nyckeln. I så fall är din inställning för anslutningssträngen:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

Lägg till följande `using`-direktiv längst upp i filen `Program.cs`:

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Skapa Queue Storage-klienten

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Med [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) klassen kan du hämta köer som lagras i Queue Storage. Här är ett sätt att skapa tjänstklienten:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Med [`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) klassen kan du hämta köer som lagras i Queue Storage. Här är ett sätt att skapa tjänstklienten:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Nu är du redo att skriva kod som läser data från och skriver data till Queue Storage.

## <a name="create-a-queue"></a>Skapa en kö

Det här exemplet visar hur du skapar en kö:

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Infoga ett meddelande i en kö

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Anropa metoden om du vill infoga ett meddelande i en befintlig kö [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) . Ett meddelande kan vara antingen en sträng (i UTF-8-format) eller en byte mat ris. Följande kod skapar en kö (om den inte finns) och infogar ett meddelande:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Om du vill infoga ett meddelande i en befintlig kö börjar du med att skapa ett nytt [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Anropa sedan [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metoden. En `CloudQueueMessage` kan skapas från antingen en sträng (i UTF-8-format) eller en byte mat ris. Här är kod som skapar en kö (om den inte finns) och infogar meddelandet `Hello, World` : om du vill infoga ett meddelande i en befintlig kö skapar du först ett nytt [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Anropa sedan [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metoden. En `CloudQueueMessage` kan skapas från antingen en sträng (i UTF-8-format) eller en byte mat ris. Här är kod som skapar en kö (om den inte finns) och infogar meddelandet `Hello, World` :

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Du kan titta på meddelandena i kön utan att ta bort dem från kön genom att anropa- [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) metoden. Om du inte skickar ett värde för `maxMessages` parametern är standardvärdet att titta på ett meddelande.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa- [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) metoden.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och utökar tidsgränsen för visning med ytterligare 60 sekunder. Koden sparar statusen för arbetsuppgiften som associeras med meddelandet och ger klienten ytterligare en minut att fortsätta arbeta med meddelandet. Du kan använda den här metoden för att spåra arbets flöden i flera steg för att köa meddelanden, utan att behöva börja om från början om ett bearbetnings steg Miss lyckas på grund av maskin-eller program varu fel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än *n* försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>Ta bort nästa meddelande i kö

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Ta bort ett meddelande från en kö i två steg. När du anropar [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) får du nästa meddelande i en kö. Ett meddelande som returnerades från `ReceiveMessages` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Dina kod anrop `DeleteMessage` direkt efter att meddelandet har bearbetats.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Din kod avstår ett meddelande från en kö i två steg. När du anropar [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) får du nästa meddelande i en kö. Ett meddelande som returnerades från `GetMessage` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Dina kod anrop `DeleteMessage` direkt efter att meddelandet har bearbetats.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Använd Async-Await mönster med vanliga Queue Storage-API: er

Det här exemplet visar hur du använder Async-Await mönstret med vanliga Queue Storage-API: er. Exemplet anropar den asynkrona versionen av var och en av de angivna metoderna, som anges av `Async` suffixet för varje metod. När en async-metod används pausar Async-Awaits mönstret lokal körning tills anropet har slutförts. Detta gör att den aktuella tråden kan arbeta med annat, vilket innebär att flaskhalsar kan undvikas samtidigt som programmets svarstider förbättras. Mer information om hur du använder Async-Await-mönstret i .NET finns i [async och await (C# och Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>Använd ytterligare alternativ för demsmq-meddelanden

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

I följande kod exempel används [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) metoden för att få 20 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `foreach` loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, så efter att fem minuter har gått sedan anropet till `ReceiveMessages` , kommer alla meddelanden som inte har tagits bort att bli synliga igen.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

I följande kod exempel används [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) metoden för att få 20 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `foreach` loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, så efter att fem minuter har gått sedan anropet till `GetMessages` , kommer alla meddelanden som inte har tagits bort att bli synliga igen.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Hämta kölängden

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Du kan hämta en uppskattning av antalet meddelanden i en kö. [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties)Metoden returnerar köegenskaper inklusive antal meddelanden. [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount)Egenskapen innehåller det ungefärliga antalet meddelanden i kön. Talet är inte lägre än det faktiska antalet meddelanden i kön, men det kan vara högre.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Du kan hämta en uppskattning av antalet meddelanden i en kö. [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true)Metoden returnerar attribut för kön inklusive antal meddelanden. [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true)Egenskapen returnerar det sista värdet som hämtades av `FetchAttributes` metoden, utan att anropa Queue Storage.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Ta bort en kö

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) metoden i objektet köobjekt.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) metoden i objektet köobjekt.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

- Se Queue Storage referens dokumentation för fullständig information om tillgängliga API: er:
  - [Azure Storage klient bibliotek för .NET-referens](/dotnet/api/overview/azure/storage)
  - [Azure Storage REST API referens](/rest/api/storageservices/)
- Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.
  - [Kom igång med Azure Table Storage att använda .net](../../cosmos-db/tutorial-develop-table-dotnet.md) för att lagra strukturerade data.
  - [Kom igång med Azure Blob Storage att använda .net](../blobs/storage-quickstart-blobs-dotnet.md) för att lagra ostrukturerade data.
  - [Anslut till SQL Database med hjälp av .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) för att lagra relationsdata.
- Lär dig hur du förenklar koden du skriver så att den fungerar med Azure Storage genom att använda [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
