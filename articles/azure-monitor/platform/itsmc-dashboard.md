---
title: Fel undersökning med instrument panelen
description: Det här dokumentet innehåller information om fel undersökning med hjälp av instrument panelen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540747"
---
# <a name="error-investigation-using-the-dashboard"></a>Fel undersökning med instrument panelen

Den här sidan innehåller information om ITSM Connector-instrumentpanelen. På den här instrument panelen kan du undersöka statusen för din ITSM-anslutning.

## <a name="how-to-view-the-dashboard"></a>Visa instrument panelen

Om du vill visa felen på instrument panelen ska du följa anvisningarna nedan:

1. I **alla resurser** letar du efter **Servicedesk (*namnet på din arbets yta*)**:

   ![Skärm bild som visar de senaste resurserna i Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Under **arbets ytans data källor** i det vänstra fönstret väljer du **ITSM-anslutningar**:

   ![Skärm bild som visar meny alternativet ITSM-anslutningar.](media/itsmc-overview/add-new-itsm-connection.png)

3. Under **Sammanfattning** i den vänstra rutan **anslutningsprogram för hantering av IT-tjänster (ITSM)** väljer du **Visa sammanfattning**:

    ![Skärm bild som visar Visa sammanfattning.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Klicka på diagrammet under **Sammanfattning** i den vänstra rutan **anslutningsprogram för hantering av IT-tjänster (ITSM)**:

    ![Skärm bild som visar graf-klickning.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Med den här instrument panelen kan du granska status och fel i din anslutning.
    ![Skärm bild som visar kopplings status.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Instrument panels element

Instrument panelen innehåller information om de aviseringar som har skickats till ITSM-verktyget med hjälp av den här anslutningen.
Instrument panelen delas upp i fyra delar:

1. Arbets objekt skapat: grafen och tabellen nedan innehåller antalet arbets objekt per typ. Om du klickar på grafen eller i tabellen kan du se mer information om arbets objekt.
    ![Skärm bild som visar arbets objekt som skapats.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Påverkade datorer: tabellerna innehåller information om konfigurations objekt som skapade konfigurations objekt.
    Genom att klicka på rader i tabellerna kan du få mer information om konfigurations objekten.
    Tabellen innehåller ett begränsat antal rader om du vill se alla listor som du kan klicka på "Se alla".
    ![Skärm bild som visar påverkade datorer.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Anslutnings status: grafen och tabellen nedan innehåller meddelanden om anslutnings status. Genom att klicka på diagrammet på rader i tabellen kan du få mer information om meddelandets status.
    Tabellen innehåller ett begränsat antal rader om du vill se alla listor som du kan klicka på "Se alla".
    ![Skärm bild som visar kopplings status.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Aviserings regler: tabellerna innehåller information om antalet aviserings regler som har identifierats.
    Genom att klicka på rader i tabellerna kan du få mer information om de regler som har identifierats.
    Tabellen innehåller ett begränsat antal rader om du vill se alla listor som du kan klicka på "Se alla".
    ![Skärm bild som visar varnings regler.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)