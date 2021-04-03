---
title: Azure Event Hubs-Exchange-händelser med olika protokoll
description: Den här artikeln visar hur konsumenter och producenter som använder olika protokoll (AMQP, Apache Kafka och HTTPS) kan utbyta händelser när de använder Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010328"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Exchange-händelser mellan konsumenter och producenter som använder olika protokoll: AMQP, Kafka och HTTPS
Azure Event Hubs stöder tre protokoll för konsumenter och producenter: AMQP, Kafka och HTTPS. Var och en av dessa protokoll har sitt eget sätt att representera ett meddelande, så naturligt är följande fråga: om ett program skickar händelser till en Event Hub med ett protokoll och förbrukar dem med ett annat protokoll, vad ser de olika delarna och värdena i händelsen ut när de kommer till konsumenten? Den här artikeln beskriver metod tips för både tillverkare och konsument för att säkerställa att värdena i en händelse tolkas korrekt av det krävande programmet.

Råd i den här artikeln beskriver särskilt dessa klienter, med de listade versioner som används för att utveckla kodfragmenten:

* Kafka Java-klient (version 1.1.1 från https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs-klienten för Java (version 1.1.0 från https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs-klienten för .NET (version 2.1.0 från https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (version 5.0.0 från https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (endast stöd för producenter)

Andra AMQP-klienter kan bete sig något annorlunda. AMQP har ett väldefinierat typ system, men de specifika funktionerna för att serialisera språkspecifika typer till och från det typen system är beroende av klienten, precis som i hur klienten ger åtkomst till delarna i ett AMQP-meddelande.

## <a name="event-body"></a>Händelse text
Alla Microsoft AMQP-klienter representerar händelse texten som en avtolkad uppsättning byte. Ett program som skapar ett program skickar en sekvens med byte till klienten, och ett program som tar emot samma sekvens från klienten. Tolkningen av byte-sekvenser sker i program koden.

När du skickar en händelse via HTTPS är händelse texten det publicerade innehållet, som också behandlas som tolkade byte. Det är enkelt att uppnå samma tillstånd i en Kafka tillverkare eller konsument genom att använda de tillhandahållna ByteArraySerializer och ByteArrayDeserializer som visas i följande kod:

### <a name="kafka-byte-producer"></a>Kafka byte [] producent

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte [] konsument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Den här koden skapar en transparent byte-pipeline mellan de två hälften av programmet och gör det möjligt för programutvecklaren att manuellt serialisera och deserialisera på valfritt sätt, inklusive att fatta beslut om deserialisering vid körning, till exempel baserat på typ eller avsändar information i egenskaperna för användar uppsättning i händelsen.

Program som har en enskild, fast händelse typ kan använda andra Kafka-serialiserare och deserialiserare för transparent konvertering av data. Överväg till exempel ett program som använder JSON. Konstruktion och tolkning av JSON-strängen sker på program nivå. På Event Hubs nivå är händelse texten alltid en sträng, en sekvens med byte som representerar tecken i UTF-8-kodningen. I det här fallet kan Kafka-producenten eller-konsumenten dra nytta av den tillhandahållna StringSerializer eller StringDeserializer som visas i följande kod:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8-sträng tillverkare
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8-sträng konsument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

För AMQP-sidan tillhandahåller både Java och .NET inbyggda sätt att konvertera strängar till och från UTF-8-sekvenser. Microsoft AMQP-klienter representerar händelser som en klass med namnet EventData. I följande exempel visas hur du serialiserar en UTF-8-sträng till en EventData Event-text i en AMQP-tillverkare och hur du deserialiserar en EventData i en UTF-8-sträng i en AMQP-konsument.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8-sträng tillverkare
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java-AMQP UTF-8-sträng konsument
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8-sträng tillverkare
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8-sträng konsument
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Eftersom Kafka är öppen källkod kan programutvecklaren inspektera implementeringen av en serialiserare eller deserialiserare och implementera kod, vilket skapar eller förbrukar en kompatibel sekvens av byte på AMQP-sidan.

## <a name="event-user-properties"></a>Egenskaper för händelse användare

Egenskaper för användar uppsättning kan ställas in och hämtas från både AMQP-klienter (i de Microsoft AMQP-klienter som de heter egenskaper) och Kafka (där de kallas rubriker). HTTPS-avsändare kan ange användar egenskaper för en händelse genom att tillhandahålla dem som HTTP-huvuden i POST-åtgärden. Kafka behandlar dock både händelse-och händelse rubrik värden som byte sekvenser. I AMQP-klienter har egenskaps värden typer, som kommunicerar genom att koda egenskaps värden enligt typ systemet AMQP.

HTTPS är ett specialfall. Vid sändnings tillfället är alla egenskaps värden UTF-8-text. Event Hubs tjänsten gör en begränsad tolkning för att konvertera lämpliga egenskaps värden till AMQP 32-bitars och 64-bitars signerade heltal, 64-bitars flyttal och booleska värden. Alla egenskaps värden, som inte passar någon av dessa typer, behandlas som en sträng.

Genom att blanda dessa metoder för att ange egenskaper innebär det att en Kafka-konsument ser rå AMQP byte-sekvens, inklusive AMQP typ information. En AMQP-konsument ser den intyped byte-sekvens som skickas av Kafka-producenten, som programmet måste tolka.

För Kafka-konsumenter som tar emot egenskaper från AMQP-eller HTTPS-tillverkare använder du klassen AmqpDeserializer, som modelleras efter de andra deserialiserarna i Kafka-eko systemet. Den tolkar typ informationen i AMQP byte-sekvenser för att deserialisera data byte till en Java-typ.

Som bästa praxis rekommenderar vi att du inkluderar en egenskap i meddelanden som skickas via AMQP eller HTTPS. Kafka-konsumenten kan använda den för att avgöra om huvud värden kräver AMQP-deserialisering. Egenskapens värde är inte viktigt. Det behöver bara ett välkänt namn som Kafka-konsumenten kan hitta i listan över rubriker och anpassa dess beteende.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP till Kafka del 1: skapa och skicka en händelse i C# (.NET) med egenskaper
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP till Kafka part 2: Använd AmqpDeserializer för att deserialisera egenskaperna i en Kafka-konsument
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Om programmet känner till den förväntade typen för en egenskap, finns det metoder för deserialisering som inte kräver en omvandling efteråt, men de genererar ett fel om egenskapen inte är av den förväntade typen.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP till Kafka del 3: ett annat sätt att använda AmqpDeserializer i en Kafka-konsument
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Den andra riktningen är mer engagerad, eftersom huvuden som anges av en Kafka-tillverkare alltid ses av en AMQP-konsument som RAW-byte (typ org. apache. qpid. Proton. AMQP. Binary för Microsoft Azure Event Hubs-klienten för Java eller `System.Byte[]` för Microsoft .net AMQP-klienter). Den enklaste sökvägen är att använda en av de Kafka serialiserarna för att generera byte för huvud värden på Kafka-producent sidan och sedan skriva en kompatibel deserialiserings kod på AMQP-konsument sidan.

Precis som med AMQP-to-Kafka rekommenderar vi att du lägger till en egenskap i meddelanden som skickas via Kafka. AMQP-konsumenten kan använda egenskapen för att avgöra om huvud värden behöver deserialiseras. Egenskapens värde är inte viktigt. Det behöver bara ett välkänt namn som AMQP-konsumenten kan hitta i listan över rubriker och anpassa dess beteende. Om Kafka-producenten inte kan ändras är det också möjligt att använda programmet för att kontrol lera om egenskap svärdet är av typen Binary eller byte och försök att deserialisera baserat på typen.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka till AMQP del 1: skapa och skicka en händelse från Kafka med egenskaper
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka till AMQP part 2: deserialisera dessa egenskaper manuellt i C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka till AMQP del 3: deserialisera egenskaperna i Java manuellt
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du strömmar till Event Hubs utan att ändra protokoll klienter eller köra egna kluster. Mer information om Event Hubs och Event Hubs för Kafka finns i följande artiklar:  

* [Läs om Event Hubs](./event-hubs-about.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Utforska fler exempel på Event Hubs för Kafka GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att [strömma händelser från Kafka lokalt till Event Hubs i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
* Lär dig att strömma till Event Hubs med hjälp av [inbyggda Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md)eller [Akka strömmar](event-hubs-kafka-akka-streams-tutorial.md)
