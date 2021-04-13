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
ms.openlocfilehash: e5b5433be4a95a9df9d3b3527473c3004d24acac
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327649"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) version 8 eller senare.
- [Apache Maven](https://maven.apache.org/download.cgi).
- En distribuerad Communication Services resurs och anslutningssträng. [Skapa en Communication Services resurs](../../create-communication-resource.md).
- En [användaråtkomsttoken](../../access-tokens.md). Se till att ange omfånget till "chat" och notera tokensträngen samt userId-strängen.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-java-application"></a>Skapa ett nytt Java-program

Öppna terminalen eller kommandofönstret och gå till den katalog där du vill skapa Java-programmet. Kör kommandot nedan för att generera Java-projektet från mallen maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Du ser att målet "generera" skapade en katalog med samma namn som artifactId. Under den här katalogen innehåller innehåller projektets källkod, katalogen innehåller testkällan och pom.xml-filen är projektets `src/main/java directory` `src/test/java` Project Object Model, eller [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Uppdatera pom-filen för ditt program så att java 8 eller senare används:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Lägga till paketreferenserna för Chat SDK

I POM-filen refererar du till `azure-communication-chat` paketet med chatt-API:erna:

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

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Chat SDK för Java.

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Den här klassen behövs för chattfunktionen. Du instansierar den med din prenumerationsinformation och använder den för att skapa, hämta och ta bort trådar. |
| ChatAsyncClient | Den här klassen behövs för funktionen för asynkron chatt. Du instansierar den med din prenumerationsinformation och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen behövs för chatttrådsfunktionen. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka skrivmeddelanden och läskvitton. |
| ChatThreadAsyncClient | Den här klassen behövs för funktionen för asynkron chatttråd. Du får en instans via ChatAsyncClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka skrivmeddelanden och läskvitton. |

## <a name="create-a-chat-client"></a>Skapa en chattklient
Om du vill skapa en chattklient använder du kommunikationstjänstens slutpunkt och den åtkomsttoken som genererades som en del av de nödvändiga stegen. Med token för användaråtkomst kan du skapa klientprogram som autentiserar direkt Azure Communication Services. När du har genererat dessa token på servern skickar du tillbaka dem till en klientenhet. Du måste använda klassen CommunicationTokenCredential från Common SDK för att skicka token till chattklienten.

Läs mer om [chattarkitektur](../../../concepts/chat/concepts.md)

När du lägger till importutsatserna måste du bara lägga till importer från namnrymderna com.azure.communication.chat och com.azure.communication.chat.models och inte från namnområdet com.azure.communication.chat.implementation. I filen App.java som genererades via Maven kan du börja med följande kod:

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

## <a name="start-a-chat-thread"></a>Starta en chatttråd

Använd metoden `createChatThread` för att skapa en chatttråd.
`createChatThreadOptions` används för att beskriva trådbegäran.

- Använd `topic` -parametern för konstruktorn för att ge ett ämne till den här chatten; Ämnet kan uppdateras när chatttråden har skapats med hjälp av `UpdateThread` funktionen .
- Använd `participants` för att lista tråddeltagarna som ska läggas till i tråden. `ChatParticipant` tar den användare som du skapade i [snabbstarten för](../../access-tokens.md) användaråtkomsttoken.

`CreateChatThreadResult` är svaret som returneras när du skapar en chatttråd.
Den innehåller en -metod som returnerar det -objekt som kan användas för att hämta trådklienten som du kan hämta för att utföra åtgärder på den skapade tråden: lägga till `getChatThread()` `ChatThread` `ChatThreadClient` deltagare, skicka meddelande osv. -objektet `ChatThread` innehåller också metoden som hämtar `getId()` trådens unika ID.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Lista chatttrådar

Använd metoden `listChatThreads` för att hämta en lista över befintliga chatttrådar.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Hämta en chatttrådsklient

Metoden `getChatThreadClient` returnerar en trådklient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: lägga till deltagare, skicka meddelande osv. `chatThreadId` är det unika ID:t för den befintliga chatttråden.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatttråd

Använd metoden `sendMessage` för att skicka ett meddelande till tråden som du nyss skapade och som identifieras av chatThreadId.
`sendChatMessageOptions` används för att beskriva begäran om chattmeddelande.

- Använd `content` för att tillhandahålla innehållet i chattmeddelandet.
- Använd `type` för att ange innehållstyp för chattmeddelanden, TEXT eller HTML.
- Använd `senderDisplayName` för att ange avsändarens visningsnamn.

Svaret innehåller `sendChatMessageResult` ett , som är det unika `id` ID:t för meddelandet.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot chattmeddelanden från en chatttråd

Du kan hämta chattmeddelanden genom att avssöka `listMessages` metoden på chatttrådklienten vid angivna intervall.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som har inträffat med hjälp av .editMessage() och .deleteMessage(). För borttagna meddelanden `chatMessage.getDeletedOn()` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `chatMessage.getEditedOn()` returneras ett datetime-värde som anger när meddelandet redigerades. Den ursprungliga tiden då meddelandet skapades kan nås med hjälp av `chatMessage.getCreatedOn()` , och den kan användas för att ordna meddelanden.

Läs mer om meddelandetyper här: [Meddelandetyper](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Skicka läskvitton

Använd metoden `sendReadReceipt` för att skicka en mottagningshändelse för läsning till en chatttråd för en användares räkning.
`chatMessageId` är det unika ID:t för det chattmeddelande som lästes.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Lista chattdeltagare

Använd `listParticipants` för att hämta en sidad samling som innehåller deltagarna i chatttråden som identifieras av chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Lägga till en användare som deltagare i chatttråden

När en chatttråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst att skicka meddelanden till chatttråden och lägga till/ta bort andra deltagare. Du måste börja med att hämta en ny åtkomsttoken och identitet för den användaren. Innan du anropar metoden addParticipants måste du se till att du har skaffat en ny åtkomsttoken och identitet för den användaren. Användaren behöver denna åtkomsttoken för att kunna initiera chattklienten.

Använd metoden `addParticipants` för att lägga till deltagare i tråden.

- `communicationIdentifier`, krävs, är den CommunicationIdentifier som du har skapat av CommunicationIdentityClient i snabbstarten [för användaråtkomsttoken.](../../access-tokens.md)
- `displayName`, valfritt, är visningsnamnet för tråddeltagaren.
- `shareHistoryTime`, valfritt, är den tid som chatthistoriken delas med deltagaren. Om du vill dela historiken sedan chatttråden skapades anger du det datum som är lika med eller mindre än tiden för trådskapande. Om du inte vill dela någon historik före när deltagaren lades till anger du det aktuella datumet. Om du vill dela partiell historik anger du det till det datum som krävs.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Kör koden

Navigera till katalogen som innehåller *pom.xml* och kompilera projektet med hjälp av följande `mvn` kommando.

```console
mvn compile
```

Skapa sedan paketet.

```console
mvn package
```

Kör följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
