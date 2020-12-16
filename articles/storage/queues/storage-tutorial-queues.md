---
title: 'Självstudie: arbeta med Azure Queue Storage-köer i .NET'
description: En själv studie kurs om hur du använder Azure-Queue Storage för att skapa köer och infoga, hämta och ta bort meddelanden med hjälp av .NET-kod.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 06/09/2020
ms.topic: tutorial
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c41b218ac0d347b2e58931421493755346b13d7
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591981"
---
# <a name="tutorial-work-with-azure-queue-storage-queues-in-net"></a>Självstudie: arbeta med Azure Queue Storage-köer i .NET

Azure Queue Storage implementerar molnbaserade köer för att möjliggöra kommunikation mellan komponenter i ett distribuerat program. Varje kö underhåller en lista över meddelanden som kan läggas till av en avsändarens komponent och bearbetas av en mottagar komponent. Med en kö kan ditt program skalas direkt för att möta efter frågan. Den här artikeln visar de grundläggande stegen för att arbeta med en Azure Queue Storage-kö.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa ett Azure Storage-konto
> - Skapa appen
> - Lägg till Azures klient bibliotek
> - Lägg till stöd för asynkron kod
> - Skapa en kö
> - Infoga meddelanden i en kö
> - Ta bort meddelanden från kön
> - Ta bort en tom kö
> - Sök efter kommando rads argument
> - Kompilera och köra appen

## <a name="prerequisites"></a>Krav

