---
title: Visa flera resurser i Azure Metrics Explorer
description: Lär dig hur du visualiserar flera resurser med hjälp av Azure Metrics Explorer.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 4c895b287f72929e2a0571ccc2cae8cc4f673388
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250493"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Visa flera resurser i Azure Metrics Explorer

Med resurs omfattnings Väljaren kan du visa mått över flera resurser i samma prenumeration och region. Den här artikeln förklarar hur du visar flera resurser med hjälp av Azure Metrics Explorer-funktionen i Azure Monitor. 

## <a name="select-a-resource"></a>Välj en resurs 

Välj **mått** på **Azure Monitor** -menyn eller från **övervaknings** avsnittet på en resurs meny. Välj sedan **Välj ett omfång** för att öppna omfattnings väljaren. 

Använd omfattnings väljaren för att välja de resurser vars mått du vill se. Omfattningen bör vara ifylld om du öppnade Metrics Explorer från en resurs meny. 

![Skärm bild som visar hur du öppnar resurs omfattnings väljaren.](./media/metrics-charts/019.png)

## <a name="select-multiple-resources"></a>Välj flera resurser 

Vissa resurs typer kan fråga efter mått över flera resurser. Måtten måste vara inom samma prenumeration och plats. Sök efter dessa resurs typer överst i menyn **resurs typer** .

![Skärm bild som visar en meny med resurser som är kompatibla med flera resurser.](./media/metrics-charts/020.png)

> [!WARNING] 
> Du måste ha övervaknings läsar behörighet på prenumerations nivå för att visualisera mått över flera resurser, resurs grupper eller en prenumeration. Mer information finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Om du vill visualisera mått över flera resurser börjar du med att välja flera resurser i resurs omfattnings väljaren. 

![Skärm bild som visar hur du väljer flera resurser.](./media/metrics-charts/021.png)

> [!NOTE]
> De resurser du väljer måste ligga inom samma resurs typ, plats och prenumeration. Resurser som inte uppfyller dessa villkor är inte valbara. 

När du är klar väljer du **Använd** för att spara dina val. 

## <a name="select-a-resource-group-or-subscription"></a>Välj en resurs grupp eller prenumeration 

> [!WARNING]
> Du måste ha övervaknings läsar behörighet på prenumerations nivå för att visualisera mått över flera resurser, resurs grupper eller en prenumeration. 

För typer som är kompatibla med flera resurser kan du fråga efter mått i en prenumeration eller flera resurs grupper. Börja med att välja en prenumeration eller en eller flera resurs grupper: 

![Skärm bild som visar hur du frågar i flera resurs grupper.](./media/metrics-charts/022.png)

Välj en resurs typ och plats. 

![Skärm bild som visar de valda resurs grupperna.](./media/metrics-charts/023.png)

Du kan expandera de valda omfattningarna för att kontrol lera vilka resurser som dina val gäller för.

![Skärm bild som visar de valda resurserna i grupperna.](./media/metrics-charts/024.png)

När du har valt omfattning väljer du **tillämpa**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Dela och filtrera efter resurs grupp eller resurser

När du har ritat dina resurser kan du använda delning och filtrering för att få bättre insikt i dina data. 

Genom att dela upp kan du visualisera hur olika segment i måttet jämförs med varandra. När du till exempel ritar ett mått för flera resurser kan du välja **Använd delning** för att dela efter resurs-ID eller resurs grupp. Delningen gör att du kan jämföra ett enda mått över flera resurser eller resurs grupper.  

Följande diagram visar exempelvis procent CPU-processorn för nio virtuella datorer. När du delar per resurs-ID kan du se hur stor CPU-processor skiljer sig från den virtuella datorn. 

![Skärm bild som visar hur du använder delning för att se procent andelen av processorn på virtuella datorer.](./media/metrics-charts/026.png)

Tillsammans med delning kan du använda filtrering för att visa endast de resurs grupper som du vill se.  Om du till exempel vill visa procent CPU-processorn för virtuella datorer för en viss resurs grupp kan du välja **Lägg till filter** för att filtrera efter resurs grupp. 

I det här exemplet filtrerar vi efter TailspinToysDemo. Här tar filtret bort mått som är kopplade till resurser i ladan. 

![Skärm bild som visar hur du filtrerar efter resurs grupp.](./media/metrics-charts/027.png)

## <a name="pin-multiple-resource-charts"></a>Fäst flera resurs diagram 

Diagram med flera resurser som visualiserar mått över resurs grupper och prenumerationer kräver att användaren har *övervaknings läsar* behörighet på prenumerations nivå. Se till att alla användare av instrument paneler till vilka du fäster flera resurs diagram har tillräckliga behörigheter. Mer information finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Om du vill fästa ett diagram med flera resurser på en instrument panel, se [fästa på instrument paneler](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#pinning-to-dashboards). 

## <a name="next-steps"></a>Nästa steg

* [Felsöka mått Utforskaren](metrics-troubleshoot.md)
* [Visa en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md)
* [Visa exempel på konfigurerade diagram](metric-chart-samples.md)

