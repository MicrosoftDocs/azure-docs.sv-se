---
title: Flytta Azure SQL-resurser mellan regioner med Azure Resource-arbetskraft
description: Lär dig hur du flyttar Azure SQL-resurser till en annan region med Azure Resource-arbetskraften
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4678a6128be13ac61dc4ac67bbd1a17e99c6d24d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820236"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Självstudie: flytta Azure SQL Database resurser till en annan region

I den här självstudien lär du dig att flytta Azure SQL-databaser och elastiska pooler till en annan Azure-region med hjälp av [Azure Resource](overview.md)överfart.

> [!NOTE]
> Azure Resource-arbetskraften är för närvarande en för hands version.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrol lera krav och krav.
> * Välj de resurser som du vill flytta.
> * Lös resurs beroenden.
> * Förbered och flytta SQL Server till mål regionen.
> * Förbered och flytta databaser och elastiska pooler.
> * Bestäm om du vill ta bort eller bekräfta flytten. 
> * Du kan också ta bort resurser i käll regionen efter flytten. 

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar. Logga sedan in på [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Förutsättningar

-  Kontrol lera att du har *ägar* åtkomst till prenumerationen som innehåller de resurser som du vill flytta.
    - Första gången du lägger till en resurs för ett visst käll-och mål par i en Azure-prenumeration skapar resurs förflyttningen en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare känd som hanterad tjänst identifiering (MSI)) som är betrodd av prenumerationen.
    - Om du vill skapa identiteten och tilldela den rollen som krävs (deltagare eller administratör för användar åtkomst i käll prenumerationen) måste kontot som du använder för att lägga till resurser ha *ägar* behörigheter för prenumerationen. [Lär dig mer](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) om Azure-roller.
