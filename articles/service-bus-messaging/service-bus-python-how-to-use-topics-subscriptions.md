---
title: Använd Azure Service Bus ämnen och prenumerationer med Python azure-servicebus package version 7.0.0
description: Den här artikeln visar hur du använder Python för att skicka meddelanden till ett ämne och ta emot meddelanden från prenumerationen.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 49e80e277c6df5372341293861d5bda0580f3e8c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537171"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Skicka meddelanden till ett Azure Service Bus ämne och ta emot meddelanden från prenumerationer till ämnet (Python)
Den här artikeln visar hur du använder Python för att skicka meddelanden Service Bus ett ämne och ta emot meddelanden från en prenumeration till ämnet. 

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du kan aktivera dina [Visual Studio- eller MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Följ stegen i [Snabbstart: Använd Azure Portal för att skapa ett Service Bus och prenumerationer på ämnet](service-bus-quickstart-topics-subscriptions-portal.md). Anteckna anslutningssträngen, ämnesnamnet och ett prenumerationsnamn. Du använder bara en prenumeration för den här snabbstarten. 
- Python 2.7 eller senare med paketet [Azure Python SDK][Azure Python-paket] installerat. Mer information finns i [installationsguiden för Python.](/azure/developer/python/azure-sdk-install)

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne

1. Lägg till följande importutdrag. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Lägg till följande konstanter. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Ersätt `<NAMESPACE CONNECTION STRING>` med anslutningssträngen för namnområdet.
    > - Ersätt `<TOPIC NAME>` med namnet på ämnet.
    > - Ersätt `<SUBSCRIPTION NAME>` med namnet på prenumerationen på ämnet. 
3. Lägg till en metod för att skicka ett enda meddelande.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Avsändaren är ett objekt som fungerar som en klient för det ämne som du skapade. Du skapar den senare och skickar den som ett argument till den här funktionen. 
4. Lägg till en metod för att skicka en lista med meddelanden.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Lägg till en metod för att skicka en batch med meddelanden.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Skapa en Service Bus klient och sedan ett ämnesavsändarobjekt för att skicka meddelanden.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Lägg till följande kod efter utskriftsuttrycket. Den här koden tar kontinuerligt emot nya meddelanden tills den inte tar emot några nya meddelanden under 5 ( `max_wait_time` ) sekunder. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Fullständig kod

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Kör appen
När du kör programmet bör du se följande utdata: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

I Azure Portal navigerar du till Service Bus namnområdet. På sidan **Översikt kontrollerar** du att antalet **inkommande** och **utgående** meddelanden är 16. Om du inte ser antalet uppdaterar du sidan efter att ha väntat i några minuter. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Antal inkommande och utgående meddelanden":::

Välj ämnet i det nedre fönstret för att se **Service Bus ämnessidan** för ditt ämne. På den här sidan bör du se tre inkommande och tre utgående meddelanden i **diagrammet** Meddelanden. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Inkommande och utgående meddelanden":::

Om du väljer en prenumeration på den här sidan kommer du till sidan **Service Bus Prenumeration.** Du kan se antal aktiva meddelanden, antal meddelanden med dead letter och mer på den här sidan. I det här exemplet har alla meddelanden tagits emot, så antalet aktiva meddelanden är noll. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Antal aktiva meddelanden":::

Om du kommenterar ut mottagningskoden visas antalet aktiva meddelanden som 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Antal aktiva meddelanden – ingen mottagning":::

## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel: 

- [Azure Service Bus klientbibliotek för Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - Mappen **sync_samples** innehåller exempel som visar hur du interagerar med Service Bus på ett synkront sätt. I den här snabbstarten använde du den här metoden. 
    - Mappen **async_samples** innehåller exempel som visar hur du interagerar med Service Bus på ett asynkront sätt. 
- [referensdokumentation för azure-servicebus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
