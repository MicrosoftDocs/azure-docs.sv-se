---
title: 'Snabb start: skapa en Event Hub med PowerShell – Azure Event Hubs'
description: Den här snabbstarten beskriver hur du skapar en händelsehubb med Azure PowerShell och hur du sedan skickar och tar emot händelser med hjälp av .NET Standard SDK.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 1c81024a9195e463eeaaf384d5cbbd1e0054c485
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88933978"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Snabbstart: Skapa en händelsehubb med Azure PowerShell

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten har du skapat en händelsehubb med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att kunna följa den här självstudien måste du ha:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto][] innan du börjar.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [SDK för .NET Standard](https://www.microsoft.com/net/download/windows) version 2.0 eller senare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du använder PowerShell lokalt måste du köra den senaste versionen av PowerShell för att kunna slutföra den här snabbstarten. Om du behöver installera eller uppgradera kan du läsa [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk samling Azure-resurser. Du behöver en resursgrupp för att kunna skapa en händelsehubb. 

I följande exempel skapas en resursgrupp i regionen USA, östra. Ersätt `myResourceGroup` med namnet på resursgruppen som du vill använda:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

När resursgruppen har skapats skapar du ett Event Hubs-namnområde i resursgruppen. Ett Event Hubs-namnområde ger ett unikt fullständigt kvalificerat domännamn som du kan skapa din händelsehubb i. Ersätt `namespace_name` med ett unikt namn för ditt namnområde:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Nu när du har skapat ett Event Hubs-namnområde skapar du en händelsehubb i namnområdet:  
Tillåten period för `MessageRetentionInDays` är mellan 1 och 7 dagar.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Grattis! Du har använt Azure PowerShell för att skapa en Event Hubs-namnrymd och en händelsehubb i den namnrymden. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en Event Hubs-namnrymd och använde exempelprogram för att skicka och ta emot meddelanden från din händelsehubb. Stegvisa instruktioner för att skicka händelser till (eller) ta emot händelser från en händelsehubben finns i självstudierna **skicka och ta emot händelser** : 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Kör](event-hubs-go-get-started-send.md)
- [C (skickar endast)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tar endast emot)](event-hubs-storm-getstarted-receive.md)


[skapa ett kostnads fritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
