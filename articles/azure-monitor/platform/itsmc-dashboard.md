---
title: Undersök fel med hjälp av instrument panelen
description: Det här dokumentet innehåller information om fel på ITSMC-instrumentpanelen.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089283"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Undersök fel med hjälp av ITSMC-instrumentpanelen

Den här artikeln innehåller information om instrument panelen för Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC). Med instrument panelen kan du undersöka status för ITSMC.

## <a name="view-the-dashboard"></a>Visa instrumentpanelen

Följ dessa steg för att öppna instrument panelen.

1. Välj **alla resurser** och Sök efter **Servicedesk (*namnet på din arbets yta*)**.

   ![Skärm bild som visar resurserna i Azure-tjänster.](media/itsmc-definition/create-new-connection-from-resource.png)

1. I det vänstra fönstret väljer du **data källor för arbets ytan** och väljer sedan **ITSM-anslutningar**.

   ![Skärm bild som visar val av ITSM-anslutningar under arbets plats data källor.](media/itsmc-overview/add-new-itsm-connection.png)

1. I avsnittet **Sammanfattning** väljer du **Visa sammanfattning** för att visa ett sammanfattande diagram.

    ![Skärm bild som visar alternativet Visa sammanfattning i avsnittet Sammanfattning.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Välj grafen i avsnittet **Sammanfattning** för att öppna instrument panelen.

    ![Skärm bild som visar att du väljer sammanfattnings diagrammet.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Granska instrument panelen för status och eventuella fel i din anslutning.
    ![Skärm bild som visar instrument panelen.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Förstå instrument panels element

Instrument panelen innehåller information om de aviseringar som har skickats till ITSM-verktyget med hjälp av den här anslutningen.

Instrument panelen är uppdelad i fyra delar:

- **Skapade arbets objekt**: grafen och tabellen visar antalet arbets objekt efter typ. Välj grafen eller tabellen om du vill veta mer om dina arbets uppgifter.
      ![Skärm bild som visar avsnittet arbets objekt som skapats.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **Påverkade datorer**: tabellen innehåller information om de konfigurations objekt som skapade arbets objekt.
      Välj rader i tabellerna om du vill ha mer information om konfigurations objekt.
      Tabellen innehåller ett begränsat antal rader. Om du vill se hela listan väljer du **Visa alla**.
      ![Skärm bild som visar avsnittet datorer som påverkas.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **ANSLUTNINGS status**: grafen och tabellen visar information om anslutnings status. Markera grafen eller meddelandena i tabellen om du vill ha mer information. Tabellen visar ett begränsat antal rader. Om du vill se hela listan väljer du **Visa alla**.
      ![Skärm bild som visar avsnittet anslutnings status.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **Aviserings regler**: det här avsnittet visar information om antalet aviserings regler som har identifierats. Välj rader i tabellerna om du vill ha mer information om reglerna som har identifierats. Tabellen har ett begränsat antal rader. Om du vill se hela listan väljer du **Visa alla**.
      ![Skärm bild som visar avsnittet varnings regler.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Nästa steg

Ta en titt på [status fel för vanliga anslutningar](itsmc-dashboard-errors.md).
