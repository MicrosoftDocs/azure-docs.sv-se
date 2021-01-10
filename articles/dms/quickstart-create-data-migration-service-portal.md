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
ms.date: 07/21/2020
ms.openlocfilehash: ff9fc2baaf1563d4a02364db00344ffc0bc46a6a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060273"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Snabbstart: Skapa en instans av Azure Database Migration Service med hjälp av Azure-portalen

I den här snabb starten använder du Azure Portal för att skapa en instans av Azure Database Migration Service.  När du har skapat instansen kan du använda den för att migrera data från SQL Server till Azure SQL Database.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren, gå till [Microsoft Azure-portalen](https://portal.azure.com/) och logga in genom att ange dina autentiseringsuppgifter.

Standardvyn är instrumentpanelen.

> [!NOTE]
> Du kan skapa upp till 10 instanser av DMS per prenumeration per region. Om du behöver ett större antal instanser skapar du ett support ärende.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

Registrera resursprovidern Microsoft.DataMigration innan du skapar din första instans av Database Migration Service.

1. Gå till Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Skapar en instans av tjänsten

1. Välj +**skapa en resurs** för att skapa en instans av Azure Database migration service.

2. Sök efter ”migration” på Marketplace och välj **Azure Database Migration Service**. På skärmen **Azure Database Migration Service** väljer du sedan **Skapa**.

3. På skärmen **Skapa migreringstjänst**:

    - Välj ett **tjänst namn** som är minnes värt och unikt för att identifiera din instans av Azure Database migration service.
    - Välj den Azure-**prenumeration** där du vill skapa instansen.
    - Välj en befintlig **resurs grupp** eller skapa en ny.
    - Välj den **plats** som ligger närmast din käll- eller målserver.
    - Välj ett befintligt **virtuellt nätverk** eller skapa ett.

        Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll databasen och mål miljön.

        Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

    - Välj Basic: 1 virtuell kärna för **Prisnivå**.

        ![Skapa migreringstjänsten](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Välj **Skapa**.

    Efter en liten stund skapas din instans av Azure Database migration service och är redo att användas. Azure Database Migration Service visas på det sätt som visas i följande bild:

    ![Migreringstjänsten har skapats](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du har skapat i den här snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/management/overview.md). Ta bort resursgruppen genom att navigera till instansen av Azure Database Migration Service som du skapade. Markera **resursgruppsnamnet** och välj sedan **Ta bort resursgrupp**. Den här åtgärden tar bort alla resurser i resursgruppen samt själva gruppen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera SQL Server till Azure SQL Database](tutorial-sql-server-to-azure-sql.md)