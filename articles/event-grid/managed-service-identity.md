---
title: Händelse leverans, hanterad tjänst identitet och privat länk
description: I den här artikeln beskrivs hur du aktiverar hanterad tjänst identitet för ett Azure Event Grid-ämne. Använd den för att vidarebefordra händelser till destinationer som stöds.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625286"
---
# <a name="event-delivery-with-a-managed-identity"></a>Händelse leverans med en hanterad identitet
Den här artikeln beskriver hur du använder en [hanterad tjänst identitet](../active-directory/managed-identities-azure-resources/overview.md) för ett Azure Event Grid-system, anpassat ämne eller domän. Använd den för att vidarebefordra händelser till stödda destinationer som Service Bus köer och ämnen, Event Hub och lagrings konton.



## <a name="prerequisites"></a>Förutsättningar
1. Tilldela en tilldelad identitet till ett system ämne, ett anpassat ämne eller en domän. 
    - För anpassade ämnen och domäner, se [Aktivera hanterad identitet för anpassade ämnen och domäner](enable-identity-custom-topics-domains.md). 
    - Information om system finns i [Aktivera hanterad identitet för system ämnen](enable-identity-system-topics.md)
1. Lägg till identiteten i en lämplig roll (till exempel Service Bus data avsändare) på målet (till exempel en Service Bus kö). Detaljerade anvisningar finns i [lägga till identitet till Azure-roller på destinationer](add-identity-roles.md)

    > [!NOTE]
    > För närvarande går det inte att leverera händelser med [privata slut punkter](../private-link/private-endpoint-overview.md). Mer information finns i avsnittet [privata slut punkter](#private-endpoints) i slutet av den här artikeln. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Skapa händelse prenumerationer som använder en identitet
När du har ett anpassat ämne i Event Grid eller ett system ämne eller en domän med en Systemhanterad identitet och har lagt till identiteten i rätt roll på målet, är du redo att skapa prenumerationer som använder identiteten. 

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
När du skapar en händelse prenumeration visas ett alternativ för att aktivera användning av en tilldelad identitet för en slut punkt i avsnittet **information om slut punkt** . 

![Aktivera identitet när en händelse prenumeration skapas för en Service Bus kö](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Du kan också aktivera användning av en tilldelad identitet som ska användas för obeställbara meddelanden på fliken **ytterligare funktioner** . 

![Aktivera systemtilldelad identitet för obeställbara meddelanden](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Använd Azure CLI-Service Bus-kön 
I det här avsnittet får du lära dig hur du använder Azure CLI för att aktivera användning av en systemtilldelad identitet för att leverera händelser till en Service Bus kö. Identiteten måste vara medlem i rollen **Azure Service Bus data avsändare** . Det måste också vara medlem i rollen **Storage BLOB data Contributor** på det lagrings konto som används för obeställbara meddelanden. 

#### <a name="define-variables"></a>Definiera variabler
Börja med att ange värden för följande variabler som ska användas i CLI-kommandot. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Skapa en händelse prenumeration genom att använda en hanterad identitet för leverans 
Det här exempel kommandot skapar en händelse prenumeration för ett anpassat ämne i Event Grid med en slut punkts typ som har angetts till **Service Bus kö**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Skapa en händelse prenumeration genom att använda en hanterad identitet för leverans och obeställbara meddelanden
Det här exempel kommandot skapar en händelse prenumeration för ett anpassat ämne i Event Grid med en slut punkts typ som har angetts till **Service Bus kö**. Det anger också att den systemhanterade identiteten ska användas för obeställbara meddelanden. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Använd Azure CLI – Event Hubs 
I det här avsnittet får du lära dig hur du använder Azure CLI för att aktivera användning av en systemtilldelad identitet för att leverera händelser till en Event Hub. Identiteten måste vara medlem i rollen **Azure Event Hubs data Sender** . Det måste också vara medlem i rollen **Storage BLOB data Contributor** på det lagrings konto som används för obeställbara meddelanden. 

#### <a name="define-variables"></a>Definiera variabler
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Skapa en händelse prenumeration genom att använda en hanterad identitet för leverans 
Det här exempel kommandot skapar en händelse prenumeration för ett anpassat ämne i Event Grid med en slut punkts typ inställd på **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Skapa en händelse prenumeration genom att använda en hanterad identitet för leverans och obeställbara meddelanden kön 
Det här exempel kommandot skapar en händelse prenumeration för ett anpassat ämne i Event Grid med en slut punkts typ inställd på **Event Hubs**. Det anger också att den systemhanterade identiteten ska användas för obeställbara meddelanden. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Använd Azure CLI-Azure Storage-kön 
I det här avsnittet får du lära dig hur du använder Azure CLI för att aktivera användning av en systemtilldelad identitet för att leverera händelser till en Azure Storage kö. Identiteten måste vara medlem i rollen för **lagrings köns data meddelande avsändare** på lagrings kontot. Det måste också vara medlem i rollen **Storage BLOB data Contributor** på det lagrings konto som används för obeställbara meddelanden.

#### <a name="define-variables"></a>Definiera variabler  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Skapa en händelse prenumeration genom att använda en hanterad identitet för leverans 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Skapa en händelse prenumeration genom att använda en hanterad identitet för leverans och obeställbara meddelanden kön 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Privata slutpunkter
För närvarande går det inte att leverera händelser med [privata slut punkter](../private-link/private-endpoint-overview.md). Det finns inget stöd om du har strikta krav på nätverks isolering där dina levererade händelse trafik inte får lämna det privata IP-utrymmet. 

Men om dina krav kräver ett säkert sätt att skicka händelser med hjälp av en krypterad kanal och en känd identitet på avsändaren (i det här fallet Event Grid) med hjälp av offentligt IP-utrymme kan du leverera händelser till Event Hubs, Service Bus eller Azure Storage tjänst med ett anpassat ämne i Azure Event Grid eller en domän med Systemhanterad identitet som du ser i den här artikeln. Sedan kan du använda en privat länk som kon figurer ATS i Azure Functions eller webhooken som distribueras i det virtuella nätverket för att hämta händelser. Se exemplet: [Anslut till privata slut punkter med Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Under den här konfigurationen går trafiken över den offentliga IP/Internet från Event Grid till Event Hubs, Service Bus eller Azure Storage, men kanalen kan krypteras och en hanterad identitet för Event Grid används. Om du konfigurerar Azure Functions eller webhook som har distribuerats till det virtuella nätverket för att använda en Event Hubs, Service Bus eller Azure Storage via privat länk kommer det att finnas kvar i Azure i den här delen av trafiken.


## <a name="next-steps"></a>Nästa steg
Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).
