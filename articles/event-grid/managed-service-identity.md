---
title: Händelse leverans, hanterad tjänst identitet och privat länk
description: I den här artikeln beskrivs hur du aktiverar hanterad tjänst identitet för ett Azure Event Grid-ämne. Använd den för att vidarebefordra händelser till destinationer som stöds.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 3e643465db7cc918499ca962c4697cb61cb4b594
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007779"
---
# <a name="event-delivery-with-a-managed-identity"></a>Händelse leverans med en hanterad identitet
I den här artikeln beskrivs hur du aktiverar en [hanterad tjänst identitet](../active-directory/managed-identities-azure-resources/overview.md) för anpassade ämnen eller domäner i Azure Event Grid. Använd den för att vidarebefordra händelser till stödda destinationer som Service Bus köer och ämnen, Event Hub och lagrings konton.

Här följer de steg som beskrivs i detalj i den här artikeln:
1. Skapa ett anpassat ämne eller en domän med en tilldelad identitet eller uppdatera ett befintligt anpassat ämne eller domän för att aktivera identitet. 
1. Lägg till identiteten i en lämplig roll (till exempel Service Bus data avsändare) på målet (till exempel en Service Bus kö).
1. När du skapar händelse prenumerationer kan du aktivera användningen av identiteten för att leverera händelser till målet. 

