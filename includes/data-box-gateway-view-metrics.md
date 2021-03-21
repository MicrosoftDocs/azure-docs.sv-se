---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/16/2020
ms.author: alkohli
ms.openlocfilehash: 27503d1d60d961bac6dd41ebfe4fb083948cb251
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582446"
---
Du kan också visa måtten för att övervaka enhetens prestanda och i vissa fall vid fel sökning av enhets problem.

Utför följande steg i Azure Portal för att skapa ett diagram för valda enhets mått.

1. För resursen i Azure Portal går du till **övervakning > mått** och väljer **Lägg till mått**.

    ![Lägga till mått](media/data-box-gateway-view-metrics/view-metrics-add-metric.png)

2. Resursen fylls i automatiskt.  

    ![Aktuell resurs](media/data-box-gateway-view-metrics/view-metrics-current-resource.png)

    Om du vill ange en annan resurs väljer du resursen. På bladet **Välj en resurs** väljer du den prenumeration, resurs grupp, resurs typ och den resurs som du vill visa måtten för och väljer **tillämpa**.

    ![Välj en annan resurs](media/data-box-gateway-view-metrics/view-metrics-choose-another-resource.png)

3. I list rutan väljer du ett mått för att övervaka enheten. En fullständig lista över dessa mått finns i [mått på enheten](#metrics-on-your-device).

4. När ett mått väljs i list rutan kan agg regering också definieras. Aggregator refererar till det faktiska värdet som sammanställs under en angiven tidsrymd. De sammanlagda värdena kan vara genomsnittet, lägsta eller högsta värde. Välj agg regeringen från AVG, Max eller min.

    ![Visa diagram](media/data-box-gateway-view-metrics/view-metrics-view-chart.png)

5. Om det mått du har valt har flera instanser är delnings alternativet tillgängligt. Välj **Använd delning** och välj sedan det värde som du vill visa uppdelningen för.

    ![Använd delning](media/data-box-gateway-view-metrics/view-metrics-apply-splitting.png)

6. Om du nu bara vill se detalj nivån för några få instanser kan du filtrera data. I det här fallet kan du till exempel filtrera dessa gränssnitt om du bara vill se nätverks data flödet för de två anslutna nätverks gränssnitten på enheten. Välj **Lägg till filter** och ange nätverks gränssnitts namnet för filtrering.

    ![Lägg till filter](media/data-box-gateway-view-metrics/view-metrics-add-filter.png)

7. Du kan också fästa diagrammet på instrument panelen för enkel åtkomst.

    ![Fäst vid instrumentpanelen](media/data-box-gateway-view-metrics/view-metrics-pin-to-dashboard.png)

8. Om du vill exportera diagram data till ett Excel-kalkylblad eller hämta en länk till diagrammet som du kan dela, väljer du alternativet dela i kommando fältet.

    ![Exportera data](media/data-box-gateway-view-metrics/view-metrics-export-data.png)
