---
title: Autoskala i Azure med hjälp av ett anpassat mått
description: Lär dig hur du skalar din resurs efter anpassad mått i Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 8e744e6a91eb6fbe23a6b45f95c39b1acfdcb61f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100623879"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Kom igång med automatisk skalning efter anpassade mått i Azure
Den här artikeln beskriver hur du skalar din resurs med ett anpassat mått i Azure Portal.

Azure Monitor autoskalning gäller endast för [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [Azure datautforskaren-kluster](https://azure.microsoft.com/services/data-explorer/) ,   
Integration Service Environment-och [API Management-tjänster](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Gör det möjligt att komma igång
Den här artikeln förutsätter att du har en webbapp med konfigurerat Application Insights. Om du inte redan har en, kan du [konfigurera Application Insights för din ASP.net-webbplats][1]

- Öppna [Azure Portal][2]
- Klicka på Azure Monitor ikonen i det vänstra navigerings fönstret.
  ![Starta Azure Monitor][3]
- Klicka på Inställningar för automatisk skalning för att visa alla resurser som den automatiska skalningen gäller för, tillsammans med dess aktuella status ![ för automatisk skalning identifiera automatisk skalning i Azure Monitor][4]
- Öppna bladet autoskalning i Azure Monitor och välj en resurs som du vill skala
  > Obs! stegen nedan använder en app service-plan som är associerad med en webbapp som har konfigurerat App Insights.
- Observera att det aktuella instans antalet är 1 på bladet skalnings inställning för resursen. Klicka på Aktivera autoskalning.
  ![Skalnings inställning för ny webbapp][5]
- Ange ett namn för skalnings inställningen och klicka på Lägg till en regel. Observera de skalnings regel alternativ som öppnas som ett kontext fönster på den högra sidan. Som standard anger den alternativet att skala antalet instanser med 1 om resursens procent andel överskrider 70%. Ändra mått källan överst till "Application Insights", Välj resursen App Insights i list rutan resurs och välj sedan det anpassade mått som du vill skala.
  ![Skala efter anpassat mått][6]
- Precis som i steget ovan lägger du till en skalnings regel som skalar i och minskar skalnings antalet med 1 om det anpassade måttet är under ett tröskelvärde.
  ![Skala baserat på CPU][7]
- Ange instans gränserna. Om du till exempel vill skala mellan 2-5 instanser beroende på anpassade mått, ställer du in ' minimum ' på ' 2 ', ' max ' till ' 5 ' och ' default ' till ' 2 '
  > OBS! om det uppstår problem med att läsa resurs måtten och den aktuella kapaciteten är lägre än standard kapaciteten, så att den automatiska skalningen kan skalas upp till standardvärdet för att säkerställa resursens tillgänglighet. Om den aktuella kapaciteten redan är högre än standard kapaciteten kommer autoskalning inte att skalas i.
- Klicka på Spara

Grattis! Du har nu skapat skalnings inställningen för att automatiskt skala din webbapp baserat på ett anpassat mått.

> Obs: samma steg gäller för att komma igång med en VMSS-eller moln tjänst roll.

<!--Reference-->
[1]: ../app/asp-net.md
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png
