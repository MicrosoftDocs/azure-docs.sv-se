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
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386636"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar bör du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera [Python](https://www.python.org/downloads/)
- Skapa en Azure Communication Services resurs. Mer information finns i [Skapa en Azure-kommunikationsresurs.](../../create-communication-resource.md) Du måste registrera resursslutpunkten för **den här** snabbstarten
- En [token för användaråtkomst.](../../access-tokens.md) Se till att ange omfånget till "chat" och notera tokensträngen samt userId-strängen.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Öppna terminalen eller kommandofönstret och skapa en ny katalog för appen och navigera till den.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Använd en textredigerare för att skapa **en fil start-chat.py** i projektets rotkatalog och lägg till strukturen för programmet, inklusive grundläggande undantagshantering. Du lägger till all källkod för den här snabbstarten i den här filen i följande avsnitt.

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

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Chat SDK för Python.

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Den här klassen behövs för chattfunktionen. Du instansierar den med din prenumerationsinformation och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen behövs för chatttrådsfunktionen. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka skrivmeddelanden och läskvitton. |

## <a name="create-a-chat-client"></a>Skapa en chattklient

För att skapa en chattklient använder du kommunikationstjänstens slutpunkt och den som genererades som en `Access Token` del av de nödvändiga stegen. Läs mer om [token för användaråtkomst.](../../access-tokens.md)

Den här snabbstarten omfattar inte att skapa en tjänstnivå för att hantera token för ditt chattprogram, även om det rekommenderas. Mer information om chattarkitekturen finns [i följande dokumentation](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Starta en chatttråd

Använd metoden `create_chat_thread` för att skapa en chatttråd.

- Använd `topic` för att ge ett trådämne; Ämnet kan uppdateras när chatttråden har skapats med hjälp av `update_thread` funktionen .
- Använd för att lista som ska läggas till i `thread_participants` chatttråden, tar typen som , vilket är vad du fick när `ChatParticipant` du skapade av Skapa en `ChatParticipant` `CommunicationUserIdentifier` `user` [användare](../../access-tokens.md#create-an-identity)

`CreateChatThreadResult` är resultatet som returnerades när du skapade en tråd. Du kan använda den för att `id` hämta chatttråden som skapades. Detta `id` kan sedan användas för att hämta ett objekt med hjälp av metoden `ChatThreadClient` `get_chat_thread_client` . `ChatThreadClient` kan användas för att utföra andra chattåtgärder i den här chatttråden.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Hämta en chatttrådsklient
Metoden `get_chat_thread_client` returnerar en trådklient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: lägga till deltagare, skicka meddelande osv. thread_id är det unika ID:t för den befintliga chatttråden.

`ChatThreadClient` kan användas för att utföra andra chattåtgärder i den här chatttråden.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Lista alla chatttrådar
Metoden `list_chat_threads` returnerar en iterator av typen `ChatThreadItem` . Den kan användas för att visa en lista över alla chatttrådar.

- Använd `start_time` för att ange den tidigaste tidpunkten för att hämta upp chatttrådar.
- Använd `results_per_page` för att ange det maximala antalet chatttrådar som returneras per sida.

En iterator av `[ChatThreadItem]` är svaret som returneras från att lista trådar

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatttråd

Använd `send_message` metoden för att skicka ett meddelande till en chatttråd som du precis har skapat och som thread_id.

- Använd `content` för att tillhandahålla innehållet i chattmeddelandet.
- Använd `chat_message_type` för att ange meddelandeinnehållstyp. Möjliga värden är "text" och "html". om inget standardvärde för "text" tilldelas.
- Använd `sender_display_name` för att ange avsändarens visningsnamn.

`SendChatMessageResult` är svaret som returneras när ett meddelande skickas. Det innehåller ett ID, som är det unika ID:t för meddelandet.

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


## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot chattmeddelanden från en chatttråd

Du kan hämta chattmeddelanden genom att avssöka `list_messages` metoden vid angivna intervall.

- Använd `results_per_page` för att ange det maximala antalet meddelanden som ska returneras per sida.
- Använd `start_time` för att ange den tidigaste tidpunkten för att hämta meddelanden fram till.

En iterator av `[ChatMessage]` är svaret som returneras från listan med meddelanden

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som har inträffat i meddelandet med `update_message` hjälp av och `delete_message` . För borttagna meddelanden `ChatMessage.deleted_on` returneras ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `ChatMessage.edited_on` returnerar ett datetime-värde som anger när meddelandet redigerades. Den ursprungliga tiden då meddelandet skapades kan nås med `ChatMessage.created_on` hjälp av vilken kan användas för att ordna meddelanden.

`list_messages` returnerar olika typer av meddelanden som kan identifieras av `ChatMessage.type` . 

Läs mer om meddelandetyper här: [Meddelandetyper](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Skicka läskvitton
Metoden `send_read_receipt` kan användas för att posta en händelse med läskvitton till en tråd för en användares räkning.

- Använd `message_id` för att ange ID för det senaste meddelandet som lästs av den aktuella användaren.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Lägga till en användare som deltagare i chatttråden

När en chatttråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst för att kunna skicka meddelanden till chatttråden och lägga till/ta bort andra deltagare. Innan du `add_participants` anropar metoden måste du se till att du har skaffat en ny åtkomsttoken och identitet för den användaren. Användaren behöver denna åtkomsttoken för att kunna initiera chattklienten.

En eller flera användare kan läggas till i chatttråden med hjälp av metoden , förutsatt att ny `add_participants` åtkomsttoken och identifiering är tillgänglig för alla användare.

En `list(tuple(ChatParticipant, CommunicationError))` returneras. När deltagaren har lagts till förväntas en tom lista. Om ett fel uppstår när deltagare läggs till fylls listan i med de misslyckade deltagarna tillsammans med det fel som påträffades.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
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
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
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


## <a name="list-thread-participants-in-a-chat-thread"></a>Lista tråddeltagare i en chatttråd

På samma sätt som du lägger till en deltagare kan du även visa deltagare från en tråd.

Använd `list_participants` för att hämta deltagarna i tråden.
- Använd `results_per_page` , valfritt, Det maximala antalet deltagare som ska returneras per sida.
- Använd `skip` , valfritt, för att hoppa över deltagare upp till en angiven position som svar.

En iterator av `[ChatParticipant]` är svaret som returneras från listdeltagarna

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Kör koden

Kör programmet från programkatalogen med `python` kommandot .

```console
python start-chat.py
```
