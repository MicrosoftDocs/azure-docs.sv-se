---
title: Aktivera dead lettering för Azure Service Bus köer och prenumerationer
description: Den här artikeln förklarar hur du aktiverar dead lettering för köer och prenumerationer med hjälp av Azure Portal, PowerShell, CLI och programmeringsspråk (C#, Java, Python och JavaScript)
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819405"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Aktivera dead lettering när meddelanden upphör att gälla för Azure Service Bus köer och prenumerationer
Azure Service Bus köer och prenumerationer för ämnen tillhandahåller en sekundär underkö som kallas för en kö med oställbara meddelanden (DLQ). Kön för oställbara meddelanden behöver inte skapas explicit och kan inte tas bort eller hanteras oberoende av huvudentiteten. Syftet med kön för dead-letter är att lagra meddelanden som inte kan levereras till någon mottagare eller meddelanden som inte kunde bearbetas. Mer information finns i [Översikt över Service Bus köer med dead-letter.](service-bus-dead-letter-queues.md) Den här artikeln visar olika sätt att aktivera dead lettering för Service Bus köer och prenumerationer. 

## <a name="using-azure-portal"></a>Använda Azure Portal
När du skapar **en kö** **eller** en prenumeration för ett ämne i Azure Portal väljer du Aktivera dead **lettering** vid förfallodatum för meddelanden enligt följande exempel. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Skapa en kö med dead lettering aktiverat
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Aktivera dead lettering när kön skapas":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Skapa en prenumeration med dead lettering aktiverat
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Aktivera dead lettering när prenumerationen skapades":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Uppdatera inställningen för dead lettering för meddelandeförfallotid för en befintlig kö
På sidan **Översikt** för din Service Bus väljer du det aktuella värdet för inställningen Dead lettering on message expiration **(Dead lettering** vid meddelandeförfallotid). I följande exempel är det aktuella värdet **Inaktiverat.** Du kan aktivera eller inaktivera dead lettering när meddelanden upphör att gälla i popup-fönstret. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Aktivera dead-lettering när meddelanden upphör att gälla för en befintlig kö":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Uppdatera inställningen Förfallotidsmeddelanden för en befintlig prenumeration
På sidan **Översikt** för din Service Bus prenumeration väljer du det aktuella värdet för inställningen Dead lettering on message expiration **(Dead lettering** vid meddelandeförfallotid). I följande exempel är det aktuella värdet **Inaktiverat.** Du kan aktivera eller inaktivera dead lettering när meddelanden upphör att gälla i popup-fönstret. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Aktivera dead-lettering när meddelandet upphör att gälla för en befintlig prenumeration":::

## <a name="using-azure-cli"></a>Använda Azure CLI
Om **du vill skapa en kö med dead lettering när förfallodatum för meddelanden** är aktiverat använder du kommandot med [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-dead-lettering-on-message-expiration` inställt på `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Om **du vill aktivera inställningen för förfallotid för** meddelanden med oövervakade meddelanden för en befintlig kö använder du kommandot med [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) `--enable-dead-lettering-on-message-expiration` inställt på `true` . 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Om **du vill skapa en prenumeration på ett ämne med dead lettering när meddelandet upphör att gälla** aktiverat använder du kommandot med [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--enable-dead-lettering-on-message-expiration` inställt på `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Om **du vill aktivera inställningen för förfallotid för** meddelanden för en prenumeration på ett ämne använder du kommandot med set [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Om **du vill skapa en kö med dead lettering när förfallodatum för meddelanden** är aktiverat använder du kommandot med [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-DeadLetteringOnMessageExpiration` inställt på `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Om **du vill aktivera inställningen för förfallotid för** meddelanden med oställbara meddelanden för en befintlig kö använder du kommandot enligt följande [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) exempel.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Om **du vill skapa en prenumeration för ett ämne med dead lettering när meddelandet upphör att gälla** aktiverat använder du kommandot med [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-DeadLetteringOnMessageExpiration` inställt på `$True` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Om **du vill aktivera inställningen Förfallotidsmeddelanden för meddelanden för en befintlig** prenumeration kan du läsa följande exempel.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Om **du vill skapa en kö med förfallotidsmeddelanden aktiverade** anger du i avsnittet för `deadLetteringOnMessageExpiration` köegenskaper till `true` . Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnområden/köer.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Om **du vill skapa en prenumeration för ett ämne med dead lettering när meddelandet upphör att gälla** aktiverat anger du i avsnittet för `deadLetteringOnMessageExpiration` köegenskaper till `true` . Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/ämnen/prenumerationer.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "deadLetteringOnMessageExpiration": true
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