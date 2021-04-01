---
title: Azure Service Bus – pausa meddelande enheter
description: Den här artikeln förklarar hur du tillfälligt inaktiverar och återaktiverar Azure Service Bus meddelande enheter (köer, ämnen och prenumerationer).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94543059"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pausa och återaktivera meddelande enheter (inaktivera)

Köer, ämnen och prenumerationer kan tillfälligt avbrytas. SUS Pension placerar entiteten i ett inaktiverat tillstånd där alla meddelanden bevaras i lagrings utrymmet. Meddelanden kan dock inte tas bort eller läggas till, och motsvarande protokoll åtgärder ger fel.

Du kanske vill inaktivera en entitet för brådskande administrativa orsaker. Till exempel tar en trasig mottagare bort meddelanden från kön, Miss lyckas bearbetningen och har ännu felaktigt slutfört meddelandena och tar bort dem. I det här fallet kanske du vill inaktivera kön för mottagning tills du har korrigerat och distribuerat koden. 

En SUS pension eller reaktivering kan utföras antingen av användaren eller av systemet. Systemet pausar bara entiteter på grund av de administrativa skäl som är av sekretesskäl, till exempel när prenumerations utgifts gränsen påträffas. System-inaktiverade entiteter kan inte återaktiveras av användaren, men återställs när orsaken till uppskjutningen har åtgärd ATS.

## <a name="queue-status"></a>Status för kö 
De tillstånd som kan ställas in för en **kö** är:

-   **Aktiv**: kön är aktiv. Du kan skicka meddelanden till och ta emot meddelanden från kön. 
-   **Inaktive rad**: kön har pausats. Det motsvarar att ställa in både **SendDisabled** och **ReceiveDisabled**. 
-   **SendDisabled**: det går inte att skicka meddelanden till kön, men du kan ta emot meddelanden från den. Du får ett undantag om du försöker skicka meddelanden till kön. 
-   **ReceiveDisabled**: du kan skicka meddelanden till kön, men du kan inte ta emot meddelanden från den. Du får ett undantag om du försöker ta emot meddelanden till kön.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Ändra köns status i Azure Portal: 

1. I Azure Portal navigerar du till Service Bus namn området. 
1. Välj den kö som du vill ändra status för. Du ser köer i det nedre fönstret i mitten. 
1. På sidan **Service Bus kö** , se köns aktuella status som en hyperlänk. Om **översikten** inte är markerad på den vänstra menyn väljer du den för att se köns status. Välj aktuell status för kön för att ändra den. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Välj status för kön":::
4. Välj ny status för kön och välj **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Ange tillstånd för kön":::
    
Du kan också inaktivera åtgärderna skicka och ta emot med hjälp av Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -API: er i .NET SDK eller genom att använda en Azure Resource Manager-mall via Azure CLI eller Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Ändra köns status med hjälp av Azure PowerShell
PowerShell-kommandot för att inaktivera en kö visas i följande exempel. Kommandot reaktive ras är detsamma, `Status` inställningen **aktiv**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Ämnes status
Du kan ändra ämnes status i Azure Portal. Välj aktuell status för avsnittet om du vill se följande sida, vilket gör att du kan ändra status. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Ändra ämnes status":::

De tillstånd som kan ställas in för ett **ämne** är:
- **Aktiv**: ämnet är aktivt. Du kan skicka meddelanden till ämnet. 
- **Inaktive rad**: avsnittet har pausats. Du kan inte skicka meddelanden till ämnet. 
- **SendDisabled**: samma resultat som **inaktive rad**. Du kan inte skicka meddelanden till ämnet. Du får ett undantag om du försöker skicka meddelanden till ämnet. 

## <a name="subscription-status"></a>Prenumerations status
Du kan ändra prenumerations status i Azure Portal. Välj aktuell status för prenumerationen för att se följande sida, vilket gör att du kan ändra status. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Ändra prenumerations status":::

De tillstånd som kan ställas in för en **prenumeration** är:
- **Aktiv**: prenumerationen är aktiv. Du kan ta emot meddelanden frm prenumerationen.
- **Inaktive rad**: prenumerationen har pausats. Du kan inte ta emot meddelanden från prenumerationen. 
- **ReceiveDisabled**: samma resultat som **inaktive rad**. Du kan inte ta emot meddelanden från prenumerationen. Du får ett undantag om du försöker ta emot meddelanden till prenumerationen.

| Ämnes status | Prenumerations status | Beteende | 
| ------------ | ------------------- | -------- | 
| Aktiv | Aktiv | Du kan skicka meddelanden till ämnet och ta emot meddelanden från prenumerationen. | 
| Aktiv | Inaktive rad eller inaktive rad | Du kan skicka meddelanden till ämnet, men du kan inte ta emot meddelanden från prenumerationen | 
| Inaktiverat eller skickat inaktiverat | Aktiv | Du kan inte skicka meddelanden till ämnet, men du kan ta emot meddelanden som redan finns i prenumerationen. | 
| Inaktiverat eller skickat inaktiverat | Inaktive rad eller inaktive rad | Du kan inte skicka meddelanden till ämnet och du kan inte ta emot från prenumerationen. | 

## <a name="other-statuses"></a>Andra statusar
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) -uppräkningen definierar också en uppsättning över gångs tillstånd som bara kan ställas in av systemet. 


## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

