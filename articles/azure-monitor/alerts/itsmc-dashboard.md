---
title: Undersök fel med hjälp av ITSMC-instrumentpanelen
description: Lär dig hur du använder instrument panelen för Anslutningsprogram för hantering av IT-tjänster (ITSM) för att undersöka fel.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039537"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Undersök fel med hjälp av ITSMC-instrumentpanelen

Den här artikeln innehåller information om instrument panelen för Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC). På instrument panelen kan du undersöka anslutnings status.

## <a name="view-errors"></a>Visa fel

Visa fel på instrument panelen:

1. Välj **alla resurser** och Sök efter **Servicedesk (*namnet på din arbets yta*)**.

   ![Skärm bild som visar resurserna i Azure-tjänster.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Under **arbets ytans data källor** i det vänstra fönstret väljer du **ITSM-anslutningar**:

   ![Skärm bild som visar val av ITSM-anslutningar under arbets plats data källor.](media/itsmc-overview/add-new-itsm-connection.png)

3. Under **Sammanfattning**, i **anslutningsprogram för hantering av IT-tjänster (ITSM)** -ytan, väljer du **Visa sammanfattning**:

   ![Skärm bild som visar knappen Visa sammanfattning.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. När ett diagram visas i **anslutningsprogram för hantering av IT-tjänster (ITSM)s** område väljer du det:

   ![Skärm bild som visar markering av ett diagram.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Instrument panelen visas. Använd den för att granska status och felen i din anslutning.
   
   ![Skärm bild som visar kopplings status på instrument panelen.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Förstå instrument panels element

Instrument panelen innehåller information om de aviseringar som har skickats till ITSM-verktyget via den här anslutningen. Instrument panelen delas upp i fyra delar.

### <a name="created-work-items"></a>Skapade arbets objekt 

I det **skapade arbets objekts** fältet innehåller grafen och tabellen nedan antalet arbets objekt per typ. Om du väljer grafen eller tabellen kan du se mer information om arbets objekt.

![Skärm bild som visar en skapad arbets uppgift.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Berörda datorer 

I den berörda **datorns** områden listar tabellen datorer och deras associerade arbets objekt. Genom att välja rader i tabellerna kan du få mer information om datorerna.

Tabellen innehåller ett begränsat antal rader. Om du vill se alla rader väljer du **Visa alla**.

![Skärm bild som visar berörda datorer.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Status för anslutningsprogrammet 

I fältet **kopplings status** innehåller grafen och tabellen nedan meddelanden om anslutnings status. Genom att markera grafen eller raderna i tabellen kan du få mer information om meddelandena.

Tabellen innehåller ett begränsat antal rader. Om du vill se alla rader väljer du **Visa alla**.

Mer information om meddelandena i tabellen finns i [den här artikeln](itsmc-dashboard-errors.md).

![Skärm bild som visar kopplings status.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Aviseringsregler 

I avsnittet **aviserings regler** innehåller tabellen information om antalet aviserings regler som har identifierats. Genom att markera rader i tabellen kan du få mer information om identifierade regler.
    
Tabellen innehåller ett begränsat antal rader. Om du vill se alla rader väljer du **Visa alla**.

![Skärm bild som visar varnings regler.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
