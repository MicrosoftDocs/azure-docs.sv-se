---
title: Hämta anslutnings sträng – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller anvisningar för att få en anslutnings sträng som klienter kan använda för att ansluta till Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5ae6c66ddbbf4b9946e7037e1a7723043bf60507
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86537198"
---
# <a name="get-an-event-hubs-connection-string"></a>Hämta en Event Hubs anslutnings sträng

Om du vill använda Event Hubs måste du skapa ett Event Hubs-namnområde. Ett namn område är en omfattnings behållare för flera Event Hub-eller Kafka-ämnen. Det här namn området ger dig ett unikt [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). När ett namn område har skapats kan du hämta den anslutnings sträng som krävs för att kommunicera med Event Hubs.

Anslutnings strängen för Azure Event Hubs har följande komponenter inbäddade i den,

* FQDN = FQDN för namn området EventHubs som du skapade (det innehåller namn områdes namnet EventHubs följt av servicebus.windows.net)
* SharedAccessKeyName = det namn som du har valt för programmets SAS-nycklar
* SharedAccessKey = det genererade värdet för nyckeln.

Mallen för anslutnings strängen ser ut så här
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Ett exempel på en anslutnings sträng kan se ut `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Den här artikeln vägleder dig genom olika sätt att hämta anslutnings strängen.

## <a name="get-connection-string-from-the-portal"></a>Hämta anslutnings sträng från portalen
1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj **alla tjänster** i den vänstra navigerings menyn. 
3. Välj **Event Hubs** i avsnittet **analys** . 
4. I listan över händelse hubbar väljer du händelsehubben.
6. På sidan **Event Hubs namn område** väljer du **principer för delad åtkomst** på den vänstra menyn.

    ![Meny alternativet principer för delad åtkomst](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Välj en **princip för delad åtkomst** i listan över principer. Standardvärdet heter: **RootManageSharedAccessPolicy**. Du kan lägga till en princip med rätt behörigheter (läsa, skriva) och använda den principen. 

    ![Event Hubs principer för delad åtkomst](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Välj **kopierings** knappen bredvid fältet **anslutnings sträng – primär nyckel** . 

    ![Event Hubs – hämta anslutnings sträng](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Hämtar anslutnings strängen med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) för att hämta anslutnings strängen för den aktuella principen/regel namnet enligt nedan:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Hämtar anslutnings strängen med Azure CLI
Du kan använda följande för att hämta anslutnings strängen för namn området:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Du kan också använda följande för att hämta anslutnings strängen för en EventHub-entitet:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Mer information om Azure CLI-kommandon för Event Hubs finns i [Azure CLI för Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](./event-hubs-about.md)
* [Skapa en händelsehubb](event-hubs-create.md)
