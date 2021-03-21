---
title: Skapa och ta bort jobb i ett Azure Stream Analytics kluster
description: Lär dig hur du hanterar Stream Analytics jobb i ett Azure Stream Analytics kluster
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 09a67d11ac4daf3e87a50ee2171f1ca49060c5bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018130"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Lägga till och ta bort jobb i ett Azure Stream Analytics kluster

Du kan köra flera Azure Stream Analytics jobb på ett Stream Analytics-kluster. Jobb som körs i ett kluster är en enkel 2-stegs process: Lägg till jobbet i klustret och starta jobbet. Den här artikeln visar hur du lägger till och tar bort jobb från ett befintligt kluster. Följ snabb starten för att [skapa ett Stream Analytics kluster](create-cluster.md) om du inte redan har en.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Lägga till ett Stream Analytics jobb i ett kluster

Det går bara att lägga till befintliga Stream Analytics jobb i kluster. Följ snabb starten för att [lära dig hur du skapar ett jobb](stream-analytics-quick-create-portal.md) med hjälp av Azure Portal. När du har ett jobb som du vill lägga till i ett kluster använder du följande steg för att lägga till jobbet i klustret.

1. Leta upp och välj ditt Stream Analytics-kluster i Azure Portal.

1. Under **Inställningar** väljer du **Stream Analytics jobb**. Välj sedan **Lägg till befintligt jobb**.

1. Välj den prenumeration och det Stream Analytics jobb som du vill lägga till i klustret. Endast Stream Analytics jobb i samma region som klustret kan läggas till i klustret.

   ![Lägg till jobb i kluster](./media/manage-jobs-cluster/add-job.png)

1. När du har lagt till jobbet i klustret går du till jobb resursen och [startar jobbet](start-job.md#azure-portal). Jobbet börjar sedan köras på klustret.

Du kan utföra alla andra åtgärder, till exempel övervakning, aviseringar och diagnostiska loggar, från sidan Stream Analytics jobb resurs.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Ta bort ett Stream Analytics jobb från ett kluster

Stream Analytics jobb måste vara i ett stoppat tillstånd innan det kan tas bort från klustret. Om jobbet fortfarande körs stoppar du jobbet innan du går vidare till följande steg.

1. Leta upp och välj ditt Stream Analytics-kluster.

1. Under **Inställningar** väljer du **Stream Analytics jobb**.

1. Välj de jobb som du vill ta bort från klustret och välj sedan **ta bort**.

   ![ta bort jobb från kluster](./media/manage-jobs-cluster/remove-job.png)

   När ett jobb tas bort från ett Stream Analytics-kluster återgår det till standard miljön för flera klienter.

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du lägger till och tar bort jobb i ditt Azure Stream Analytics-kluster. Sedan kan du lära dig hur du hanterar privata slut punkter och skalar dina kluster:

* [Skala ett Azure Stream Analytics kluster](scale-cluster.md)
* [Hantera privata slut punkter i ett Azure Stream Analytics kluster](private-endpoints.md)
