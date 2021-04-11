---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 5bf4bbe2c8dc863f67dffb50609f7775a4499e3a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073469"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installera [Xcode](https://developer.apple.com/xcode/) och [CocoaPods](https://cocoapods.org/). Du kan använda Xcode för att skapa ett iOS-program för snabb starten och CocoaPods för att installera beroenden.
- Skapa en Azure Communication Services-resurs. Mer information finns i [snabb start: skapa och hantera resurser för kommunikations tjänster](../../create-communication-resource.md). I den här snabb starten måste du registrera resurs slut punkten.
- Skapa två användare i Azure Communication Services och utfärda dem till en [åtkomsttoken för användare](../../access-tokens.md). Se till att ange omfånget till `chat` och anteckna `token` strängen samt `userId` strängen. I den här snabb starten skapar du en tråd med en första deltagare och lägger sedan till en andra deltagare i tråden.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-ios-application"></a>Skapa ett nytt iOS-program

Öppna Xcode och välj **skapa ett nytt Xcode-projekt**. Välj sedan **iOS** som plattform och **app** för mallen.

Ange **ChatQuickstart** som projekt namn. Välj sedan **storyboard** som gränssnitt, **UIKit-appen delegeras** som livs cykel och **Swift** som språk.

Välj **Nästa** och välj den katalog där du vill att projektet ska skapas.

### <a name="install-the-libraries"></a>Installera biblioteken

Använd CocoaPods för att installera de nödvändiga beroendena för kommunikations tjänster.

På kommando raden går du in i rot katalogen för iOS- `ChatQuickstart` projektet. Skapa en Podfile med följande kommando: `pod init` .

Öppna Podfile och Lägg till följande beroenden till `ChatQuickstart` målet:

```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Installera beroenden med följande kommando: `pod install` . Observera att det också skapar en Xcode-arbetsyta.

När du `pod install` har kört öppnar du projektet på nytt i Xcode genom att välja det nyss skapade `.xcworkspace` .

### <a name="set-up-the-placeholders"></a>Konfigurera plats hållarna

Öppna arbets ytan `ChatQuickstart.xcworkspace` i Xcode och öppna sedan `ViewController.swift` .

I den här snabb starten lägger du till din kod till `viewController` och visar utdata i Xcode-konsolen. Den här snabb starten tar inte itu med att skapa ett användar gränssnitt i iOS. 

`viewController.swift`Importera `AzureCommunication` och- `AzureCommunicatonChat` biblioteken överst i:

```
import AzureCommunication
import AzureCommunicationChat
```

Kopiera följande kod till- `viewDidLoad()` metoden för `ViewController` :

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

I demonstrations syfte använder vi en semafor för att synkronisera koden. I följande steg ersätter du plats hållarna med exempel kod med hjälp av Azure Communication Services chat-biblioteket.


### <a name="create-a-chat-client"></a>Skapa en Chat-klient

Ersätt kommentaren `<CREATE A CHAT CLIENT>` med följande kod:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

Ersätt `<ACS_RESOURCE_ENDPOINT>` med slut punkten för Azure Communication Services-resursen. Ersätt `<ACCESS_TOKEN>` med en giltig åtkomsttoken för kommunikations tjänster.

Den här snabb starten behandlar inte att skapa en tjänst nivå för att hantera token för chatt-programmet, men det rekommenderas. Mer information finns i avsnittet "chatt-arkitektur" i [chatt-koncept](../../../concepts/chat/concepts.md).

Mer information om token för användar åtkomst finns i [snabb start: skapa och hantera åtkomsttoken](../../access-tokens.md).

## <a name="object-model"></a>Objekt modell 

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Chat SDK för Java Script.

| Name                                   | Beskrivning                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| `ChatThreadClient` | Den här klassen krävs för chatt-trådens funktion. Du får en instans via `ChatClient` , och använder den för att skicka, ta emot, uppdatera och ta bort meddelanden. Du kan också använda den för att lägga till, ta bort och hämta användare, skicka meddelanden till Skriv meddelanden och läsa kvitton och prenumerera på chatt-händelser. |

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Nu använder du `ChatClient` för att skapa en ny tråd med en första användare.

Ersätt kommentaren `<CREATE A CHAT THREAD>` med följande kod:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Ersätt `<USER_ID>` med ett giltigt användar-ID för kommunikations tjänster.

Du använder en semafor här för att vänta på slut för ande hanteraren innan du fortsätter. I senare steg använder du `threadId` från svaret som returnerades till slut för ande hanteraren.

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd

Nu när du har skapat en chatt-tråd kan du hämta en `ChatThreadClient` för att utföra åtgärder i tråden.

Ersätt kommentaren `<CREATE A CHAT THREAD CLIENT>` med följande kod:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Ersätt kommentaren `<SEND A MESSAGE>` med följande kod:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Först skapar du `SendChatMessageRequest` , som innehåller innehållet och avsändarens visnings namn. Den här begäran kan också innehålla resurs historik tiden, om du vill ta med den. Svaret som returnerades till slut för ande hanteraren innehåller ID: t för meddelandet som skickades.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

Ersätt kommentaren `<ADD A USER>` med följande kod:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Ersätt `<USER_ID>` med kommunikations tjänsternas användar-ID för den användare som ska läggas till.

När du lägger till en deltagare i en tråd kan svaret som returneras innehålla fel. Felen representerar fel vid tillägg av vissa deltagare.

## <a name="list-users-in-a-thread"></a>Lista användare i en tråd

Ersätt kommentaren `<LIST USERS>` med följande kod:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

Ersätt kommentaren `<REMOVE A USER>` med följande kod:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Ersätt `<USER ID>` med kommunikations tjänsternas användar-ID för den deltagare som tas bort.

## <a name="run-the-code"></a>Kör koden

I Xcode väljer du **Kör** för att skapa och köra projektet. I-konsolen kan du visa utdata från koden och logga utdata från Chat-klienten.

