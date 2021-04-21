---
title: Aktivera partitionering i Azure Service Bus köer och ämnen
description: Den här artikeln förklarar hur du aktiverar partitionering i Azure Service Bus-köer och ämnen med hjälp av Azure Portal, PowerShell, CLI och programmeringsspråk (C#, Java, Python och JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755238"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Aktivera partitionering för en Azure Service Bus kö eller ett ämne
Service Bus partitioner gör att köer och ämnen, eller meddelandeentiteter, kan partitioneras över flera a brokers och meddelandearkiv. Partitionering innebär att det övergripande dataflödet för en partitionerad entitet inte längre begränsas av prestanda för en enskild a broker eller meddelandearkiv. Dessutom gör ett tillfälligt avbrott i ett meddelandearkiv inte att en partitionerad kö eller ett ämne inte är tillgängligt. Partitionerade köer och ämnen kan innehålla alla Service Bus funktioner, till exempel stöd för transaktioner och sessioner. Mer information finns i [Partitionerade köer och ämnen.](service-bus-partitioning.md) Den här artikeln visar olika sätt att aktivera identifiering av duplicerade meddelanden för Service Bus en kö eller ett ämne. 

> [!IMPORTANT]
> - Partitionering är tillgängligt när entiteten skapas för alla köer och ämnen i Basic- eller Standard-SKU:er. Den är inte tillgänglig för Premium-meddelande-SKU:n, men alla tidigare partitionerade entiteter i Premium-namnområden fortsätter att fungera som förväntat.
> - Det går inte att ändra partitioneringsalternativet för en befintlig kö eller ett ämne. Du kan bara ange alternativet när du skapar en kö eller ett ämne. 

## <a name="using-azure-portal"></a>Använda Azure Portal
När du skapar **en** kö i Azure Portal väljer **du Aktivera partitionering** enligt följande bild. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Aktivera partitionering när kön skapas":::

När du skapar ett ämne i Azure Portal väljer du **Aktivera partitionering** enligt följande bild. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Aktivera partitionering när ämnet skapas":::

## <a name="using-azure-cli"></a>Använda Azure CLI
Om **du vill skapa en kö med partitionering aktiverat** använder du kommandot med [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-partitioning` inställt på `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Om **du vill skapa ett ämne med partitionering aktiverat** använder du kommandot med [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-partitioning` inställt på `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Om **du vill skapa en kö med partitionering aktiverat** använder du kommandot med [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-EnablePartitioning` inställt på `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Om **du vill skapa ett ämne med partitionering aktiverat** använder du kommandot med [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-EnablePartitioning` inställt på `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Om **du vill skapa en kö med partitionering aktiverat** anger du till i avsnittet för `enablePartitioning` `true` köegenskaper. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/köer.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Om **du vill skapa ett ämne med dubblettidentifiering aktiverat** anger du till i avsnittet `enablePartitioning` `true` ämnesegenskaper. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/ämnen.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

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
            "enablePartitioning": true
          }
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