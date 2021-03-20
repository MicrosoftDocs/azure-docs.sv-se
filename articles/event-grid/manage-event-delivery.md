---
title: Principer för obeställbara meddelanden och återförsök-Azure Event Grid
description: Beskriver hur du anpassar alternativ för händelse leverans för Event Grid. Ange ett mål för obeställbara meddelanden och ange hur lång tid överföringen ska göras.
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 7d8cd74ccfb77bcec45d06071a4f46fb2a640cf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460945"
---
# <a name="set-dead-letter-location-and-retry-policy"></a>Konfigurera plats för obeställbara meddelanden och återförsöksprincip

När du skapar en händelse prenumeration kan du anpassa inställningarna för händelse leverans. Den här artikeln visar hur du konfigurerar en plats för obeställbara meddelanden och hur du anpassar inställningarna för återförsök. Information om dessa funktioner finns i [Event Grid meddelande leverans och försök igen](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Om du vill veta mer om meddelande leverans, nya försök och obeställbara meddelanden, se den konceptuella artikeln: [Event Grid meddelande leverans och försök igen](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Ange plats för obeställbara meddelanden

Om du vill ange en plats för död brev behöver du ett lagrings konto för att lagra händelser som inte kan levereras till en slut punkt. Exemplen hämtar resurs-ID för ett befintligt lagrings konto. De skapar en händelse prenumeration som använder en behållare i lagrings kontot för slut punkten för obeställbara meddelanden.

> [!NOTE]
> - Skapa ett lagrings konto och en BLOB-behållare i lagrings platsen innan du kör kommandona i den här artikeln.
> - Event Grids tjänsten skapar blobbar i den här behållaren. Namnen på blobbar kommer att ha namnet på Event Grid prenumerationen med alla bokstäver i versaler. Om namnet på prenumerationen till exempel är min-BLOB-prenumeration, kommer namn på blobarna för obeställbara meddelanden att ha MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-111111111111.jspå). Det här beteendet är att skydda mot skillnader i fall hantering mellan Azure-tjänster.


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Om du vill inaktivera obeställbara meddelanden kör du kommandot igen för att skapa händelse prenumerationen men Ange inget värde för `deadletter-endpoint` . Du behöver inte ta bort händelse prenumerationen.

> [!NOTE]
> Om du använder Azure CLI på din lokala dator ska du använda Azure CLI version 2.0.56 eller större. Anvisningar om hur du installerar den senaste versionen av Azure CLI finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Om du vill inaktivera obeställbara meddelanden kör du kommandot igen för att skapa händelse prenumerationen men Ange inget värde för `DeadLetterEndpoint` . Du behöver inte ta bort händelse prenumerationen.

> [!NOTE]
> Om du använder Azure-PowerShell på din lokala dator använder du Azure PowerShell version 1.1.0 eller senare. Hämta och installera den senaste Azure PowerShell från [Azure-nedladdningar](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Ange princip för återförsök

När du skapar en Event Grid-prenumeration kan du ange värden för hur lång tid Event Grid ska försöka leverera händelsen. Som standard försöker Event Grid i 24 timmar (1440 minuter) eller 30 gånger. Du kan ange något av dessa värden för Event Grid-prenumerationen. Värdet för Event Time-to-Live måste vara ett heltal mellan 1 och 1440. Värdet för högsta antal återförsök måste vara ett heltal mellan 1 och 30.

Du kan inte konfigurera [schemat för förnyad försök](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

Använd följande om du vill ange ett annat värde än 1440 minuter för Event Time-to-Live:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Om du vill ange högsta antal försök till ett annat värde än 30 använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

> [!NOTE]
> Om du anger både `event-ttl` och `max-deliver-attempts` , använder Event Grid första för att gå ut för att avgöra när händelse leveransen ska stoppas. Om du till exempel anger 30 minuter som TTL (Time-to-Live) och 10 högsta leverans försök. När en händelse inte levereras efter 30 minuter (eller) inte levereras efter 10 försök, oavsett vad som inträffar först, är händelsen obeställbara.  

### <a name="powershell"></a>PowerShell

Använd följande om du vill ange ett annat värde än 1440 minuter för Event Time-to-Live:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Om du vill ange högsta antal försök till ett annat värde än 30 använder du:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

> [!NOTE]
> Om du anger både `event-ttl` och `max-deliver-attempts` , använder Event Grid första för att gå ut för att avgöra när händelse leveransen ska stoppas. Om du till exempel anger 30 minuter som TTL (Time-to-Live) och 10 högsta leverans försök. När en händelse inte levereras efter 30 minuter (eller) inte levereras efter 10 försök, oavsett vad som inträffar först, är händelsen obeställbara.  

## <a name="next-steps"></a>Nästa steg

* Ett exempel program som använder en Azure Function-app för att bearbeta händelser för obeställbara meddelanden finns i [Azure Event Grid döda brev exempel för .net](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
