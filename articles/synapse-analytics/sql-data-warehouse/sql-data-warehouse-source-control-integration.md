---
title: Källkontrollsintegrering
description: Databas DevOps-upplevelse i företags klass för dedikerad SQL-pool med intern käll kontroll integrering med Azure databaser (git och GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 681b60ac664a210971f083900b9d8b5ba5df119f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617564"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Käll kontrolls integrering för dedikerad SQL-pool i Azure Synapse Analytics

I den här självstudien beskrivs hur du integrerar ditt SQL Server Data Tools (SSDT)-databas projekt med käll kontroll.  Käll kontroll integrering är det första steget i att skapa en kontinuerlig integrerings-och distributions-pipeline med den dedikerade SQL-poolen i Azure Synapse Analytics.

## <a name="before-you-begin"></a>Innan du börjar

- Registrera dig för en [Azure DevOps-organisation](https://azure.microsoft.com/services/devops/)
- Gå igenom självstudien [skapa och Anslut](create-data-warehouse-portal.md)
- [Installera Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurera och Anslut till Azure-DevOps

1. Skapa ett projekt i din Azure DevOps-organisation som är värd för ditt SSDT Database-projekt via en Azure lagrings platsen-lagringsplats.

   ![Skapa projekt](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Skapa projekt")

2. Öppna Visual Studio och Anslut till din Azure DevOps-organisation och projekt från steg ett genom att välja **Hantera anslutning**.

   ![Hantera anslutningar](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Hantera anslutningar")

3. Anslut till projektet genom att välja **hantera anslutningar** och sedan **ansluta till ett projekt**.
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Anslut")


4. Leta upp projektet som du skapade i steg ett, Välj **Anslut**.
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Anslut")


3. Klona din Azure DevOps-lagringsplats från projektet till din lokala dator.

   ![Klona lagrings platsen](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klona lagrings platsen")

Mer information om hur du ansluter projekt med Visual Studio finns i avsnittet [ansluta till projekt i team Explorer](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true). Information om hur du klonar en lagrings platsen med Visual Studio finns i artikeln [klona a avslutar git lagrings platsen](/azure/devops/repos/git/clone?tabs=visual-studio) . 

## <a name="create-and-connect-your-project"></a>Skapa och Anslut ditt projekt

1. I Visual Studio skapar du ett nytt SQL Server Database-projekt med både en katalog och en lokal git-lagringsplats i din **lokala klonade lagrings plats**.

   ![Skapa nytt projekt](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Skapa nytt projekt")  

2. Högerklicka på din tomma sqlproject och importera data lagret till databas projektet.

   ![Importera projekt](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importera projekt")  

3. I team Explorer i Visual Studio ska du spara ändringarna i din lokala git-lagringsplats.

   ![Checka in](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Checka in")  

4. Nu när du har gjort ändringarna lokalt i den klonade lagrings platsen synkroniserar du och skickar ändringarna till din Azure lagrings platsen-lagringsplats i Azure DevOps-projektet.

   ![Synkronisering och push-mellanlagring](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synkronisering och push-mellanlagring")

   ![Synkronisera och push](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synkronisera och push")  

## <a name="validation"></a>Validering

1. Kontrol lera att ändringarna har flyttats till din Azure-lagrings platsen genom att uppdatera en tabell kolumn i ditt databas projekt från Visual Studio SQL Server Data Tools (SSDT).

   ![Verifiera uppdaterings kolumn](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Verifiera uppdaterings kolumn")

2. Genomför och skicka ändringarna från din lokala lagrings plats till din Azure-lagrings platsen.

   ![Push-överföring av ändringar](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Push-överföring av ändringar")

3. Kontrol lera att ändringen har överförts i din Azure lagrings platsen-lagringsplats.

   ![Verifiera](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verifiera ändringarna")

4. (**Valfritt**) Använd schema jämför och uppdatera ändringarna i din dedikerade SQL-pool med hjälp av SSDT för att se till att objekt definitionerna i Azure lagrings platsen-lagringsplatsen och den lokala lagrings platsen återspeglar din dedikerade SQL-pool.

## <a name="next-steps"></a>Nästa steg

- [Utveckla för dedikerad SQL-pool](sql-data-warehouse-overview-develop.md)