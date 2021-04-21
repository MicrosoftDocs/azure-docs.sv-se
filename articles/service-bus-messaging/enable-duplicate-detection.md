---
title: Aktivera identifiering av duplicerade meddelanden – Azure Service Bus
description: Den här artikeln förklarar hur du aktiverar identifiering av duplicerade meddelanden med hjälp av Azure Portal, PowerShell, CLI och programmeringsspråk (C#, Java, Python och JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755253"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Aktivera identifiering av dubblettmeddelanden för en Azure Service Bus kö eller ett ämne
När du aktiverar dubblettidentifiering för en kö eller ett ämne Azure Service Bus en historik över alla meddelanden som skickas till kön eller ämnet under en konfigurerad tidsperiod. Under det intervallet kommer din kö eller ditt ämne inte att lagra några dubblettmeddelanden. Aktivering av den här egenskapen garanterar exakt en leverans över ett användardefinierat tidsintervall. Mer information finns i [Dubblettidentifiering.](duplicate-detection.md) Den här artikeln visar olika sätt att aktivera identifiering av duplicerade meddelanden för en Service Bus kö eller ett ämne. 


> [!NOTE]
> - Basic-nivån för Service Bus stöder inte dubblettidentifiering. Standard- och Premium-nivåerna stöder dubblettidentifiering. Skillnader mellan dessa nivåer finns i [Service Bus priser.](https://azure.microsoft.com/pricing/details/service-bus/)
> - Du kan inte aktivera eller inaktivera dubblettidentifiering när kön eller ämnet har skapats. Du kan bara göra det när du skapar kön eller ämnet. 

## <a name="using-azure-portal"></a>Använda Azure Portal
När du skapar **en kö** i Azure Portal väljer du **Aktivera dubblettidentifiering** enligt följande bild. Du kan konfigurera storleken på fönstret för dubblettidentifiering när du skapar en kö eller ett ämne. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Aktivera dubblettidentifiering när kön skapas":::

När du skapar ett ämne i Azure Portal du Aktivera **dubblettidentifiering** enligt följande bild. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Aktivera dubblettidentifiering när ämnet skapas":::

Du kan också konfigurera den här inställningen för en befintlig kö eller ett ämne om du hade aktiverat dubblettidentifiering när den skapades. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Uppdatera fönsterstorleken för dubblettidentifiering för en befintlig kö eller ett ämne
Om du vill ändra fönsterstorleken för dubblettidentifiering för en befintlig kö eller ett ämne går du till sidan **Översikt** och väljer **Ändra** för **fönstret Dubblettidentifiering.**  

#### <a name="queue"></a>Kö
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Ange fönsterstorlek för dubblettidentifiering för en kö":::

#### <a name="topic"></a>Avsnitt
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Ange fönsterstorlek för dubblettidentifiering för ett ämne":::


## <a name="using-azure-cli"></a>Använda Azure CLI
Om **du vill skapa en kö med dubblettidentifiering** aktiverat använder du kommandot med [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-duplicate-detection` inställt på `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Om **du vill skapa ett ämne med dubblettidentifiering** aktiverat använder du kommandot med [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-duplicate-detection` inställt på `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

I exemplen ovan anges även storleken på fönstret för dubblettidentifiering med hjälp av `--duplicate-detection-history-time-window` parametern . Fönsterstorleken är inställd på en dag. Standardvärdet är 10 minuter och det högsta tillåtna värdet är sju dagar.

Om **du vill uppdatera en kö med en ny storlek för identifieringsfönstret** använder [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) du kommandot med `--duplicate-detection-history-time-window` parametern . I det här exemplet uppdateras fönsterstorleken till sju dagar. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Om du vill uppdatera **ett ämne med en ny fönsterstorlek för identifiering använder** du kommandot med [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) `--duplicate-detection-history-time-window` parametern . I det här exemplet uppdateras fönsterstorleken till sju dagar.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Om **du vill skapa en kö med dubblettidentifiering** aktiverat använder du kommandot med [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresDuplicateDetection` inställt på `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Om **du vill skapa ett ämne med dubblettidentifiering** aktiverat använder du kommandot med [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-RequiresDuplicateDetection` inställt på `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

I exemplen ovan anges även storleken på fönstret för dubblettidentifiering med hjälp av `-DuplicateDetectionHistoryTimeWindow` parametern . Fönsterstorleken är inställd på en dag. Standardvärdet är 10 minuter och det högsta tillåtna värdet är sju dagar.

Om **du vill uppdatera en kö med en ny storlek för identifieringsfönstret** kan du se följande exempel.  I det här exemplet uppdateras fönsterstorleken till sju dagar.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

Information **om hur du uppdaterar ett ämne med en ny fönsterstorlek för identifiering** finns i följande exempel. I det här exemplet uppdateras fönsterstorleken till sju dagar.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Om **du vill skapa en kö med dubblettidentifiering aktiverat** anger du till i avsnittet för `requiresDuplicateDetection` `true` köegenskaper. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/köer.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) Ange ett värde för egenskapen `duplicateDetectionHistoryTimeWindow` för att ange storleken på fönstret för dubblettidentifiering. I följande exempel är det inställt på en dag.  

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Om **du vill skapa ett ämne med dubblettidentifiering aktiverat** anger du till i avsnittet `requiresDuplicateDetection` `true` ämnesegenskaper. Mer information finns i [Mallreferens för Microsoft.ServiceBus-namnrymder/ämnen.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
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