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
ms.openlocfilehash: 9e83203e937d794451dfb91fe0403117df72c8c0
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489709"
---
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
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Installera klient bibliotek

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Servicess Chat-klient bibliotek för python.

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
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `create_chat_thread` metoden för att skapa en chatt-tråd.

- Använd `topic` för att ge ett tråd ämne. Ämnet kan uppdateras när chatt-tråden har skapats med hjälp av `update_thread` funktionen.
- Använd `thread_participants` för att visa en lista över `ChatThreadParticipant` som ska läggas till i chatten. `ChatThreadParticipant` tar med `CommunicationUserIdentifier` typ som det `user` du fick när du skapade genom att [skapa en användare](../../access-tokens.md#create-an-identity)
- Används `repeatability_request_id` för att dirigera om att begäran kan upprepas. Klienten kan göra begäran flera gånger med samma repeterbarhet-Request-ID och få tillbaka ett lämpligt svar utan att servern kör begäran flera gånger.

Svaret `chat_thread_client` används för att utföra åtgärder på den nya chatt-tråden som att lägga till deltagare i chatt-tråden, skicka meddelande, ta bort meddelande, osv. Den innehåller en `thread_id` egenskap som är det unika ID: t för chatt-tråden.

#### <a name="without-repeatability-request-id"></a>Utan repeterbarhet-begärande-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>Med repeterbarhet – begärande-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd
`get_chat_thread_client`Metoden returnerar en tråd klient för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till deltagare, skicka meddelande, osv. thread_id är det unika ID: t för den befintliga chatt tråden.

```python
thread_id = chat_thread_client.thread_id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>Lista alla chatt trådar
`list_chat_threads`Metoden returnerar en iterator av typen `ChatThreadInfo` . Den kan användas för att visa alla Chat-trådar.

- Används `start_time` för att ange den tidigaste tidpunkt som chatten ska få upp till.
- Används `results_per_page` för att ange det maximala antalet chatt trådar som returneras per sida.

```python
from datetime import datetime, timedelta
import pytz

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        # Iterate over all chat threads
        print("thread id:", chat_thread_info.id)
```

## <a name="delete-a-chat-thread"></a>Ta bort en chatt-tråd
`delete_chat_thread`Används för att ta bort en chatt-tråd.

- Används `thread_id` för att ange thread_id för en befintlig chatt-tråd som måste tas bort

```python
thread_id = chat_thread_client.thread_id
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Använd `send_message` metoden för att skicka ett meddelande till en chatt-tråd som du nyss skapade, som identifieras av thread_id.

- Används `content` för att tillhandahålla chatt-meddelandets innehåll.
- Används `chat_message_type` för att ange meddelandets innehålls typ. Möjliga värden är ' text ' och ' HTML '; om inte det angivna standardvärdet ' text ' har tilldelats.
- Används `sender_display_name` för att ange visnings namnet på avsändaren.

Svaret är ett "ID" av typen `str` , vilket är det unika ID: t för meddelandet.

#### <a name="message-type-not-specified"></a>Meddelande typ har inte angetts
```python
chat_thread_client = chat_client.create_chat_thread(topic, participants)

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>Meddelande typ har angetts
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Hämta ett enskilt chatt-meddelande från en chatt
`get_message`Funktionen kan användas för att hämta ett speciellt meddelande som identifieras av en message_id

- Används `message_id` för att ange meddelande-ID.

Svaret av typen `ChatMessage` innehåller all information som är relaterad till det enskilda meddelandet.

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Du kan hämta chatt meddelanden genom att avsöka `list_messages` metoden vid angivna intervall.

- Används `results_per_page` för att ange det maximala antalet meddelanden som ska returneras per sida.
- Används `start_time` för att ange den tidigaste tidpunkt som meddelanden ska hämtas till.

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print('ChatMessage: ', chat_message)
        print('ChatMessage: ', chat_message.content.message)
```

`list_messages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av `update_message` och `delete_message` . För borttagna meddelanden `ChatMessage.deleted_on` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `ChatMessage.edited_on` returnerar en datetime som anger när meddelandet redigerades. Det går att få åtkomst till den ursprungliga tiden för att skapa meddelanden med `ChatMessage.created_on` som kan användas för att ordna meddelandena.

`list_messages` returnerar olika typer av meddelanden som kan identifieras av `ChatMessage.type` . Dessa typer är:

- `ChatMessageType.TEXT`: Vanligt chatt-meddelande som skickas av en tråd deltagare.

- `ChatMessageType.HTML`: HTML chat-meddelande som skickats av en tråd deltagare.

- `ChatMessageType.TOPIC_UPDATED`: System meddelande som anger att ämnet har uppdaterats.

- `ChatMessageType.PARTICIPANT_ADDED`: System meddelande som anger att en eller flera deltagare har lagts till i chatt-tråden.

- `ChatMessageType.PARTICIPANT_REMOVED`: System meddelande som anger att en deltagare har tagits bort från chatt-tråden.

Mer information finns i [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="update-topic-of-a-chat-thread"></a>Uppdatera ämnet i en chatt-tråd
Du kan uppdatera ämnet i en chatt-tråd med hjälp av `update_topic` metoden

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)
updated_topic = chat_client.get_chat_thread(chat_thread_client.thread_id).topic
print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Uppdatera ett meddelande
Du kan uppdatera innehållet i ett befintligt meddelande med hjälp av `update_message` -metoden, som identifieras av message_id

- Använd `message_id` för att ange message_id
- Använd `content` för att ange det nya innehållet i meddelandet

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>Skicka Läs kvitto för ett meddelande
`send_read_receipt`Metoden kan användas för att bokföra en Läs kvitto-händelse till en tråd, å en användares vägnar.

- Används `message_id` för att ange ID: t för det senaste meddelandet som lästs av den aktuella användaren.

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Lista Läs kvitton för en chatt-tråd
`list_read_receipts`Metoden kan användas för att hämta Läs kvitton för en tråd.

- Används `results_per_page` för att ange det högsta antalet läsnings kvitton för chatt som ska returneras per sida.
- Används `skip` för att ange Skip chat-meddelande med Läs-och skriv åtgärder upp till en angiven befattning.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
```

## <a name="send-typing-notification"></a>Skicka meddelande vid inmatning
`send_typing_notification`Metoden kan användas för att skicka en händelse till en tråd för en användares räkning.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Ta bort meddelande
`delete_message`Metoden kan användas för att ta bort ett meddelande som identifieras av en message_id

- Använd `message_id` för att ange message_id

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

När en chatt-tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att kunna skicka meddelanden till chatt-tråden och lägga till/ta bort andra deltagare. Innan du anropar `add_participant` -metoden kontrollerar du att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

Använd `add_participant` metoden för att lägga till tråd deltagare i den tråd som identifieras av thread_id.

- Används `thread_participant` för att ange den deltagare som ska läggas till i chatten.
- `user`, krävs, är `CommunicationUserIdentifier` du skapade av `CommunicationIdentityClient` vid [skapa en användare](../../access-tokens.md#create-an-identity)
- `display_name`, valfritt är visnings namnet för tråd deltagaren.
- `share_history_time`, valfritt, är den tid från vilken chatt-historiken delas med deltagaren. Om du vill dela historiken på grund av att chatten är i gång, anger du den här egenskapen till ett datum som är lika med eller mindre än tiden för tråd skapande. Om du vill dela ingen Historik tidigare till när deltagaren lades in, ställer du in den på det aktuella datumet. Om du vill dela partiell historik anger du ett mellanliggande datum.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

Flera användare kan också läggas till i chatten med hjälp av `add_participants` metoden, förutsatt att en ny åtkomsttoken och identifiering är tillgänglig för alla användare.

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

På samma sätt som du lägger till en deltagare kan du också ta bort deltagare från en tråd. För att kunna ta bort måste du följa ID: na för de deltagare som du har lagt till.

Använd `remove_participant` metoden för att ta bort tråd deltagare från den tråd som identifieras av threadId.
- `user` är den `CommunicationUserIdentifier` som ska tas bort från tråden.

```python
chat_thread_client.remove_participant(new_user)
```

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `python` kommandot.

```console
python start-chat.py
```
