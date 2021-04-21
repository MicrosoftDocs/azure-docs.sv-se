---
title: Övervaka Key Vault med Azure Event Grid
description: Använda Azure Event Grid för att prenumerera Key Vault händelser
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 4fb6d57bb84f4a3b4c5c138be9306489191bfce8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753370"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Övervaka Key Vault med Azure Event Grid

Key Vault integrering med Event Grid kan användarna meddelas när statusen för en hemlighet som lagras i nyckelvalvet har ändrats. En statusändring definieras som en hemlighet som snart upphör att gälla (30 dagar före förfallodatum), en hemlighet som har upphört att gälla eller en hemlighet som har en ny tillgänglig version. Meddelanden för alla tre typer av hemligheter (nyckel, certifikat och hemlighet) stöds.

Program kan reagera på dessa händelser med hjälp av moderna serverlösa arkitekturer, utan behov av komplicerad kod eller dyra och ineffektiva avsökningstjänster. Händelser skickas via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till händelsehanterare som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med till din egen webhook, och du betalar bara för det du använder. Information om priser finns i [Event Grid priser.](https://azure.microsoft.com/pricing/details/event-grid/)

## <a name="key-vault-events-and-schemas"></a>Key Vault händelser och scheman

Event Grid använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Key Vault händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera Key Vault händelse eftersom egenskapen eventType börjar med "Microsoft.KeyVault".

Mer information finns i Key Vault [händelseschemat](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Meddelandehändelser utlöses endast på nya versioner av hemligheter, nycklar och certifikat, och du måste först prenumerera på händelsen i ditt nyckelvalv för att kunna ta emot dessa meddelanden.

## <a name="practices-for-consuming-events"></a>Metoder för att använda händelser

Program som hanterar Key Vault händelser bör följa några rekommenderade metoder:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare. Det är viktigt att inte anta att händelser kommer från en viss källa, utan att kontrollera ämnet i meddelandet för att se till att det kommer från nyckelvalvet som du förväntar dig.
* Kontrollera på samma sätt att eventType är en som du är beredd att bearbeta och anta inte att alla händelser som du tar emot är de typer som du förväntar dig.
* Ignorera fält som du inte förstår.  Den här övningen hjälper dig att hålla dig motståndskraftig mot nya funktioner som kan läggas till i framtiden.
* Använd prefixet "ämne" och suffixmatchningar för att begränsa händelser till en viss händelse.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault översikt](overview.md)
- [Översikt för Azure Event Grid](../../event-grid/overview.md)
- Gör så här: [Dirigera Key Vault händelser till Automation Runbook](event-grid-tutorial.md).
- Gör så här: Ta [emot e-post när en nyckelvalvshemlighet ändras](event-grid-logicapps.md)
- [Azure Event Grid händelseschema för Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Azure Automation översikt](../../automation/index.yml)
