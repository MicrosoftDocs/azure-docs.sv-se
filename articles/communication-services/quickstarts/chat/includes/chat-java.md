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
ms.openlocfilehash: 3cbed124963fe6e56d6721669d0feedc6e34ffc6
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107059"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare.
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
    <version>1.0.0-beta.4</version> 
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
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `createChatThread` metoden för att skapa en chatt-tråd.
`createChatThreadOptions` används för att beskriva tråd förfrågan.

- Används `topic` för att ge ett ämne till den här chatten. Ämnet kan uppdateras när chatt-tråden har skapats med hjälp av `UpdateThread` funktionen.
- Används `participants` för att visa en lista över tråd deltagarna som ska läggas till i tråden. `ChatParticipant` tar användaren som du skapade i snabb starten av [användar åtkomst-token](../../access-tokens.md) .

Svaret `chatThreadClient` används för att utföra åtgärder på den skapade chatten: lägga till deltagare i chatt-tråden, skicka ett meddelande, ta bort ett meddelande, osv. Den innehåller en `chatThreadId` egenskap som är det unika ID: t för chatt-tråden. Egenskapen kan nås av den offentliga metoden. getChatThreadId ().

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");
    
ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setParticipants(participants);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
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


## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd

`getChatThreadClient`Metoden returnerar en tråd klient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till deltagare, skicka meddelande, osv. `chatThreadId` är det unika ID: t för den befintliga chatt tråden.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Du kan hämta Chat-meddelanden genom att avsöka `listMessages` metoden på chatt-trådens klient vid angivna intervall.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av. editMessage () och. deleteMessage (). För borttagna meddelanden `chatMessage.getDeletedOn()` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `chatMessage.getEditedOn()` returnerar en datetime som anger när meddelandet redigerades. Det går att komma åt den ursprungliga tiden för att skapa meddelanden med `chatMessage.getCreatedOn()` , och det kan användas för att ordna meddelandena.

`listMessages` returnerar olika typer av meddelanden som kan identifieras av `chatMessage.getType()` . Dessa typer är:

- `text`: Vanligt chatt-meddelande som skickas av en tråd deltagare.

- `html`: HTML chat-meddelande som skickats av en tråd deltagare.

- `topicUpdated`: System meddelande som anger att ämnet har uppdaterats.

- `participantAdded`: System meddelande som anger att en eller flera deltagare har lagts till i chatt-tråden.

- `participantRemoved`: System meddelande som anger att en deltagare har tagits bort från chatt-tråden.

Mer information finns i [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

När en chatt-tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att skicka meddelanden till chatt-tråden och lägga till/ta bort andra deltagare. Du måste börja med att hämta en ny åtkomsttoken och identitet för den användaren. Innan du anropar addParticipants-metoden kontrollerar du att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

Använd `addParticipants` metoden för att lägga till deltagare i tråden som identifieras av threadId.

- Används `listParticipants` för att visa en lista över deltagare som ska läggas till i chatt-tråden.
- `communicationIdentifier`, krävs, är den CommunicationIdentifier som du har skapat av CommunicationIdentityClient i [användar åtkomst-token](../../access-tokens.md) snabb start.
- `display_name`, valfritt är visnings namnet för tråd deltagaren.
- `share_history_time`, valfritt, är den tid från vilken chatt-historiken delas med deltagaren. Om du vill dela historiken på grund av att chatten är i gång, anger du den här egenskapen till ett datum som är lika med eller mindre än tiden för tråd skapande. Om du vill dela ingen Historik tidigare till när deltagaren lades in, ställer du in den på det aktuella datumet. Om du vill dela del historik anger du det datum som krävs.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

AddChatParticipantsOptions addChatParticipantsOptions = new AddChatParticipantsOptions()
    .setParticipants(participants);
chatThreadClient.addParticipants(addChatParticipantsOptions);
```

## <a name="remove-participant-from-a-chat-thread"></a>Ta bort deltagare från en chatt-tråd

På samma sätt som du lägger till en deltagare i en tråd kan du ta bort deltagare från en chatt-tråd. Om du vill göra det måste du spåra identiteterna för de deltagare som du har lagt till.

Använd `removeParticipant` , där `identifier` är den CommunicationIdentifier som du har skapat.

```Java
chatThreadClient.removeParticipant(identity);
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