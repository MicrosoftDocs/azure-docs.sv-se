---
title: Filtrera händelser för Azure Event Grid
description: Den här artikeln visar hur du filtrerar händelser (efter händelse typ, efter ämne, efter operatorer och data osv.) när du skapar en Event Grid-prenumeration.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 99fb00f99a055033ccfcd99e32a52d423878fb44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86105599"
---
# <a name="filter-events-for-event-grid"></a>Filtrera händelser för Event Grid

Den här artikeln visar hur du filtrerar händelser när du skapar en Event Grid-prenumeration. Information om alternativen för händelse filtrering finns i [förstå händelse filtrering för Event Grid prenumerationer](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrera efter händelse typ

När du skapar en Event Grid-prenumeration kan du ange vilka [händelse typer](event-schema.md) som ska skickas till slut punkten. Exemplen i det här avsnittet skapar händelse prenumerationer för en resurs grupp, men begränsar de händelser som skickas till `Microsoft.Resources.ResourceWriteFailure` och `Microsoft.Resources.ResourceWriteSuccess` . Om du behöver mer flexibilitet när du filtrerar händelser efter händelse typer, se filtrera efter avancerade operatorer och data fält.

För PowerShell använder du `-IncludedEventType` parametern när du skapar prenumerationen.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

För Azure CLI använder du `--included-event-types` parametern. I följande exempel används Azure CLI i ett bash-gränssnitt:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Använd egenskapen för en Resource Manager-mall `includedEventTypes` .

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrera efter ämne

Du kan filtrera händelser efter ämne i händelse data. Du kan ange ett värde som ska matchas i början eller slutet av ämnet. Om du behöver mer flexibilitet när du filtrerar händelser efter ämne, se filtrera efter avancerade operatorer och data fält.

I följande PowerShell-exempel skapar du en händelse prenumeration som filtrerar efter ämnets början. Du kan använda `-SubjectBeginsWith` parametern för att begränsa händelser till för en speciell resurs. Du skickar resurs-ID: t för en nätverks säkerhets grupp.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Nästa PowerShell-exempel skapar en prenumeration för en blob-lagring. Den begränsar händelser till dem med ett ämne som slutar med `.jpg` .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

I följande Azure CLI-exempel skapar du en händelse prenumeration som filtrerar efter ämnets början. Du kan använda `--subject-begins-with` parametern för att begränsa händelser till för en speciell resurs. Du skickar resurs-ID: t för en nätverks säkerhets grupp.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Nästa Azure CLI-exempel skapar en prenumeration för en blob-lagring. Den begränsar händelser till dem med ett ämne som slutar med `.jpg` .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

I följande exempel på en Resource Manager-mall skapar du en händelse prenumeration som filtrerar efter ämnets början. Du kan använda `subjectBeginsWith` egenskapen för att begränsa händelser till för en speciell resurs. Du skickar resurs-ID: t för en nätverks säkerhets grupp.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

I nästa exempel på en Resource Manager-mall skapas en prenumeration för en blob-lagring. Den begränsar händelser till dem med ett ämne som slutar med `.jpg` .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrera efter operatorer och data

Om du vill ha mer flexibilitet i filtreringen kan du använda operatorer och data egenskaper för att filtrera händelser.

### <a name="subscribe-with-advanced-filters"></a>Prenumerera med avancerade filter

Mer information om operatörer och nycklar som du kan använda för avancerad filtrering finns i [avancerad filtrering](event-filtering.md#advanced-filtering).

I de här exemplen skapas ett anpassat ämne. De prenumererar på det anpassade ämnet och filtrerar efter ett värde i dataobjektet. Händelser som har egenskapen färg inställd på blå, röd eller grön skickas till prenumerationen.

Om du använder Azure CLI använder du:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Observera att ett [förfallodatum](concepts.md#event-subscription-expiration) har angetts för prenumerationen.

Om du använder PowerShell använder du:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Test filter

Testa filtret genom att skicka en händelse med färg fältet inställt på grönt. Eftersom grönt är ett av värdena i filtret skickas händelsen till slut punkten.

Om du använder Azure CLI använder du:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Om du använder PowerShell använder du:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Om du vill testa ett scenario där händelsen inte skickas skickar du en händelse med färg fältet inställt på gult. Gult är inte ett av de värden som anges i prenumerationen, så händelsen levereras inte till din prenumeration.

Om du använder Azure CLI använder du:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Om du använder PowerShell använder du:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Mer information om nyckeln för autentisering finns i [Event Grid säkerhet och autentisering](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
