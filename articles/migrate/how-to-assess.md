---
title: Lägg till utvärderings verktyg i Azure Migrate
description: Lär dig hur du lägger till bedömnings verktyg i Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 9a4ed5542945b8d281ec750d9bbd3a8f444e44d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752268"
---
# <a name="add-assessment-tools"></a>Lägga till utvärderingsverktyg

Den här artikeln beskriver hur du lägger till bedömnings verktyg i [Azure Migrate](./migrate-services-overview.md). 

- Om du vill lägga till ett utvärderings verktyg och du inte har ett Azure Migrate projekt, följer du den här [artikeln](create-manage-projects.md).
- Om du har lagt till ett ISV-verktyg, eller om du vill ha en kontroll, [följer du stegen](prepare-isv-movere.md)för att förbereda arbetet med verktyget.

## <a name="select-an-assessment-scenario"></a>Välj ett utvärderings scenario

1. Klicka på **Översikt** i Azure Migrate projektet.
2. Välj utvärderings scenario:

    - Välj **utvärdera och migrera servrar** för att identifiera och utvärdera datorer och arbets belastningar för migrering till Azure.
    - Välj **utvärdera och migrera databaser** för att utvärdera lokala SQL Server databaser.
    - Om du vill utvärdera eller migrera lokala webbappar väljer du **utforska fler**  >  **Web Apps**.
    - Om du vill utvärdera din infrastruktur för virtuella skriv bord väljer du **utforska mer**  >  **virtuell Skriv bords infrastruktur**.

    ![Alternativ för att välja ett utvärderings scenario](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Välj ett Server bedömnings verktyg 


1. Lägg till ett verktyg:

    - Om du har skapat ett Azure Migrate-projekt med hjälp av alternativet **utvärdera och migrera servrar** i portalen läggs verktyget Azure Migrate Server Assessment automatiskt till i projektet. Om du vill lägga till ytterligare utvärderings verktyg väljer du **Lägg till fler verktyg** i **servrar** bredvid **utvärderings verktyg**.
    
         ![Knapp för att lägga till ytterligare utvärderings verktyg](./media/how-to-assess/add-assessment-tool.png)

    - Om du har skapat ett projekt med ett annat alternativ och ännu inte har några utvärderings verktyg, i verktyg för **Server**  >  **utvärdering**, väljer du **Klicka här**.

        ![Knapp för att lägga till första bedömnings verktyget](./media/how-to-assess/no-assessment-tool.png)

2. I **Azure Migrate**  >  **Lägg till verktyg** väljer du de verktyg som du vill lägga till. Välj sedan **Lägg till verktyg**.

    ![Välj utvärderings verktyg från listan](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Välj ett verktyg för databas bedömning

1. Lägg till ett verktyg:

    - Om du har skapat ett Azure Migrate-projekt med hjälp av alternativet **utvärdera och migrera databas** i portalen läggs verktyget för databas bedömning automatiskt till i projektet. Om du vill lägga till ytterligare utvärderings verktyg väljer du **Lägg till fler verktyg** i **databaser** bredvid **bedömnings verktyg**.

    - Om du har skapat ett projekt med ett annat alternativ och ännu inte har några verktyg för databas utvärdering, i verktyg för utvärdering av **databaser**  >  , väljer du **Klicka här**.

2. I **Azure Migrate**  >  **Lägg till verktyg** väljer du de verktyg som du vill lägga till. Välj sedan **Lägg till verktyg**.

    ![Välj verktyg för databas utvärdering från listan](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Välj ett verktyg för bedömning av webb program

Om du har skapat ett Azure Migrate-projekt med hjälp av alternativet **utforska fler**  >  **webapps** i portalen läggs verktyget för webbappens bedömning automatiskt till i projektet. 


1. Om verktyget Web App Assessment inte finns i projektet   >  väljer du **Klicka här** i Web Apps **utvärderings verktyg**.
    
    ![Lägg till verktyg för webb program utvärdering](./media/how-to-assess/no-web-app-assessment-tool.png)


2. I **Azure Migrate**  >  **Lägg till verktyg** väljer du verktyget för webbapp. Välj sedan **Lägg till verktyg**.

    ![Välj Migreringsverktyget för databas från listan](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Nästa steg

Identifiera lokala datorer för utvärdering med verktyget Azure Migrate Server Assessment för virtuella [VMware](./tutorial-discover-vmware.md) -datorer, [Hyper-V](./tutorial-discover-hyper-v.md)eller [fysiska servrar](./tutorial-discover-physical.md)