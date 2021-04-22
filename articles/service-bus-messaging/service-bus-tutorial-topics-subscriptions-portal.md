---
title: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer
description: I den här självstudien får du lära dig hur du skickar och tar emot meddelanden från ett ämne och en prenumeration och hur du lägger till och använder filterregler med .NET
author: spelluru
ms.author: spelluru
ms.date: 10/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: a7b03727c574dcee1cd56144a521f36de6dc48c6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861381"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Självstudie: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer

Azure Service Bus är en meddelandetjänst i molnet för flera klienter som skickar information mellan program och tjänst. Asynkrona åtgärder ger dig en flexibel, asynkron meddelandetjänst med funktioner för strukturerade meddelanden enligt FIFO-metoden (först-in-först-ut) och funktioner för publicering/prenumeration. Den här kursen visar hur du använder Service Bus-ämnen och prenumerationer i ett scenario med butikslager, med publicera/prenumerera kanaler med Azure-portalen och .NET.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure-portalen
> * Lägg till ämnesfilter med .NET-kod
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Ett exempel på det här scenariot är en uppdatering av lagersortimentet för flera butiker. I det här scenariot, får varje butik eller uppsättning butiker meddelanden för att uppdatera sina sortiment. Den här självstudien visar hur du implementerar det här scenariot med prenumerationer och filter. Först skapar du ett ämne med 3 prenumerationer, lägger till några regler och filter och skickar och tar sedan emot meddelanden från ämnet och prenumerationerna.

