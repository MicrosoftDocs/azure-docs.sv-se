---
title: Hantera varnings instanser i Azure Monitor
description: Hantera aviserings instanser i Azure
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dc4b62f937e9338051c4018554ec47c94f01a743
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033545"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Hantera aviserings instanser med enhetliga aviseringar

Med den [enhetliga aviserings upplevelsen](./alerts-overview.md) i Azure Monitor kan du se alla olika typer av aviseringar i Azure. Detta omfattar flera prenumerationer i ett enda fönster. Den här artikeln visar hur du kan visa dina aviserings instanser och hur du hittar vissa aviserings instanser för fel sökning.

> [!NOTE]
> Du kan endast få åtkomst till aviseringar som genererats under de senaste 30 dagarna.

## <a name="go-to-the-alerts-page"></a>Gå till sidan aviseringar

Du kan gå till sidan aviseringar på något av följande sätt:

- I [Azure Portal](https://portal.azure.com/)väljer du **övervaka**  >  **aviseringar**.  

     ![Skärm bild av övervaknings aviseringar](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Använd kontexten för en speciell resurs. Öppna en resurs, gå till avsnittet **övervakning** och välj **aviseringar**. Landnings sidan filtreras i förväg för aviseringar på den aktuella resursen.

     ![Skärm bild av aviseringar för resurs övervakning](media/alerts-managing-alert-instances/alert-resource.JPG)

- Använd kontexten för en speciell resurs grupp. Öppna en resurs grupp, gå till avsnittet **övervakning** och välj **aviseringar**. Landnings sidan filtreras för aviseringar i den aktuella resurs gruppen.    

     ![Skärm bild av övervaknings aviseringar för resurs grupp](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Hitta aviserings instanser

På sidan **aviserings Sammanfattning** får du en översikt över alla aviserings instanser i Azure. Du kan ändra sammanfattningsvyn genom att välja **flera prenumerationer** (högst 5) eller genom att filtrera över **resurs grupper**, vissa **resurser** eller **tidsintervall**. Välj **Totalt antal aviseringar**, eller något av allvarlighets graderna, för att gå till listvyn för dina aviseringar.     

![Skärm bild av sidan aviserings Sammanfattning](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
På sidan **alla aviseringar** visas alla aviserings instanser i Azure. Om du kommer till portalen från ett aviserings meddelande kan du använda de filter som är tillgängliga för att begränsa den specifika varnings instansen.

> [!NOTE]
> Om du har kommit till sidan genom att välja något av allvarlighets graderna är listan förfiltrerad för den allvarlighets graden.

Förutom de filter som är tillgängliga på föregående sida kan du också filtrera utifrån övervaknings tjänsten (till exempel plattform för mått), övervaka villkor (utlöst eller löst), allvarlighets grad, aviserings tillstånd (nytt/bekräftat/stängt) eller det smarta grupp-ID: t.

![Skärm bild av sidan alla aviseringar](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Om du har kommit till sidan genom att välja något av allvarlighets graderna är listan förfiltrerad för den allvarlighets graden.

Om du väljer en varnings instans öppnas sidan **aviserings information** , så att du kan se mer information om den aktuella aviserings instansen.   

![Skärm bild av sidan med aviserings information](media/alerts-managing-alert-instances/alert-details.jpg)