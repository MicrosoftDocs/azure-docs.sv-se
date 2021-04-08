---
title: 'Snabb start: skala beräkning för Synapse SQL-pool (Azure Portal)'
description: Du kan skala beräkning för Synapse SQL-pool (informations lager) med hjälp av Azure Portal.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/28/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 26a8a865a787a9c9b17031f94456272c93380704
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117052"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Snabb start: skala beräkning för Synapse SQL-pool med Azure Portal

Du kan skala beräkning för Synapse SQL-pool (informations lager) med hjälp av Azure Portal. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för bättre prestanda eller skala upp beräkning för att spara kostnader. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan skala en SQL-pool som du redan har eller använda [snabb start: skapa och ansluta – Portal](create-data-warehouse-portal.md) för att skapa en SQL-pool med namnet **mySampleDataWarehouse**. I den här snabbstarten skalas **mySampleDataWarehouse**.

>[!IMPORTANT] 
>SQL-poolen måste vara online för att kunna skalas. 

## <a name="scale-compute"></a>Skala beräkning

Beräknings resurser för SQL-pool kan skalas genom att öka eller minska informations lager enheter. [Snabb starten: skapa och ansluta – portalen](create-data-warehouse-portal.md) skapade **mySampleDataWarehouse** och initierade den med 400 DWU: er. Följande steg justerar DWU för **mySampleDataWarehouse**.

Så här ändrar du informationslagerenheter:

1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
2. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** . SQL-poolen öppnas.
3. Klicka på **Skala**.

    ![Klicka på Skala](./media/quickstart-scale-compute-portal/click-scale.png)

2. På panelen Skala ändrar du DWU-inställningen genom att dra skjutreglaget åt vänster eller höger. Välj sedan skala.

    ![Dra skjutreglaget](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om SQL-poolen fortsätter du till självstudien [Läs in data i SQL-pool](./load-data-from-azure-blob-storage-using-copy.md) .