---
title: 'Självstudie: Migrera SQL Server offline till Azure SQL Database'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera från SQL Server till Azure SQL Database offline genom att använda Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 9c3fa0d8ac4540495e8580fd208507a2c1aaa7ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180792"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>Självstudie: Migrera SQL Server till Azure SQL Database med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en SQL Server instans till [Azure SQL Database](/azure/sql-database/). I den här självstudien migrerar du [Adventureworks2016](/sql/samples/adventureworks-install-configure#download-backup-files) -databasen som återställs till en lokal instans av SQL Server 2016 (eller senare) till en databas eller en databas i pooler i Azure SQL Database genom att använda Azure Database migration service.

Du lär dig hur du:
> [!div class="checklist"]
>
> - Utvärdera och utvärdera din lokala databas för eventuella spärrnings problem med hjälp av Data Migration Assistant.
> - Använd Data Migration Assistant för att migrera databasens exempel schema. 
> - Registrera Azure Data Migration Resource Provider.
> - Skapa en instans av Azure Database Migration Service.
> - Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> - Köra migreringen.
> - Övervaka migreringen.


## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Hämta och installera [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads).
- Aktivera TCP/IP-protokollet, som är inaktiverat som standard under SQL Server Express-installation, genom att följa instruktionerna i artikeln om att [aktivera eller inaktivera ett servernätverksprotokoll](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Skapa en databas i Azure SQL Database som du gör genom att följa informationen i artikeln [skapa en databas i Azure SQL Database med hjälp av Azure Portal](../azure-sql/database/single-database-create-quickstart.md). För den här självstudiekursen antas namnet på Azure SQL Database vara **AdventureWorksAzure** men du kan ange ett valfritt namn.

    > [!NOTE]
    > Om du använder SQL Server Integration Services (SSIS) och vill migrera katalogdatabasen för dina SSIS-projekt/-paket (SSISDB) från SQL Server till Azure SQL Database skapas och hanteras målet SSISDB och automatiskt åt dig när du etablerar SSIS i Azure Data Factory (ADF). Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](./how-to-migrate-ssis-packages.md) (Migrera SQL Server Integration Services-paket till Azure).
  
