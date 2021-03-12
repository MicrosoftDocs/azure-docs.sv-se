---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: ce6d2c34c48a26f99f78c364db5f06f9931c9dd7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021819"
---
## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:
- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installera [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste registrera resurs **slut punkten** för den här snabb starten.
- En [åtkomsttoken för användare](../../access-tokens.md). Var noga med att ange omfånget till "chatt" och anteckna token-strängen och userId-strängen.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `ChatQuickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: **program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Ändra katalogen till den nya app-mappen och Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installera paketet

Installera klient biblioteket för Azure Communication Chat för .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.5
```

## <a name="object-model"></a>Objekt modell

Följande klasser hanterar några av de viktigaste funktionerna i Azure Communication Servicess Chat-klient bibliotek för C#.

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/Hämta deltagare, skicka Skriv meddelanden och läsa kvitton. |

## <a name="create-a-chat-client"></a>Skapa en Chat-klient

Om du vill skapa en chatt-klient använder du kommunikations tjänstens slut punkt och den åtkomsttoken som har genererats som en del av de nödvändiga stegen. Du måste använda- `CommunicationIdentityClient` klassen från identitets klient biblioteket för att skapa en användare och utfärda en token som skickas till din Chat-klient.

Läs mer om [åtkomsttoken för användare](../../access-tokens.md).

Den här snabb starten omfattar inte att skapa en tjänst nivå för att hantera token för chatt-programmet, men det rekommenderas. Läs mer om [chatt-arkitekturen](../../../concepts/chat/concepts.md)

Kopiera följande kodfragment och klistra in dem i käll filen: **program.cs**
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `createChatThread` metoden på chatClient för att skapa en chatt-tråd
- Används `topic` för att ge ett ämne till den här chatten. Ämnet kan uppdateras när chatt-tråden har skapats med hjälp av `UpdateTopic` funktionen.
- Använd `participants` egenskap för att skicka en lista med `ChatParticipant` objekt som ska läggas till i chatt-tråden. `ChatParticipant`Objektet initieras med ett `CommunicationIdentifier` objekt. `CommunicationIdentifier` kan vara av typen `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` eller `PhoneNumberIdentifier` . Om du till exempel vill hämta ett `CommunicationIdentifier` objekt måste du skicka ett åtkomst-ID som du skapade genom att följa anvisningarna för att [skapa en användare](../../access-tokens.md#create-an-identity)

Objektet Response från `createChatThread` metoden innehåller `chatThread` information. För att interagera med chatt-åtgärder, till exempel att lägga till deltagare, skicka ett meddelande, ta bort ett meddelande osv., `chatThreadClient` måste en klient instans instansieras med hjälp av- `GetChatThreadClient` metoden på `ChatClient` klienten.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd
`GetChatThreadClient`Metoden returnerar en tråd klient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till medlemmar, skicka meddelande, osv. threadId är det unika ID: t för den befintliga chatt tråden.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Används `SendMessage` för att skicka ett meddelande till en tråd.

- Används `content` för att ange innehållet för meddelandet, det krävs.
- Används `type` för meddelandets innehålls typ, till exempel text eller HTML. Om det inte anges så anges text.
- Används `senderDisplayName` för att ange avsändarens visnings namn. Om inget anges anges en tom sträng.

```csharp
var messageId = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
```
## <a name="get-a-message"></a>Hämta ett meddelande

Används `GetMessage` för att hämta ett meddelande från tjänsten.
`messageId` är det unika ID: t för meddelandet.

`ChatMessage` är svaret som returnerades från att hämta ett meddelande, det innehåller ett ID, som är den unika identifieraren för meddelandet, bland andra fält. Se Azure. Communication. chat. ChatMessage

```csharp
ChatMessage chatMessage = await chatThreadClient.GetMessageAsync(messageId: messageId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Du kan hämta Chat-meddelanden genom att avsöka `GetMessages` metoden på chatt-trådens klient vid angivna intervall.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` tar en valfri `DateTimeOffset` parameter. Om förskjutningen anges visas meddelanden som har tagits emot, uppdaterats eller tagits bort efter det. Observera att meddelanden som mottagits före förskjutnings tiden, men som redige ras eller tas bort efter att de också kommer att returneras.

`GetMessages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av `UpdateMessage` och `DeleteMessage` . För borttagna meddelanden `chatMessage.DeletedOn` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `chatMessage.EditedOn` returnerar en datetime som anger när meddelandet redigerades. Det går att komma åt den ursprungliga tiden för att skapa meddelanden med `chatMessage.CreatedOn` , och det kan användas för att ordna meddelandena.

`GetMessages` returnerar olika typer av meddelanden som kan identifieras av `chatMessage.Type` . Dessa typer är:

- `Text`: Vanligt chatt-meddelande som skickas av en tråd medlem.

- `Html`: Ett formaterat textmeddelande. Observera att kommunikations tjänst användare för närvarande inte kan skicka RichText-meddelanden. Den här meddelande typen stöds av meddelanden som skickas från Team användare till kommunikations tjänster användare i team interop-scenarier.

- `TopicUpdated`: System meddelande som anger att ämnet har uppdaterats. ReadOnly

- `ParticipantAdded`: System meddelande som anger att en eller flera deltagare har lagts till i chatt-tråden. ReadOnly

- `ParticipantRemoved`: System meddelande som anger att en deltagare har tagits bort från chatt-tråden.

Mer information finns i [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Uppdatera ett meddelande

Du kan uppdatera ett meddelande som redan har skickats genom att anropa `UpdateMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(messageId: id, content: content);
```

## <a name="deleting-a-message"></a>Ta bort ett meddelande

Du kan ta bort ett meddelande genom att `DeleteMessage` anropa `ChatThreadClient` .

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(messageId: id);
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

När en tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att kunna skicka meddelanden till tråden och lägga till/ta bort andra deltagare. Innan du anropar `AddParticipants` bör du se till att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

Används `AddParticipants` för att lägga till en eller flera deltagare i chatt-tråden. Följande är de attribut som stöds för varje tråd deltagare:
- `communicationUser`, krävs, är tråd deltagarens identitet.
- `displayName`, valfritt är visnings namnet för tråd deltagaren.
- `shareHistoryTime`, valfri tid från vilken chatt-historiken delas med deltagaren.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```
## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

På samma sätt som du lägger till en användare i en tråd kan du ta bort användare från en chatt-tråd. För att göra det måste du spåra identiteten `CommunicationUser` för den deltagare som du har lagt till.

```csharp
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
await chatThreadClient.RemoveParticipantAsync(identifier: gloria);
```

## <a name="get-thread-participants"></a>Få tråd deltagare

Används `GetParticipants` för att hämta deltagarna i chatt-tråden.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-typing-notification"></a>Skicka meddelande vid inmatning

Används `SendTypingNotification` för att ange att användaren skriver ett svar i tråden.

```csharp
await chatThreadClient.SendTypingNotificationAsync();
```

## <a name="send-read-receipt"></a>Skicka Läs kvitto

Används `SendReadReceipt` för att meddela andra deltagare att meddelandet har lästs av användaren.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="get-read-receipts"></a>Hämta Läs kvitton

Använd `GetReadReceipts` för att kontrol lera status för meddelanden för att se vilka som har lästs av andra deltagare i en chatt-tråd.

```csharp
AsyncPageable<ChatMessageReadReceipt> allReadReceipts = chatThreadClient.GetReadReceiptsAsync();
await foreach (ChatMessageReadReceipt readReceipt in allReadReceipts)
{
    Console.WriteLine($"{readReceipt.ChatMessageId}:{((CommunicationUserIdentifier)readReceipt.Sender).Id}:{readReceipt.ReadOn}");
}
```
## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `dotnet run` kommandot.

```console
dotnet run
```
