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
ms.openlocfilehash: bcbf2137e578f703cf70b1b47952736aa50f7f17
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178439"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installera [python](https://www.python.org/downloads/)
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste registrera resurs **slut punkten** för den här snabb starten
- En [åtkomsttoken för användare](../../access-tokens.md). Var noga med att ange omfånget till "chatt" och anteckna token-strängen och userId-strängen.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Använd en text redigerare för att skapa en fil med namnet **Start-chat.py** i projektets rot Katalog och lägga till strukturen för programmet, inklusive grundläggande undantags hantering. Du kommer att lägga till alla käll koder för den här snabb starten till den här filen i följande avsnitt.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Installera SDK

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Chat SDK för python.

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton. |

## <a name="create-a-chat-client"></a>Skapa en Chat-klient

Om du vill skapa en chatt-klient använder du kommunikations tjänstens slut punkt och den `Access Token` som har genererats som en del av de nödvändiga stegen. Läs mer om [åtkomsttoken för användare](../../access-tokens.md).

Den här snabb starten omfattar inte att skapa en tjänst nivå för att hantera token för chatt-programmet, men det rekommenderas. Se följande dokumentation för mer information om [Chat-arkitekturen](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `create_chat_thread` metoden för att skapa en chatt-tråd.

- Använd `topic` för att ge ett tråd ämne. Ämnet kan uppdateras när chatt-tråden har skapats med hjälp av `update_thread` funktionen.
- Använd `thread_participants` för att visa en lista över `ChatThreadParticipant` som ska läggas till i chatten. `ChatThreadParticipant` tar med `CommunicationUserIdentifier` typ som det `user` du fick när du skapade genom att [skapa en användare](../../access-tokens.md#create-an-identity)

`CreateChatThreadResult` är resultatet som returneras från att skapa en tråd. du kan använda det för att hämta den `id` tråd som skapades av chatten. Detta `id` kan sedan användas för att hämta ett `ChatThreadClient` objekt med hjälp av `get_chat_thread_client` metoden. `ChatThreadClient` kan användas för att utföra andra chatt-åtgärder för den här chatt-tråden.

```python
from azure.communication.chat import ChatThreadParticipant

topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd
`get_chat_thread_client`Metoden returnerar en tråd klient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till deltagare, skicka meddelande, osv. thread_id är det unika ID: t för den befintliga chatt tråden.

`ChatThreadClient` kan användas för att utföra andra chatt-åtgärder för den här chatt-tråden.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Lista alla chatt trådar
`list_chat_threads`Metoden returnerar en iterator av typen `ChatThreadItem` . Den kan användas för att visa alla Chat-trådar.

- Används `start_time` för att ange den tidigaste tidpunkt som chatten ska få upp till.
- Används `results_per_page` för att ange det maximala antalet chatt trådar som returneras per sida.

En iterator av `[ChatThreadItem]` är svaret som returneras från List trådar

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Använd `send_message` metoden för att skicka ett meddelande till en chatt-tråd som du nyss skapade, som identifieras av thread_id.

- Används `content` för att tillhandahålla chatt-meddelandets innehåll.
- Används `chat_message_type` för att ange meddelandets innehålls typ. Möjliga värden är ' text ' och ' HTML '; om inte det angivna standardvärdet ' text ' har tilldelats.
- Används `sender_display_name` för att ange visnings namnet på avsändaren.

`SendChatMessageResult` är svaret som returnerades från att skicka ett meddelande, det innehåller ett ID, som är det unika ID: t för meddelandet.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Du kan hämta chatt meddelanden genom att avsöka `list_messages` metoden vid angivna intervall.

- Används `results_per_page` för att ange det maximala antalet meddelanden som ska returneras per sida.
- Används `start_time` för att ange den tidigaste tidpunkt som meddelanden ska hämtas till.

En iterator av `[ChatMessage]` är svaret som returneras från listan över meddelanden

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av `update_message` och `delete_message` . För borttagna meddelanden `ChatMessage.deleted_on` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `ChatMessage.edited_on` returnerar en datetime som anger när meddelandet redigerades. Det går att få åtkomst till den ursprungliga tiden för att skapa meddelanden med `ChatMessage.created_on` som kan användas för att ordna meddelandena.

`list_messages` returnerar olika typer av meddelanden som kan identifieras av `ChatMessage.type` . 

Läs mer om meddelande typer här: [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Skicka Läs kvitto
`send_read_receipt`Metoden kan användas för att bokföra en Läs kvitto-händelse till en tråd, å en användares vägnar.

- Används `message_id` för att ange ID: t för det senaste meddelandet som lästs av den aktuella användaren.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

När en chatt-tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att kunna skicka meddelanden till chatt-tråden och lägga till/ta bort andra deltagare. Innan du anropar `add_participants` -metoden kontrollerar du att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

En eller flera användare kan läggas till i chatt-tråden med hjälp av `add_participants` metoden, förutsatt att en ny åtkomsttoken och identifiering är tillgänglig för alla användare.

A `list(tuple(ChatThreadParticipant, CommunicationError))` returneras. En tom lista förväntas när deltagaren har lagts till. Om ett fel uppstår när deltagare lades till fylls listan i med de misslyckade deltagarna tillsammans med det fel som påträffades.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Lista tråd deltagare i en chatt-tråd

På samma sätt som du lägger till en deltagare, kan du också Visa deltagare från en tråd.

Används `list_participants` för att hämta deltagarna i tråden.
- Använd `results_per_page` , valfritt, det maximala antalet deltagare som ska returneras per sida.
- Använd `skip` , valfritt, för att hoppa över deltagare upp till en angiven befattning i svar.

En iterator av `[ChatThreadParticipant]` är svaret som returneras från listan deltagare

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatThreadParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `python` kommandot.

```console
python start-chat.py
```
