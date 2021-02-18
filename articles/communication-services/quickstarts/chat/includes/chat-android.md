---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7e62bbc5929eaf23a9b7be12de222105bc2529cd
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653592"
---
## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installera [Android Studio](https://developer.android.com/studio)kommer vi att använda Android Studio för att skapa ett Android-program för snabb starten för att installera beroenden.
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste **Registrera resurs slut punkten** för den här snabb starten.
- Skapa **två** kommunikations tjänst användare och utfärda dem till [en åtkomsttoken för](../../access-tokens.md)användar åtkomst användare. Var noga med att ange omfånget till **chatten** och **notera token-strängen och userId-strängen**. I den här snabb starten kommer vi att skapa en tråd med en första deltagare och sedan lägga till en andra deltagare i tråden.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-android-application"></a>Skapa ett nytt Android-program

1. Öppna Android Studio och välj `Create a new project` . 
2. I nästa fönster väljer `Empty Activity` du som projekt mal len.
3. När du väljer alternativ anger `ChatQuickstart` du som projekt namn.
4. Klicka på Nästa och välj den katalog där du vill att projektet ska skapas.

### <a name="install-the-libraries"></a>Installera biblioteken

Vi använder Gradle för att installera de nödvändiga kommunikations tjänst beroendena. Från kommando raden navigerar du i projektets rot Katalog `ChatQuickstart` . Öppna appens build. gradle-fil och Lägg till följande beroenden till `ChatQuickstart` målet:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.5'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.5'
```

Klicka på Synkronisera nu i Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>Andra Installera bibliotek via maven
Om du vill importera biblioteket till projektet med [maven](https://maven.apache.org/) build-systemet lägger du till det i `dependencies` avsnittet i appens `pom.xml` fil och anger dess artefakt-ID och den version som du vill använda:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.5</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Konfigurera plats hållarna

Öppna och redigera filen `MainActivity.java` . I den här snabb starten ska vi lägga till vår kod i `MainActivity` och visa utdata i-konsolen. Den här snabb starten tar inte itu med att skapa ett användar gränssnitt. Importera och bibliotek överst i filen `Communication common` `Communication chat` :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Kopiera följande kod till filen `MainActivity` :

```
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

I följande steg ska vi ersätta plats hållarna med exempel kod med hjälp av Azure Communication Services chat-biblioteket.


### <a name="create-a-chat-client"></a>Skapa en Chat-klient

Ersätt kommentaren `<CREATE A CHAT CLIENT>` med följande kod (Lägg till import instruktionerna överst i filen):

```java
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatClient client = new ChatClient.Builder()
    .endpoint(endpoint)
    .credentialInterceptor(chain -> chain.proceed(chain.request()
        .newBuilder()
        .header(HttpHeader.AUTHORIZATION, userAccessToken)
        .build());
```

1. Använd `AzureCommunicationChatServiceAsyncClient.Builder` för att konfigurera och skapa en instans av `AzureCommunicationChatClient` .
2. Ersätt `<resource>` med kommunikations tjänst resursen.
3. Ersätt `<user_access_token>` med en giltig åtkomsttoken för kommunikations tjänster.

## <a name="object-model"></a>Objekt modell
Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Servicess Chat-klient bibliotek för Java Script.

| Name                                   | Beskrivning                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton, prenumerera på chatt-händelser. |

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Vi använder oss `ChatClient` för att skapa en ny tråd med en första användare.

Ersätt kommentaren `<CREATE A CHAT THREAD>` med följande kod:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setId(id)
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
    .setTopic(topic)
    .setParticipants(participants);

// optional, set a repeat request ID 
final String repeatabilityRequestID = '123';

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
    public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
        // MultiStatusResponse is the result returned from creating a thread.
        // It has a 'multipleStatus' property which represents a list of IndividualStatusResponse.
        String threadId;
        List<IndividualStatusResponse> statusList = result.getMultipleStatus();
        for (IndividualStatusResponse status : statusList) {
            if (status.getId().endsWith("@thread.v2")
                && status.getType().contentEquals("Thread")) {
                threadId = status.getId();
                break;
            }
        }
        // Take further action.
    }

    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
    }
});
```

Ersätt `<user_id>` med ett giltigt användar-ID för kommunikations tjänster. Vi använder `threadId` från svaret som returnerades till slut för ande hanteraren i senare steg.

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd

Nu när vi har skapat en chatt-tråd får vi en `ChatThreadClient` för att utföra åtgärder i tråden. Ersätt kommentaren `<CREATE A CHAT THREAD CLIENT>` med följande kod:

```
ChatThreadClient threadClient =
        new ChatThreadClient.Builder()
            .endpoint(<endpoint>))
            .build();
```

Ersätt `<endpoint>` med kommunikations tjänstens slut punkt.

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Ersätt kommentaren `<SEND A MESSAGE>` med följande kod:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.sendChatMessage(threadId, message, new Callback<String>() {
    @Override
    public void onSuccess(String messageId, Response response) {
        // A string is the response returned from sending a message, it is an id, 
        // which is the unique ID of the message.
        final String chatMessageId = messageId;
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

Ersätt `<thread_id>` med det tråd-ID som skickar meddelandet till.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

Ersätt kommentaren `<ADD A USER>` med följande kod:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The CommunicationUser.identifier you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "a new participant";
participants.add(new ChatParticipant().setId(id).setDisplayName(displayName));
// The model to pass to the add method.
AddChatParticipantsRequest participants = new AddChatParticipantsRequest()
    .setParticipants(participants);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.addChatParticipants(threadId, participants, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Ersätt `<user_id>` med kommunikations tjänsternas användar-ID för den användare som ska läggas till. 
2. Ersätt `<thread_id>` med det tråd-ID som användaren lägger till i.

## <a name="list-users-in-a-thread"></a>Lista användare i en tråd

Ersätt kommentaren `<LIST USERS>` med följande kod:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";

// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
    @Override
    public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> firstPage,
        Response response) {
        // pageCollection enables enumerating list of chat participants.
        pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> firstPage, Response response) {
                for (ChatParticipant participant : firstPage.getItems()) {
                    // Take further action.
                }
                retrieveNextParticipantsPages(firstPage.getPageId(), pageCollection);
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                // Handle error.
            }
         }
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});

void listChatParticipantsNext(String nextLink,
    AsyncPagedDataCollection<Page<ChatParticipant>> pageCollection) {
        @Override
        public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
            for (ChatParticipant participant : nextPage.getItems()) {
                // Take further action.
            }
            if (nextPage.getPageId() != null) {
                retrieveNextParticipantsPages(nextPage.getPageId(), pageCollection);
            }
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            // Handle error.
        }
}
```

Ersätt `<thread_id>` med det tråd-ID som du visar användare för.

## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

Ersätt kommentaren `<REMOVE A USER>` med följande kod:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";
// The unique ID of the participant.
final String participantId = "<participant_id>";
threadClient.removeChatParticipant(threadId, participantId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Ersätt `<thread_id>` med det tråd-ID som tar bort användaren från.
1. Ersätt `<participant_id>` med kommunikations tjänsternas användar-ID för den deltagare som tas bort.

## <a name="run-the-code"></a>Kör koden

I Android Studio trycker du på Kör-knappen för att skapa och köra projektet. I-konsolen kan du visa utdata från koden och logga utdata från ChatClient.
