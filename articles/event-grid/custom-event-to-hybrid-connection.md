---
title: 'Självstudie: Skicka anpassade händelser till hybridanslutning – Event Grid'
description: 'Självstudie: Använd Azure Event Grid och Azure CLI för att publicera ett ämne och prenumerera på händelsen. En hybridanslutning används för slutpunkten.'
ms.date: 07/07/2020
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7fcbc76f3ab58aac6beca3e142eba3a9b62c28a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770416"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Självstudie: Dirigera anpassade händelser till Azure Relay hybridanslutningar med Azure CLI och Event Grid

Azure Event Grid är en händelsetjänst för molnet. Azure Relay hybridanslutningar är en av de händelsehanterare som stöds. Du kan använda hybridanslutningar som händelsehanterare när du behöver bearbeta händelser från program som inte har en offentlig slutpunkt. Dessa program kan finnas i ditt företagsnätverk. I den här artikeln använder du Azure CLI för att skapa ett anpassat ämne, prenumerera på det anpassade ämnet och utlösa händelsen för att visa resultatet. Du skickar händelser till hybridanslutningen.

## <a name="prerequisites"></a>Förutsättningar

- Den här artikeln förutsätter att du redan har en hybridanslutning och ett lyssnarprogram. För att komma igång med hybridanslutningar, se [Kom igång med Relay hybridanslutningar – .NET](../azure-relay/relay-hybrid-connections-dotnet-get-started.md) eller [Kom igång med Relay hybridanslutningar – nod](../azure-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0.56 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). 

I följande exempel skapas en resursgrupp med namnet *gridResourceGroup* på platsen *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar dina händelser. I följande exempel skapas det anpassade ämnet i din resursgrupp. Ersätt `<topic_name>` med ett unikt namn för ditt anpassade ämne. Ämnesnamnet för händelserutnätet måste vara unikt eftersom det representeras av en DNS-post.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Prenumerera på ett anpassat ämne

Du prenumererar på ett Event Grid-ämne för att Event Grid vilka händelser du vill spåra. I följande exempel prenumererar vi på det anpassade ämne som du har skapat och resurs-ID:t för hybridanslutningen för slutpunkten. Hybridanslutnings-ID är i formatet:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Följande skript hämtar resurs-ID för relay-namnområdet. Det skapar ID för hybridanslutningen och prenumererar på ett Event Grid-ämne. Skriptet sätter slutpunktstypen till `hybridconnection` och använder hybridanslutning-ID:t för slutpunkten.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

Observera att ett [förfallodatum](concepts.md#event-subscription-expiration) har angetts för prenumerationen.

## <a name="create-application-to-process-events"></a>Skapa program för att bearbeta händelser

Du behöver ett program som kan hämta händelser från hybridanslutningen. [Microsoft Azure Event Grid Hybrid Connection Consumer sample for C#](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination) utför den här åtgärden. Du har redan slutfört förutsättningsstegen.

1. Kontrollera att du har Visual Studio 2019 eller senare.

1. Klona lagringsplatsen till din lokala dator.

1. Läs in projektet HybridConnectionConsumer i Visual Studio.

1. I Program.cs byter du ut `<relayConnectionString>` och `<hybridConnectionName>` med reläets anslutningssträng och namnet på hybridanslutningen du skapade.

1. Kompilera och kör programmet från Visual Studio.

## <a name="send-an-event-to-your-topic"></a>Skicka en händelse till ditt ämne

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten. I den här artikeln visas hur du använder Azure CLI till att utlösa händelsen. Du kan också använda [Event Grid publisher-programmet](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher).

Först måste vi ta fram URL och nyckel för det anpassade ämnet. Använd återigen ditt anpassade ämnesnamn för `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

För att förenkla den här artikeln skickar du exempelhändelsedata till det anpassade ämnet. Ett program eller en Azure-tjänst skulle vanligtvis skicka sådana händelsedata. CURL är ett verktyg som skickar HTTP-förfrågningar. I den här artikeln använder du CURL för att skicka händelsen till det anpassade ämnet.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Lyssnarprogrammet ska ta emot händelsemeddelandet.

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här händelsen ska du inte rensa upp bland de resurser som skapades i den här artikeln. I annat fall kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och prenumerationer på händelser kan du läsa mer om vad Event Grid kan hjälpa dig med:

- [Om Event Grid](overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webbslutpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md)