---
title: Guide för Azure Service Bus JMS 2,0-utvecklare
description: Så här använder du JMS (Java Message Service) 2,0 API för att kommunicera med Azure Service Bus
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726962"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Guide för Azure Service Bus JMS 2,0-utvecklare

Den här guiden innehåller detaljerad information som hjälper dig att kommunicera med Azure Service Bus med hjälp av Java Message Service (JMS) 2,0 API.

Som Java-utvecklare, om du är nybörjare på Azure Service Bus, kan du läsa mer i artiklarna nedan.

| Komma igång | Begrepp |
|----------------|-------|
| <ul> <li> [Vad är Azure Service Bus](service-bus-messaging-overview.md) </li> <li> [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus-Premium-nivå](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Programmerings modell för Java-JMS (Java Message Service)

API-programmerings modellen Java Message Service är som visas nedan – 

> [!NOTE]
>
>**Azure Service Bus Premium-nivån** stöder jms 1,1 och JMS 2,0.
> <br> <br>
> **Azure Service Bus-standard-** nivån stöder begränsade jms 1,1-funktioner. Mer information finns i den här [dokumentationen](service-bus-java-how-to-use-jms-api-amqp.md).
>

# <a name="jms-20-programming-model"></a>[Programmerings modell för JMS 2,0](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="Diagram som visar programmerings modellen JMS 2,0." border="false":::

# <a name="jms-11-programming-model"></a>[Programmerings modell för JMS 1,1](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="Diagram som visar programmerings modellen JMS 1,1." border="false":::

---

## <a name="jms---building-blocks"></a>JMS-Bygg stenar

Bygg blocken nedan är tillgängliga för kommunikation med JMS-programmet.

> [!NOTE]
> Guiden nedan har anpassats från [själv studie kursen om Oracle Java EE 6 för Java Message Service (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> Vi rekommenderar att du följer den här självstudien för att få en bättre förståelse för Java Message Service (JMS).
>

### <a name="connection-factory"></a>Anslutnings fabrik
Objektet anslutnings fabrik används av-klienten för att ansluta till JMS-providern. Anslutnings fabriken kapslar in en uppsättning anslutnings konfigurations parametrar som definieras av administratören.

Varje anslutnings fabrik är en instans av `ConnectionFactory` - `QueueConnectionFactory` eller- `TopicConnectionFactory` gränssnittet.

För att förenkla anslutningen till Azure Service Bus implementeras dessa gränssnitt via `ServiceBusJmsConnectionFactory` respektive `ServiceBusJmsQueueConnectionFactory` `ServiceBusJmsTopicConnectionFactory` . Anslutnings fabriken kan instansieras med parametrarna nedan – 
   * Anslutnings sträng – anslutnings strängen för namn området för Azure Service Bus Premium-nivån.
   * ServiceBusJmsConnectionFactorySettings egenskaps uppsättning som innehåller
      * connectionIdleTimeoutMS – timeout för inaktiv anslutning i millisekunder.
      * traceFrames – boolesk flagga för att samla in AMQP-spårnings ramar för fel sökning.
      * *andra konfigurations parametrar*

Fabriken kan skapas enligt nedan. Anslutnings strängen är en obligatorisk parameter, men de ytterligare egenskaperna är valfria.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> Java-program som utnyttjar JMS 2,0-API: et måste ansluta till Azure Service Bus enbart med anslutnings strängen. För närvarande stöds endast autentisering för JMS-klienter som använder anslutnings strängen.
>
> Autentisering med Azure Active Directory (AAD) stöds inte för närvarande.
>

### <a name="jms-destination"></a>JMS-mål

Ett mål är det objekt som en klient använder för att ange målet för de meddelanden som skapas och källan till de meddelanden som den förbrukar.

Destinationer mappar till entiteter i Azure Service Bus-köer (i punkt-till-punkt-scenarier) och ämnen (i pub-sub-scenarier).

### <a name="connections"></a>Anslutningar

En anslutning kapslar in en virtuell anslutning med en JMS-Provider. Med Azure Service Bus representerar detta en tillstånds känslig anslutning mellan programmet och Azure Service Bus över AMQP.

En anslutning skapas från anslutnings fabriken på det sätt som visas nedan.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Sessioner

En session är en enkel trådad kontext för att skapa och använda meddelanden. Det kan användas för att skapa meddelanden, meddelande producenter och konsumenter, men det innehåller också en transaktionell kontext för att tillåta gruppering av skicka och ta emot i en atomisk arbets enhet.

En session kan skapas från anslutningsobjektet på det sätt som visas nedan.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Lägen för sessioner

En session kan skapas med något av nedanstående lägen.

| Lägen för sessioner | Beteende |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | Sessionen bekräftar automatiskt en klients mottagande av ett meddelande, antingen när sessionen har lyckats returnerat från ett anrop till Receive eller när meddelande lyssnaren har anropat för att bearbeta meddelandet.|
|**Session.CLIENT_ACKNOWLEDGE** |Klienten bekräftar ett förbrukat meddelande genom att anropa meddelandets bekräftelse metod.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Detta bekräftelse läge instruerar sessionen att Lazy bekräfta leveransen av meddelanden.| 
|**Session.SESSION_TRANSACTED**|Det här värdet kan skickas som argumentet för metoden createSession (int sessionMode) på anslutningsobjektet för att ange att sessionen ska använda en lokal transaktion.| 

Om sessionens läge inte anges, kommer **session.AUTO_ACKNOWLEDGE** att plockas som standard.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext definieras som en del av JMS 2,0-specifikationen.
>

JMSContext kombinerar de funktioner som tillhandahålls av objektet anslutning och session. Den kan skapas från objektet anslutnings fabrik.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext-lägen

Precis som **session** -objektet kan JMSContext skapas med samma bekräftelse lägen som anges i [lägen för sessioner](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

När läget inte anges kommer **JMSContext.AUTO_ACKNOWLEDGE** att plockas som standard.

### <a name="jms-message-producers"></a>JMS meddelande producenter

En meddelande tillverkare är ett objekt som skapas med hjälp av en JMSContext eller en session och används för att skicka meddelanden till ett mål.

Det kan skapas antingen som ett fristående objekt enligt nedan – 

```java
JMSProducer producer = context.createProducer();
```

eller skapas vid körning när ett meddelande måste skickas.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS-meddelande konsumenter

En meddelande konsument är ett objekt som skapas av en JMSContext eller en session och används för att ta emot meddelanden som skickas till ett mål. Det kan skapas på det sätt som visas nedan –

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Synkron mottagning via receive ()-metoden

Meddelande konsumenten tillhandahåller ett synkront sätt att ta emot meddelanden från målet via- `receive()` metoden.

Om inga argument/timeout har angetts, eller om en tids gräns på 0 har angetts, så blockerar klienten i oändlighet, om inte meddelandet anländer eller anslutningen är bruten (beroende på vilket som är tidigare).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

När ett positivt argument som inte är noll anges, blockeras konsumenten tills tids gränsen upphör att gälla.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Asynkrona mottagningar med JMS meddelande lyssnare

En meddelande lyssnare är ett objekt som används för asynkron hantering av meddelanden på ett mål. Det implementerar `MessageListener` gränssnittet som innehåller `onMessage` metoden där den specifika affärs logiken måste vara Live.

Ett meddelande lyssnar objekt måste instansieras och registreras mot en speciell meddelande konsument som använder `setMessageListener` metoden.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Förbrukning från ämnen

[JMS meddelande konsumenter](#jms-message-consumers) skapas mot ett [mål](#jms-destination) som kan vara en kö eller ett ämne.

Konsumenter i köer är bara klients IDE objekt som är aktiva i kontexten för sessionen (och anslutningen) mellan klient programmet och Azure Service Bus.

Konsumenter på ämnen, har dock två delar – 
   * Ett **klient objekts objekt som ligger** i kontexten för sessionen (eller JMSContext) och,
   * En **prenumeration** som är en entitet på Azure Service Bus.

Prenumerationerna dokumenteras [här](java-message-service-20-entities.md#java-message-service-jms-subscriptions) och kan vara något av följande: 
   * Delade varaktiga prenumerationer
   * Delade icke-varaktiga prenumerationer
   * Delade varaktiga prenumerationer
   * Ej delade icke-varaktiga prenumerationer

### <a name="jms-queue-browsers"></a>JMS i kö

JMS-API: et tillhandahåller ett `QueueBrowser` objekt som gör det möjligt för programmet att söka i meddelanden i kön och Visa rubrik värden för varje meddelande.

En Queue Browser kan skapas med hjälp av JMSContext enligt nedan.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> JMS-API: et tillhandahåller inte ett API för att bläddra i ett ämne.
>
> Detta beror på att själva ämnet inte lagrar meddelandena. Så snart meddelandet skickas till ämnet vidarebefordras det till lämpliga prenumerationer.
>

### <a name="jms-message-selectors"></a>JMS meddelande väljare

Meddelande väljare kan användas genom att ta emot program för att filtrera meddelanden som tas emot. Med meddelande väljare avlastar det mottagande programmet arbetet med att filtrera meddelanden till JMS-providern (i det här fallet Azure Service Bus) i stället för att ta det ansvaret.

Väljare kan användas när du skapar någon av nedanstående konsumenter – 
   * Delad beständig prenumeration
   * Unshared beständig prenumeration
   * Delad icke-beständig prenumeration
   * Ej delad icke-beständig prenumeration
   * Köa webbläsare


## <a name="summary"></a>Sammanfattning

Den här utvecklings guiden visar hur Java-klientprogram som använder Java Message Service (JMS) kan ansluta till Azure Service Bus.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Service Bus och information om JMS-enheter (Java Message Service) finns i länkarna nedan – 
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java Message Service-entiteter](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [AMQP 1,0-stöd i Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 Developer ' s guide](service-bus-amqp-dotnet.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [API för Java Message Service (externt Oracle-dokument)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Lär dig hur du migrerar från ActiveMQ till Service Bus](migrate-jms-activemq-to-servicebus.md)
