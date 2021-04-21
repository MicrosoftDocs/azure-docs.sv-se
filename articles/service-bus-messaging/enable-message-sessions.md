---
title: Aktivera Azure Service Bus-meddelandesessioner | Microsoft Docs
description: Den här artikeln förklarar hur du aktiverar meddelandesessioner med Azure Portal, PowerShell, CLI och programmeringsspråk (C#, Java, Python och JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755246"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Aktivera meddelandesessioner för en Azure Service Bus kö eller en prenumeration
Azure Service Bus-sessioner möjliggör gemensam och ordnad hantering av obundna sekvenser av relaterade meddelanden. Sessioner kan användas i **först in, först ut (FIFO) och** **begäran-svar-mönster.** Mer information finns i [Meddelandesessioner.](message-sessions.md) Den här artikeln visar olika sätt att aktivera sessioner för en Service Bus kö eller prenumeration. 

> [!IMPORTANT]
> - Basic-nivån för Service Bus stöder inte sessioner. Standard- och Premium-nivåerna stöder sessioner. Skillnader mellan dessa nivåer finns i [Service Bus priser.](https://azure.microsoft.com/pricing/details/service-bus/)
> - Du kan inte aktivera eller inaktivera meddelandesessioner när kön eller prenumerationen har skapats. Du kan bara göra det när du skapar kön eller prenumerationen. 

## <a name="using-azure-portal"></a>Använda Azure Portal
När du skapar **en** kö i Azure Portal väljer **du Aktivera sessioner** enligt följande bild. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Aktivera session när kön skapas":::

När du skapar en prenumeration för ett ämne i Azure Portal väljer du **Aktivera sessioner** enligt följande bild. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Aktivera session när prenumerationen skapas":::

## <a name="using-azure-cli"></a>Använda Azure CLI
Om **du vill skapa en kö med meddelandesessioner** aktiverade använder du kommandot med [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-session` inställt på `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Om **du vill skapa en prenumeration för ett ämne med meddelandesessioner aktiverade** använder du kommandot med [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--enable-session` inställt på `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Om **du vill skapa en kö med meddelandesessioner** aktiverade använder du kommandot med [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresSession` inställt på `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Om **du vill skapa en prenumeration för ett ämne med meddelandesessioner aktiverade** använder du kommandot med [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-RequiresSession` inställt på `true` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Om **du vill skapa en kö med meddelandesessioner aktiverade** anger du till i avsnittet för `requiresSession` `true` köegenskaper. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/köer.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Om **du vill skapa en prenumeration för ett ämne med meddelandesessioner aktiverade** anger du till i avsnittet `requiresSession` `true` prenumerationsegenskaper. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/ämnen/prenumerationer.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "requiresSession": true
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