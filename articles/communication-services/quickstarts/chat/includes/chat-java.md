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
ms.openlocfilehash: 7fe50a6236cf67f1048dddecbf46fea836ec05c5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125963"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) version 8 eller senare.
- [Apache Maven](https://maven.apache.org/download.cgi).
- En distribuerad kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- En [åtkomsttoken för användare](../../access-tokens.md). Var noga med att ange omfånget till "chatt" och anteckna token-strängen och userId-strängen.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-java-application"></a>Skapa ett nytt Java-program

Öppna terminalen eller kommando fönstret och navigera till den katalog där du vill skapa ditt Java-program. Kör kommandot nedan för att skapa Java-projektet från maven-archetype-snabb starts mal len.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Du ser att "generera"-målet skapade en katalog med samma namn som artifactId. Under den här katalogen `src/main/java directory` innehåller projekt käll koden, `src/test/java` katalogen innehåller test källan och pom.xml-filen är projektets projekt objekts modell eller [Pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Uppdatera programmets POM-fil för att använda Java 8 eller högre:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Lägg till paket referenser för chatt-SDK

Referera till `azure-communication-chat` paketet med chatt-API: erna i Pom-filen:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

För autentisering måste klienten referera till `azure-communication-common` paketet:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Chat SDK för Java.

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatAsyncClient | Den här klassen krävs för den asynkrona chatt funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton. |
| ChatThreadAsyncClient | Den här klassen krävs för den asynkrona chatt funktionen. Du får en instans via ChatAsyncClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton. |

## <a name="create-a-chat-client"></a>Skapa en Chat-klient
Om du vill skapa en chatt-klient använder du SIP-slutpunkten och den åtkomsttoken som genererades som en del av de nödvändiga stegen. Med token för användar åtkomst kan du skapa klient program som direkt autentiserar till Azure Communication Services. När du har genererat dessa token på servern skickar du tillbaka dem till en klient enhet. Du måste använda klassen CommunicationTokenCredential från common SDK för att skicka token till din Chat-klient.

Läs mer om [chatt-arkitekturen](../../../concepts/chat/concepts.md)

När du lägger till import instruktionerna ska du bara lägga till importer från com. Azure. Communication. Chat och com. Azure. Communication. chatt. Models-namnrymder, och inte från com. Azure. Communication. chatt. implementation-namnrymden. I app. java-filen som genererades via maven kan du använda följande kod för att börja med:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `createChatThread` metoden för att skapa en chatt-tråd.
`createChatThreadOptions` används för att beskriva tråd förfrågan.

- Använd `topic` parametern för konstruktorn för att ge ett ämne till den här chatten. Ämnet kan uppdateras när chatt-tråden har skapats med hjälp av `UpdateThread` funktionen.
- Används `participants` för att visa en lista över tråd deltagarna som ska läggas till i tråden. `ChatParticipant` tar användaren som du skapade i snabb starten av [användar åtkomst-token](../../access-tokens.md) .

`CreateChatThreadResult` är svaret som returnerades från att skapa en chatt-tråd.
Den innehåller en `getChatThread()` metod som returnerar det `ChatThread` objekt som kan användas för att hämta tråd klienten som du kan använda `ChatThreadClient` för att utföra åtgärder på den skapade tråden: Lägg till deltagare, skicka meddelande, osv. `ChatThread` Objektet innehåller också `getId()` metoden som hämtar trådens unika ID.

```Java
ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Visa lista över Chat-trådar

Använd `listChatThreads` metoden för att hämta en lista över befintliga chatt trådar.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd

`getChatThreadClient`Metoden returnerar en tråd klient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till deltagare, skicka meddelande, osv. `chatThreadId` är det unika ID: t för den befintliga chatt tråden.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Använd `sendMessage` metoden för att skicka ett meddelande till den tråd som du nyss skapade, som identifieras av chatThreadId.
`sendChatMessageOptions` används för att beskriva begäran om chatt-meddelande.

- Används `content` för att tillhandahålla Chat-meddelandets innehåll.
- Används `type` för att ange innehålls typen för chatt meddelandet, text eller HTML.
- Används `senderDisplayName` för att ange avsändarens visnings namn.

Svaret `sendChatMessageResult` innehåller ett `id` , som är det unika ID: t för meddelandet.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Du kan hämta Chat-meddelanden genom att avsöka `listMessages` metoden på chatt-trådens klient vid angivna intervall.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av. editMessage () och. deleteMessage (). För borttagna meddelanden `chatMessage.getDeletedOn()` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `chatMessage.getEditedOn()` returnerar en datetime som anger när meddelandet redigerades. Det går att komma åt den ursprungliga tiden för att skapa meddelanden med `chatMessage.getCreatedOn()` , och det kan användas för att ordna meddelandena.

Läs mer om meddelande typer här: [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Skicka Läs kvitto

Använd `sendReadReceipt` metoden för att skicka en Läs inläsnings händelse till en chatt-tråd för en användares räkning.
`chatMessageId` är det unika ID: t för det chatt meddelande som lästes.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Visa lista över Chat-deltagare

Används `listParticipants` för att hämta en sida-samling som innehåller deltagarna i den chatt-tråd som identifieras av chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

När en chatt-tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att skicka meddelanden till chatt-tråden och lägga till/ta bort andra deltagare. Du måste börja med att hämta en ny åtkomsttoken och identitet för den användaren. Innan du anropar addParticipants-metoden kontrollerar du att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

Använd `addParticipants` metoden för att lägga till deltagare i tråden.

- `communicationIdentifier`, krävs, är den CommunicationIdentifier som du har skapat av CommunicationIdentityClient i [användar åtkomst-token](../../access-tokens.md) snabb start.
- `displayName`, valfritt är visnings namnet för tråd deltagaren.
- `shareHistoryTime`, valfritt, är den tid från vilken chatt-historiken delas med deltagaren. Om du vill dela historiken på grund av att chatten är i gång, anger du den här egenskapen till ett datum som är lika med eller mindre än tiden för tråd skapande. Om du vill dela ingen Historik tidigare till när deltagaren lades in, ställer du in den på det aktuella datumet. Om du vill dela del historik anger du det datum som krävs.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Kör koden

Navigera till den katalog som innehåller *pom.xml* -filen och kompilera projektet med hjälp av följande `mvn` kommando.

```console
mvn compile
```

Sedan skapar du paketet.

```console
mvn package
```

Kör följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
