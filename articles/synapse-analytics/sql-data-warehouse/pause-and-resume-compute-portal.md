---
title: 'Snabbstart: Pausa och återuppta beräkning i dedikerad SQL-pool via Azure Portal'
description: Använd Azure Portal för att pausa beräkning för dedikerad SQL-pool för att spara kostnader. Återuppta beräkningen när du är redo att använda informationslagret.
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
manager: craigg
ms.reviewer: igorstan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 27587f436013bcaccbacd668ce62f1d939f89e48
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566825"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Snabbstart: Pausa och återuppta beräkning i dedikerad SQL-pool via Azure Portal

Du kan använda den här Azure Portal pausa och återuppta de dedikerade beräkningsresurserna för SQL-poolen. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Använd [Skapa och anslut – portal för att](../quickstart-create-sql-pool-portal.md) skapa en dedikerad SQL-pool med namnet **mySampleDataWarehouse.** 

## <a name="pause-compute"></a>Pausa beräkning

Du kan minska kostnaderna genom att pausa och återuppta beräkningsresurser på begäran. Om du till exempel inte kommer att använda databasen under natten och på helger kan du pausa den under dessa tider och återuppta den under dagen.
 
>[!NOTE]
>Du debiteras inte för beräkningsresurser när databasen pausas. Du fortsätter dock att debiteras för lagring. 

Följ dessa steg om du vill pausa en dedikerad SQL-pool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till sidan Dedikerad **SQL-pool för** att öppna SQL-poolen. 
3. Observera **att Status** är **Online**.

    ![Beräkna online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Om du vill pausa den dedikerade SQL-poolen klickar du **på knappen Pausa.** 
5. En bekräftelsefråga visas som frågar om du vill fortsätta. Klicka på **Ja**.
6. Vänta en stund och lägg sedan märke till **att Status** **pausar**.

    ![Skärmbild som visar Azure Portal för ett exempelinformationslager med statusvärdet Pausa.](./media/pause-and-resume-compute-portal/pausing.png)

7. När pausåtgärden är klar är statusen **Pausad och** alternativknappen är **Återuppta.**
8. Beräkningsresurserna för den dedikerade SQL-poolen är nu offline. Du debiteras inte för beräkning förrän du återupptar tjänsten.

    ![Beräkna offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Återuppta beräkning

Följ de här stegen för att återuppta en dedikerad SQL-pool.

1. Gå till sidan Dedikerad **SQL-pool för** att öppna SQL-poolen.
3. På sidan **mySampleDataWarehouse ser** du att **Status** är **Pausad.**

    ![Beräkna offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Om du vill återuppta SQL-poolen klickar du på **Återuppta**. 
1. En bekräftelsefråga visas som frågar om du vill starta. Klicka på **Ja**.
1. Observera att **Status** **återupptar**.

    ![Skärmbild som visar Azure Portal för ett exempelinformationslager med knappen Starta markerad och statusvärdet Återuppta.](./media/pause-and-resume-compute-portal/resuming.png)

1. När SQL-poolen är online igen är statusen **Online** och alternativknappen är **Pausa**.
1. Beräkningsresurserna för SQL-poolen är nu online och du kan använda tjänsten. Debitering för beräkning har återupptagits.

    ![Beräkna online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och de data som lagras i din dedikerade SQL-pool. Dessa beräknings- och lagringsresurser debiteras separat. 

- Om du vill lagra data pausar du beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort den dedikerade SQL-poolen. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj din dedikerade SQL-pool.

    ![Rensa resurser](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. 

1. Om du vill ta bort den dedikerade SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **Ta bort**.



## <a name="next-steps"></a>Nästa steg

Nu har du pausat och återupptat beräkningen för din dedikerade SQL-pool. Fortsätt till nästa artikel om du vill veta mer om hur du läser [in data till en dedikerad SQL-pool.](./load-data-from-azure-blob-storage-using-copy.md) Mer information om hur du hanterar beräkningsfunktioner finns i artikeln [Hantera beräkningsöversikt.](sql-data-warehouse-manage-compute-overview.md)
