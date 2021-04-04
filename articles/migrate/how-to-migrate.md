---
title: Lägg till Migreringsverktyg i Azure Migrate
description: Lär dig hur du lägger till Migreringsverktyg i Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751792"
---
# <a name="add-migration-tools"></a>Lägga till migreringsverktyg

I den här artikeln beskrivs hur du lägger till Migreringsverktyg i [Azure Migrate](./migrate-services-overview.md).

- Om du vill lägga till ett Migreringsverktyg och ännu inte har skapat ett Azure Migrate projekt, följer du den här [artikeln](create-manage-projects.md).
- Om du har lagt till ett ISV-verktyg för migrering [följer du stegen](prepare-isv-movere.md)för att förbereda arbetet med verktyget.

## <a name="select-a-migration-scenario"></a>Välj ett scenario för migrering

1. Klicka på **Översikt** i Azure Migrate projektet.
2. Välj det scenario för migrering som du vill använda:

    - Om du vill migrera datorer och arbets belastningar till Azure väljer du **utvärdera och migrera servrar**.
    - Om du vill migrera lokala databaser väljer du **utvärdera och migrera databaser**.
    - Om du vill migrera lokala webbappar väljer du **utforska fler**  >  **Web Apps**.
    - Om du vill migrera data till Azure med data Box väljer du **utforska mer**  >  **data Box**.

    ![Alternativ för att välja ett migrerings scenario](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Välj ett Migreringsverktyg för Server

1. Lägg till ett verktyg:

    - Om du har skapat ett Azure Migrate projekt med hjälp av alternativet för att **utvärdera och migrera servrar** i portalen läggs verktyget Azure Migrate Migreringsverktyg automatiskt till i projektet. Om du vill lägga till ytterligare Migreringsverktyg går du till **servrar**, bredvid **Migreringsverktyg**, och väljer **Lägg till fler verktyg**.
    
         ![Knapp för att lägga till ytterligare Migreringsverktyg](./media/how-to-migrate/add-migration-tools.png)

    - Om du har skapat ett projekt med ett annat alternativ och ännu inte har några Migreringsverktyg, i Migreringsverktyg för **servrar**  >  , väljer du **Klicka här**.

    ![Knapp för att lägga till verktyg för första migrering](./media/how-to-migrate/no-migration-tool.png)

2. I **Azure Migrate**  >  **Lägg till verktyg** väljer du de verktyg som du vill lägga till. Välj sedan **Lägg till verktyg**.

    ![Välj utvärderings verktyg från listan](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Välj ett Migreringsverktyg för databas

Om du har skapat ett Azure Migrate-projekt med hjälp av alternativet **utvärdera och migrera databas** i portalen läggs Migreringsverktyget för databas automatiskt till i projektet. 

1. Om migreringen av databasen inte finns i projektet väljer du   >  **Klicka här** i **utvärderings verktyg** för databaser.
    
    ![Lägga till Migreringsverktyg för databas](./media/how-to-migrate/no-database-migration-tool.png)


2. I **Azure Migrate**  >  **Lägg till verktyg** väljer du Migreringsverktyget för databas. Välj sedan **Lägg till verktyg**.

    ![Välj Migreringsverktyget för databas från listan](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Välj ett Migreringsverktyg för webbapp

Om du har skapat ett Azure Migrate-projekt med hjälp av alternativet **utforska fler**  >  **webapps** i portalen läggs Migreringsverktyg för webbappar automatiskt till i projektet. 

1. Om Migreringsverktyg för webbapp inte finns i projektet väljer du klicka här i **Web Apps**  >  **Assessment tools**( **här**).

    ![Lägg till Migreringsverktyg för webbapp](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. I **Azure Migrate**  >  **Lägg till verktyg** väljer du Migreringsverktyg för webbapp. Välj sedan **Lägg till verktyg**.

    ![Välj verktyg för webapp Assessment från listan](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Beställ en Azure Data Box

Om du vill migrera stora mängder data till Azure kan du beställa en Azure Data Box för data överföring offline.

1. I **Översikt** väljer du **utforska mer**.
2. I **utforska mer** väljer du **data Box**.
3. I **Kom igång med data Box-enhet** väljer du den prenumeration och resurs grupp som du vill använda när du beställer en data Box-enhet.
4. **Överförings typen** är en import till Azure. Ange det land där data finns och den Azure-region som du vill överföra data till. 
5. Spara inställningarna genom att klicka på **Använd** .

## <a name="next-steps"></a>Nästa steg

Prova en migrering med hjälp av Migreringsverktyg för Azure Migrate Server för [Hyper-V-](tutorial-migrate-hyper-v.md) eller [VMware](tutorial-migrate-vmware.md) -datorer.
