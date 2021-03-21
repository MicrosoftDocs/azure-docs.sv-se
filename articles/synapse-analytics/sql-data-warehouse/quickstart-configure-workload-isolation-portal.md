---
title: 'Snabb start: Konfigurera arbets belastnings isolering – Portal'
description: Använd Azure Portal för att konfigurera arbets belastnings isolering för dedikerad SQL-pool.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5773691852ddc723ec84503edee37c678bbbcfd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677512"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Snabb start: konfigurera dedikerad arbets belastnings isolering i SQL-pool med en arbets belastnings grupp i Azure Portal

I den här snabb starten konfigurerar du [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md) genom att skapa en arbets belastnings grupp för reservering av resurser.  I den här självstudien skapar vi arbets belastnings gruppen för inläsning av data som anropas `DataLoads` . Arbets belastnings gruppen kommer att reservera 20% av system resurserna.  Med 20% isolering för data inläsningar är de garanterat resurser som gör att de kan trycka på service avtal.  När du har skapat arbets belastnings gruppen [skapar du en klassificering för arbets belastning](quickstart-create-a-workload-classifier-portal.md) för att tilldela frågor till den här arbets belastnings gruppen


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.


## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

> [!NOTE]
> Att skapa en dedikerad instans av SQL-poolen i Azure Synapse Analytics kan resultera i en ny fakturerbar tjänst.  Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten förutsätter att du redan har en särskild instans av SQL-poolen i Synapse SQL och att du har behörighet att kontrol lera databasen. Om du behöver skapa ett använder du [snabb start: skapa en dedikerad SQL-pool – Portal](../quickstart-create-sql-pool-portal.md) för att skapa ett informations lager med namnet **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Din dedikerade SQL-pool måste vara online för att konfigurera arbets belastnings hantering. 

## <a name="configure-workload-isolation"></a>Konfigurera arbets belastnings isolering

Dedikerade SQL pool-resurser kan isoleras och reserveras för särskilda arbets belastningar genom att skapa arbets belastnings grupper.  I dokumentationen för arbets belastnings [isolering](sql-data-warehouse-workload-isolation.md) finns mer information om hur arbets belastnings grupper kan hjälpa dig att hantera arbets belastningen.  Snabb starten [skapa och Anslut – portalen](create-data-warehouse-portal.md) skapade **mySampleDataWarehouse** och initierade den på DW100c. Följande steg skapar en arbets belastnings grupp i **mySampleDataWarehouse**.

Så här skapar du en arbets belastnings grupp med 20% isolering:
1.  Gå till sidan **mySampleDataWarehouse** -dedikerad SQL-pool..
1.  Välj  **arbets belastnings hantering**.
1.  Välj **ny arbets belastnings grupp**.
1.  Välj **anpassad**.

    ![Klicka på anpassad](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Ange `DataLoads` för **arbets belastnings gruppen**.
7.  Ange `20` för **min. Resources%**.
8.  Ange `5` för **min. resurser% per begäran**.
9.  Ange `100` för **Cap-resurser%**.
10. Ange **Spara**.

   ![Klicka på Spara](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Ett Portal meddelande visas när arbets belastnings gruppen skapas.  Resurserna för arbets belastnings gruppen visas i diagrammen under de konfigurerade värdena.

   ![Klicka på slutgiltig](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort `DataLoads` arbets belastnings gruppen som skapats i den här självstudien:
1. Klicka på **`...`** till höger om arbets `DataLoads` belastnings gruppen.
2. Klicka på **ta bort arbets belastnings grupp**.
3. Klicka på **Ja** när du uppmanas att bekräfta borttagningen av arbets belastnings gruppen.
4. Klicka på **Spara**.

   ![Klicka på Ta bort](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Du debiteras för data lager enheter och data som lagras i ditt informations lager. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkning debiteras du bara för data lagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Rensa resurserna genom att följa dessa steg.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj din dedikerade SQL-pool.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **pausa** . När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort data lagret så att du inte debiteras för beräkning eller lagring väljer du **ta bort**.

## <a name="next-steps"></a>Nästa steg

Om du vill använda `DataLoads` arbets belastnings gruppen måste en [arbets belastnings klassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) skapas för att dirigera begär anden till arbets belastnings gruppen.  Fortsätt till självstudien [skapa arbets belastnings klassificering](quickstart-create-a-workload-classifier-portal.md) för att skapa en klassificering för arbets belastning för `DataLoads` .

## <a name="see-also"></a>Se även
Se artikeln om hur du [hanterar och övervakar arbets belastnings hantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) för mer information om hur du övervakar arbets belastningar för hantering av arbets belastning.
