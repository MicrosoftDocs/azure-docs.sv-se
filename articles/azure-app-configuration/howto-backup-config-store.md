---
title: Säkerhetskopiera nyckel värden automatiskt från Azure App konfigurations lager
description: Lär dig hur du konfigurerar en automatisk säkerhets kopiering av nyckel värden mellan konfigurations lager för appar.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454251"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Säkerhetskopiera konfigurations Arkiv för appar automatiskt

I den här artikeln får du lära dig hur du konfigurerar en automatisk säkerhets kopiering av nyckel värden från en primär Azure App konfigurations lagring till en sekundär lagrings plats. Den automatiska säkerhets kopieringen använder integrering av Azure Event Grid med app-konfiguration. 

När du har konfigurerat den automatiska säkerhets kopieringen kommer app-konfigurationen att publicera händelser till Azure Event Grid för alla ändringar som görs i nyckel värden i ett konfigurations lager. Event Grid stöder en mängd olika Azure-tjänster där användare kan prenumerera på de händelser som genereras när nyckel värden skapas, uppdateras eller tas bort.

## <a name="overview"></a>Översikt

I den här artikeln använder du Azure Queue Storage för att ta emot händelser från Event Grid och använda en timer-utlösare för Azure Functions för att bearbeta händelser i kön i batchar. 

När en funktion utlöses, baserat på händelser, hämtar den de senaste värdena för de nycklar som har ändrats från konfigurations arkivet för den primära appen och uppdaterar den sekundära lagringen på motsvarande sätt. Med den här inställningen kan du kombinera flera ändringar som sker under en kort period i en säkerhets kopierings åtgärd, vilket gör att det inte finns orimliga begär Anden som görs till dina konfigurations lager