![ämne](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt [konto][] innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) eller senare.
- [NET Core SDK](https://dotnet.microsoft.com/download), version 2.0 eller senare.

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-ämnen och prenumerationer

Varje [prenumeration på ett ämne](service-bus-messaging-overview.md#topics) får en kopia av varje meddelande. Ämnen är fullständigt protokollmässigt och semantiskt kompatibla med Service Bus-köer. Service Bus-ämnen stöder en mängd olika urvalsregler med filtervillkor, med valfria åtgärder som anger eller ändrar meddelandeegenskaperna. Varje gång en regel matchar så genererar den ett meddelande. Mer information om regler, filter och åtgärder, finns på den här [länken](topic-filters.md).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Skapa filterregler för prenumerationer

När namnområdet och ämnet/sprenumerationerna har etablerats och du har de nödvändiga behörigheterna, är du redo att skapa filterregler för prenumerationerna och sedan skicka och ta emot meddelanden. Du kan granska koden i [den här GitHub-exempelmappen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Skicka och ta emot meddelanden

Om du vill köra koden gör du följande:

1. I en kommandotolk eller PowerShell-prompt, klonar du [Service Bus GitHub-lagringsplatsen](https://github.com/Azure/azure-service-bus/) genom att utfärda följande kommando:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Gå till exempelmappen `azure-service-bus\samples\DotNet\Azure.Messaging.ServiceBus\BasicSendReceiveTutorialwithFilters`.

3. Hämta anslutningssträngen som du kopierade till Anteckningar i avsnittet Hämta autentiseringsuppgifter för hantering i den här självstudien. Du behöver också namnet på det ämne som du skapade i föregående avsnitt.

4. Skriv följande kommando i kommandotolken:

   ```shell
   dotnet build
   ```

5. Gå till mappen `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp3.1`.

6. Skriv följande kommando för att köra programmet. Se till att ersätta `myConnectionString` med värdet som du tidigare fick och `myTopicName` med namnet på det ämne som du skapade:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Följ instruktionerna i konsolen för att välja filterskapande först. En del av att skapa filter är att ta bort standardfiltren. När du använder PowerShell eller CLI, behöver du inte ta bort standardfiltret, men om du gör detta i koden, måste du ta bort dem. Konsolkommandona 1 och 3 hjälper dig att hantera filter för de prenumerationer som du skapade tidigare:

   - Kör 1: för att ta bort standardfiltren.
   - Kör 2: för att lägga till dina egna filter.
   - Kör 3: för att alternativt ta bort dina egna filter. Obeservera att det här inte återskapar standardfiltren.

     ![Visar resultat av 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Efter att filtret har skapats, kan du skicka meddelanden. Tryck på 4 och notera 10 meddelanden som skickas till ämnet:

    ![Skicka utdata](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Tryck på 5 och se de meddelanden som tas emot. Om du inte fick tillbaka 10 meddelanden, trycker du på m för att visa menyn och tryck sedan på 5 igen.

    ![Ta emot utdata](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort namnområdet och ämnet när de inte längre behövs. Om du vill göra det, väljer du resurserna i portalen och klickar på **Ta bort**.

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Det här avsnittet innehåller mer information om vad exempelkoden gör.

### <a name="get-connection-string-and-topic"></a>Hämta anslutningssträngen och ämnet

Koden deklarerar först en uppsättning variabler som driver den återstående körningen av programmet.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Anslutningssträngen och ämnets namn skickas in via kommandoradsparametrar som det visas och därefter läses de i `Main()`-metoden:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Ta bort standardfiltren

När du skapar en prenumeration, skapar Service Bus ett standardfilter per prenumeration. Det här filtret låter dig ta emot alla meddelanden som skickas till ämnet. Om du vill använda anpassade filter kan du ta bort standardfiltret som visas i följande kod:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
        foreach (var subscription in Subscriptions)
        {
            await client.DeleteRuleAsync(TopicName, subscription, CreateRuleOptions.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Skapa filter

Följande kod lägger till anpassade filter som definierats i den självstudien:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Ta bort dina anpassade skapade filter

Om du vill ta bort alla filter för din prenumeration, visar följande kod hur du gör detta:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            IAsyncEnumerator<RuleProperties> rules = client.GetRulesAsync(TopicName, subscription).GetAsyncEnumerator();
            while (await rules.MoveNextAsync())
            {
                await client.DeleteRuleAsync(TopicName, subscription, rules.Current.Name);
                Console.WriteLine($"Rule {rules.Current.Name} has been removed.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Skicka meddelanden

Att skicka meddelanden till ett ämne liknar att skicka meddelanden till en kö. Det här exemplet visar hur du skickar meddelanden med hjälp av en uppgiftslista och asynkron bearbetning:

```csharp
public async Task SendMessages()
{
    try
    {
        await using var client = new ServiceBusClient(ServiceBusConnectionString);
        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(client, Store[i]));
        }

        await Task.WhenAll(taskList);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(ServiceBusClient client, string store)
{
    // create the sender
    ServiceBusSender tc = client.CreateSender(TopicName);

    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        ServiceBusMessage message = item.AsMessage();
        message.To = store;
        message.ApplicationProperties.Add("StoreId", store);
        message.ApplicationProperties.Add("Price", item.GetPrice().ToString());
        message.ApplicationProperties.Add("Color", item.GetColor());
        message.ApplicationProperties.Add("Category", item.GetItemCategory());

        await tc.SendMessageAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Ta emot meddelanden

Meddelanden tas emot igen via en uppgiftslista och koden använder batchbearbetning. Du kan skicka och ta emot med batchbearbetning, men det här exemplet visar bara hur du tar emot i batch. I verkligheten, skulle du inte avbryta loopen, utan fortsätta den och ställa in ett högre tidsspann, till exempel en minut. Mottagningsanropet till den asynkrona meddelandekön hålls öppen under denna tid och om meddelanden anländer så returneras de omedelbart och ett nytt mottagningsanrop utfärdas. Det här konceptet kallas *lång avsökning*. Att använda mottagningspumpen som du ser i [snabbstarten](service-bus-quickstart-portal.md) och i flera andra exempel på lagringsplatsen, är ett mer typiskt alternativ.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    await using var client = new ServiceBusClient(ServiceBusConnectionString);
    ServiceBusReceiver receiver = client.CreateReceiver(TopicName, subscription);

    // In reality you would not break out of the loop like in this example but would keep looping. The receiver keeps the connection open
    // to the broker for the specified amount of seconds and the broker returns messages as soon as they arrive. The client then initiates
    // a new connection. So in reality you would not want to break out of the loop. 
    // Also note that the code shows how to batch receive, which you would do for performance reasons. For convenience you can also always
    // use the regular receive pump which we show in our Quick Start and in other github samples.
    while (true)
    {
        try
        {
            //IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            // Note the extension class which is serializing an deserializing messages and testing messages is null or 0.
            // If you think you did not receive all messages, just press M and receive again via the menu.
            IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(maxMessages: 100);

            if (messages.Any())
            {
                foreach (ServiceBusReceivedMessage message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IReadOnlyDictionary<string, object> myApplicationProperties = message.ApplicationProperties;
                        Console.WriteLine($"StoreId={myApplicationProperties["StoreId"]}");
                        if (message.Subject != null)
                        {
                            Console.WriteLine($"Subject={message.Subject}");
                        }
                        Console.WriteLine(
                            $"Item data: Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}");
                    }

                    await receiver.CompleteMessageAsync(message);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
}
```

> [!NOTE]
> Du kan hantera Service Bus resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan användarna ansluta till en Service Bus och administrera meddelandeentiteter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämnen, köer, prenumerationer, vidarebefordrande tjänster, meddelandehubbbar och händelsehubbbar. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien, etablerade du resurser med hjälp av Azure-portalen och därefter skickade du och tog emot meddelanden från ett Service Bus-ämne och dess prenumerationer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure-portalen
> * Lägg till ämnesfilter med .NET-kod
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Om du vill ha fler exempel på att skicka och ta emot meddelanden, kan du komma igång med [Service Bus-exempel på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Gå vidare till nästa självstudie för att läsa mer om att använda Service Bus-funktionerna publicera/prenumerera.

> [!div class="nextstepaction"]
> [Svara på händelser via Event Grid](service-bus-to-event-grid-integration-example.md)

[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
