---
title: 'Snabb start: använda Service Bus ämnen (ruby)'
description: 'Snabb start: Lär dig hur du använder Service Bus ämnen och prenumerationer i Azure. Kod exempel skrivs för ruby-program.'
services: service-bus-messaging
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: aba326a63558632bee3bf0c48d34e471bbe30886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067570"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Snabb start: använda Service Bus ämnen och prenumerationer med ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här artikeln beskriver hur du använder Service Bus ämnen och prenumerationer från ruby-program. Scenarierna som omfattas är:

- Skapa ämnen och prenumerationer 
- Skapa prenumerations filter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer


## <a name="prerequisites"></a>Förutsättningar
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa ett Service Bus- **namnområde** och hämta **anslutnings strängen**. 

    > [!NOTE]
    > Du kommer att skapa ett **ämne** och en **prenumeration** på avsnittet med hjälp av **ruby** i den här snabb starten. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Skapa ett ämne
Med objektet **Azure:: ServiceBusService** kan du arbeta med ämnen. Följande kod skapar ett **Azure:: ServiceBusService** -objekt. Använd-metoden för att skapa ett ämne `create_topic()` . I följande exempel skapas ett ämne eller så skrivs eventuella fel ut.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Du kan också skicka ett **Azure:: Service Bus:: topic** -objekt med ytterligare alternativ, vilket gör att du kan åsidosätta standard ämnes inställningar, till exempel meddelande tid till Live eller maximal kös Tor lek. I följande exempel visas hur du ställer in maximal kös Tor lek på 5 GB och tid till 1 minut:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnes prenumerationer skapas också med objektet **Azure:: ServiceBusService** . Prenumerationer namnges och kan ha ett valfritt filter som begränsar den uppsättning meddelanden som skickas till prenumerationens virtuella kö.

Som standard är prenumerationerna permanenta. De fortsätter att finnas tills de, eller ämnet de är associerade med, tas bort. Om ditt program innehåller logik för att skapa en prenumeration bör den först kontrol lera om prenumerationen redan finns med hjälp av metoden getSubscription.

Du kan ta bort prenumerationerna automatiskt genom att ange [egenskapen AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
Om inget filter anges när en ny prenumeration skapas, används **MatchAll** -filtret (standard). När **MatchAll** -filtret används placeras alla meddelanden som publiceras till ämnet i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet "alla meddelanden" och använder standard filtret **MatchAll** .

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan också definiera filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas i en speciell prenumeration.

Den mest flexibla typen av filter som stöds av prenumerationer är **Azure:: Service Bus:: SqlFilter**, som implementerar en delmängd av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om de uttryck som kan användas med ett SQL-filter finns i [SqlFilter](service-bus-messaging-sql-filter.md) -syntaxen.

Du kan lägga till filter i en prenumeration med hjälp av `create_rule()` metoden för objektet **Azure:: ServiceBusService** . Med den här metoden kan du lägga till nya filter till en befintlig prenumeration.

Eftersom standard filtret används automatiskt för alla nya prenumerationer måste du först ta bort standard filtret, annars åsidosätter **MatchAll** eventuella andra filter som du kan ange. Du kan ta bort standard regeln med hjälp av `delete_rule()` metoden på objektet **Azure:: ServiceBusService** .

I följande exempel skapas en prenumeration med namnet "High-Messages" med en **Azure:: Service Bus:: SqlFilter** som endast väljer meddelanden som har en anpassad egenskap som är `message_number` större än 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

På samma sätt skapar följande exempel en prenumeration med namnet `low-messages` med en **Azure:: Service Bus:: SqlFilter** som endast väljer meddelanden som har en `message_number` egenskap som är mindre än eller lika med 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

När ett meddelande nu skickas till `test-topic` levereras det alltid till mottagare som prenumererar på `all-messages` ämnes prenumerationen och är selektivt levererade till mottagare som prenumererar på `high-messages` och `low-messages` ämnes prenumerationer (beroende på meddelandets innehåll).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till ett Service Bus ämne måste programmet använda- `send_topic_message()` metoden på **Azure:: ServiceBusService** -objektet. Meddelanden som skickas till Service Bus ämnen är instanser av **Azure:: Service Bus:: BrokeredMessage** -objekten. **Azure:: Service Bus:: BrokeredMessage** -objekt har en uppsättning standard egenskaper (till exempel `label` och `time_to_live` ), en ord lista som används för att lagra anpassade programspecifika egenskaper och en text i sträng data. Ett program kan ange meddelandets brödtext genom att skicka ett sträng värde till- `send_topic_message()` metoden och alla obligatoriska standard egenskaper fylls med standardvärden.

Följande exempel visar hur du skickar fem test meddelanden till `test-topic` . Det `message_number` anpassade egenskap svärdet för varje meddelande varierar beroende på upprepningen (det avgör vilken prenumeration som tar emot det):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en prenumeration med hjälp av `receive_subscription_message()` metoden på objektet **Azure:: ServiceBusService** . Som standard läses meddelanden (högsta) och låses utan att tas bort från prenumerationen. Du kan läsa och ta bort meddelandet från prenumerationen genom att ange `peek_lock` alternativet till **falskt**.

Standard beteendet gör läsningen och borttagningen av en åtgärd i två steg, vilket även gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att anropa `delete_subscription_message()` metoden och tillhandahålla meddelandet som ska tas bort som en parameter. `delete_subscription_message()`Metoden markerar meddelandet som förbrukat och tar bort det från prenumerationen.

Om `:peek_lock` parametern är inställd på **false**, läser och tar bort meddelandet blir den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande när ett fel uppstår. Överväg ett scenario där klienten utfärdar Receive-begäran och sedan kraschar innan den bearbetas. Eftersom Service Bus har markerat meddelandet som förbrukat, när programmet startas om och börjar förbruka meddelanden igen, har det fått meddelandet som förbrukades innan kraschen.

Följande exempel visar hur meddelanden kan tas emot och bearbetas med hjälp av `receive_subscription_message()` . Exemplet tar först emot och tar bort ett meddelande från `low-messages` prenumerationen med hjälp av `:peek_lock` set till **false**. sedan får det ett nytt meddelande från `high-messages` och sedan tas meddelandet med `delete_subscription_message()` :

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa- `unlock_subscription_message()` metoden på **Azure:: ServiceBusService** -objektet. Det gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt att tas emot igen, antingen genom samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i prenumerationen. om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar) låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan `delete_subscription_message()` metoden anropas, skickas meddelandet vidare till programmet när det startas om. Det kallas ofta *minst en gång bearbetning*. det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Den här logiken uppnås ofta med hjälp av `message_id` meddelandets egenskap, som förblir konstant över leverans försök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ämnen och prenumerationer är permanenta om inte [AutoDeleteOnIdle-egenskapen](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) har angetts. De kan tas bort antingen via [Azure Portal][Azure portal] eller program mässigt. I följande exempel visas hur du tar bort ämnet med namnet `test-topic` .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar bort en prenumeration med namnet `high-messages` från `test-topic` avsnittet:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).
* API-referens för [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Besök [Azure SDK för ruby](https://github.com/Azure/azure-sdk-for-ruby) -lagringsplatsen på GitHub.

[Azure portal]: https://portal.azure.com