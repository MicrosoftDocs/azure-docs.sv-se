---
title: 'Självstudie: & redundans för geo-replikering i portalen'
description: Konfigurera geo-replikering för en databas med hjälp av Azure Portal och initiera redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673264"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Självstudie: Konfigurera aktiv geo-replikering och redundans i Azure Portal (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln visar hur du konfigurerar [aktiv geo-replikering för Azure SQL Database](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) att använda [Azure Portal](https://portal.azure.com) och för att initiera redundans.

För bästa praxis med grupper för automatisk redundans, se [metod tips för Azure SQL Database](auto-failover-group-overview.md#best-practices-for-sql-database) och [metod tips för Azure SQL-hanterad instans](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera aktiv geo-replikering med hjälp av Azure Portal behöver du följande resurs:

* En databas i Azure SQL Database: den primära databasen som du vill replikera till en annan geografisk region.

> [!Note]
> När du använder Azure Portal kan du bara skapa en sekundär databas inom samma prenumeration som den primära. Om det krävs en sekundär databas i en annan prenumeration använder du [create database REST API](/rest/api/sql/databases/createorupdate) eller [Alter Database Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Lägg till en sekundär databas

Följande steg skapar en ny sekundär databas i en partnerskap för geo-replikering.  

Om du vill lägga till en sekundär databas måste du vara prenumerations ägare eller medägare.

Den sekundära databasen har samma namn som den primära databasen och har som standard samma tjänste nivå och beräknings storlek. Den sekundära databasen kan vara en enskild databas eller en databas i poolen. Mer information finns i [DTU-baserad inköps modell](service-tiers-dtu.md) och [vCore-baserad inköps modell](service-tiers-vcore.md).
När den sekundära har skapats och dirigerats påbörjar data replikeringen från den primära databasen till den nya sekundära databasen.

> [!NOTE]
> Om partner databasen redan finns (till exempel som ett resultat av en tidigare geo-replikeringsrelation) Miss lyckas kommandot.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till den databas som du vill konfigurera för geo-replikering.
2. På sidan SQL Database väljer du **geo-replikering** och väljer sedan den region där du vill skapa den sekundära databasen. Du kan välja en annan region än den region som är värd för den primära databasen, men vi rekommenderar den [kopplade regionen](../../best-practices-availability-paired-regions.md).

    ![Konfigurera geo-replikering](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Välj eller konfigurera server och pris nivå för den sekundära databasen.

    ![Skapa sekundärt formulär](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Alternativt kan du lägga till en sekundär databas i en elastisk pool. Om du vill skapa den sekundära databasen i en pool klickar du på **elastisk pool** och väljer en pool på mål servern. En pool måste redan finnas på mål servern. Det här arbets flödet skapar inte någon pool.
5. Klicka på **skapa** för att lägga till den sekundära.
6. Den sekundära databasen skapas och initierings processen påbörjas.

    ![sekundär mappning](./media/active-geo-replication-configure-portal/seeding0.png)
7. När initierings processen är klar visas dess status i den sekundära databasen.

    ![Dirigering har slutförts](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initiera en redundansväxling

Den sekundära databasen kan växlas till den primära.  

1. I [Azure Portal](https://portal.azure.com)bläddrar du till den primära databasen i partnerskapet för geo-replikering.
2. På bladet SQL Database väljer du **alla inställningar**  >  **geo-replikering**.
3. I listan **sekundära** väljer du den databas som du vill bli den nya primära och klickar på **Framtvinga redundans**.

    ![redundans](./media/active-geo-replication-configure-portal/secondaries.png)
4. Klicka på **Ja** för att starta redundansväxlingen.

Kommandot växlar omedelbart den sekundära databasen till den primära rollen. Den här processen bör normalt slutföras inom 30 SEK eller mindre.

Det finns en kort period under vilken båda databaserna inte är tillgängliga (i ordningen 0 till 25 sekunder) När rollerna växlas. Om den primära databasen har flera sekundära databaser konfigurerar kommandot automatiskt om de andra sekundärerna för att ansluta till den nya primära servern. Hela åtgärden bör ta mindre än en minut att slutföra under normala omständigheter.

> [!NOTE]
> Det här kommandot har utformats för snabb återställning av databasen i händelse av ett avbrott. Den utlöser redundans utan datasynkronisering (framtvingad redundansväxling).  Om den primära är online och genomför transaktioner när kommandot utfärdas kan viss data förlust uppstå.

## <a name="remove-secondary-database"></a>Ta bort sekundär databas

Den här åtgärden avslutar replikeringen till den sekundära databasen permanent och ändrar rollen för den sekundära till en vanlig Läs-och skriv databas. Om anslutningen till den sekundära databasen bryts, lyckas kommandot men den sekundära blir inte skrivskyddad förrän anslutningen har återställts.  

1. I [Azure Portal](https://portal.azure.com)bläddrar du till den primära databasen i partnerskapet för geo-replikering.
2. På SQL Database-sidan väljer du **geo-replikering**.
3. I listan **sekundära** väljer du den databas som du vill ta bort från partnerskapet för geo-replikering.
4. Klicka på **stoppa replikering**.

    ![Ta bort sekundär](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Ett bekräftelse fönster öppnas. Klicka på **Ja** om du vill ta bort databasen från partnerskapet för geo-replikering. (Ange den som en skrivskyddad databas som inte tillhör någon replikering.)

## <a name="next-steps"></a>Nästa steg

* Mer information om aktiv geo-replikering finns i [aktiv geo-replikering](active-geo-replication-overview.md).
* Information om grupper för automatisk redundans finns i [grupper för automatisk redundans](auto-failover-group-overview.md)
* En översikt över kontinuitet och scenarier för affärs kontinuitet finns i [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).