---
title: 'Snabbstart: Konfigurera arbetsbelastningsisolering – portalen'
description: Använd Azure Portal för att konfigurera arbetsbelastningsisolering för dedikerad SQL-pool.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: c15f21064012c195315a7f91908b3160591dc6f8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534256"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Snabbstart: Konfigurera isolering av dedikerade SQL-poolarbetsbelastningar med hjälp av en arbetsbelastningsgrupp i Azure Portal

I den här snabbstarten konfigurerar du [arbetsbelastningsisolering](sql-data-warehouse-workload-isolation.md) genom att skapa en arbetsbelastningsgrupp för att reservera resurser.  I den här självstudien skapar vi arbetsbelastningsgruppen för dataläsning med namnet `DataLoads` . Arbetsbelastningsgruppen reserverar 20 % av systemresurserna.  Med 20 % isolering för databelastningar är de garanterade resurser som gör att de kan använda serviceavtal.  När du har skapat arbetsbelastningsgruppen [skapar du en arbetsbelastningsklassare för](quickstart-create-a-workload-classifier-portal.md) att tilldela frågor till den här arbetsbelastningsgruppen.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.


## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

> [!NOTE]
> Att skapa en dedikerad SQL-poolinstans Azure Synapse Analytics kan resultera i en ny fakturerbar tjänst.  Mer information finns i [Azure Synapse Analytics prissättning.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten förutsätter att du redan har en dedikerad SQL-poolinstans i Synapse SQL och att du har CONTROL DATABASE-behörigheter. Om du behöver skapa en använder du [Snabbstart: Skapa dedikerad SQL-pool – portalen](../quickstart-create-sql-pool-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse.**

>[!IMPORTANT] 
>Din dedikerade SQL-pool måste vara online för att konfigurera arbetsbelastningshantering. 

## <a name="configure-workload-isolation"></a>Konfigurera arbetsbelastningsisolering

Dedikerade SQL-poolresurser kan isoleras och reserveras för specifika arbetsbelastningar genom att skapa arbetsbelastningsgrupper.  I [konceptdokumentationen för](sql-data-warehouse-workload-isolation.md) arbetsbelastningsisolering finns mer information om hur arbetsbelastningsgrupper hjälper dig att hantera din arbetsbelastning.  [Snabbstarten Skapa och ansluta –](create-data-warehouse-portal.md) portalen skapade **mySampleDataWarehouse** och initierade den på DW100c. Följande steg skapar en arbetsbelastningsgrupp i **mySampleDataWarehouse**.

Så här skapar du en arbetsbelastningsgrupp med 20 % isolering:
1.  Gå till din **dedikerade SQL-poolsida för mySampleDataWarehouse..**
1.  Välj **Arbetsbelastningshantering.**
1.  Välj **Ny arbetsbelastningsgrupp.**
1.  Välj **anpassad**.

    ![Klicka på Anpassad](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Ange `DataLoads` för **arbetsbelastningsgruppen**.
7.  Ange `20` för **Min. resources %**.
8.  Ange `5` för **Min. resources % per request**.
9.  Ange `100` för Cap resources % **(Takresurser i %**).
10. Ange **Spara**.

   ![Klicka på Spara](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Ett portalmeddelande visas när arbetsbelastningsgruppen skapas.  Arbetsbelastningsgruppens resurser visas i diagrammen nedanför de konfigurerade värdena.

   ![Klicka på Slutför](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort `DataLoads` arbetsbelastningsgruppen som skapades i den här självstudien:
1. Klicka på till **`...`** höger om `DataLoads` arbetsbelastningsgruppen.
2. Klicka på Ta **bort arbetsbelastningsgrupp**.
3. Klicka på **Ja när** du uppmanas att bekräfta borttagningen av arbetsbelastningsgruppen.
4. Klicka på **Spara.**

   ![Klicka på Ta bort](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Du debiteras för informationslagerenheter och data som lagras i informationslagret. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkningen debiteras du bara för datalagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ de här stegen för att rensa resurser.

1. Logga in på [Azure Portal](https://portal.azure.com), välj din dedikerade SQL-pool.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du **knappen Pausa.** När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Starta**.

3. Om du vill ta bort informationslagret så att du inte debiteras för beräkning eller lagring väljer du Ta **bort**.

## <a name="next-steps"></a>Nästa steg

Om du vill `DataLoads` använda arbetsbelastningsgruppen [måste en arbetsbelastningsklassare](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) skapas för att dirigera begäranden till arbetsbelastningsgruppen.  Fortsätt till [självstudien skapa arbetsbelastningsklassare](quickstart-create-a-workload-classifier-portal.md) för att skapa en arbetsbelastningsklassare för `DataLoads` .

## <a name="see-also"></a>Se även
Mer information om hur du övervakar [arbetsbelastningar för arbetsbelastningshantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) finns i artikeln Hantera och övervaka arbetsbelastningshantering.
