---
title: Reagerar på Azure Media Services händelser
description: Den här artikeln beskriver hur du använder Azure Event Grid för att prenumerera på Media Services händelser.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 9bed493ca37d21d0c3e5c289bb8c26607975bdc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610064"
---
# <a name="handling-event-grid-events"></a>Hantera Event Grid-händelser

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Media Services händelser gör att program kan reagera på olika händelser (till exempel händelsen jobb status ändring) med hjälp av moderna Server lösa arkitekturer. Det gör det utan att det behövs komplicerade kod eller dyra och ineffektiva avsöknings tjänster. I stället skickas händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till händelse hanterare som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med din egen webhook och du betalar bara för det du använder. Information om priser finns i [Event Grid priser](https://azure.microsoft.com/pricing/details/event-grid/).

Tillgänglighet för Media Services händelser är knutna till Event Grid [tillgänglighet](../../../event-grid/overview.md) och blir tillgängliga i andra regioner som Event Grid gör.  

## <a name="media-services-events-and-schemas"></a>Media Services händelser och scheman

Event Grid använder [händelse prenumerationer](../../../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Media Services händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Media Services-händelse eftersom egenskapen eventType börjar med "Microsoft. Media.".

Mer information finns i [Media Services händelse scheman](../media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Media Services händelser bör följa några rekommenderade metoder:

* Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, är det viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att säkerställa att det kommer från det lagrings konto som du förväntar dig.
* På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
* Ignorera fält som du inte förstår.  Den här övningen hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
* Använd prefixet "subject" och suffixet matchar för att begränsa händelser till en viss händelse.

> [!NOTE]
> Händelser omfattas Event Grid [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Om du vill hämta händelse meddelanden med hjälp av API: er, se exempel på hur du använder händelser, med [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) eller [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Nästa steg

* [Övervaka händelser – Portal](../monitor-events-portal-how-to.md)
* [Övervaka händelser – CLI](../job-state-events-cli-how-to.md)
