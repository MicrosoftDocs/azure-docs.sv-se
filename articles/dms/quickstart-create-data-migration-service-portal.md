---
title: 'Snabb start: skapa en instans med hjälp av Azure Portal'
titleSuffix: Azure Database Migration Service
description: Använd Azure Portal för att skapa en instans av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 8a500104a0273b9e131815c4dc832bd33729cd51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566599"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Snabbstart: Skapa en instans av Azure Database Migration Service med hjälp av Azure-portalen

I den här snabb starten använder du Azure Portal för att skapa en instans av Azure Database Migration Service. När du har skapat instansen kan du använda den för att migrera data från flera databas källor till Azure-dataplattformar, till exempel från SQL Server till Azure SQL Database eller från SQL Server till en Azure SQL-hanterad instans.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren, gå till [Microsoft Azure-portalen](https://portal.azure.com/) och logga in genom att ange dina autentiseringsuppgifter. Standardvyn är instrumentpanelen.

> [!NOTE]
> Du kan skapa upp till 10 instanser av DMS per prenumeration per region. Om du behöver ett större antal instanser skapar du ett support ärende.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

Registrera resursprovidern Microsoft.DataMigration innan du skapar din första instans av Database Migration Service.

1. Sök efter och välj **prenumerationer** i Azure Portal.

   ![Visa portalprenumerationer](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** för **Microsoft. data migration**.

    ![Registrera resursprovider](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Skapar en instans av tjänsten

1. På Azure Portal-menyn eller på **Start** sidan väljer du **skapa en resurs**. Sök efter och välj **Azure Database migration service**.

    ![Azure Marketplace](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. På skärmen för att **skapa Migreringsverktyg för tjänsten** :

     - Välj prenumerationen.
     - Skapa en ny resurs grupp eller Välj en befintlig.
     - Ange ett namn på instansen för Azure Database Migration Service.
     - Välj den plats där du vill skapa instansen av Azure Database Migration Service.
     - Välj **Azure** som tjänst läge.
     - Välj en prisnivå. Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).
     
    ![Konfigurera inställningar för grundläggande Azure Database Migration Service-instans](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Välj Nästa: Nätverk.

4. På skärmen **skapa migrerings tjänst** nätverk:

    - Välj ett befintligt virtuellt nätverk eller skapa ett nytt. Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll databasen och mål miljön. Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurera Azure Database Migration Service instans nätverks inställningar](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Välj **Granska + skapa** för att skapa tjänsten. 
    
    - Efter en liten stund skapas din instans av Azure Database migration service och är redo att användas:

    ![Migreringstjänsten har skapats](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan rensa resurserna som du har skapat i den här snabb starten genom att ta bort [Azures resurs grupp](../azure-resource-manager/management/overview.md). Ta bort resursgruppen genom att navigera till instansen av Azure Database Migration Service som du skapade. Markera **resursgruppsnamnet** och välj sedan **Ta bort resursgrupp**. Den här åtgärden tar bort alla resurser i resursgruppen samt själva gruppen.

## <a name="next-steps"></a>Nästa steg

* [Migrera SQL Server till Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
* [Migrera SQL Server till Azure SQL Database online](./tutorial-sql-server-to-azure-sql.md)
* [Migrera SQL Server till en Azure SQL-hanterad instans offline](tutorial-sql-server-to-managed-instance.md)
* [Migrera SQL Server till en Azure SQL-hanterad instans online](tutorial-sql-server-managed-instance-online.md)