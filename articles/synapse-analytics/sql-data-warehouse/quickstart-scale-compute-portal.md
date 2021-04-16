---
title: 'Snabbstart: Skala beräkning för Synapse SQL -pool (Azure Portal)'
description: Du kan skala beräkning för Synapse SQL (informationslager) med hjälp av Azure Portal.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534225"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Snabbstart: Skala beräkning för Synapse SQL en pool med Azure Portal

Du kan skala beräkning för Synapse SQL (informationslager) med hjälp av Azure Portal. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för bättre prestanda eller skala tillbaka beräkning för att spara kostnader. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan skala en SQL-pool som du redan har eller använda Snabbstart: Skapa och ansluta – portal för att skapa en [SQL-pool](create-data-warehouse-portal.md) med namnet **mySampleDataWarehouse.** I den här snabbstarten skalas **mySampleDataWarehouse**.

>[!IMPORTANT] 
>DIN SQL-pool måste vara online för skalning. 

## <a name="scale-compute"></a>Skala beräkning

Beräkningsresurser för SQL-pooler kan skalas genom att öka eller minska informationslagerenheter. [Snabbstarten: Skapa och anslut – portalen](create-data-warehouse-portal.md) skapade **mySampleDataWarehouse** och initierade den med 400 DWUs. Följande steg justerar DWU för **mySampleDataWarehouse**.

Så här ändrar du informationslagerenheter:

1. Klicka **Azure Synapse Analytics (tidigare SQL DW)** på vänster sida i Azure Portal.
2. Välj **mySampleDataWarehouse** på **Azure Synapse Analytics (tidigare SQL DW).** SQL-poolen öppnas.
3. Klicka på **Skala**.

    ![Klicka på Skala](./media/quickstart-scale-compute-portal/click-scale.png)

2. På panelen Skala ändrar du DWU-inställningen genom att dra skjutreglaget åt vänster eller höger. Välj sedan skala.

    ![Dra skjutreglaget](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om SQL-pool fortsätter du till [självstudien Läsa in data till SQL-pool.](./load-data-from-azure-blob-storage-using-copy.md)