- Prenumerationen måste ha tillräckligt med kvot för att skapa de resurser som du flyttar i mål regionen. Om den inte har någon kvot [begär du ytterligare begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Kontrol lera priser och avgifter som är kopplade till mål regionen som du flyttar resurser till. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att hjälpa dig.
    

## <a name="check-sql-requirements"></a>Kontrol lera SQL-krav

1. [Kontrol lera](support-matrix-move-region-sql.md) vilken databas/Elastic pool funktioner som stöds för att flytta till en annan region.
2. I mål regionen skapar du en mål server för varje käll Server. [Läs mer](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users).
4. Om databaserna är krypterade med transparent data kryptering (TDE) och du använder din egen krypterings nyckel i Azure Key Vault, [lär du dig att](../key-vault/general/move-region.md) flytta nyckel valv till en annan region.
5. Om SQL Data Sync är aktiverat stöds flyttning av medlems databaser. När du har flyttat måste du konfigurera SQL Data Sync till den nya mål databasen.
6. Ta bort avancerade data säkerhets inställningar innan du flyttar. Efter flyttningen [konfigurerar du inställningarna](../azure-sql/database/azure-defender-for-sql.md) på SQL Server nivå i mål regionen.
7. Om granskning har Aktiver ATS återställs principerna till standard efter flytten. [Konfigurera granskning](../azure-sql/database/auditing-overview.md) igen, efter flytten.
7. Principer för säkerhets kopiering av käll databasen överförs till mål databasen. [Läs mer](../azure-sql/database/long-term-backup-retention-configure.md) om att ändra inställningarna efter flytten.
8. Ta bort brand Väggs regler på server nivå innan du flyttar. Brand Väggs regler på databas nivå kopieras från käll servern till mål servern under flytten. Efter flyttningen [ställer du in brand Väggs regler](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) för SQL Server i mål regionen.
9. Ta bort inställningarna för autojustering före flytten. [Konfigurera autojusteringen ](../azure-sql/database/automatic-tuning-enable.md) igen efter flytt.
10. Ta bort varnings inställningarna för databasen innan du flyttar. [Återställ](../azure-sql/database/alerts-insights-configure-portal.md) efter flytt.
    
## <a name="select-resources"></a>Välj resurser

Välj de resurser som du vill flytta.

- Du kan välja vilken resurs typ som helst som stöds i alla resurs grupper i den valda käll regionen.
- Du flyttar resurser till en mål region i samma prenumeration som käll regionen. Om du vill ändra prenumerationen kan du göra det när resurserna har flyttats.

1. Sök efter *resurs förflyttning* i Azure Portal. Under **tjänster** väljer du sedan **Azure Resource förflyttning**.

     ![Sök Resultat för resurs förflyttning i Azure Portal](./media/tutorial-move-region-sql/search.png)

2. I **Översikt** klickar du på **Kom igång**.

    ![För att lägga till resurser som ska flyttas till en annan region](./media/tutorial-move-region-sql/get-started.png)

3. I **Flytta resurser**  >  **källa + mål** väljer du käll prenumeration och region.
4. I **mål** väljer du den region som du vill flytta resurserna till. Klicka på **Nästa**.

    ![Sidan för att välja käll-och mål region](./media/tutorial-move-region-sql/source-target.png)

6. I **resurser att flytta klickar du** på **Välj resurser**.
7. I **Välj resurser** väljer du resurserna. Du kan bara lägga till resurser som stöds för flytt. Klicka sedan på **färdig**.

    ![Sidan för att välja de SQL-resurser som ska flyttas](./media/tutorial-move-region-sql/select-resources.png)

8. I **resurser att flytta klickar du** på **Nästa**.

9. Kontrol lera käll-och mål inställningarna i **Granska + Lägg till**. Kontrol lera att du förstår att metadata om flyttningen kommer att lagras i en resurs grupp som skapats för det här syftet i metadata-regionen.


    ![Sida för att granska inställningar och fortsätta med flytten](./media/tutorial-move-region-sql/review.png)

10. Klicka på **Fortsätt** för att börja lägga till resurserna.
11. När processen har slutförts klickar du på Lägg **till resurser för flytta** i meddelande ikonen.
12. När du har klickat på meddelandet granskar du resurserna på sidan **över flera regioner** .


> [!NOTE]
> 
> - SQL Server är nu i ett *väntande tillstånd för manuell tilldelning* .
> - Andra tillagda resurser är i *vänte* läge för förberedelse.
> - Om du vill ta bort en resurs från en flyttnings samling är metoden för att göra det beroende av var du befinner dig i flyttnings processen. [Läs mer](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Matcha beroenden


1. I **flera regioner**, om resurser visar ett *validerings beroende* meddelande i kolumnen **ärenden** , klickar du på knappen **Verifiera beroenden** . Verifierings processen börjar.
2. Om det finns beroenden klickar du på **Lägg till beroenden**.

    ![Knapp för att lägga till beroenden](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. I **Lägg till beroenden** väljer du de beroende resurserna > **lägga till beroenden**. Övervaka förloppet i aviseringarna.

4. Lägg till ytterligare beroenden om det behövs och verifiera beroenden igen. 

5. På sidan **över regioner** kontrollerar du att resurserna nu är i ett *förberedelse* tillstånd utan problem.

    ![Sida som visar resurser i förberedelse av väntande tillstånd](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>Flytta SQL Server

Tilldela en mål SQL Server i mål regionen och genomför flyttningen.

### <a name="assign-a-target-sql-server"></a>Tilldela ett mål SQL Server

1. Klicka på **resurs inte tilldelad** i kolumnen **mål konfiguration** i **flera regioner** för SQL Server resursen.
2. Välj en befintlig SQL Server-resurs i mål regionen. 
    
    ![Post som visar SQL Server status som bekräftelse väntar](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> Källans SQL Server tillstånd ändras till *bekräftat flyttning väntar*. 

### <a name="commit-the-sql-server-move"></a>Genomför SQL Server flytta

1. I **flera regioner** väljer du SQL Server och klickar sedan på **genomför flyttning**.
2. I **genomför resurser** klickar du på **genomför**.

    ![För att bekräfta SQL Server flytta](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Spåra flyttnings förlopp i meddelande fältet.

> [!NOTE]
> Efter genomförandet är SQL Server nu i ett *väntande tillstånd för borttagnings källa* .


## <a name="prepare-resources-to-move"></a>Förbered resurser för att flytta

När käll SQL Server flyttas kan du förbereda dig för att flytta de andra resurserna.

## <a name="prepare-an-elastic-pool"></a>Förbereda en elastisk pool

1. I **flera regioner** väljer du den elastiska källan för den elastiska poolen (demo-TEST1-elasticpool i vår genom gång) och klickar sedan på **Förbered**.

    ![Knapp för att förbereda resurser](./media/tutorial-move-region-sql/prepare-elastic.png)

2. I **Förbered resurser** klickar du på **Förbered**.
3. När aviseringar visar att förberedelse processen har slutförts klickar du på **Uppdatera**.

> [!NOTE]
> Den elastiska poolen är nu i ett *initierings* tillstånd som väntar.

## <a name="prepare-a-single-database"></a>Förbered en enskild databas

1. I **flera regioner** väljer du den enkla databasen (inte i en elastisk pool) och klickar sedan på **Förbered**.

    ![För att förbereda valda resurser](./media/tutorial-move-region-sql/prepare-db.png)

2. I **Förbered resurser** klickar du på **Förbered**.
3. När aviseringar visar att förberedelse processen har slutförts klickar du på **Uppdatera**.

> [!NOTE]
> Databasen är nu i ett *initierings* tillstånd som väntar och har skapats i mål regionen.


## <a name="move-the-pool-and-prepare-pool-databases"></a>Flytta poolen och Förbered databaserna för pooler

För att förbereda databaser i en elastisk pool måste den elastiska poolen vara i ett tillstånd där *flytt väntar* . Om du vill flytta till det här läget börjar du flytta för poolen.

#### <a name="initiate-move---elastic-pool"></a>Initiera Move-elastisk pool

1. I **flera regioner** väljer du den elastiska källan (demo-TEST1-elasticpool i vår genom gång) och klickar sedan på **Starta flyttning**.
2. I **Flytta resurser** klickar du på **påbörja flyttning**.

    
    ![Knapp för att initiera flyttning av elastisk pool](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Spåra flyttnings förlopp i meddelande fältet.
1. När meddelandena visar att flyttningen har slutförts klickar du på **Uppdatera**.

> [!NOTE]
> Den elastiska poolen är nu i ett *förväntat flyttnings* tillstånd.

#### <a name="prepare-database"></a>Förbered databas

1. I **flera regioner** väljer du databasen (demo-TEST2-SQLDB i vår genom gång) och klickar sedan på **Förbered**.
2. I **Förbered resurser** klickar du på **Förbered**.

    ![Knapp för att förbereda databasen i elastisk pool](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Under förberedelsen skapas mål databasen i mål regionen och datareplikeringen startar. Efter förberedelsen är databasen i ett *initierings* tillstånd som väntar. 

![För att förbereda den valda databasen i den elastiska poolen](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Flytta databaser

Börja flytta databaserna.
1. I **flera regioner** väljer du resurser med tillstånds **initieringen väntar**. Klicka sedan på **Starta flyttning**.
2. I **Flytta resurser** klickar du på **påbörja flyttning**.

    ![Sida för att initiera flytt](./media/tutorial-move-region-sql/initiate-move.png)

3. Spåra flyttnings förlopp i meddelande fältet.

> [!NOTE]
> Databaserna är nu i ett tillstånd där *flytt väntar* .


## <a name="discard-or-commit"></a>Ta bort eller bekräfta?

Efter den första flyttningen kan du bestämma om du vill att flyttningen ska utföras eller om du vill ta bort den. 

- **Ignorera**: du kanske vill ta bort en flytt om du testar och du inte vill flytta käll resursen. Om du tar bort flytten returneras resursen till ett tillstånd där **initieringen väntar**.
- **Commit**: commit Slutför flyttningen till mål regionen. När du har gjort det kommer en käll resurs att vara i ett tillstånd där **borttagnings källan väntar**, och du kan välja om du vill ta bort den.


## <a name="discard-the-move"></a>Ta bort flytten 

Du kan ta bort flytten på följande sätt:

1. I **flera regioner** väljer du resurser med status **commit flytta väntar** och klickar på **ta bort flyttning**.
2. I **ta bort flyttning** klickar du på **Ignorera**.
3. Spåra flyttnings förlopp i meddelande fältet.


> [!NOTE]
> - När du har tagit bort resurser är de i ett *initierings* tillstånd som väntar.
> - Om det bara finns en elastisk pool raderas förloppet och den elastiska pool som skapas i mål regionen tas bort.
> - Om det finns en elastisk pool med associerade databaser i väntande tillstånd för att *Bekräfta förflyttningen* kan du inte ta bort den elastiska poolen.
> - Om du tar bort en SQL-databas raderas inte mål regions resurser. 

Om du vill starta flyttningen igen efter att den tagits bort väljer du SQL-databasen eller den elastiska poolen och påbörjar flyttningen igen.


## <a name="commit-the-move"></a>Genomför flyttningen

Avslutar flyttningen av databaser och elastiska pooler enligt följande:


1. Kontrol lera att den SQL Server är ett *väntande tillstånd för borttagnings källan* .
2. Uppdatera databas anslutnings strängarna till mål regionen innan du genomför.
3. I **flera regioner** väljer du SQL-resurser och klickar sedan på **genomför flyttning**.
4. I **genomför resurser** klickar du på **genomför**.

    ![Genomför flytt](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Spåra inchecknings förloppet i meddelande fältet.


> [!NOTE]
> En del avbrott inträffar för SQL-databaser under inchecknings processen.
> Allokerade databaser och elastiska pooler är nu i ett *väntande tillstånd för borttagnings källa* .
> Efter incheckningen ska du uppdatera databas inställningarna, inklusive brand Väggs regler, principer och aviseringar, på mål databasen.


## <a name="delete-source-resources-after-commit"></a>Ta bort käll resurser efter incheckning

Efter flytten kan du välja att ta bort resurser i käll regionen. 

> [!NOTE]
> Det går inte att ta bort SQL Server servrar från portalen och de måste tas bort från egenskaps sidan resurs.

1. I **flera regioner** klickar du på namnet på den käll resurs som du vill ta bort.
2. Välj **ta bort källa**.

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Flyttade Azure SQL-databaser till en annan Azure-region.
> * Flyttade Azure SQL elastiska pooler till en annan region.

Nu kan du prova att flytta virtuella Azure-datorer till en annan region.

> [!div class="nextstepaction"]
> [Migrera virtuella Azure-datorer](./tutorial-move-region-virtual-machines.md)