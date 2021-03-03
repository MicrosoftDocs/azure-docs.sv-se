---
title: Hantera tillstånd för aviseringar och smarta grupper
description: Hantera tillstånd för aviseringen och de smarta grupp instanserna
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: fa1701c59f099f5222ab6454cc67b9dc20d1e7e7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701118"
---
# <a name="manage-alert-and-smart-group-states"></a>Hantera tillstånd för aviseringar och smarta grupper

Aviseringar i Azure Monitor har nu ett [aviserings tillstånd och ett övervaknings villkor](./alerts-overview.md) och kan, på samma sätt, smarta grupper ha ett [smart grupp tillstånd](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json). Ändringar av statusen samlas nu in i historik som är associerade med respektive avisering eller smart grupp. Den här artikeln vägleder dig genom processen att ändra tillstånd, för både en avisering och en smart grupp.

## <a name="change-the-state-of-an-alert"></a>Ändra status för en avisering

1. Du kan ändra status för en avisering på följande sätt: 
    * På sidan alla aviseringar klickar du på kryss rutan bredvid de aviseringar du vill ändra status för och klickar på Ändra tillstånd.   
    ![Skärm bild som visar sidan alla aviseringar med ändrings status markerat.](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * På sidan aviserings information för en viss varnings instans kan du klicka på Ändra tillstånd   
    ![Skärm bild som visar sidan aviserings information där ändrings aviserings tillstånd har valts.](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * I fönstret smart grupp på sidan aviserings information kan du klicka på kryss rutan bredvid de aviseringar du vill    
    ![Skärm bild som visar aviserings informations sidan för pulsslags aviseringen med vissa instanser med bock markeringar.](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * På sidan information om smart grupp klickar du på kryss rutan bredvid de aviseringar som du vill ändra status för i listan över medlems aviseringar och klickar på Ändra Stateto ändra status och klickar på Ändra tillstånd.   
    ![Skärm bild som visar informations sidan för smart grupp där du kan välja vilka aviseringar du vill ändra tillstånd för.](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. När du klickar på Ändra tillstånd öppnas en popup-meny där du kan välja tillstånd (nytt/bekräftat/stängt) och ange en kommentar om det behövs.   
![Skärm bild som visar dialog rutan information om att ändra avisering.](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. När detta görs registreras tillstånds ändringen i historiken för respektive avisering. Detta kan visas genom att öppna respektive informations sida och kontrol lera historik avsnittet.    
![Skärm bild som visar historiken för tillstånds ändringar.](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Ändra status för en smart grupp
1. Du kan ändra status för en smart grupp på följande sätt:
    1. På sidan smart grupp lista kan du klicka på kryss rutan bredvid de smarta grupper som du vill ändra tillstånd för och klicka på Ändra tillstånd  
    ![Skärm bild som visar sidan Ändra tillstånd för smarta grupper.](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. På sidan information om smart grupp kan du klicka på Ändra tillstånd        
    ![Skärm bild som visar sidan Smart Group-information med ändra Smart Group-tillstånd valt.](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. När du klickar på Ändra tillstånd öppnas en popup-meny där du kan välja tillstånd (nytt/bekräftat/stängt) och ange en kommentar om det behövs. 
![Skärm bild som visar dialog rutan Ändra tillstånd för den smarta gruppen.](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Att ändra status för en smart grupp ändrar inte statusen för de enskilda medlems aviseringarna.

1. När detta görs registreras status ändringen i historiken för respektive smart grupp. Detta kan visas genom att öppna respektive informations sida och kontrol lera historik avsnittet.     
![Skärm bild som visar ändrings historiken för den smarta gruppen.](./media/alerts-managing-alert-states/state-sg-history.jpg)