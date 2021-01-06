---
title: Azure Functions dedikerad värd
description: Lär dig mer om fördelarna med att köra Azure Functions på en dedikerad App Service värd plan.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ebf83aa919d91f161b247539ae20873242a8ed8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937699"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Dedikerade värd planer för Azure Functions

Den här artikeln är att vara värd för din Function-app i en App Service plan, inklusive i en App Service-miljön (ASE). Andra värd alternativ finns i [artikeln hosting plan](functions-scale.md).

En App Service plan definierar en uppsättning beräknings resurser för en app som ska köras. Dessa beräknings resurser motsvarar [_Server gruppen_](https://wikipedia.org/wiki/Server_farm) i konventionell värd. En eller flera Function-appar kan konfigureras för att köras på samma dator resurser (App Service plan) som andra App Service appar, till exempel Web Apps. Dessa planer är bland annat Basic, standard, Premium och isolerade SKU: er. Mer information om hur App Service plan fungerar finns [i Översikt över Azure App Service planer](../app-service/overview-hosting-plans.md).

Överväg ett App Service plan i följande situationer:

* Du har befintliga, underutnyttjade virtuella datorer som redan kör andra App Service instanser.
* Du vill ange en anpassad avbildning som dina funktioner ska köras på.

## <a name="billing"></a>Fakturering

Du betalar för Function-appar i en App Service planera precis som för andra App Service resurser. Detta skiljer sig från Azure Functions [förbruknings plan](consumption-plan.md) eller [Premium-plan](functions-premium-plan.md) -värd, som har förbruknings-baserade kostnads komponenter. Du faktureras bara för planen, oavsett hur många Function-appar eller webbappar som körs i planen. Mer information finns på sidan med [App Service priser](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="always-on"></a><a name="always-on"></a> Always on

Om du kör på en App Service plan bör du aktivera inställningen **Always on** så att din funktions app fungerar som den ska. På en App Service plan aktive ras funktions körningen efter några minuter av inaktivitet, så endast HTTP-utlösare kommer att aktivera dina funktioner. Inställningen **Always on** är bara tillgänglig på en app service plan. I en förbruknings plan aktiverar plattformen automatiskt funktions appar.

Även om Always On är aktiverat, styrs körningens tids gräns för enskilda funktioner av `functionTimeout` inställningen i [host.js](functions-host-json.md#functiontimeout) i projekt filen.

## <a name="scaling"></a>Skalning

Med hjälp av en App Service plan kan du manuellt skala ut genom att lägga till fler VM-instanser. Du kan också aktivera autoskalning, även om autoskalning är långsammare än Premium planens elastiska skala. Mer information finns i [skala antalet instanser manuellt eller automatiskt](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Du kan också skala upp genom att välja en annan App Service plan. Mer information finns i [skala upp en app i Azure](../app-service/manage-scale-up.md). 

> [!NOTE] 
> När du kör JavaScript-funktioner (Node.js) på en App Service plan bör du välja en plan som har färre virtuella processorer. Mer information finns i [välj App Service planer med en kärna](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>Apptjänstmiljöer

Genom att köra i en [App Service-miljön](../app-service/environment/intro.md) (ASE) kan du helt isolera dina funktioner och dra nytta av ett större antal instanser än en app service plan. Information om hur du kommer igång finns i.

Om du bara vill köra din Function-app i ett virtuellt nätverk kan du göra detta med hjälp av [Premium-prenumerationen](functions-premium-plan.md). Mer information finns i [upprätta Azure Functions privat plats åtkomst](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Nästa steg

+ [Azure Functions värd alternativ](functions-scale.md)
+ [Översikt över Azure App Service-plan](../app-service/overview-hosting-plans.md)