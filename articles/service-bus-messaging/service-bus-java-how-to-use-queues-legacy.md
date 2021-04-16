---
title: Använda Azure Service Bus köer med Java
description: I den här självstudien lär du dig att skapa Java-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö.
ms.date: 06/23/2020
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 38657f333fea4ca18fb76eb5832649067bfd01b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538251"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Snabbstart: Använda Azure Service Bus med Java för att skicka och ta emot meddelanden

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
I den här självstudien lär du dig att skapa Java-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö. 

> [!WARNING]
>  Den här snabbstarten använder de gamla `azure-servicebus` paketen. En snabbstart som använder det senaste paketet `azure-messaging-servicebus` finns i Skicka och ta emot meddelanden [ `azure-messaging-servicebus` med ](service-bus-java-how-to-use-queues.md). 


## <a name="prerequisites"></a>Förutsättningar
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Om du inte har någon kö att arbeta med följer du stegen i artikeln Använda Azure Portal för att skapa en [Service Bus för](service-bus-quickstart-portal.md) att skapa en kö.
    1. Läs **snabböversikten** över Service Bus **köer**. 
    2. Skapa en Service Bus **namnområdet**. 
    3. Hämta **anslutningssträngen**.
    4. Skapa en Service Bus **kö**.
3. Installera [Azure SDK för Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Kontrollera att du har installerat [Azure SDK för Java innan][Azure SDK for Java] du skapar det här exemplet. 

Om du använder Eclipse kan du installera Azure Toolkit for Eclipse [som][Azure Toolkit for Eclipse] innehåller Azure SDK för Java. Du kan sedan lägga till **Microsoft Azure-bibliotek för Java** i projektet. Om du använder IntelliJ kan du [se Installera Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Lägga Microsoft Azure-bibliotek för Java i ditt Eclipse-projekt](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Lägg till följande `import` -instruktioner överst i Java-filen:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Om du vill skicka meddelanden till Service Bus Queue instansierar programmet ett **QueueClient-objekt** och skickar meddelanden asynkront. Följande kod visar hur du skickar ett meddelande för en kö som har skapats via portalen.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Meddelanden som skickas till och tas emot Service Bus köer är instanser av [klassen](/java/api/com.microsoft.azure.servicebus.message) Message. Meddelandeobjekt har en uppsättning standardegenskaper (till exempel Label och TimeToLive), en ordlista som används för att lagra anpassade programspecifika egenskaper och en brödtext med godtyckliga programdata. Ett program kan ange meddelandetexten genom att skicka alla serialiserbara objekt till meddelandekonstruktorn, och lämplig serialiserare används sedan för att serialisera objektet. Du kan också ange en **java. Io. InputStream-objekt.**


Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Det primära sättet att ta emot meddelanden från en kö är att använda ett **ServiceBusContract-objekt.** Mottagna meddelanden kan fungera i två olika lägen: **ReceiveAndDelete** och **PeekLock**.

När du använder **läget ReceiveAndDelete** är mottagning en engångsåtgärd– det vill säga när Service Bus tar emot en läsbegäran för ett meddelande i en kö, markerar den meddelandet som förbrukat och returnerar det till programmet. **ReceiveAndDelete-läge** (som är standardläget) är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande i händelse av ett fel. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran.
Eftersom Service Bus har markerat meddelandet som förbrukat har det missat meddelandet som förbrukades innan kraschen när programmet startas om och börjar använda meddelanden igen.

I **PeekLock-läget** blir mottagning en åtgärd i två steg, vilket gör det möjligt att stödja program som inte tolererar att meddelanden saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning) slutför det det andra steget i mottagningsprocessen genom att anropa **complete()** på det mottagna meddelandet. När Service Bus ser **complete()-anropet** markerar det meddelandet som förbrukat och tar bort det från kön. 

I följande exempel visas hur meddelanden kan tas emot och bearbetas med **PeekLock-läge** (inte standardläget). I exemplet nedan används återanropsmodellen med en registrerad meddelandehanterare och bearbetar meddelanden när de tas emot i vår `TestQueue` . Det här läget **anropar complete()** automatiskt när återanropet returneras normalt och anropar **abandon()** om återanropet returnerar ett undantag. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram av någon anledning inte kan bearbeta meddelandet kan det anropa **metoden abandon()** på klientobjektet med det mottagna meddelandets låstoken som hämtas via **getLockToken()**. Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns kopplad till ett meddelande som är låst i kön, och om programmet inte kan bearbeta meddelandet innan tidsgränsen för låsning upphör att gälla (till exempel om programmet kraschar) låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

Om programmet kraschar efter att meddelandet har bearbetats men innan **slutförande()-begäran** utfärdas skickas meddelandet på nytt till programmet när det startas om. Det här kallas ofta för *At Least Once Processing*, det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av **metoden getMessageId** i meddelandet, som är konstant under leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan användarna ansluta till en Service Bus och administrera meddelandeentiteter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämnen, köer, prenumerationer, vidarebefordrande tjänster, meddelandehubbbar och händelsehubbbar. 

## <a name="next-steps"></a>Nästa steg
Du hittar Java-exempel på GitHub på [ `azure-service-bus` lagringsplatsen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
