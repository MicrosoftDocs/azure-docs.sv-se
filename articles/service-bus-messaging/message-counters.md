---
title: Azure Service Bus – antal meddelanden
description: Hämta antalet meddelanden som finns i köer och prenumerationer med hjälp av Azure Resource Manager och Azure Service Bus NamespaceManager-API:er.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814621"
---
# <a name="get-message-counters"></a>Hämta meddelanderäknare
Den här artikeln visar olika sätt att få följande meddelandeantal för en kö eller prenumeration. Att känna till det aktiva meddelandeantalet är användbart för att avgöra om en kö skapar en eftersläpning som kräver mer resurser för att bearbeta än vad som har distribuerats för närvarande. 

| Räknare | Description |
| ----- | ---------- | 
| ActiveMessageCount | Antal meddelanden i kön eller prenumerationen som är i aktivt tillstånd och redo för leverans. |
| ScheduledMessageCount | Antal meddelanden i schemalagt tillstånd. |
| DeadLetterMessageCount | Antal meddelanden i kön för döda meddelanden. |
| TransferMessageCount | Antal meddelanden som väntar på överföring till en annan kö eller ett annat ämne. |
| TransferDeadLetterMessageCount | Antal meddelanden som inte kunde överföras till en annan kö eller ett annat ämne och som har flyttats till kön för överföring av dead-letter. |

Om ett program vill skala resurser baserat på längden på kön bör det göras med en uppmätt takt. Att förvärva meddelanderäknarna är en dyr åtgärd i meddelandeutjämningen, och att köra den ofta direkt och negativt påverkar entitetens prestanda.

> [!NOTE]
> Meddelanden som skickas till ett Service Bus ämne vidarebefordras till prenumerationer för det ämnet. Det aktiva antalet meddelanden i själva ämnet är alltså 0, eftersom dessa meddelanden har vidarebefordrats till prenumerationen. Hämta antalet meddelanden i prenumerationen och kontrollera att det är större än 0. Även om du ser meddelanden i prenumerationen lagras de i en lagring som ägs av ämnet. Om du tittar på prenumerationerna skulle de ha ett meddelandeantal som inte är noll (som ger upp till 323 MB utrymme för hela entiteten).


## <a name="using-azure-portal"></a>Använda Azure Portal
Gå till namnområdet och välj kön. Du ser meddelanderäknare på **översiktssidan** för kön.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Meddelanderäknare på översiktssidan för kön":::

Navigera till ditt namnområde, välj ämnet och välj sedan prenumerationen för ämnet. Du ser meddelanderäknare på **översiktssidan** för kön.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Meddelanderäknare på prenumerationens översiktssida":::

## <a name="using-azure-cli"></a>Använda Azure CLI
Använd kommandot [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) för att hämta information om antalet meddelanden för en kö, som du ser i följande exempel. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Här är ett exempel på utdata:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Använd kommandot [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) för att hämta information om antalet meddelanden för en prenumeration, som du ser i följande exempel. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Med PowerShell kan du hämta information om antal meddelanden för en kö på följande sätt:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Här är exempel på utdata:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

du kan hämta information om antal meddelanden för en prenumeration på följande sätt:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

Det `MessageCountDetails` returnerade objektet har följande egenskaper: `ActiveMessageCount` , , , , `DeadLetterMessageCount` `ScheduledMessageCount` `TransferDeadLetterMessageCount` `TransferMessageCount` . 

## <a name="next-steps"></a>Nästa steg

Prova exemplen på det språk du väljer för att utforska Azure Service Bus funktioner. 

- [Azure Service Bus exempel på klientbibliotek för Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus exempel på klientbibliotek för Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus klientbiblioteksexempel för JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus exempel på klientbibliotek för TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Azure.Messaging.ServiceBus-exempel för .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Hitta exempel för äldre .NET- och Java-klientbibliotek nedan:
- [Microsoft.Azure.ServiceBus-exempel för .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-exempel för Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