> [!NOTE]
> För närvarande går det inte att leverera händelser med [privata slut punkter](../private-link/private-endpoint-overview.md). Mer information finns i avsnittet [privata slut punkter](#private-endpoints) i slutet av den här artikeln. 

## <a name="create-a-custom-topic-or-domain-with-an-identity"></a>Skapa ett anpassat ämne eller en domän med en identitet
Först ska vi titta på hur du skapar ett ämne eller en domän med en Systemhanterad identitet.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
Du kan aktivera systemtilldelad identitet för ett anpassat ämne eller en domän medan du skapar den i Azure Portal. Följande bild visar hur du aktiverar en Systemhanterad identitet för ett anpassat ämne. I princip väljer du alternativet **Aktivera en systemtilldelad identitet** på sidan **Avancerat** i guiden skapa ämne. Du ser det här alternativet på sidan **Avancerat** i guiden skapa domän också. 

![Aktivera identitet när du skapar ett anpassat ämne](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Använda Azure CLI
Du kan också använda Azure CLI för att skapa ett anpassat ämne eller en domän med en tilldelad identitet. Använd `az eventgrid topic create` kommandot med `--identity` parametern inställt på `systemassigned` . Om du inte anger något värde för den här parametern används standardvärdet `noidentity` . 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

På samma sätt kan du använda `az eventgrid domain create` kommandot för att skapa en domän med en Systemhanterad identitet.

## <a name="enable-an-identity-for-an-existing-custom-topic-or-domain"></a>Aktivera en identitet för ett befintligt anpassat ämne eller en befintlig domän
I föregående avsnitt har du lärt dig hur du aktiverar en Systemhanterad identitet när du skapade ett anpassat ämne eller en domän. I det här avsnittet får du lära dig hur du aktiverar en Systemhanterad identitet för ett befintligt anpassat ämne eller en befintlig domän. 

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
Följande procedur visar hur du aktiverar Systemhanterad identitet för ett anpassat ämne. Stegen för att aktivera en identitet för en domän är liknande. 

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Sök efter **händelse rutnäts ämnen** i Sök fältet högst upp.
3. Välj det **anpassade ämne** som du vill aktivera den hanterade identiteten för. 
4. Växla till fliken **identitet** . 
5. Aktivera **växeln** för att aktivera identiteten. 
1. Spara inställningen genom att välja **Spara** i verktygsfältet. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Sidan identitet för ett anpassat ämne"::: 

Du kan använda liknande steg för att aktivera en identitet för en Event Grid-domän.

### <a name="use-the-azure-cli"></a>Använda Azure CLI
Använd `az eventgrid topic update` kommandot med `--identity` set to `systemassigned` för att aktivera systemtilldelad identitet för ett befintligt anpassat ämne. Ange som värde om du vill inaktivera identiteten `noidentity` . 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Kommandot för att uppdatera en befintlig domän är liknande ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-azure-roles"></a>Destinationer och Azure-roller som stöds
När du har aktiverat identitet för ditt anpassade ämne eller domän i Event Grid skapar Azure en identitet automatiskt i Azure Active Directory. Lägg till den här identiteten i lämpliga Azure-roller så att det anpassade ämnet eller domänen kan vidarebefordra händelser till destinationer som stöds. Du kan till exempel lägga till identiteten i rollen **azure Event Hubs data avsändare** för ett Azure Event Hubs-namnområde så att det anpassade avsnittet om Event Grid kan vidarebefordra händelser till händelse nav i det namn området. 

För närvarande stöder Azure Event Grid anpassade ämnen eller domäner som kon figurer ATS med en systemtilldelad hanterad identitet för att vidarebefordra händelser till följande mål. I den här tabellen får du också de roller som identiteten ska vara i så att det anpassade ämnet kan vidarebefordra händelserna.

| Mål | Azure-roll | 
| ----------- | --------- | 
| Service Bus köer och ämnen | [Azure Service Bus data avsändare](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs data avsändare](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Storage Blob Data-deltagare](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Avsändare av data meddelande i lagrings köer](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Lägga till en identitet i Azure-roller på destinationer
I det här avsnittet beskrivs hur du lägger till identiteten för ditt anpassade ämne eller din domän i en Azure-roll. 

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
Du kan använda Azure Portal för att tilldela det anpassade ämnet eller domän identiteten till en lämplig roll så att det anpassade ämnet eller domänen kan vidarebefordra händelser till målet. 

I följande exempel läggs en hanterad identitet för ett anpassat Event Grid-ämne med namnet **msitesttopic** till rollen **Azure Service Bus data avsändare** för ett Service Bus namn område som innehåller en kö eller ämnes resurs. När du lägger till rollen på namn områdes nivån kan det anpassade avsnittet för Event Grid vidarebefordra händelser till alla entiteter i namn området. 

1. Gå till ditt **Service Bus namnrum** i [Azure Portal](https://portal.azure.com). 
1. Välj **Access Control** i det vänstra fönstret. 
1. Välj **Lägg till** i avsnittet **Lägg till en roll tilldelning** . 
1. Utför följande steg på sidan **Lägg till en roll tilldelning** :
    1. Välj rollen. I det här fallet är det **Azure Service Bus data avsändaren**. 
    1. Välj **identitet** för ditt event Grid-anpassade ämne eller domän. 
    1. Välj **Spara** för att spara konfigurationen.

Stegen är liknande för att lägga till en identitet för andra roller som anges i tabellen. 

### <a name="use-the-azure-cli"></a>Använda Azure CLI
Exemplet i det här avsnittet visar hur du använder Azure CLI för att lägga till en identitet i en Azure-roll. Exempel kommandona är för anpassade ämnen i Event Grid. Kommandona för events Grid-domäner liknar varandra. 

#### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Hämta ägar-ID för det anpassade ämnets system identitet 
Först hämtar du huvud-ID för det anpassade ämnets systemhanterade identitet och tilldelar identiteten till lämpliga roller.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Skapa en roll tilldelning för händelse nav i olika omfång 
Följande CLI-exempel visar hur du lägger till en anpassad ämnes identitet till **Azure Event Hubs data Sender** -rollen på namn områdes nivån eller på händelsehubben. Om du skapar roll tilldelningen på namn områdes nivån kan det anpassade avsnittet vidarebefordra händelser till alla händelse nav i namn området. Om du skapar en roll tilldelning på händelse Hub-nivån kan det anpassade avsnittet endast vidarebefordra händelser till just den aktuella händelsehubben. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Skapa en roll tilldelning för ett Service Bus ämne i olika omfång 
Följande CLI-exempel visar hur du lägger till en anpassad ämnes identitet i Event Grid i rollen **Azure Service Bus data avsändare** på namn områdes nivå eller på Service Bus ämnes nivå. Om du skapar roll tilldelningen på namn områdes nivån kan Event Grid-avsnittet vidarebefordra händelser till alla entiteter (Service Bus köer eller ämnen) inom det namn området. Om du skapar en roll tilldelning på Service Bus kö-eller ämnes nivå kan det anpassade avsnittet Event Grid vidarebefordra händelser enbart till den aktuella Service Bus kön eller ämnet. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Skapa händelse prenumerationer som använder en identitet
När du har ett anpassat Event Grid-ämne eller en domän med en Systemhanterad identitet och lagt till identiteten i lämplig roll på målet, är du redo att skapa prenumerationer som använder identiteten. 

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
Mer information om hanterade tjänst identiteter finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). 
