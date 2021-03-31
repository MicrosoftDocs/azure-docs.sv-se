---
title: Azure Service Bus-antal meddelanden
description: 'Hämta antalet meddelanden som lagras i köer och prenumerationer med hjälp av Azure Resource Manager och Azure Service Bus NamespaceManager-API: er.'
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d0e1a7a5c6eb0b281b4e6ac08135f41f28ecbec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341270"
---
# <a name="message-counters"></a>Meddelanderäknare

Du kan hämta antalet meddelanden som lagras i köer och prenumerationer genom att använda Azure Resource Manager och Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -API: er i .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Med PowerShell kan du hämta antalet på följande sätt:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Information om antal meddelanden

Att veta om antalet aktiva meddelanden är användbart när du avgör om en kö bygger upp en efter släpning som kräver mer resurser för att bearbeta än vad som för närvarande har distribuerats. Följande räknar information finns i [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) -klassen:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): meddelanden i kön eller prenumerationen som är i aktivt läge och redo för leverans.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): meddelanden i kön för obeställbara meddelanden.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): meddelanden i schemalagt tillstånd.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): meddelanden som inte kunde överföras till en annan kö eller ett ämne och som har flyttats till överförings kön för obeställbara meddelanden.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): meddelanden som väntar på överföring till en annan kö eller ämne.

Om ett program vill skala resurser baserat på köns längd, bör det göra det med en uppmätt hastighet. Hämtningen av meddelande räknarna är en dyr åtgärd i meddelande utjämningen och kör den ofta direkt och påverkar enhetens prestanda negativt.

> [!NOTE]
> Meddelanden som skickas till ett Service Bus ämne vidarebefordras till prenumerationer för det avsnittet. Detta innebär att antalet aktiva meddelanden i själva avsnittet är 0, eftersom dessa meddelanden har vidarebefordrats till prenumerationen. Hämta antalet meddelanden i prenumerationen och kontrol lera att det är större än 0. Även om du ser meddelanden i prenumerationen lagras de faktiskt i en lagrings plats som ägs av ämnet. 

Om du tittar på prenumerationerna skulle de ha antal meddelanden som inte är noll (vilket ger till 323MB utrymme för hela entiteten).

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