![Diagram som visar arkitekturen för säkerhets kopian av appens konfigurations arkiv.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Resursetablering

Motivation bakom att säkerhetskopiera konfigurations lager för appar är att använda flera konfigurations lager i olika Azure-regioner för att öka geo-återhämtningen för ditt program. För att uppnå detta bör dina primära och sekundära lager finnas i olika Azure-regioner. Alla andra resurser som skapas i den här självstudien kan tillhandahållas i valfri region. Detta beror på att om den primära regionen är nere, kommer det inte att finnas några nya att säkerhetskopiera förrän den primära regionen är tillgänglig igen.

I den här självstudien skapar du en sekundär butik i `centralus` regionen och alla andra resurser i `westus` regionen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Förutsättningar 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen Azure Development.

- [.Net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- I den här självstudien krävs version 2.3.1 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group).

I följande exempel skapas en resursgrupp med namnet `<resource_group_name>` på platsen `westus`.  Ersätt `<resource_group_name>` med ett unikt namn för din resursgrupp.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Skapa konfigurations lager för appar

Skapa dina primära och sekundära konfigurations lager för appar i olika regioner.
Ersätt `<primary_appconfig_name>` och `<secondary_appconfig_name>` med unika namn för dina konfigurations lager. Varje Arkiv namn måste vara unikt eftersom det används som ett DNS-namn.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Skapa en kö

Skapa ett lagrings konto och en kö för att ta emot de händelser som publiceras av Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Prenumerera på dina program konfigurations lagrings händelser

Du prenumererar på dessa två händelser från den primära appens konfigurations Arkiv:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Följande kommando skapar en Event Grid-prenumeration för de två händelser som skickas till din kö. Slut punkts typen anges till `storagequeue` och slut punkten anges till kön-ID. Ersätt `<event_subscription_name>` med namnet på ditt val för händelse prenumerationen.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Skapa funktioner för att hantera händelser från Queue Storage

### <a name="set-up-with-ready-to-use-functions"></a>Konfigurera med färdiga funktioner

I den här artikeln arbetar du med C#-funktioner som har följande egenskaper:
- Körnings stack .NET Core 3,1
- Azure Functions körnings version 3. x
- Funktionen utlöses av en timer var tionde minut

För att göra det enklare för dig att börja säkerhetskopiera dina data har vi [testat och publicerat en funktion](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) som du kan använda utan att göra några ändringar i koden. Ladda ned projektfilerna och [publicera dem till din egen Azure Function-app från Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Gör inga ändringar i miljövariablerna i den kod som du har laddat ned. Du ska skapa de appinställningar som krävs i nästa avsnitt.
>

### <a name="build-your-own-function"></a>Bygg din egen funktion

Om den exempel kod som angavs tidigare inte uppfyller dina krav kan du också skapa en egen funktion. Funktionen måste kunna utföra följande aktiviteter för att kunna slutföra säkerhets kopieringen:
- Läs innehållet i din kö med jämna mellanrum för att se om det innehåller några meddelanden från Event Grid. Se SDK för [lagrings kön](../storage/queues/storage-quickstart-queues-dotnet.md) för implementerings information.
- Om kön innehåller [händelse meddelanden från Event Grid](./concept-app-configuration-event.md#event-schema)extrahera all unik `<key, label>` information från händelse meddelanden. Kombinationen av nyckel och etikett är den unika identifieraren för nyckel värdes ändringar i det primära lagret.
- Läs alla inställningar från det primära lagret. Uppdatera endast de inställningar i den sekundära lagringen som har en motsvarande händelse i kön. Ta bort alla inställningar från den sekundära lagrings platsen som fanns i kön men inte i den primära lagringen. Du kan använda [app Configuration SDK](https://github.com/Azure/AppConfiguration#sdks) för att få åtkomst till dina konfigurations lager program mässigt.
- Ta bort meddelanden från kön om det inte fanns några undantag under bearbetningen.
- Implementera fel hantering enligt dina behov. Se föregående kod exempel för att se några vanliga undantag som du kanske vill hantera.

Mer information om hur du skapar en funktion finns i: [skapa en funktion i Azure som utlöses av en timer](../azure-functions/functions-create-scheduled-function.md) och [utveckla Azure Functions med Visual Studio](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> Använd din bästa bedömning för att välja det tidsinställda schemat utifrån hur ofta du gör ändringar i din primära konfigurations lagring. Att köra funktionen för ofta kan leda till begränsnings begär Anden för butiken.
>


## <a name="create-function-app-settings"></a>Skapa funktions program inställningar

Om du använder en funktion som vi har tillhandahållit behöver du följande appinställningar i din Function-app:
- `PrimaryStoreEndpoint`: Slut punkt för konfigurations arkivet för den primära appen. Ett exempel är `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Slut punkt för konfigurations arkivet för den sekundära appen. Ett exempel är `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: Kö-URI. Ett exempel är `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Följande kommando skapar de appinställningar som krävs i Function-appen. Ersätt `<function_app_name>` med namnet på din Function-app.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Bevilja åtkomst till Function-appens hanterade identitet

Använd följande kommando eller [Azure Portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) för att lägga till en systemtilldelad hanterad identitet för din Function-app.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> För att utföra den nödvändiga resurs skapande-och roll hanteringen måste ditt konto ha `Owner` behörighet i rätt omfång (din prenumeration eller resurs grupp). Om du behöver hjälp med roll tilldelning kan du läsa mer om [hur du tilldelar Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

Använd följande kommandon eller [Azure Portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) för att bevilja den hanterade identiteten för din Function app-åtkomst till dina app Configuration-butiker. Använd följande roller:
- Tilldela `App Configuration Data Reader` rollen i konfigurations arkivet för den primära appen.
- Tilldela `App Configuration Data Owner` rollen i konfigurations arkivet för den sekundära appen.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Använd följande kommando eller [Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) för att ge den hanterade identiteten för din Function-app åtkomst till din kö. Tilldela `Storage Queue Data Contributor` rollen i kön.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Utlös en konfigurations händelse för appen

För att testa att allt fungerar kan du skapa, uppdatera eller ta bort ett nyckel värde från den primära butiken. Du bör automatiskt se den här ändringen i det sekundära arkivet några sekunder efter att timern utlöser Azure Functions.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Du har utlöst händelsen. Under en liten stund skickar Event Grid händelse aviseringen till kön. *Efter nästa schemalagda körning av funktionen* kan du Visa konfigurations inställningarna i den sekundära lagrings platsen för att se om det innehåller det uppdaterade nyckel värdet från det primära lagret.

> [!NOTE]
> Du kan [utlösa din funktion manuellt](../azure-functions/functions-manually-run-non-http.md) under testningen och fel sökningen utan att vänta på schemalagd timer-utlösare.

När du har kontrollerat att säkerhets kopierings funktionen har körts kan du se att nyckeln nu finns i ditt sekundära arkiv.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Felsökning

Om du inte ser den nya inställningen i det sekundära arkivet:

- Se till att säkerhets kopierings funktionen utlöstes *efter* att du har skapat inställningen i ditt primära arkiv.
- Det är möjligt att Event Grid inte kunde skicka händelse meddelandet till kön i tid. Kontrol lera om din kö fortfarande innehåller händelse meddelandet från ditt primära arkiv. Om det gör det utlöses säkerhets kopierings funktionen igen.
- Kontrol lera [Azure Functions loggar](../azure-functions/functions-create-scheduled-function.md#test-the-function) för fel eller varningar.
- Använd [Azure Portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) för att säkerställa att Azure Function-appen innehåller korrekta värden för de program inställningar som Azure Functions försöker läsa.
- Du kan också konfigurera övervakning och aviseringar för Azure Functions med hjälp av [Azure Application insikter](../azure-functions/functions-monitoring.md?tabs=cmd). 


## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här program konfigurationen och händelse prenumerationen ska du inte rensa resurserna som du skapade i den här artikeln. Om du inte planerar att fortsätta använder du följande kommando för att ta bort resurserna som skapats i den här artikeln.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du konfigurerar automatisk säkerhets kopiering av dina nyckel värden kan du läsa mer om hur du kan öka geo-återhämtningen för ditt program:

- [Återhämtning och haveriberedskap](concept-disaster-recovery.md)