- Hämta och installera den senaste versionen av [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    >
    > - Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > - Lagrings slut punkt
    > - Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.
    >
    >Om du inte har plats-till-plats-anslutning mellan det lokala nätverket och Azure, eller om det finns begränsad bandbredd för plats-till-plats-anslutning, bör du överväga att använda Azure Database Migration Service i hybrid läge (för hands version). Hybrid läge utnyttjar en lokal migrering av arbetare tillsammans med en instans av Azure Database Migration Service som körs i molnet. Information om hur du skapar en instans av Azure Database Migration Service i hybrid läge finns i artikeln [skapa en instans av Azure Database migration service i hybrid läge med hjälp av Azure Portal](./quickstart-create-data-migration-service-hybrid-portal.md).

- Se till att säkerhets reglerna för utgående säkerhets grupper i nätverket för nätverks säkerhets gruppen inte blockerar den utgående porten 443 av ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG trafik filtrering i Azure Virtual Network finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen för att tillåta Azure Database Migration Service åtkomst till käll SQL Server, vilket som standard är TCP-port 1433. Om standard instansen lyssnar på någon annan port lägger du till den i brand väggen.
- Om du kör flera namngivna SQL Server instanser med dynamiska portar kanske du vill aktivera tjänsten SQL Browser och tillåta åtkomst till UDP-port 1434 genom brand väggarna så att Azure Database Migration Service kan ansluta till en namngiven instans på käll servern.
- När du använder en brand Väggs installation framför dina käll databaser, kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering.
- Skapa en regel för IP- [brandvägg](../azure-sql/database/firewall-configure.md) på server nivå för Azure SQL Database att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen har [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-behörigheter.
- Se till att de autentiseringsuppgifter som används för att ansluta till mål Azure SQL Databases instansen har [kontroll databas](/sql/t-sql/statements/grant-database-permissions-transact-sql) behörighet på mål databaserna.

## <a name="assess-your-on-premises-database"></a>Utvärdera din lokala databas

Innan du kan migrera data från en SQL Server instans till en databas eller databas i pooler i Azure SQL Database måste du utvärdera SQL Server databasen för eventuella spärrnings problem som kan förhindra migrering. Med hjälp av Data Migration Assistant följer du stegen som beskrivs i artikeln [utföra en utvärdering av SQL Server migrering](/sql/dma/dma-assesssqlonprem) för att slutföra utvärderingen av den lokala databasen. En sammanfattning av de steg som krävs följer:

1. I Data Migration Assistant väljer du ikonen Nytt (+) och väljer sedan **Utvärdering** projekttyp.
2. Ange ett projekt namn. I list **rutan bedömnings typ** väljer du **databas motor** i text rutan **käll Server typ** , väljer **SQL Server**, i text rutan **mål server typ** , väljer **Azure SQL Database** och väljer sedan **skapa** för att skapa projektet.

    När du utvärderar SQL Server-källdatabasen som ska migreras till en enkel databas eller pooldatabas i Azure SQL Database, kan du välja en eller båda av följande utvärderingsrapporttyper:

   - Kontrollera databaskompatibilitet
   - Kontrollera funktionsparitet

    Båda rapporttyperna väljs som standard.

3. I Data Migration Assistant, på sidan **Alternativ** väljer du **Näsa**.
4. På sidan **Välj källor** i dialogrutan **Anslut till en server**, anger du anslutningsinformationen till SQL Server och väljer sedan **Anslut**.
5. I dialog rutan **Lägg till källor** väljer du **Adventureworks2016**, väljer **Lägg till** och väljer sedan **Starta utvärdering**.

    > [!NOTE]
    > Om du använder SSIS så har DMA för närvarande inget stöd för utvärdering av SSISDB-källan. Dock utvärderas/verifieras SSIS-projekt/-paket när de omdistribueras till SSISDB-målet med Azure SQL Database. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](./how-to-migrate-ssis-packages.md) (Migrera SQL Server Integration Services-paket till Azure).

    När utvärderingen är klar visas resultatet enligt följande bild:

    ![Utvärdera datamigrering](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    För databaser i Azure SQL Database identifierar utvärderingarna funktions paritets problem och problem vid migrering som ska distribueras till en enskild databas eller en databas i poolen.

    - Kategorin **SQL Server-funktionsparitet** innehåller en omfattande uppsättning rekommendationer, alternativa metoder tillgängliga i Azure och minimeringsåtgärder för att hjälpa dig att planera insatsen för dina migreringsprojekt.
    - Kategorin **kompatibilitetsproblem** identifierar funktioner som stöds delvis eller som inte stöds och som avspeglar kompatibilitetsproblem som kan blockera migrering av SQL Server databas (er) till Azure SQL Database. Det ges även rekommendationer, så att du kan hantera problemen.

6. Granska utvärderingsresultaten för blockeringsproblem för migreringen och funktionsparitetsproblem genom att välja de specifika alternativen.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

När du är nöjd med utvärderingen och finner att den valda databasen är en giltig kandidat för migrering till en enkel databas eller en pooldatabas i Azure SQL Database använder du DMA för att migrera schemat till Azure SQL Database.

> [!NOTE]
> Innan du skapar ett migreringsjobb i Data Migration Assistant måste du vara säker på att du redan har skapat en databas i Azure som nämns i kraven. 

> [!IMPORTANT]
> Om du använder SSIS stöder DMA för närvarande inte migrering av käll-SSISDB, men du kan distribuera om ditt SSIS-projekt/-paket till SSISDB-målet med Azure SQL Database. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](./how-to-migrate-ssis-packages.md) (Migrera SQL Server Integration Services-paket till Azure).

Gör så här om du vill migrera **Adventureworks2016** -schemat till en databas eller databas Azure SQL Database:

1. I Data Migration Assistant väljer du ikonen Nytt (+) och sedan under **Projekttyp** väljer du **Migrering**.
2. Ange ett projektnamn i textrutan **Typ av källserver**, välj **SQL Server**. I textrutan **Målservertyp** väljer du sedan **Azure SQL Database**.
3. Under **Migreringsomfång** väljer du **Endast schema**.

    När du har utfört de föregående stegen bör Data Migration Assistant-gränssnittet visas i följande bild:

    ![Skapa Data Migration Assistant-projekt](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Välj **Skapa** för att skapa projektet.
5. I Data Migration Assistant anger du källans anslutnings information för din SQL Server, väljer **Anslut** och väljer sedan **Adventureworks2016** -databasen.

    ![Källanslutningsinformation för Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Välj **Nästa**, under **Anslut till mål server**, ange mål anslutnings information för Azure SQL Database, Välj **Anslut** och välj sedan den **AdventureWorksAzure** -databas som du hade fördefinierat i Azure SQL Database.

    ![Målanslutningsinformation för Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Välj **Nästa** för att gå vidare till skärmen **Välj objekt** där du kan ange de schema objekt i **Adventureworks2016** -databasen som måste distribueras till Azure SQL Database.

    Som standard markeras alla objekt.

    ![Skapa SQL-skript](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Välj **Skapa SQL-skript** för att skapa SQL-skripten och granska sedan om det finns fel i skripten.

    ![Schemaskript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Välj **Distribuera schema** för att distribuera schemat till Azure SQL Database, och när schemat har distribuerats kontrollerar du om det finns avvikelser på målservern.

    ![Distribuera schema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen. Sök efter och välj **prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-sql-server-to-azure-sql/portal-select-subscription-1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** för **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans

1. På Azure Portal-menyn eller på **Start** sidan väljer du **skapa en resurs**. Sök efter och välj **Azure Database migration service**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. På skärmen för att **skapa Migreringsverktyg för tjänsten** :

     - Välj prenumerationen.
     - Skapa en ny resurs grupp eller Välj en befintlig.
     - Ange ett namn på instansen för Azure Database Migration Service.
     - Välj den plats där du vill skapa instansen av Azure Database Migration Service.
     - Välj **Azure** som tjänst läge.
     - Välj en prisnivå. Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera inställningar för grundläggande Azure Database Migration Service-instans](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - Välj **Nästa: Nätverk**.

4. På skärmen **skapa migrerings tjänst** nätverk:

    - Välj ett befintligt virtuellt nätverk eller skapa ett nytt. Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll SQL Server och mål Azure SQL Database instansen. Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurera Azure Database Migration Service instans nätverks inställningar](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - Välj **Granska + skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I menyn Azure Portal väljer du **alla tjänster**. Sök efter och välj **Azure Database migration Services**.

     ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. På skärmen **Azure Database migration Services** väljer du Azure Database migration service-instansen som du skapade.

3. Välj **nytt migreringsjobb**.

     ![Hitta din instans av Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **käll Server typ** , väljer **SQL Server**, i text rutan **mål server typ** , väljer **Azure SQL Database** och klickar sedan på * * Välj typ av migrering av aktivitet * *, väljer **datamigrering**.

    ![Skapa Database Migration Service-projekt](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

## <a name="specify-source-details"></a>Ange källinformation

1. På skärmen **Välj källa** anger du anslutnings informationen för käll SQL Servers instansen.

    Se till att använda ett fullständigt domännamn (FQDN) för SQL Server-källinstansens namn. Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

2. Om du inte har installerat ett betrott certifikat på källservern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktions miljö eller på servrar som är anslutna till Internet.

    > [!IMPORTANT]
    > Om du använder SSIS stöder DMS för närvarande inte migrering av käll-SSISDB, men du kan distribuera om ditt SSIS-projekt/-paket till SSISDB-målet med Azure SQL Database. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](./how-to-migrate-ssis-packages.md) (Migrera SQL Server Integration Services-paket till Azure).

   ![Källinformation](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. Välj **Nästa: Välj databaser**.

## <a name="select-databases-for-migration"></a>Välj databaser för migrering

Välj antingen alla databaser eller vissa databaser som du vill migrera till Azure SQL Database. DMS ger den förväntade migrerings tiden för valda databaser. Om avbrotts tiden för migreringen är acceptabel fortsätter du med migreringen. Om migreringens stillestånds tid inte är acceptabel kan du migrera till [SQL-hanterad instans med nästan noll stillestånd](tutorial-sql-server-managed-instance-online.md) eller kontakta [DMS-teamet](mailto:DMSFeedback@microsoft.com) för andra alternativ. 

1. Välj de databaser som du vill migrera från listan över tillgängliga databaser. 
1. Granska den förväntade stillestånds tiden. Om den är acceptabel väljer du **Nästa: Välj mål >>**

   ![Käll databaser](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **Välj mål** anger du autentiseringsinställningar för Azure SQL Database. 

   ![Välja mål](media/tutorial-sql-server-to-azure-sql/select-target.png)

1. Välj **Nästa: mappa till mål databaser** -skärmen, mappa källan och mål databasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Mappa till måldatabaser](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. Välj **Nästa: inställningar för migrering av konfiguration**, expandera tabell listan och granska sedan listan över berörda fält.

    Azure Database Migration Service väljer automatiskt alla tomma käll tabeller som finns på mål Azure SQL Databases instansen. Om du vill migrera tabeller på nytt som redan innehåller data måste du uttryckligen välja tabellerna på det här bladet.

    ![Välj tabeller](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. Välj **Nästa: Sammanfattning**, granska konfigurationen av migreringen och ange ett namn för migreringen i text rutan **namn på aktivitet** .

    ![Välja verifieringsalternativ](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>Köra migreringen

- Välj **Starta migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Väntande**.

    ![Aktivitetsstatus](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

    ![Aktivitetsstatus Slutförd](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. Verifiera mål databaserna på mål **Azure SQL Database**.

### <a name="additional-resources"></a>Ytterligare resurser

- Mer information om Azure Database Migration Service finns i artikeln [Vad är Azure Database migration service?](./dms-overview.md).
- Information om Azure SQL Database finns i artikeln [Vad är tjänsten SQL Database?](../azure-sql/database/sql-database-paas-overview.md).
