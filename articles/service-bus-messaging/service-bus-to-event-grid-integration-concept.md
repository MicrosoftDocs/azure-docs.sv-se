---
title: Översikt över integration av Azure Service Bus till Event Grid | Microsoft Docs
description: Den här artikeln innehåller en beskrivning av hur Azure Service Bus Messaging integreras med Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 71ee21c971b71c4000a123d1561e7e93d21203e1
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629155"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Översikt över integration av Azure Service Bus till Event Grid

Azure Service Bus har en ny integration till Azure Event Grid. Detta möjliggör scenarier där Service Bus-köer eller prenumerationer med en låg volym av meddelanden inte behöver ha någon mottagare som kontinuerligt söker efter meddelanden. 

Nu kan Service Bus generera händelser till Event Grid när det finns meddelanden i en kö eller prenumeration utan att det finns några mottagare. Du kan skapa Event Grid-prenumerationer till Service Bus-namnområden, lyssna på dessa händelser och sedan reagera på dem genom att starta en mottagare. Med den här funktionen kan du använda Service Bus i reaktiva programmeringsmodeller.

Om du vill aktivera funktionen behöver du följande:

* Ett Service Bus Premium-namnområde med minst en Service Bus-kö eller ett Service Bus-ämne med minst en prenumeration.
* Deltagaråtkomst till Service Bus-namnområdet.
* Dessutom behöver du en prenumeration på Event Grid för Service Bus-namnområdet. Den här prenumerationen får ett meddelande från Event Grid om det finns meddelanden som ska hämtas. Vanliga prenumeranter kan vara Logic Apps-funktionen i Azure App Service, Azure Functions eller en webhook som kontaktar en webbapp. Prenumeranten bearbetar sedan dessa meddelanden. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>Kontrollera att du har deltagarbehörighet
Gå till Service Bus namn området och välj sedan **åtkomst kontroll (IAM)** och välj fliken **roll tilldelningar** . Kontrol lera att du har deltagar åtkomst till namn området. 

### <a name="events-and-event-schemas"></a>Händelser och händelsescheman

Service Bus skickar händelser för två scenarier:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#deadletter-messages-available-event)
* [ActiveMessagesAvailablePeriodicNotifications](#active-messages-available-periodic-notifications)
* [DeadletterMessagesAvailablePeriodicNotifications](#deadletter-messages-available-periodic-notifications)

Dessutom använder Service Bus standardsäkerhet för Event Grid och [autentiseringsmekanismer](../event-grid/security-authentication.md).

Mer information finns i [Azure Event Grid event schemas](../event-grid/event-schema.md) (Händelsescheman i Azure Event Grid).

#### <a name="active-messages-available-event"></a>Aktiva meddelanden, tillgänglig händelse

Den här händelsen genereras om det finns aktiva meddelanden i en kö eller prenumeration och inga mottagare lyssnar.

Schemat för den här händelsen är följande:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="deadletter-messages-available-event"></a>Händelse för tillgängliga obeställbara meddelanden kön-meddelanden

Du kan hämta minst en händelse per kö för obeställbara meddelanden, som innehåller meddelanden utan aktiva mottagare.

Schemat för den här händelsen är följande:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Tillgängliga periodiska meddelanden för aktiva meddelanden

Den här händelsen genereras regelbundet om du har aktiva meddelanden i den aktuella kön eller prenumerationen, även om det finns aktiva lyssnare på den aktuella kön eller prenumerationen.

Schemat för händelsen är följande.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Obeställbara meddelanden kön meddelanden tillgängliga periodiska meddelanden

Den här händelsen genereras regelbundet om du har obeställbara meddelanden kön meddelanden i den aktuella kön eller prenumerationen, även om det finns aktiva lyssnare på obeställbara meddelanden kön entiteten för den aktuella kön eller prenumerationen.

Schemat för händelsen är följande.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Hur många händelser genereras, och hur ofta?

Om du har flera köer och ämnen eller prenumerationer i namnområdet, får du minst en händelse per kö och en per prenumeration. Händelserna genereras omedelbart om det inte finns några meddelanden i Service Bus-entiteten och ett nytt meddelande anländer. Eller så genereras händelserna varannan minut, om inte Service Bus identifierar en aktiv mottagare. Bläddring bland meddelanden avbryter inte händelserna.

Som standard genererar Service Bus händelser för alla entiteter i namnområdet. Om du enbart vill hämta händelser för specifika entiteter kan du läsa mer i nästa avsnitt.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Använda filter för att begränsa var du hämtar händelser från

Om du enbart vill hämta händelser från exempelvis en kö eller en prenumeration inom namnområdet, kan du använda filtren *Börjar med* eller *Slutar med* som finns i Event Grid. I vissa gränssnitt kallas filtren *För* och *Suffix*. Om du vill hämta händelser för flera, men inte alla, köer och prenumerationer, kan du skapa flera Event Grid-prenumerationer och ange ett filter för varje.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Skapa Event Grid-prenumerationer för Service Bus-namnområden

Du kan skapa Event Grid-prenumerationer för Service Bus-namnområden på tre sätt:

* I Azure-portalen
* I [Azure CLI](#azure-cli-instructions)
* I [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Instruktioner för Azure Portal

Så här skapar du en ny Event Grid-prenumeration:
1. Gå till ditt namnområde i Azure Portal.
2. Välj **Event Grid** i rutan till vänster. 
3. Välj **händelse prenumeration**.  

   Följande bild visar ett namnområde som har en Event Grid-prenumeration:

   ![Event Grid-prenumerationer](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   Följande bild visar hur du prenumererar på en funktion eller en webhook utan någon specifik filtrering:

   ![21][]

## <a name="azure-cli-instructions"></a>Azure CLI-instruktioner

Kontrollera först att du har Azure CLI version 2.0 eller senare installerad. [Hämta installations programmet](/cli/azure/install-azure-cli). Välj **Windows + X** och öppna sedan en ny PowerShell-konsol med administratörs behörighet. Du kan också använda en kommandotolk i Azure Portal.

Kör följande kod:

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Om du använder BASH 

## <a name="powershell-instructions"></a>PowerShell-instruktioner

Kontrollera att du har Azure PowerShell installerat. [Hämta installations programmet](/powershell/azure/install-Az-ps). Välj **Windows + X** och öppna sedan en ny PowerShell-konsol med administratörsbehörighet. Du kan också använda en kommandotolk i Azure Portal.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Härifrån kan du utforska andra installationsalternativ eller testa om händelser flödar in.

## <a name="next-steps"></a>Nästa steg

* Hämta Service Bus- och Event Grid-[exempel](service-bus-to-event-grid-integration-example.md).
* Läs mer om [Event Grid](../event-grid/index.yml).
* Läs mer om [Azure Functions](../azure-functions/index.yml).
* Läs mer om [Logic Apps](../logic-apps/index.yml).
* Läs mer om [Service Bus](/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19.3]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[30]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
