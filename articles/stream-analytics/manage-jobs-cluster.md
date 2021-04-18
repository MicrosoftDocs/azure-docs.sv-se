---
title: Skapa och ta bort jobb i ett Azure Stream Analytics kluster
description: Lär dig hur du Stream Analytics jobb i ett Azure Stream Analytics kluster
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 04/16/2021
ms.openlocfilehash: 4a6334d33dea959bdd3704f848e7bc8250b6e7c6
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600731"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Lägga till och ta bort jobb i Azure Stream Analytics kluster

Du kan köra flera Azure Stream Analytics jobb i ett Stream Analytics kluster. Att köra jobb i ett kluster är en enkel tvåstegsprocess: lägg till jobbet i klustret och starta jobbet. Den här artikeln visar hur du lägger till och tar bort jobb från ett befintligt kluster. Följ snabbstarten för [att skapa Stream Analytics ett](create-cluster.md) kluster om du inte redan har ett.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Lägga till Stream Analytics jobb i ett kluster

Endast befintliga Stream Analytics jobb kan läggas till i kluster. Följ snabbstarten för [att lära dig hur du skapar ett jobb](stream-analytics-quick-create-portal.md) med hjälp av Azure Portal. När du har ett jobb som du vill lägga till i ett kluster kan du använda följande steg för att lägga till jobbet i klustret.

1. I Azure Portal letar du upp och väljer Stream Analytics klustret.

1. Under **Inställningar** väljer du **Stream Analytics jobb**. Välj sedan Lägg **till befintligt jobb**.

1. Välj den prenumeration och det Stream Analytics jobb som du vill lägga till i klustret. Endast Stream Analytics jobb som finns i samma region som klustret kan läggas till i klustret.

   ![Lägga till jobb i kluster](./media/manage-jobs-cluster/add-job.png)

1. När du har lagt till jobbet i klustret går du till jobbresursen [och startar jobbet](start-job.md#azure-portal). Jobbet börjar sedan köras i klustret.

Du kan göra alla andra åtgärder, till exempel övervakning, aviseringar och diagnostikloggar, Stream Analytics jobbresurssidan.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Ta bort Stream Analytics jobb från ett kluster

Stream Analytics måste vara i stoppat tillstånd innan det kan tas bort från klustret. Om jobbet fortfarande körs stoppar du jobbet innan du går vidare till följande steg.

1. Leta upp och välj Stream Analytics kluster.

1. Under **Inställningar** väljer du **Stream Analytics jobb**.

1. Välj de jobb som du vill ta bort från klustret och välj sedan **Ta bort.**

   ![ta bort jobb från kluster](./media/manage-jobs-cluster/remove-job.png)

   När ett jobb tas bort från ett Stream Analytics kluster återgår det till standardmiljön för flera innehavare.

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du lägger till och tar bort jobb i Azure Stream Analytics kluster. Härnäst kan du lära dig hur du hanterar privata slutpunkter och skalar dina kluster:

* [Skala ett Azure Stream Analytics kluster](scale-cluster.md)
* [Hantera privata slutpunkter i ett Azure Stream Analytics kluster](private-endpoints.md)
