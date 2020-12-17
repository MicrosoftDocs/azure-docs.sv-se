---
title: Visa flera resurser i Metrics Explorer
description: Lär dig hur du visualiserar flera resurser på Azure Monitor Metrics Explorer
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 9d1460a8bebe75a3ee471eb304fcf642d566b5dd
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614650"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Visa flera resurser i Metrics Explorer

Med resurs omfattnings Väljaren kan du visa mått över flera resurser i samma prenumeration och region. Nedan visas instruktioner om hur du visar flera resurser i Azure Monitor Metrics Explorer. 

## <a name="selecting-a-resource"></a>Välja en resurs 

Välj **mått** på **Azure Monitor** -menyn eller från **övervaknings** avsnittet på en resurs meny. Klicka på knappen "Välj ett omfång" för att öppna resurs omfattnings väljaren, vilket gör att du kan välja de resurser som du vill visa mått för. Detta bör redan vara ifyllt om du har öppnat Metric Explorer från en resurs meny. 

![Skärm bild av väljare för resurs omfång markerade i rött](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Välja flera resurser 

Vissa resurs typer har aktiverat möjligheten att fråga efter mått över flera resurser, så länge de är inom samma prenumeration och plats. Du hittar dessa resurs typer överst i list rutan "resurs typer". 

![Skärm bild som visar en lista över resurser som är kompatibla med flera resurser ](./media/metrics-charts/020.png)

> [!WARNING] 
> Du måste ha övervaknings läsar behörighet på prenumerations nivå för att visualisera mått över flera resurser, resurs grupper eller en prenumeration. Följ anvisningarna i [det här dokumentet](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)för att göra detta.

Om du vill visualisera mått över flera resurser börjar du med att välja flera resurser i resurs omfattnings väljaren. 

![Skärm bild som visar hur du väljer flera resurser](./media/metrics-charts/021.png)

> [!NOTE]
> Du kan bara välja flera resurser inom samma resurs typ, plats och prenumeration. Resurser utanför det här kriteriet kan inte väljas. 

När du är färdig med valet klickar du på knappen Använd för att spara ditt val. 

## <a name="selecting-a-resource-group-or-subscription"></a>Välja en resurs grupp eller prenumeration 

> [!WARNING]
> Du måste ha övervaknings läsar behörighet på prenumerations nivå för att visualisera mått över flera resurser, resurs grupper eller en prenumeration. 

För typer som är kompatibla med flera resurser kan du också fråga efter mått i en prenumeration eller flera resurs grupper. Börja med att välja en prenumeration eller en eller flera resurs grupper: 

![Skärm bild som visar hur man frågar över flera resurs grupper ](./media/metrics-charts/022.png)

Sedan måste du välja en resurs typ och plats innan du kan fortsätta att använda det nya omfånget. 

![Skärm bild som visar de valda resurs grupperna ](./media/metrics-charts/023.png)

Du kan också expandera de valda omfattningarna för att kontrol lera vilka resurser som ska gälla för.

![Skärm bild som visar de valda resurserna i grupperna ](./media/metrics-charts/024.png)

När du har valt dina omfattningar klickar du på "Använd" för att spara dina val. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Dela upp och filtrera efter resurs grupp eller resurser

När du har ritat dina resurser kan du använda verktyget Dela upp och filtrera för att få bättre insikt i dina data. 

Genom att dela upp kan du visualisera hur olika segment i måttet jämförs med varandra. När du till exempel ritar ett mått för flera resurser kan du använda verktyget "Använd delning" för att dela efter resurs-ID eller resurs grupp. På så sätt kan du enkelt jämföra ett enda mått över flera resurser eller resurs grupper.  

Nedan visas till exempel ett diagram över procents ATS CPU över 9VMs. Genom att dela efter resurs-ID kan du enkelt se hur procent CPU skiljer sig per virtuell dator. 

![Skärm bild som visar hur du kan använda delning för att se procent processor per virtuell dator](./media/metrics-charts/026.png)

Förutom delning kan du använda funktionen filtrering för att bara visa de resurs grupper som du vill se.  Om du till exempel vill visa procent CPU-processorn för virtuella datorer för en viss resurs grupp kan du använda verktyget Lägg till filter för att filtrera efter resurs grupp. I det här exemplet filtrerar vi efter TailspinToysDemo, vilket tar bort mått som är kopplade till resurser i ladan. 

![Skärm bild som visar hur du kan filtrera efter resurs grupp](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Fästa flera resurs diagram 

> [!WARNING] 
> Du måste ha övervaknings läsar behörighet på prenumerations nivå för att visualisera mått över flera resurser, resurs grupper eller en prenumeration. Följ anvisningarna i [det här dokumentet](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)för att göra detta. 

Följ anvisningarna [här](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#pin-charts-to-dashboards)för att fästa ett diagram för flera resurser. 

## <a name="next-steps"></a>Nästa steg

* [Felsökning av Metrics Explorer](metrics-troubleshoot.md)
* [Visa en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md)
* [Visa exempel på konfigurerade diagram](metric-chart-samples.md)

