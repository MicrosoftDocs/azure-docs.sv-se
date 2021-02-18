---
title: Azure Service Bus messaging – java Message Service-entiteter
description: Den här artikeln innehåller en översikt över Azure Service Bus meddelande enheter som är tillgängliga via Java Message Service API.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652589"
---
# <a name="java-message-service-jms-20-entities"></a>JMS-enheter (Java Message Service) 2,0

Klient program som ansluter till Azure Service Bus Premium och använder [Azure Service Bus JMS-biblioteket](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) kan använda nedanstående entiteter.

## <a name="queues"></a>Köer

Köer i JMS är semantiskt jämförbara med traditionella [Service Bus köer](service-bus-queues-topics-subscriptions.md#queues).

Om du vill skapa en kö använder du metoderna nedan i `JMSContext` klassen-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Ämnen

Ämnen i JMS är semantiskt jämförbara med traditionella [Service Bus ämnen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Om du vill skapa ett ämne använder du metoderna nedan i `JMSContext` klassen-

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Tillfälliga köer

Om ett klient program kräver en tillfällig entitet som finns för programmets livs längd kan den använda tillfälliga köer. Dessa entiteter används i [fråge svars](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) mönstret.

Om du vill skapa en tillfällig kö använder du metoderna nedan i `JMSContext` klassen-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tillfälliga ämnen

Precis som temporära köer finns det temporära ämnen för att aktivera publicera/prenumerera via en tillfällig entitet som finns för programmets livs längd.

Om du vill skapa ett tillfälligt ämne använder du metoderna nedan i `JMSContext` klassen-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JMS-prenumerationer (Java Message Service)

Även om dessa är semantiskt likartade [prenumerationer](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (som finns i ett ämne och aktiverar semantik för publicering/prenumeration), introducerar specifikationen Java Message Service begreppen för **delade**, icke **delade**, * * varaktiga och **icke-varaktiga** attribut för en viss prenumeration.

> [!NOTE]
> Prenumerationerna nedan är tillgängliga i Azure Service Bus Premium-nivån för klient program som ansluter till Azure Service Bus med hjälp av [Azure Service Bus JMS-biblioteket](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Det går bara att skapa varaktiga prenumerationer med hjälp av Azure Portal.
>

### <a name="shared-durable-subscriptions"></a>Delade varaktiga prenumerationer

En delad varaktig prenumeration används när alla meddelanden som publicerats på ett ämne tas emot och bearbetas av ett program, oavsett om programmet aktivt konsumeras från prenumerationen hela tiden.

Alla program som autentiseras att ta emot från Service Bus kan tas emot från den delade varaktiga prenumerationen.

Om du vill skapa en delad varaktig prenumeration kan du använda nedanstående metoder i `JMSContext` klassen-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Den delade varaktiga prenumerationen fortsätter att finnas om den inte tas bort med hjälp av- `unsubscribe` metoden i `JMSContext` klassen.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Delade varaktiga prenumerationer

Precis som en delad beständig prenumeration används en opublicerad beständig prenumeration när alla meddelanden som publicerats i ett ämne tas emot och bearbetas av ett program, oavsett om programmet aktivt konsumeras från prenumerationen.

Eftersom det här är en prenumeration som inte är delad, kan endast det program som skapade prenumerationen ta emot från den.

Om du vill skapa en ej delad beständig prenumeration kan du använda nedanstående metoder från `JMSContext` klass- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`Funktionen stöds för närvarande inte och ignoreras därför.
>

Den ej delade varaktiga prenumerationen fortsätter att finnas om den inte tas bort med hjälp av- `unsubscribe` metoden i `JMSContext` klassen.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Delade icke-varaktiga prenumerationer

En delad icke-beständig prenumeration används när flera klient program behöver ta emot och bearbeta meddelanden från en enda prenumeration, bara tills de aktivt konsumeras/tas emot från den.

Eftersom prenumerationen inte är beständig är den inte beständig. Meddelanden tas inte emot av den här prenumerationen när det inte finns några aktiva konsumenter på den.

Skapa en delad icke-beständig prenumeration genom att skapa en `JmsConsumer` som visas i nedanstående metoder från `JMSContext` klassen-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Den delade icke-varaktiga prenumerationen fortsätter att finnas tills det finns aktiva konsumenter som tar emot dem.

### <a name="unshared-non-durable-subscriptions"></a>Ej delade icke-varaktiga prenumerationer

En icke-delad icke-beständig prenumeration används när klient programmet måste ta emot och bearbeta meddelanden från en prenumeration, tills det aktivt tar emot dem. Det är bara en konsument som kan finnas i den här prenumerationen, det vill säga den klient som skapade prenumerationen.

Eftersom prenumerationen inte är beständig är den inte beständig. Meddelanden tas inte emot av den här prenumerationen när det inte finns någon aktiv konsument.

Skapa en icke-varaktig prenumeration genom att skapa en `JMSConsumer` som visas i nedanstående metoder från `JMSContext` klassen-

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`Funktionen stöds för närvarande inte och ignoreras därför.
>

Den ej delade icke-varaktiga prenumerationen fortsätter att finnas tills det finns en aktiv konsument som tar emot den.

### <a name="message-selectors"></a>Meddelande väljare

Precis som **filter och åtgärder** finns för vanliga Service Bus prenumerationer finns **meddelande väljare** för JMS-prenumerationer.

Meddelande väljare kan ställas in på var och en av JMS-prenumerationerna och finns som ett filter villkor för egenskaperna för meddelande rubriken. Endast meddelanden med rubrik egenskaper som matchar uttrycket för meddelande väljaren levereras. Värdet null eller en tom sträng anger att det inte finns någon meddelande väljare för JMS-prenumerationen/konsumenten.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Ytterligare begrepp för Java Message Service (JMS) 2,0-prenumerationer

### <a name="client-scoping"></a>Klient omfånget

Prenumerationer, enligt vad som anges i JMS (Java Message Service) 2,0-API, kan komma *att begränsas till specifika klient program/s* (identifierade med lämpligt `clientId` ).

När prenumerationen är begränsad **kan den endast nås** från klient program som har samma klient-ID. 

Alla försök att komma åt en prenumeration som är begränsad till ett specifik klient-ID (t. ex. clientId1) från ett program med ett annat klient-ID (säg clientId2) leder till att en annan prenumeration som omfattas av det andra klient-ID: t (clientId2) skapas.

> [!NOTE]
> Klient-ID kan vara null eller tomt, men måste matcha det klient-ID som angetts för JMS-klient programmet. Från Azure Service Bus perspektiv har ett null-klient-ID och ett tomt klient-ID samma beteende.
>
> Om klient-ID: t är null eller tomt är det endast tillgängligt för klient program vars klient-ID också är inställt på null eller tomt.
>

### <a name="shareability"></a>Kunna dela resurser

**Delade** prenumerationer tillåter flera klient/konsumenter (d.v.s. JMSConsumer-objekt) för att ta emot meddelanden från dem.

>[!NOTE]
> Delade prenumerationer som är begränsade till ett angivet klient-ID kan fortfarande användas av flera klient/konsumenter (t. ex. JMSConsumer-objekt), men varje klient program måste ha samma klient-ID.
>
 

**Delade** prenumerationer tillåter endast en enda klient/konsument (d.v.s. JMSConsumer-objekt) för att ta emot meddelanden från dem. Om en `JMSConsumer` har skapats på en prenumeration som inte är delad och den redan har en aktiv `JMSConsumer` avlyssning av meddelanden på den, `JMSException` genereras en.


### <a name="durability"></a>Varaktighet

**Varaktiga** prenumerationer är bestående och fortsätter att samla in meddelanden från ämnet, oavsett om ett program ( `JMSConsumer` ) tar emot meddelanden från det.

**Icke-varaktiga** prenumerationer är inte beständiga och samlar in meddelanden från ämnet så länge ett program ( `JMSConsumer` ) tar emot meddelanden från den. 

## <a name="representation-of-client-scoped-subscriptions"></a>Representation av klient omfångs prenumerationer

Med tanke på att JMS-prenumerationerna (Client omfånget) måste finnas tillsammans med de befintliga [prenumerationerna](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)visas följande format för klient omfånget (JMS).

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (för varaktiga prenumerationer)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**Nd** (för icke-varaktiga prenumerationer)

Här **$** är avgränsaren.

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus meddelanden finns i följande avancerade avsnitt:

* [Översikt över Service Bus meddelande hantering](service-bus-messaging-overview.md)
* [Använd API för Java Message Service 2,0 med Azure Service Bus Premium](how-to-use-java-message-service-20.md)



