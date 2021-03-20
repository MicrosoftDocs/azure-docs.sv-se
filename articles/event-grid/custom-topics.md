---
title: Anpassade ämnen i Azure Event Grid
description: Beskriver anpassade ämnen i Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86113793"
---
# <a name="custom-topics-in-azure-event-grid"></a>Anpassade ämnen i Azure Event Grid
Ett event Grid-ämne innehåller en slut punkt där källan skickar händelser. Avsnittet Event Grid skapas i utgivaren och avgör om en händelse källa behöver ett ämne eller mer än ett ämne. Ett ämne används för en samling relaterade händelser. För att svara på vissa typer av händelser, bestämmer prenumeranter vilka ämnen som ska prenumerera på.

**Anpassade ämnen** är program-och tredje parts ämnen. När du skapar eller tilldelas åtkomst till ett anpassat ämne visas detta anpassade ämne i din prenumeration. 

När du designar ditt program är du flexibel när du bestämmer hur många ämnen som ska skapas. Skapa ett **anpassat ämne** för **varje kategori av relaterade händelser** för stora lösningar. Tänk dig ett program som skickar händelser som handlar om att ändra användarkonton och bearbeta beställningar. Det är osannolikt alla händelsehanteraren vill ha båda händelsekategorier. Skapa två anpassade ämnen och låt händelsehanteraren prenumerera på det mest relevanta. För små lösningar kanske du föredrar att skicka alla händelser till ett enda ämne. Händelse prenumeranter kan filtrera efter de händelse typer som de vill ha.

## <a name="event-schema"></a>Händelseschema
En detaljerad översikt över händelse schema finns i [Azure Event Grid Event schema](event-schema.md). För anpassade ämnen bestämmer händelse utgivaren **dataobjektet** . Data på den översta nivån ska ha samma fält som standard resurs definierade händelser.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

I följande avsnitt finns länkar till självstudier för att skapa anpassade ämnen med hjälp av Azure Portal-, CLI-, PowerShell-och Azure Resource Manager-mallar (ARM). 


## <a name="azure-portal-tutorials"></a>Azure Portal själv studie kurser
|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: skapa och dirigera anpassade händelser med Azure Portal](custom-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka anpassade händelser. |
| [Snabb start: dirigera anpassade händelser till Azure Queue Storage](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue Storage. |
| [Gör så här: skicka till anpassat ämne](post-to-custom-topic.md) | Visar hur du publicerar en händelse i ett anpassat ämne. |


## <a name="azure-cli-tutorials"></a>Azure CLI-självstudier
|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: skapa och dirigera anpassade händelser med Azure CLI](custom-event-quickstart.md) | Visar hur du använder Azure CLI för att skicka anpassade händelser. |
| [Azure CLI: skapa Event Grid anpassat ämne](./scripts/event-grid-cli-create-custom-topic.md)|Exempel skript som skapar ett anpassat ämne. Skriptet hämtar slut punkten och en nyckel.|
| [Azure CLI: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-cli-subscribe-custom-topic.md)|Exempel skript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en webhook.|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell själv studie kurser
|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: skapa och dirigera anpassade händelser med Azure PowerShell](custom-event-quickstart-powershell.md) | Visar hur du använder Azure PowerShell för att skicka anpassade händelser. |
| [PowerShell: skapa Event Grid anpassat ämne](./scripts/event-grid-powershell-create-custom-topic.md)|Exempel skript som skapar ett anpassat ämne. Skriptet hämtar slut punkten och en nyckel.|
| [PowerShell: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Exempel skript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en webhook.|

## <a name="arm-template-tutorials"></a>Själv studie kurser om ARM-mallar
|Rubrik  |Beskrivning  |
|---------|---------|
| [Resource Manager-mall: anpassad ämne och webhook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | En Resource Manager-mall som skapar ett anpassat ämne och en prenumeration för det anpassade ämnet. Den skickar händelser till en webhook. |
| [Resource Manager-mall: anpassat ämne och Event Hubs slut punkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure-Event Hubs. |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Systemämnen](system-topics.md)
- [Domäner](event-domains.md)