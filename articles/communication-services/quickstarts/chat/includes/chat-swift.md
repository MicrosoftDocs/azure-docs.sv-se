---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/11/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 75983fe89c7f8643701da96493d1b6c9ca1c159f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021896"
---
## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installera [Xcode](https://developer.apple.com/xcode/) och [Cocoapods](https://cocoapods.org/)kommer vi att använda Xcode för att skapa ett iOS-program för snabb start och Cocoapods för att installera beroenden.
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste **Registrera resurs slut punkten** för den här snabb starten.
- Skapa **två** ACS-användare och utfärda dem till användar åtkomst [token](../../access-tokens.md)för användar åtkomst. Var noga med att ange omfånget till **chatten** och **notera token-strängen och userId-strängen**. I den här snabb starten kommer vi att skapa en tråd med en första deltagare och sedan lägga till en andra deltagare i tråden.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-ios-application"></a>Skapa ett nytt iOS-program

Öppna Xcode och välj `Create a new Xcode project` .

I nästa fönster väljer `iOS` du som plattform och `App` för mallen.

När du väljer alternativ anger `ChatQuickstart` du som projekt namn. Välj `Storyboard` som gränssnitt, `UIKit App Delegate` som livs cykel och `Swift` som språk.

Klicka på Nästa och välj den katalog där du vill att projektet ska skapas.

### <a name="install-the-libraries"></a>Installera biblioteken

Vi använder Cocoapods för att installera de nödvändiga kommunikations tjänst beroendena.

Från kommando raden navigerar du i `ChatQuickstart` iOS-projektets rot Katalog.

Skapa en Podfile: `pod init`

Öppna Podfile och Lägg till följande beroenden till `ChatQuickstart` målet:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Installera beroendena skapas även en arbets yta för Xcode: `pod install`

**När du har kört Pod installerat öppnar du projektet på nytt i Xcode genom att välja det nya `.xcworkspace` .**

### <a name="setup-the-placeholders"></a>Konfigurera plats hållarna

Öppna arbets ytan `ChatQuickstart.xcworkspace` i Xcode och öppna sedan `ViewController.swift` .

I den här snabb starten kommer vi att lägga till vår kod i `viewController` och visa utdata i Xcode-konsolen. Den här snabb starten tar inte itu med att skapa ett användar gränssnitt i iOS. 

Överst i Importera- `viewController.swift` och- `AzureCommunication` `AzureCommunicatonChat` biblioteken:

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

Vi använder en semafor för att synkronisera vår kod i demonstrations syfte. I följande steg ska vi ersätta plats hållarna med exempel kod med hjälp av Azure Communication Services chat-biblioteket.


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

Ersätt `<ACS_RESOURCE_ENDPOINT>` med slut punkten för ACS-resursen.
Ersätt `<ACCESS_TOKEN>` med en giltig ACS-åtkomsttoken.

Den här snabb starten omfattar inte att skapa en tjänst nivå för att hantera token för chatt-programmet, men det rekommenderas. Se följande dokumentation för mer information om [Chat-arkitekturen](../../../concepts/chat/concepts.md)

Läs mer om [åtkomsttoken för användare](../../access-tokens.md).

## <a name="object-model"></a>Objekt modell 
Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Servicess Chat-klient bibliotek för Java Script.

| Name                                   | Beskrivning                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton, prenumerera på chatt-händelser. |

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Nu ska vi använda vår `ChatClient` för att skapa en ny tråd med en första användare.

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

Vi använder en semafor här för att vänta på slut för ande hanteraren innan du fortsätter. Vi kommer att använda `threadId` från svaret som returnerades till slut för ande hanteraren i senare steg.

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd

Nu när vi har skapat en chatt-tråd får vi en `ChatThreadClient` för att utföra åtgärder i tråden.

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

Först utformar vi den `SendChatMessageRequest` som innehåller innehållet och avsändarernas visnings namn (Alternativt kan även innehålla resurs historik tiden). Svaret som returnerades till slut för ande hanteraren innehåller ID: t för meddelandet som skickades.

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

Ersätt `<USER_ID>` med ACS-användar-ID för den användare som ska läggas till.

När du lägger till en deltagare i en tråd kan svaret som returnerades slutföras fel. Felen representerar fel vid tillägg av vissa deltagare.

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

Ersätt `<USER ID>` med användar-ID för kommunikations tjänster för den deltagare som tas bort.

## <a name="run-the-code"></a>Kör koden

I Xcode trycker du på Kör-knappen för att skapa och köra projektet. I-konsolen kan du visa utdata från koden och logga utdata från ChatClient.