- Få en kostnads fri kopia av den plattforms oberoende kod redigeraren i [Visual Studio](https://code.visualstudio.com/download) .
- Hämta och installera [.net Core SDK](https://dotnet.microsoft.com/download) version 3,1 eller senare.
- Om du inte har en aktuell Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Skapa först ett Azure Storage-konto. En steg-för-steg-guide om hur du skapar ett lagrings konto finns i [skapa ett lagrings konto](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json). Detta är ett separat steg som du utför när du har skapat ett kostnads fritt Azure-konto i kraven.

## <a name="create-the-app"></a>Skapa appen

Skapa ett .NET Core-program med namnet `QueueApp` . För enkelhetens skull kommer den här appen både att skicka och ta emot meddelanden via kön.

1. I ett konsol fönster (till exempel cmd, PowerShell eller Azure CLI) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `QueueApp` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil med namnet `Program.cs` .

   ```console
   dotnet new console -n QueueApp
   ```

2. Växla till den nya mappen `QueueApp` och kompilera appen så att du ser att allting fungerar.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Du bör se resultat som liknar följande utdata:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

<!-- markdownlint-disable MD023 -->

## <a name="add-the-azure-client-libraries"></a>Lägg till Azures klient bibliotek

1. Lägg till Azure Storage klient bibliotek i projektet med hjälp av `dotnet add package` kommandot.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   Kör följande kommando från projektmappen i konsol fönstret.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   Kör följande kommandon från projektmappen i konsol fönstret.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Lägg till using-instruktioner

1. Från kommando raden i projekt katalogen skriver `code .` du för att öppna Visual Studio Code i den aktuella katalogen. Låt kommando rads fönstret vara öppet. Det kommer att finnas fler kommandon att köra senare. Om du uppmanas att lägga till C#-till gångar som krävs för att skapa och felsöka klickar du på **Ja** -knappen.

1. Öppna `Program.cs` käll filen och Lägg till följande namn områden direkt efter `using System;` instruktionen. Den här appen använder typer från dessa namn områden för att ansluta till Azure Storage och arbeta med köer.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Spara filen `Program.cs`.

## <a name="add-support-for-asynchronous-code"></a>Lägg till stöd för asynkron kod

Eftersom appen använder moln resurser körs koden asynkront.

1. Uppdatera `Main` metoden för körning asynkront. Ersätt `void` med ett `async Task` RETUR värde.

   ```csharp
   static async Task Main(string[] args)
   ```

1. Spara filen `Program.cs`.

## <a name="create-a-queue"></a>Skapa en kö

Innan du gör några anrop till Azure-API: er måste du hämta dina autentiseringsuppgifter från Azure Portal.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Lägg till anslutnings strängen i appen

Lägg till anslutnings strängen i appen så att den kan komma åt lagrings kontot.

1. Växla tillbaka till Visual Studio Code.

1. I `Main` -metoden ersätter du `Console.WriteLine("Hello, World");` koden med följande rad som hämtar anslutnings strängen från miljö variabeln.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Lägg till följande kod för `Main` att skapa ett köobjekt, som senare skickas till metoderna för att skicka och ta emot.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Spara filen.

## <a name="insert-messages-into-the-queue"></a>Infoga meddelanden i kön

Skapa en ny metod för att skicka ett meddelande till kön.

1. Lägg till följande `InsertMessageAsync` metod i `Program` klassen.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   Den här metoden överförde en Queue-referens. En ny kö skapas, om den inte redan finns, genom att anropa [`CreateIfNotExistsAsync`](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync) . Sedan lägger den till i `newMessage` kön genom att anropa [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   Den här metoden överförde en Queue-referens. En ny kö skapas, om den inte redan finns, genom att anropa [`CreateIfNotExistsAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) . Sedan lägger den till i `newMessage` kön genom att anropa [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Valfritt:** Som standard är den maximala tiden till Live för ett meddelande inställt på sju dagar. Du kan ange ett positivt tal för Time-to-Live-meddelandet. Följande kodfragment lägger till ett meddelande som **aldrig** upphör att gälla.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

    Använd `Timespan.FromSeconds(-1)` i ditt anrop till om du vill lägga till ett meddelande som inte upphör att gälla `SendMessageAsync` .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

    Använd `Timespan.FromSeconds(-1)` i ditt anrop till om du vill lägga till ett meddelande som inte upphör att gälla `AddMessageAsync` .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Spara filen.

Ett Queue-meddelande måste ha ett format som är kompatibelt med en XML-begäran med UTF-8-kodning. Ett meddelande kan vara upp till 64 KB stort. Om ett meddelande innehåller binära data [base64-kodar](/dotnet/api/system.convert.tobase64string) du meddelandet.

## <a name="dequeue-messages"></a>Ta bort meddelanden från kön

Skapa en ny metod för att hämta ett meddelande från kön. När meddelandet har tagits emot är det viktigt att ta bort det från kön så att det inte bearbetas mer än en gång.

1. Lägg till en ny metod `RetrieveNextMessageAsync` som kallas för din `Program` klass.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   Den här metoden tar emot ett meddelande från kön genom [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) att anropa och skicka `1` in den första parametern för att bara hämta nästa meddelande i kön. När meddelandet har tagits emot tar du bort det från kön genom att anropa [`DeleteMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync) .

   När ett meddelande skickas till kön med en version av SDK före V12, base64-kodas automatiskt. Från och med V12 togs funktionen bort. När du hämtar ett meddelande med hjälp av V12 SDK är det inte automatiskt base64-avkodat. Du måste explicit [base64-avkoda](/dotnet/api/system.convert.frombase64string) innehållet själv.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   Den här metoden tar emot ett meddelande från kön genom att anropa [`GetMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) . När meddelandet har tagits emot tar du bort det från kön genom att anropa [`DeleteMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Spara filen.

## <a name="delete-an-empty-queue"></a>Ta bort en tom kö

Det är en bra idé i slutet av ett projekt att identifiera om du fortfarande behöver de resurser som du har skapat. Resurser som fortsätter att köras kostar pengar. Om kön finns men är tom ber du användaren om han vill ta bort den.

1. Expandera `RetrieveNextMessageAsync` metoden om du vill ta bort en prompt för att ta bort den tomma kön.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Spara filen.

## <a name="check-for-command-line-arguments"></a>Sök efter kommando rads argument

Om det finns kommando rads argument som skickas till appen, antar du att de är ett meddelande som ska läggas till i kön. Koppla ihop argumenten för att skapa en sträng. Lägg till den här strängen i meddelande kön genom att anropa `InsertMessageAsync` metoden som vi lade till tidigare.

Försök att hämta igen om det inte finns några kommando rads argument. Anropa `RetrieveNextMessageAsync` metoden för att hämta nästa meddelande i kön.

Vänta sedan på användarindata innan du avslutar genom att anropa `Console.ReadLine` .

1. Expandera `Main` metoden för att kontrol lera om det finns kommando rads argument och vänta på användarindata.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Spara filen.

## <a name="complete-code"></a>Fullständig kod

Här är den fullständiga kod listan för det här projektet.

   # <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>Kompilera och köra appen

1. Från kommando raden i projekt katalogen kör du följande dotNet-kommando för att skapa projektet.

   ```console
   dotnet build
   ```

1. När projektet har skapats kör du följande kommando för att lägga till det första meddelandet i kön.

   ```console
   dotnet run First queue message
   ```

   Du bör se dessa utdata:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

1. Kör appen utan kommando rads argument för att ta emot och ta bort det första meddelandet i kön.

   ```console
   dotnet run
   ```

1. Fortsätt att köra appen tills alla meddelanden har tagits bort. Om du kör den en gång till får du ett meddelande om att kön är tom och att du uppmanas att ta bort kön.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

1. Skapa en kö
1. Lägga till och ta bort meddelanden från en kö
1. Ta bort en Azure Queue Storage-kö

Mer information finns i snabb starter för Azure Queue Storage.

> [!div class="nextstepaction"]
> [Snabb start för köer för portalen](storage-quickstart-queues-portal.md)

- [Snabb start för köer för .NET](storage-quickstart-queues-dotnet.md)
- [Snabb start för köer för Java](storage-quickstart-queues-java.md)
- [Snabb start för köer för python](storage-quickstart-queues-python.md)
- [Snabb start för köer för Java Script](storage-quickstart-queues-nodejs.md)
