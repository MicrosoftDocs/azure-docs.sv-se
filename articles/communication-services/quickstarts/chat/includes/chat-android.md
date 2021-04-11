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
ms.openlocfilehash: b2c5237f3f7e949edbfb5486a3a17cc6e0a008a4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178362"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installera [Android Studio](https://developer.android.com/studio)kommer vi att använda Android Studio för att skapa ett Android-program för snabb starten för att installera beroenden.
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste **Registrera resurs slut punkten** för den här snabb starten.
- Skapa **två** kommunikations tjänst användare och utfärda dem till [en åtkomsttoken för](../../access-tokens.md)användar åtkomst användare. Se till att ange omfånget för att **chatta** och **anteckna token-strängen och userId-strängen**. I den här snabb starten kommer vi att skapa en tråd med en första deltagare och sedan lägga till en andra deltagare i tråden.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-android-application"></a>Skapa ett nytt Android-program

1. Öppna Android Studio och välj `Create a new project` . 
2. I nästa fönster väljer `Empty Activity` du som projekt mal len.
3. När du väljer alternativ anger `ChatQuickstart` du som projekt namn.
4. Klicka på Nästa och välj den katalog där du vill att projektet ska skapas.

### <a name="install-the-libraries"></a>Installera biblioteken

Vi använder Gradle för att installera de nödvändiga kommunikations tjänst beroendena. Från kommando raden navigerar du i `ChatQuickstart` projektets rot Katalog. Öppna appens build. gradle-fil och Lägg till följande beroenden till `ChatQuickstart` målet:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Exkludera meta-filer i paket alternativ i rot versionen. gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>Andra Installera bibliotek via maven
Om du vill importera biblioteket till projektet med [maven](https://maven.apache.org/) build-systemet lägger du till det i `dependencies` avsnittet i appens `pom.xml` fil och anger dess artefakt-ID och den version som du vill använda:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Konfigurera plats hållarna

Öppna och redigera filen `MainActivity.java` . I den här snabb starten ska vi lägga till vår kod i `MainActivity` och visa utdata i-konsolen. Den här snabb starten tar inte itu med att skapa ett användar gränssnitt. Importera och bibliotek överst i filen `Communication common` `Communication chat` :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Kopiera följande kod till filen `MainActivity` :

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
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
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

I följande steg ska vi ersätta plats hållarna med exempel kod med hjälp av Azure Communication Services chat-biblioteket.


### <a name="create-a-chat-client"></a>Skapa en Chat-klient

Ersätt kommentaren `<CREATE A CHAT CLIENT>` med följande kod (Lägg till import instruktionerna överst i filen):

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. Använd `ChatClientBuilder` för att konfigurera och skapa en instans av `ChatAsyncClient` .
2. Ersätt `<resource>` med kommunikations tjänst resursen.
3. Ersätt `<user_access_token>` med en giltig åtkomsttoken för kommunikations tjänster.

## <a name="object-model"></a>Objekt modell
Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Chat SDK för Java Script.

| Name                                   | Beskrivning                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient/ChatThreadAsyncClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton, prenumerera på chatt-händelser. |

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Vi använder oss `ChatAsyncClient` för att skapa en ny tråd med en första användare.

Ersätt kommentaren `<CREATE A CHAT THREAD>` med följande kod:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

Ersätt `<user_id>` med ett giltigt användar-ID för kommunikations tjänster. Vi använder `threadId` från svaret som returnerades till slut för ande hanteraren i senare steg, så Ersätt `<thread_id>` i-klassen med `threadId` vi får från den här begäran och kör appen igen.

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd

Nu när vi har skapat en chatt-tråd får vi en `ChatThreadAsyncClient` för att utföra åtgärder i tråden. Ersätt kommentaren `<CREATE A CHAT THREAD CLIENT>` med följande kod:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Se till att vi har ersatts `<thread_id>` med ett giltigt tråd-ID. vi kommer att skicka ett meddelande till den tråden nu.

Ersätt kommentaren `<SEND A MESSAGE>` med följande kod:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

När vi har fått det `chatMessageId` kan vi ersätta `<chat_message_id>` med `chatMessageId` för senare metod användning i snabb start och köra appen igen.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

Ersätt kommentaren `<ADD A USER>` med följande kod:

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

Ersätt `<second_user_id>` i-klassen med kommunikations tjänsternas användar-ID för den användare som ska läggas till. 

## <a name="list-users-in-a-thread"></a>Lista användare i en tråd

Ersätt kommentaren `<LIST USERS>` med följande kod:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Lägg till följande hjälp metod i klassen:

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

Se till att ersätta `<second_user_id>` med ett giltigt användar-ID. vi tar bort den andra användaren från tråden nu.

Ersätt kommentaren `<REMOVE A USER>` med följande kod:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Skicka ett meddelande

Ersätt kommentaren `<SEND A TYPING NOTIFICATION>` med följande kod:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Skicka ett Läs kvitto

Ersätt `<chat_message_id>` med ett giltigt chatt meddelande-ID. vi kommer att skicka Läs kvitto för det här meddelandet nu.

Ersätt kommentaren `<SEND A READ RECEIPT>` med följande kod:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Visa lista över Läs kvitton

Ersätt kommentaren `<READ RECEIPTS>` med följande kod:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Lägg till följande hjälp metod i klassen:
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>Kör koden

Tryck på Kör-knappen i Android Studio för att skapa och köra projektet. I-konsolen kan du visa utdata från koden och logga utdata från ChatClient.
