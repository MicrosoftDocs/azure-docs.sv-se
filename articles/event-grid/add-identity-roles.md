---
title: Lägg till hanterad identitet till en roll på Azure Event Grid destination
description: Den här artikeln beskriver hur du lägger till hanterad identitet till Azure-roller på mål som Azure Service Bus och Azure Event Hubs.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1578e4c24201614ce89351b3c3cee52a09cadc30
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280487"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>Bevilja hanterad identitet åtkomst till Event Grid destination
I det här avsnittet beskrivs hur du lägger till identiteten för ditt system ämne, anpassade ämne eller domän till en Azure-roll. 

## <a name="prerequisites"></a>Förutsättningar
Tilldela en systemtilldelad hanterad identitet genom att följa anvisningarna i följande artiklar:

- [Anpassade ämnen eller domäner](enable-identity-custom-topics-domains.md)
- [Systemämnen](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Destinationer och Azure-roller som stöds
När du har aktiverat identitet för ditt anpassade ämne eller domän i Event Grid skapar Azure en identitet automatiskt i Azure Active Directory. Lägg till den här identiteten i lämpliga Azure-roller så att det anpassade ämnet eller domänen kan vidarebefordra händelser till destinationer som stöds. Du kan till exempel lägga till identiteten i rollen **azure Event Hubs data avsändare** för ett Azure Event Hubs-namnområde så att det anpassade avsnittet om Event Grid kan vidarebefordra händelser till händelse nav i det namn området. 

För närvarande stöder Azure Event Grid anpassade ämnen eller domäner som kon figurer ATS med en systemtilldelad hanterad identitet för att vidarebefordra händelser till följande mål. I den här tabellen får du också de roller som identiteten ska vara i så att det anpassade ämnet kan vidarebefordra händelserna.

| Mål | Azure-roll | 
| ----------- | --------- | 
| Service Bus köer och ämnen | [Azure Service Bus data avsändare](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs data avsändare](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Storage Blob Data-deltagare](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Avsändare av data meddelande i lagrings köer](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Använda Azure-portalen
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

## <a name="use-the-azure-cli"></a>Använda Azure CLI
Exemplet i det här avsnittet visar hur du använder Azure CLI för att lägga till en identitet i en Azure-roll. Exempel kommandona är för anpassade ämnen i Event Grid. Kommandona för events Grid-domäner liknar varandra. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Hämta ägar-ID för det anpassade ämnets system identitet 
Först hämtar du huvud-ID för det anpassade ämnets systemhanterade identitet och tilldelar identiteten till lämpliga roller.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Skapa en roll tilldelning för händelse nav i olika omfång 
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

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Skapa en roll tilldelning för ett Service Bus ämne i olika omfång 
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

## <a name="next-steps"></a>Nästa steg
Nu när du har tilldelat en tilldelad identitet till ditt system ämne, anpassat ämne eller domän och lagt till identiteten för lämpliga roller på destinationer, se [leverera händelser med den hanterade identiteten](managed-service-identity.md) för att leverera händelser till mål med hjälp av identiteten.


