---
title: 'Självstudie: flödes princip tillstånd ändra händelser till Event Grid med Azure CLI'
description: I den här självstudien konfigurerar du Event Grid att lyssna efter princip tillstånds ändrings händelser och anropa en webhook.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735549"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Självstudie: flödes princip tillstånd ändra händelser till Event Grid med Azure CLI

I den här artikeln får du lära dig hur du konfigurerar Azure Policy händelse prenumerationer för att skicka princip tillstånds ändrings händelser till en webb slut punkt. Azure Policy användare kan prenumerera på händelser som släpps när princip tillstånds ändringar sker på resurser. Dessa händelser kan utlösa Webhooks, [Azure Functions](../../../azure-functions/index.yml), [Azure Storage köer](../../../storage/queues/index.yml)eller andra händelse hanterare som stöds av [Azure Event Grid](../../../event-grid/index.yml). Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. För att förenkla den här självstudien skickar du dock händelserna till en webbapp som samlar in och visar meddelandena.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Den här snabb starten kräver att du kör Azure CLI version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

- Även om du tidigare har använt Azure Policy eller Event Grid registrerar du om deras respektive resurs leverantörer:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid-ämnen är Azure-resurser och måste placeras i en Azure-resursgrupp. Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). 

I följande exempel skapas en resurs grupp med namnet `<resource_group_name>` på den _västra_ platsen. Ersätt `<resource_group_name>` med ett unikt namn för din resursgrupp.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Avsnittet Skapa ett Event Grid system

Nu när vi har en resurs grupp skapar vi ett [system ämne](../../../event-grid/system-topics.md). Ett system avsnitt i Event Grid representerar en eller flera händelser som publicerats av Azure-tjänster som Azure Policy och Azure-Event Hubs. I det här system avsnittet används `Microsoft.PolicyInsights.PolicyStates` ämnes typen för att Azure policy status ändringar. Ersätt `<SubscriptionID>` i **omfattnings** parametern med ID: t för din prenumeration och `<resource_group_name>` i **resurs grupps** parametern med den tidigare skapade resurs gruppen.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Ersätt `<your-site-name>` med ett unikt namn för din webbapp. Webbappnamnet måste vara unikt eftersom det är en del av DNS-posten.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Webbplatsen bör visas utan några meddelanden.

## <a name="subscribe-to-the-system-topic"></a>Prenumerera på system-avsnittet

Du prenumererar på ett ämne därför att du vill ange för Event Grid vilka händelser du vill följa och vart du vill skicka händelserna. I följande exempel prenumererar du på det system avsnitt som du har skapat och skickar URL: en från din webbapp som slut punkt för att ta emot händelse meddelanden. Ersätt `<event_subscription_name>` med ett namn för din händelseprenumeration. För `<resource_group_name>` och `<your-site-name>` använder du det värde du skapade tidigare.

Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Skärm bild av händelsen Event Grid prenumerations validering i den förskapade webbappen.":::

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabb starten skapar du en princip tilldelning och tilldelar definitionen **Kräv en tagg för resurs grupper** . Den här princip definitionen identifierar resurs grupper som saknar taggen som kon figurer ATS under princip tilldelning.

Kör följande kommando för att skapa en princip tilldelning som är begränsad till resurs gruppen som du skapade för Event Grid-ämnet:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

Föregående kommando använder följande information:

- **Namn** – det faktiska namnet på tilldelningen. I det här exemplet användes _requiredtags-Events_ .
- **Visningsnamn** – Visningsnamn för principtilldelningen. I det här fallet använder du en _obligatorisk tagg på rg_.
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp. Formatet för ett resurs grupps omfång är `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **Policy** – Principdefinitions-ID:t som du använder som bas för att skapa tilldelningen. I det här fallet är det ID för princip definitionen som _kräver en tagg i resurs grupper_. För att hämta principdefinitionens ID kör du det här kommandot: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

När du har skapat princip tilldelningen väntar du tills ett **Microsoft. PolicyInsights. PolicyStateCreated** -händelse meddelande visas i webbappen. Resurs gruppen som vi skapade visar ett värde som inte är `data.complianceState` _kompatibelt_ att starta.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Skärm bild av händelsen Event Grid prenumerations princip status skapad för resurs gruppen i den förskapade webbappen.":::

> [!NOTE]
> Om resurs gruppen ärver andra princip tilldelningar från hierarkin för prenumerationer eller hanterings grupper visas även händelser för var och en. Bekräfta att händelsen är för tilldelningen i den här självstudien genom att utvärdera `data.policyDefinitionId` egenskapen.

## <a name="trigger-a-change-on-the-resource-group"></a>Utlös en ändring i resurs gruppen

En tagg med namnet **EventTest** krävs för att göra resurs gruppen kompatibel. Lägg till taggen i resurs gruppen med följande kommando `<SubscriptionID>` och Ersätt med ditt prenumerations-ID och `<ResourceGroup>` med namnet på resurs gruppen:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

När du har lagt till den nödvändiga taggen i resurs gruppen väntar du tills ett meddelande om att en **Microsoft. PolicyInsights. PolicyStateChanged** -händelse visas i webbappen. Expandera händelsen och `data.complianceState` värdet visar nu _kompatibel_.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med den här webbappen och Azure Policy händelse prenumerationen ska du inte rensa resurserna som du skapade i den här artikeln. Om du inte planerar att fortsätta använder du följande kommando för att ta bort de resurser som du skapade i den här artikeln.

Ersätt `<resource_group_name>` med resursgruppen du skapade ovan.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och händelse prenumerationer för Azure Policy kan du läsa mer om ändrings händelser för princip tillstånd och vilka Event Grid kan hjälpa dig:

- [Reagerar på händelser för Azure Policy tillstånds ändring](../concepts/event-overview.md)
- [Azure Policy schema information för Event Grid](../../../event-grid/event-schema-policy.md)
- [Om Event Grid](../../../event-grid/overview.md)