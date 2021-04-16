---
title: Källkontrollsintegrering
description: Database DevOps-upplevelse i företagsklass för dedikerad SQL-pool med inbyggd källkontrollintegrering med hjälp av Azure Repos (Git och GitHub).
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 53adbc0288c28c3b18632539c3f812bbca82da92
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566162"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Källkontrollsintegrering för dedikerad SQL-pool i Azure Synapse Analytics

Den här självstudien beskriver hur du integrerar ditt SQL Server Data Tools(SSDT)-databasprojekt med källkontroll.  Källkontrollsintegrering är det första steget i att skapa en pipeline för kontinuerlig integrering och distribution med den dedikerade SQL-poolresursen i Azure Synapse Analytics.

## <a name="before-you-begin"></a>Innan du börjar

- Registrera dig för en [Azure DevOps-organisation](https://azure.microsoft.com/services/devops/)
- Gå igenom [självstudien Skapa och](create-data-warehouse-portal.md) anslut
- [Installera Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurera och ansluta till Azure DevOps

1. I Din Azure DevOps-organisation skapar du ett projekt som ska vara värd för ditt SSDT-databasprojekt via en Azure Repo-lagringsplats.

   ![Skapa projekt](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Skapa projekt")

2. Öppna Visual Studio och anslut till din Azure DevOps-organisation och projekt från steg ett genom att välja **Hantera anslutning.**

   ![Hantera anslutningar](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Hantera anslutningar")

3. Anslut till projektet genom att välja **Hantera anslutningar** och sedan Anslut till **ett projekt**.
 
    ![Anslut1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Anslut")


4. Leta upp projektet som du skapade i steg ett och välj **Anslut.**
 
    ![Anslut2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Anslut")


3. Klona Azure DevOps-lagringsplatsen från projektet till den lokala datorn.

   ![Klona lagringsplatsen](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klona lagringsplatsen")

Mer information om hur du ansluter projekt med Visual Studio finns i [Anslut till projekt i Team Explorer.](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true) Vägledning om hur du klonar en lagringsplatsen med Visual Studio finns i artikeln Clone an exiting Git repo (Klona en [avslutande Git-lagringsplatsen).](/azure/devops/repos/git/clone?tabs=visual-studio) 

## <a name="create-and-connect-your-project"></a>Skapa och ansluta ditt projekt

1. I Visual Studio skapar du ett nytt SQL Server Database Project med både en katalog och en lokal Git-lagringsplats på den **lokala klonade lagringsplatsen**.

   ![Skapa nytt projekt](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Skapa nytt projekt")  

2. Högerklicka på ditt tomma sqlproject och importera ditt informationslager till databasprojektet.

   ![Importera projekt](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importera projekt")  

3. I Team Explorer i Visual Studio du ändringarna till din lokala Git-lagringsplats.

   ![Checka in](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Checka in")  

4. Nu när du har gjort ändringarna lokalt på den klonade lagringsplatsen synkroniserar du och push-erar ändringarna till Azure Repo-lagringsplatsen i Ditt Azure DevOps-projekt.

   ![Synkronisera och push-pusha – mellanlagring](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synkronisera och push-pusha – mellanlagring")

   ![Synkronisera och push-pusha](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synkronisera och push-pusha")  

## <a name="validation"></a>Validering

1. Kontrollera att ändringar har push-lagts till i azure-lagringsplatsen genom att uppdatera en tabellkolumn i databasprojektet från Visual Studio SQL Server Data Tools (SSDT).

   ![Verifiera uppdateringskolumnen](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Verifiera uppdateringskolumnen")

2. Genomför och push-skicka ändringen från din lokala lagringsplats till Din Azure-lagringsplats.

   ![Push-överföring av ändringar](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Push-överföring av ändringar")

3. Kontrollera att ändringen har push-lagts till på azure-lagringsplatsen.

   ![Verifiera](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verifiera ändringarna")

4. (**Valfritt**) Använd SchemaJämför och uppdatera ändringarna till din dedikerade SQL-målpool med SSDT för att säkerställa att objektdefinitionerna på din Azure-lagringsplats och lokala lagringsplats återspeglar din dedikerade SQL-pool.

## <a name="next-steps"></a>Nästa steg

- [Utveckla för dedikerad SQL-pool](sql-data-warehouse-overview-develop.md)