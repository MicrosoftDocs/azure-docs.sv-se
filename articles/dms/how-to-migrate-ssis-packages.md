---
title: Distribuera om SSIS-paket till en SQL-databas
titleSuffix: Azure Database Migration Service
description: Lär dig hur du migrerar eller distribuerar om SQL Server Integration Services-paket och-projekt till Azure SQL Database enskild databas med Azure Database Migration Service och Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e42b5ab2bbdebb2584b38531bd6458a98563ef42
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643735"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Distribuera om SSIS-paket till Azure SQL Database med Azure Database Migration Service

Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt/-paket från käll-SSISDB som är värd för SQL Server till målet SSISDB som är värdbaserade av Azure SQL Database kan du distribuera om dem med hjälp av distributions guiden för integrerings tjänster. Du kan starta guiden inifrån SQL Server Management Studio (SSMS).

Om den version av SSIS som du använder är tidigare än 2012, innan du distribuerar om dina SSIS-projekt/-paket till projekt distributions modellen, måste du först konvertera dem med hjälp av konverterings guiden för integrerings tjänster, som också kan startas från SSMS. Mer information finns i artikeln [konvertera projekt till projekt distributions modellen](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) stöder för närvarande inte migrering av en käll-SSISDB till Azure SQL Database, men du kan distribuera om SSIS-projekt/-paketen med följande process.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Utvärdera käll SSIS projekt/paket.
> * Migrera SSIS-projekt/-paket till Azure.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra de här stegen:

* SSMS version 17,2 eller senare.
* En instans av mål databas servern som ska vara värd för SSISDB. Om du inte redan har en, skapar du en [logisk SQL-Server](../azure-sql/database/logical-servers.md) (utan en databas) med hjälp av Azure Portal genom att gå till SQL Server (endast logisk server) [form](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
* SSIS måste etableras i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med den mål-SSISDB som är värd för SQL Database (enligt beskrivningen i artikeln [etablera Azure-SSIS integration runtime i Azure Data Factory](../data-factory/tutorial-deploy-ssis-packages-azure.md)).

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera käll SSIS projekt/paket

Även om utvärderingen av käll-SSISDB inte har integrerats i databasen Migration Assistant (DMA) eller Azure Database Migration Service (DMS) kommer dina SSIS-projekt/-paket att bedömas/verifieras när de distribueras om till den mål SSISDB som är värd för Azure SQL Database.

## <a name="migrate-ssis-projectspackages"></a>Migrera SSIS-projekt/-paket

Utför följande steg om du vill migrera SSIS-projekt/-paket till Azure SQL Database.

1. Öppna SSMS och välj sedan **alternativ** för att Visa dialog rutan **Anslut till Server** .

2. På fliken **inloggning** anger du den information som krävs för att ansluta till den server som ska vara värd för mål-SSISDB.

    ![Fliken SSIS login](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. På fliken **anslutnings egenskaper** i text rutan **Anslut till databas** väljer eller anger du **SSISDB** och väljer sedan **Anslut**.

    ![SSIS-fliken Egenskaper för anslutning](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. I SSMS Object Explorer expanderar du noden **Integration Services-kataloger** , expanderar **SSISDB** och om det inte finns några befintliga mappar, högerklickar du på **SSISDB** och skapar en ny mapp.

5. Under **SSISDB**, expanderar du en mapp, högerklickar på **projekt** och väljer sedan **distribuera projekt**.

    ![SSIS SSISDB-noden expanderad](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Granska informationen på sidan **Introduktion** i distributions guiden för integrerings tjänster och välj sedan **Nästa**.

    ![Distributions guidens introduktions sida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. På sidan **Välj källa** anger du det befintliga SSIS-projekt som du vill distribuera.

    Om SSMS också är ansluten till SQL Server som är värd för käll-SSISDB väljer du **Integration Services-katalogen** och anger sedan Server namnet och projekt Sök vägen i katalogen för att distribuera projektet direkt.

    Alternativt väljer du **projekt distributions fil** och anger sedan sökvägen till en befintlig projekt distributions fil (. ispac) för att distribuera projektet.

    ![Sidan Välj källa i distributions guiden](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Välj **Nästa**.
9. På sidan **Välj mål** anger du målet för projektet.

    a. I text rutan Server Namn anger du det fullständigt kvalificerade Server namnet (<server_name>. database.windows.net).

    b. Ange autentiseringsinformation och välj sedan **Anslut**.

    ![Sidan för distributions guiden Välj målsidan](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Välj **Bläddra** för att ange målmappen i SSISDB och välj sedan **Nästa**.

    > [!NOTE]
    > Knappen **Nästa** aktive ras bara när du har valt **Anslut**.

10. Visa eventuella fel och varningar på sidan **Verifiera** och ändra sedan paketen efter behov.

    ![Sidan verifiera distributions guiden](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Välj **Nästa**.

12. På sidan **Granska** granskar du distributions inställningarna.

    > [!NOTE]
    > Du kan ändra inställningarna genom att välja **föregående** eller genom att välja någon av steg länkarna i det vänstra fönstret.

13. Välj **distribuera** för att starta distributions processen.

14. När distributions processen har slutförts kan du Visa resultat sidan som visar att varje distributions åtgärd lyckades eller Miss lyckas.
    a. Om en åtgärd Miss lyckas väljer du **misslyckades** i kolumnen **resultat** för att visa en förklaring av felet.
    b. Du kan också välja **Spara rapport** om du vill spara resultatet i en XML-fil.

15. Välj **Stäng** för att avsluta distributions guiden för integrerings tjänster.

Om distributionen av projektet lyckas utan problem kan du välja de paket som det innehåller för att köra på din Azure-SSIS IR.

## <a name="next-steps"></a>Nästa steg

* Läs igenom vägledningen för migrering i Microsoft [Database migration guide](https://datamigration.microsoft.com/).