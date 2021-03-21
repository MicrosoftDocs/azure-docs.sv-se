---
title: Översikt över Azure cache för Redis Event Grid
description: Använd Azure Event Grid för att publicera Azure cache för Redis-händelser.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99056308"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Översikt över Azure cache för Redis Event Grid 

Azure cache för Redis-händelser, till exempel korrigerings-, skalnings-, import/export-(RDB) händelser skickas med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som Azure Functions, Azure Logic Apps eller till och med din egen http-lyssnare. Event Grid tillhandahåller tillförlitlig händelse leverans till dina program via omfattande principer för återförsök och obeställbara meddelanden.

Se artikeln [Azure cache for Redis Events schema](../event-grid/event-schema-azure-cache.md) för att visa en fullständig lista över händelser som Azure cache för Redis stöder.

Om du vill testa Azure cache för Redis-händelser kan du läsa följande snabb starter:

|Om du vill använda det här verktyget:    |Se den här snabb starten: |
|--|-|
|Azure Portal    |[Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkten med Azure Portal](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med PowerShell](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med Azure CLI](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Händelse modellen

Event Grid använder [händelse prenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Den här bilden illustrerar relationen mellan händelse utgivare, händelse prenumerationer och händelse hanterare.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Händelse rutnäts modell.":::

Börja med att prenumerera på en slut punkt till en händelse. När en händelse utlöses skickar Event Grid-tjänsten data om händelsen till slut punkten.

Se artikeln [Azure cache for Redis Events schema](../event-grid/event-schema-azure-cache.md) för att visa:

> [!div class="checklist"]
> * En fullständig lista över Azure cache för Redis-händelser och hur varje händelse utlöses.
> * Ett exempel på de data som Event Grid skulle skicka för var och en av dessa händelser.
> * Syftet med varje nyckel värdes par som visas i data.


## <a name="best-practices-for-consuming-events"></a>Metod tips för att konsumera händelser

Program som hanterar Azure cache för Redis-händelser bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, är det viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att se till att det kommer från Azure-cachen för Redis-instansen som du förväntar dig.
> * På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
> * Azure cache för Redis-händelser garanterar minst en gång till prenumeranter, vilket säkerställer att alla meddelanden visas. Men på grund av återförsök eller prenumerations tillgänglighet kan duplicerade meddelanden ibland uppstå. Läs mer om meddelande leverans och försök igen i [Event Grid meddelande leverans och försök igen](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure cache för Redis-händelser a try:

- [Om Event Grid](../event-grid/overview.md)
- [Schema för Azure cache för Redis-händelser](../event-grid/event-schema-azure-cache.md)
- [Dirigera Azure cache för Redis-händelser till webb slut punkt med Azure CLI](cache-event-grid-quickstart-cli.md)
- [Dirigera Azure cache för Redis-händelser till webb slut punkt med Azure Portal](cache-event-grid-quickstart-portal.md)
- [Dirigera Azure cache för Redis-händelser till webb slut punkt med PowerShell](cache-event-grid-quickstart-powershell.md)
