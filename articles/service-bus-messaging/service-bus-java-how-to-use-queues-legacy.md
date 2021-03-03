---
title: Använda Azure Service Bus köer med Java
description: I den här självstudien får du lära dig hur du skapar Java-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö.
ms.devlang: Java
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 1f7c5f71e75a1b683433531a9d95905e9a7d81cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729733"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Snabb start: använda Azure Service Bus köer med Java för att skicka och ta emot meddelanden

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
I den här självstudien får du lära dig hur du skapar Java-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö. 

> [!WARNING]
>  I den här snabb starten används de gamla `azure-servicebus` paketen. En snabb start som använder det senaste `azure-messaging-servicebus` paketet finns i [skicka och ta emot meddelanden `azure-messaging-servicebus` med hjälp av ](service-bus-java-how-to-use-queues.md). 


## <a name="prerequisites"></a>Förutsättningar
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Om du inte har en kö att arbeta med följer du stegen i artikeln [använd Azure Portal för att Service Bus skapa](service-bus-quickstart-portal.md) en kö.
    1. Läs snabb **översikten** över Service Bus **köer**. 
    2. Skapa ett Service Bus- **namnområde**. 
    3. Hämta **anslutnings strängen**.
    4. Skapa en Service Bus **kö**.
3. Installera [Azure SDK för Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Kontrol lera att du har installerat [Azure SDK för Java][Azure SDK for Java] innan du skapar det här exemplet. 

Om du använder Sol förmörkelse kan du installera [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] som innehåller Azure SDK för Java. Du kan sedan lägga till **Microsoft Azure biblioteken för Java** i projektet. Om du använder IntelliJ, se [installera Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Lägg till Microsoft Azure bibliotek för java i ditt Sol förmörkelse-projekt](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Lägg till följande- `import` instruktioner överst i Java-filen:

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
För att skicka meddelanden till en Service Bus kö, instansierar ditt program ett **QueueClient** -objekt och skickar meddelanden asynkront. Följande kod visar hur du skickar ett meddelande för en kö som skapats via portalen.

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

Meddelanden som skickas till och tas emot från Service Bus köer är instanser av [meddelande](/java/api/com.microsoft.azure.servicebus.message) klassen. Meddelande objekt har en uppsättning standard egenskaper (t. ex. etikett och TimeToLive), en ord lista som används för att lagra anpassade programspecifika egenskaper och en brödtext av godtycklig program data. Ett program kan ange meddelandets brödtext genom att skicka ett serialiserbar objekt till meddelandets konstruktor, och lämplig serialiserare kommer sedan att användas för att serialisera objektet. Du kan också ange ett **Java-. I/O. InputStream** -objekt.


Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Det primära sättet att ta emot meddelanden från en kö är att använda ett **ServiceBusContract** -objekt. Mottagna meddelanden kan arbeta i två olika lägen: **ReceiveAndDelete** och **PeekLock**.

När du använder **ReceiveAndDelete** -läget är ta emot en enda åtgärd – det vill säga när Service Bus tar emot en läsbegäran för ett meddelande i en kö, markerar det meddelandet som förbrukat och returnerar det till programmet. **ReceiveAndDelete** -läget (som är standard läget) är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande i händelse av ett fel. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran.
Eftersom Service Bus har markerat meddelandet som förbrukat, när programmet startas om och börjar förbruka meddelanden igen, har det fått meddelandet som förbrukades innan kraschen.

I **PeekLock** -läge blir mottagningen en åtgärd i två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att anropa **Complete ()** för det mottagna meddelandet. När Service Bus ser det **fullständiga anropet ()** , markeras meddelandet som förbrukat och tas bort från kön. 

Följande exempel visar hur meddelanden kan tas emot och bearbetas med **PeekLock** -läge (inte standard läget). Exemplet nedan använder callback-modellen med en registrerad meddelande hanterare och bearbetar meddelanden när de anländer till vår `TestQueue` . Det här läget anropar **Complete ()** automatiskt när återanropet returnerar normalt och anropar **överge ()** om motringningen genererar ett undantag. 

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
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa metoden **Abandon ()** på klient objekt med det mottagna meddelandets lås-token som erhållits via **getLockToken ()**. Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i kön och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar) låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan **hela begäran ()** har utfärdats skickas meddelandet vidare till programmet när det startas om. Det här kallas ofta för *At Least Once Processing*, det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av **getMessageId** -metoden för meddelandet, som förblir konstant över leverans försök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Du kan hitta Java-exempel på GitHub i [ `azure-service-bus` lagrings platsen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
