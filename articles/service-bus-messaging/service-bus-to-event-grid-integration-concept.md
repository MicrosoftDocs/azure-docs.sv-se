---
title: Översikt över integration av Azure Service Bus till Event Grid | Microsoft Docs
description: Den här artikeln innehåller en beskrivning av hur Azure Service Bus Messaging integreras med Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369670"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Översikt över integration av Azure Service Bus till Event Grid
Nu kan Service Bus generera händelser till Event Grid när det finns meddelanden i en kö eller prenumeration utan att det finns några mottagare. Du kan skapa Event Grid-prenumerationer till Service Bus-namnområden, lyssna på dessa händelser och sedan reagera på dem genom att starta en mottagare. Med den här funktionen kan du använda Service Bus i reaktiva programmeringsmodeller. Detta möjliggör scenarier där Service Bus-köer eller prenumerationer med en låg volym av meddelanden inte behöver ha någon mottagare som kontinuerligt söker efter meddelanden. 

Om du vill aktivera funktionen behöver du följande:

* Ett Service Bus Premium-namnområde med minst en Service Bus-kö eller ett Service Bus-ämne med minst en prenumeration.
* Deltagaråtkomst till Service Bus-namnområdet. Navigera till Service Bus namn området i Azure Portal och välj sedan **åtkomst kontroll (IAM)** och välj fliken **roll tilldelningar** . Kontrol lera att du har deltagar åtkomst till namn området. 
* Dessutom behöver du en prenumeration på Event Grid för Service Bus-namnområdet. Den här prenumerationen får ett meddelande från Event Grid om det finns meddelanden som ska hämtas. Vanliga prenumeranter kan vara Logic Apps-funktionen i Azure App Service, Azure Functions eller en webhook som kontaktar en webbapp. Prenumeranten bearbetar sedan dessa meddelanden. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Event Grid prenumerationer för Service Bus namn områden
Du kan skapa Event Grid-prenumerationer för Service Bus-namnområden på tre sätt:

- Azure Portal. I följande självstudier lär du dig hur du använder Azure Portal för att skapa Event Grid-prenumerationer för Service Bus händelser med Azure Logic Apps och Azure Functions som hanterare. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI. Följande CLI-exempel visar hur du skapar en Azure Functions-prenumeration för ett [system avsnitt](../event-grid/system-topics.md) som skapats av ett Service Bus namn område.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Här är ett exempel:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Hur många händelser genereras, och hur ofta?

Om du har flera köer och ämnen eller prenumerationer i namnområdet, får du minst en händelse per kö och en per prenumeration. Händelserna genereras omedelbart om det inte finns några meddelanden i Service Bus-entiteten och ett nytt meddelande anländer. Eller så genereras händelserna varannan minut, om inte Service Bus identifierar en aktiv mottagare. Bläddring bland meddelanden avbryter inte händelserna.

Som standard genererar Service Bus händelser för alla entiteter i namnområdet. Om du enbart vill hämta händelser för specifika entiteter kan du läsa mer i nästa avsnitt.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Använda filter för att begränsa var du hämtar händelser från

Om du enbart vill hämta händelser från exempelvis en kö eller en prenumeration inom namnområdet, kan du använda filtren *Börjar med* eller *Slutar med* som finns i Event Grid. I vissa gränssnitt kallas filtren *För* och *Suffix*. Om du vill hämta händelser för flera, men inte alla, köer och prenumerationer, kan du skapa flera Event Grid-prenumerationer och ange ett filter för varje.

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurser: 
- [Azure Logic Apps för att hantera Service Bus meddelanden som tas emot via Event Grid](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions för att hantera Service Bus meddelanden som tas emot via Event Grid](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19.3]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
