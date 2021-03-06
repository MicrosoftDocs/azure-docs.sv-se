---
title: Aktivera automatisk vidarebefordran för Azure Service Bus köer och prenumerationer
description: Den här artikeln förklarar hur du aktiverar automatisk vidarebefordran för köer och prenumerationer med hjälp av Azure Portal, PowerShell, CLI och programmeringsspråk (C#, Java, Python och JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: e5f69a82aac96deaf96f0ae6aaa1a26d0ee3aaf3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814693"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Aktivera automatisk vidarebefordran för Azure Service Bus köer och prenumerationer
Funktionen Service Bus automatisk vidarebefordran gör att du kan länka en kö eller prenumeration till en annan kö eller ett annat ämne som ingår i samma namnområde. När automatisk vidarebefordran är aktiverat Service Bus automatiskt meddelanden som placeras i den första kön eller prenumerationen (källa) och placerar dem i den andra kön eller ämnet (målet). Det går fortfarande att skicka ett meddelande direkt till målentiteten. Mer information finns i Länka [Service Bus entiteter med automatisk vidarebefordran.](service-bus-auto-forwarding.md) Den här artikeln visar olika sätt att aktivera automatisk vidarebefordran för Service Bus köer och prenumerationer. 

> [!IMPORTANT]
> Basic-nivån för Service Bus stöder inte funktionen för automatisk vidarebefordran. Standard- och Premium-nivåerna stöder funktionen. Skillnader mellan dessa nivåer finns i [Service Bus priser.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="using-azure-portal"></a>Använda Azure Portal
När du skapar **en kö** eller **en** prenumeration för ett ämne i Azure Portal väljer du Vidarebefordra meddelanden till **kö/ämne** enligt följande exempel. Ange sedan om du vill att meddelanden ska vidarebefordras till en kö eller ett ämne. I det här exemplet **är** alternativet Kö markerat och en kö från samma namnområde har valts.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Skapa en kö med automatisk vidarebefordran aktiverat
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Aktivera automatisk vidarebefordran när kön skapas":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Skapa en prenumeration för ett ämne med automatisk vidarebefordran aktiverat
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Aktivera automatisk vidarebefordran när prenumerationen skapas":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>Uppdatera inställningen för automatisk vidarebefordran för en befintlig kö
På sidan **Översikt** för din Service Bus väljer du det aktuella värdet för inställningen Vidarebefordra meddelanden **till.** I följande exempel är det aktuella värdet **Inaktiverat.** I fönstret **Vidarebefordra meddelanden till kö/ämne** kan du välja den kö eller det ämne där du vill att meddelandena ska vidarebefordras. 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="Aktivera automatisk vidarebefordran för en befintlig kö":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>Uppdatera inställningen för automatisk vidarebefordran för en befintlig prenumeration
På sidan **Översikt** för din Service Bus prenumeration väljer du det aktuella värdet för inställningen **Vidarebefordra meddelanden till.** I följande exempel är det aktuella värdet **Inaktiverat.** I fönstret **Vidarebefordra meddelanden till kö/ämne** kan du välja den kö eller det ämne där du vill att meddelandena ska vidarebefordras. 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="Aktivera automatisk vidarebefordran för en befintlig prenumeration":::

## <a name="using-azure-cli"></a>Använda Azure CLI
Om **du vill skapa en kö** med automatisk vidarebefordran aktiverat använder du kommandot med inställt på namnet på den kö eller det ämne som du vill att [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--forward-to` meddelandena ska vidarebefordras till. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Om **du vill uppdatera inställningen för** automatisk vidarebefordran för en befintlig kö använder du kommandot med inställt på namnet på den kö eller det ämne som du vill att [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) `--forward-to` meddelandena ska vidarebefordras till. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```


Om **du vill skapa en** prenumeration på ett ämne med automatisk vidarebefordran aktiverat använder du kommandot med inställt på namnet på kön eller ämnet som du vill att [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--forward-to` meddelandena ska vidarebefordras till.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

Om **du vill uppdatera inställningen** för automatisk vidarebefordran för en prenumeration till ett ämne använder du kommandot med inställt på namnet på kön eller ämnet som du vill att [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--forward-to` meddelandena ska vidarebefordras till.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Om **du vill skapa en kö** med automatisk vidarebefordran aktiverat använder du kommandot med inställt på namnet på den kö eller det ämne som du vill att [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-ForwardTo` meddelandena ska vidarebefordras till. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Om **du vill uppdatera inställningen för automatisk vidarebefordran för en** befintlig kö använder du kommandot som i följande [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) exempel.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Om **du vill skapa en** prenumeration för ett ämne med automatisk vidarebefordran aktiverat använder du kommandot med inställt på namnet på kön eller ämnet som du vill att [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-ForwardTo` meddelandena ska vidarebefordras till.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

Information **om hur du uppdaterar inställningen för automatisk vidarebefordran för en befintlig** prenumeration finns i följande exempel.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Om **du vill skapa en kö** med automatisk vidarebefordran aktiverat anger du i avsnittet för köegenskaper namnet på den kö eller det ämne som du vill att `forwardTo` meddelandena ska vidarebefordras till. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/köer.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Om **du vill skapa en** prenumeration för ett ämne med automatisk vidarebefordran aktiverat anger du i avsnittet för köegenskaper namnet på den kö eller det ämne som du vill att `forwardTo` meddelandena ska vidarebefordras till. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/ämnen/prenumerationer.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "forwardTo": "myqueue2"
              }
            }
          ]
        }
      ]
    }
  ]
}
```


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