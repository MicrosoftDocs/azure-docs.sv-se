---
title: Använda Azure Service Bus köer med ruby
description: I den här självstudien får du lära dig hur du skapar ruby-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö.
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 16dda6fc4637f052514a0e78a0804bf4702ed20b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85336647"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Snabb start: så här använder du Service Bus köer med ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I den här självstudien får du lära dig hur du skapar ruby-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö. Exemplen skrivs i ruby och använder Azure-symbolen.

## <a name="prerequisites"></a>Förutsättningar
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [Azure Portal för att skapa en Service Bus Queue](service-bus-quickstart-portal.md) -artikel.
    1. Läs snabb **översikten** över Service Bus **köer**. 
    2. Skapa ett Service Bus- **namnområde**. 
    3. Hämta **anslutnings strängen**. 

        > [!NOTE]
        > Du skapar en **kö** i Service Bus namn området genom att använda ruby i den här självstudien. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Så skapar du en resurs
Med objektet **Azure:: ServiceBusService** kan du arbeta med köer. Använd-metoden för att skapa en kö `create_queue()` . I följande exempel skapas en kö eller så skrivs eventuella fel ut.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Du kan också skicka ett **Azure:: Service Bus:: Queue** -objekt med ytterligare alternativ, vilket gör att du kan åsidosätta standardinställningarna för kön, till exempel meddelande tid till Live eller maximal kös Tor lek. I följande exempel visas hur du ställer in maximal kös Tor lek till 5 GB och tid till 1 minut:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Så här skickar du meddelanden till en kö
Om du vill skicka ett meddelande till en Service Bus kö anropar programmet `send_queue_message()` metoden på **Azure:: ServiceBusService** -objektet. Meddelanden som skickas till (och tas emot från) Service Bus köer är **Azure:: Service Bus:: BrokeredMessage** -objekt och har en uppsättning standard egenskaper (till exempel `label` och `time_to_live` ), en ord lista som används för att lagra anpassade programspecifika egenskaper och en brödtext av godtycklig program data. Ett program kan ange meddelandets brödtext genom att skicka ett sträng värde som meddelande och alla obligatoriska standard egenskaper fylls med standardvärden.

Följande exempel visar hur du skickar ett test meddelande till kön med namnet `test-queue` med `send_queue_message()` :

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av `receive_queue_message()` metoden på objektet **Azure:: ServiceBusService** . Som standard läses och låses meddelanden utan att tas bort från kön. Du kan dock ta bort meddelanden från kön när de läses genom att ange `:peek_lock` alternativet till **falskt**.

Standard beteendet gör läsningen och borttagningen av en åtgärd i två steg, vilket även gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att anropa `delete_queue_message()` metoden och tillhandahålla meddelandet som ska tas bort som en parameter. `delete_queue_message()`Metoden kommer att markera meddelandet som förbrukat och ta bort det från kön.

Om `:peek_lock` parametern är inställd på **false**, läser och tar bort meddelandet blir den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande i händelse av ett fel. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat, när programmet startas om och börjar förbruka meddelanden igen, kommer det att ha missat meddelandet som förbrukades innan kraschen.

Följande exempel visar hur du tar emot och bearbetar meddelanden med hjälp av `receive_queue_message()` . Exemplet tar först emot och tar bort ett meddelande med hjälp av `:peek_lock` set till **false**, sedan får det ett nytt meddelande och tar sedan bort meddelandet med hjälp av `delete_queue_message()` :

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa- `unlock_queue_message()` metoden på **Azure:: ServiceBusService** -objektet. Anropet gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i kön och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar) låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan `delete_queue_message()` metoden anropas, skickas meddelandet vidare till programmet när det startas om. Den här processen anropas ofta *minst en gång*. det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av `message_id` meddelandets egenskap, som förblir konstant mellan leverans försök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-köer, kan du följa dessa länkar om du vill veta mer.

* Översikt över [köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).
* Besök [Azure SDK för ruby](https://github.com/Azure/azure-sdk-for-ruby) -lagringsplatsen på GitHub.

En jämförelse mellan Azure Service Bus köer som diskuteras i den här artikeln och Azure-köer som beskrivs i artikeln [så här använder du Queue Storage från ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) -artikeln, se [Azure-köer och Azure Service Bus köer – jämförelse och kontrast](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

