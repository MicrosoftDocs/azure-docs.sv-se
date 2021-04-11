---
title: 'Självstudie: Migrera SQL Server till SQL-hanterad instans'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera från SQL Server till en Azure SQL-hanterad instans med hjälp av Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 2dfcb4ade9da76d5ec4883e9f4b5b3cb3e0c8682
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076844"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Självstudie: Migrera SQL Server till en Azure SQL-hanterad instans offline med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en SQL Server instans till en [hanterad Azure SQL-instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). För ytterligare metoder som kan kräva viss manuell ansträngning, se artikeln [SQL Server instans migrering till SQL-hanterad instans](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

I den här självstudien migrerar du **Adventureworks2012** -databasen från en lokal instans av SQL Server till en SQL-hanterad instans med hjälp av Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> - Skapa en instans av Azure Database Migration Service.
> - Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> - Köra migreringen.
> - Övervaka migreringen.
> - Ladda ned en migreringsrapport.

> [!IMPORTANT]
> För offline-migrering från SQL Server till SQL-hanterad instans kan Azure Database Migration Service skapa säkerhetskopierade filer åt dig. Alternativt kan du ange den senaste fullständiga säkerhets kopian av databasen i SMB-nätverks resursen som tjänsten ska använda för att migrera dina databaser. Lägg inte till flera säkerhets kopior i ett enda säkerhets kopierings medium; ta varje säkerhets kopia på en separat säkerhets kopierings fil. Observera att du även kan använda komprimerade säkerhets kopieringar för att minska sannolikheten för problem med att migrera stora säkerhets kopior.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs en offline-migrering från SQL Server till en SQL-hanterad instans. En online-migrering finns i [migrera SQL Server till en SQL-hanterad instans online med DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Lär dig mer om nätverkstopologier för migrering av SQL-hanterade instanser med hjälp av Azure Database migration service](./resource-network-topologies.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    > - Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > - Lagrings slut punkt
    > - Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.

- Se till att det virtuella nätverkets regler för nätverks säkerhets grupp inte blockerar den utgående porten 443 av ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Konfigurera din [Windows-brandvägg för källdatabasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen för att tillåta Azure Database Migration Service åtkomst till käll SQL Server, vilket som standard är TCP-port 1433. Om standard instansen lyssnar på någon annan port lägger du till den i brand väggen.
- Om du kör flera namngivna SQL Server instanser med dynamiska portar kanske du vill aktivera tjänsten SQL Browser och tillåta åtkomst till UDP-port 1434 genom brand väggarna så att Azure Database Migration Service kan ansluta till en namngiven instans på käll servern.
- Om du använder en brand Väggs installation framför dina käll databaser kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering, samt filer via SMB-port 445.
- Skapa en SQL-hanterad instans genom att följa informationen i artikeln [skapa en SQL-hanterad instans i Azure Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
- Se till att de inloggningar som används för att ansluta till käll SQL Server och att SQL-hanterad instans är medlemmar i Server rollen sysadmin.

    >[!NOTE]
    >Som standard stöder Azure Database Migration Service bara migrering av SQL-inloggningar. Du kan dock aktivera möjligheten att migrera Windows-inloggningar genom att:
    >
    >- Se till att den SQL-hanterade instansen för SQL har Läs behörighet för AAD, som kan konfigureras via Azure Portal av en användare med rollen **Global administratör** .
    >- Konfigurera Azure Database Migration Service-instansen för att aktivera migrering av Windows-användare/grupper, som har kon figurer ATS via Azure Portal, på sidan konfiguration. När du har aktiverat den här inställningen startar du om tjänsten för att ändringarna ska börja gälla.
    >
    > När tjänsten har startats om visas Windows användare/grupp-inloggningar i listan över inloggningar som är tillgängliga för migrering. För alla Windows-användare/grupp-inloggningar som du migrerar uppmanas du att ange det associerade domän namnet. Tjänst användar konton (konto med domän namns UTFÄRDAre) och virtuella användar konton (konto namn med domän namns tjänsten) stöds inte.

- Skapa en nätverks resurs som Azure Database Migration Service kan använda för att säkerhetskopiera käll databasen.
- Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
- Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage behållare för återställnings åtgärden.
- Skapa en blobbcontainer och hämta dess SAS-URI med hjälp av stegen i artikeln om [hantering av Azure Blob Storage-resurser med Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container), se till att välja alla behörigheter (Läsa, Skriva, Ta bort, Lista) i principfönstret när du skapar SAS-URI:et. Den här informationen ger Azure Database Migration Service åtkomst till din lagrings konto behållare för överföring av de säkerhetskopierade filer som används för att migrera databaser till SQL-hanterad instans.

    > [!NOTE]
    > Azure Database Migration Service stöder inte användning av en SAS-token på konto nivå när du konfigurerar inställningarna för lagrings kontot under steget [Konfigurera migrerings inställningar](#configure-migration-settings) .
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure Portal väljer du + **skapa en resurs**, söker efter **Azure Database migration service** och väljer sedan **Azure Database migration service** i list rutan.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa ett.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll SQL Server och mål SQL-hanterad instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

    Mer information finns i artikeln [nätverks topologier för migrering av Azure SQL-hanterad instans med hjälp av Azure Database migration service](./resource-network-topologies.md).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skapa DMS-tjänst](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. På **Azure Database Migration Service-sidan** söker du efter namnet på instansen som du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **typ av käll server** , väljer **SQL Server** i text rutan **mål server typ** , väljer **Azure SQL-hanterad instans** och väljer sedan typ av **data migrering offline** under **Välj typ av aktivitet**.

   ![Skapa DMS-projekt](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Välj **Skapa** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktions miljö eller på servrar som är anslutna till Internet.

   ![Källinformation](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Välj **Spara**.

4. På sidan **Välj källdatabaser**, välj databsen **Adventureworks2012** för migrering.

   ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Om du använder SQL Server Integration Services (SSIS) stöder inte DMS för närvarande migrering av katalog databasen för SSIS-projekt/-paket (SSISDB) från SQL Server till SQL-hanterad instans. Du kan dock etablera SSIS i Azure Data Factory (ADF) och distribuera om dina SSIS-projekt/-paket till mål-SSISDB som hanteras av SQL-hanterad instans. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](./how-to-migrate-ssis-packages.md) (Migrera SQL Server Integration Services-paket till Azure).

5. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **information om migrerings mål** anger du anslutnings information för målet, vilket är den FÖRetablerade SQL-hanterade instans som du migrerar **AdventureWorks2012** -databasen till.

    Om du inte redan har etablerat SQL-hanterad instans väljer du [länken](../azure-sql/managed-instance/instance-create-quickstart.md) för att hjälpa dig att etablera instansen. Du kan fortfarande fortsätta att skapa projektet och sedan, när den SQL-hanterade instansen är klar, gå tillbaka till det här projektet för att utföra migreringen.

    ![Välja mål](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Välj **Spara**.

## <a name="select-source-databases"></a>Välj källdatabaser

1. På sidan **Välj källdatabaser** väljer du de källdatabaser som du vill migrera.

    ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Välj **Spara**.

## <a name="select-logins"></a>Välj inloggningar

1. På sidan **Välj inloggningar** väljer du de inloggningar du vill migrera.

    >[!NOTE]
    >Som standard stöder Azure Database Migration Service bara migrering av SQL-inloggningar. Information om hur du aktiverar stöd för migrering av Windows-inloggningar finns i avsnittet **krav** i den här självstudien.

    ![Välj inloggningar](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Välj **Spara**.

## <a name="configure-migration-settings"></a>Konfigurera migreringsinställningar

1. På sidan **Konfigurera migreringsinställningar** anger du följande information:

    | Parameter | Beskrivning |
    |--------|---------|
    |**Välj alternativ för att säkerhetskopiera källa** | Välj alternativet **Jag tillhandahåller de senaste säkerhetskopierade filerna** när du redan har fullständiga säkerhetskopior tillgängliga för DMS att använda för databasmigrering. Välj alternativet **Jag låter Azure Database Migration Service skapa säkerhetskopierade filer** när du vill att DMS ska ta källdatabasens fullständiga säkerhetskopia först och använda den för migrering. |
    |**Nätverksplatsresurs** | Den lokala SMB-nätverks resurs som Azure Database Migration Service kan ta säkerhets kopior av käll databasen till. Tjänstkontot som kör en SQL Server-källinstans måste ha skrivbehörighet på den här nätverksresursen. Ange ett fullständigt domännamn eller IP-adresser för servern i nätverksresursen, till exempel \\\servernamn.domännamn.com\säkerhetskopieringsmapp eller \\\IP-adress\säkerhetskopieringsmapp.|
    |**Användarnamn** | Kontrollera att Windows-användaren har fullständig kontrollbehörighet på nätverksresursen du har angett ovan. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage behållare för återställnings åtgärden. Om TDE-aktiverade databaser har valts för migrering måste Windows-användaren ovan vara det inbyggda administratörskontot och [User Account Control](/windows/security/identity-protection/user-account-control/user-account-control-overview) måste vara inaktiverat för Azure Database Migration Service för att ladda upp och ta bort certifikatfilerna.) |
    |**Lösenord** | Lösenordet för användaren. |
    |**Inställningar för lagringskonto** | SAS-URI: n som ger Azure Database Migration Service med åtkomst till din lagrings konto behållare som tjänsten laddar upp de säkerhetskopierade filerna och som används för att migrera databaser till SQL-hanterad instans. [Lär dig hur du hämtar SAS-URI för blobbcontainer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Den här SAS-URI: n måste vara för BLOB-behållaren, inte för lagrings kontot.|
    |**TDE-inställningar** | Om du migrerar käll databaserna med transparent datakryptering (TDE) aktive rad måste du ha Skriv behörighet för den SQL-hanterade instansen för målet.  Välj den prenumeration där SQL-hanterad instans har skapats från den nedrullningsbara menyn.  Välj den hanterade **Azure SQL Database-målinstansen** på den nedrullningsbara menyn. |

    ![Konfigurera migreringsinställningar](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Välj **Spara**.

## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. Expandera avsnittet **Valideringsalternativ** för att visa sidan **Välj verifieringsalternativ**, ange om du vill verifiera att den migrerade databasen är frågeriktig och välj sedan **Spara**.

3. Granska och verifiera informationen som är kopplad till migreringsprojektet.

    ![Sammanfattning av migreringsprojekt](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Välj **Spara**.

## <a name="run-the-migration"></a>Köra migreringen

- Välj **Kör migrering**.

  Fönstret migrering av aktivitet visas och aktivitetens status är **väntande**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen.

   ![Skärm bild som visar migrerings aktivitet och knappen Uppdatera.](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Du kan expandera databaserna och inloggningskategorierna ytterligare för att övervaka migreringsstatusen för respektive serverobjekt.

   ![Migreringsaktivitet pågår](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. När migreringen har slutförts väljer du **Ladda ned rapport** för att få en rapport som visar informationen som rör migreringsprocessen.

3. Kontrol lera att mål databasen i mål miljön för SQL-hanterad instans.

## <a name="next-steps"></a>Nästa steg

- En själv studie kurs som visar hur du migrerar en databas till SQL-hanterad instans med hjälp av kommandot T-SQL Restore finns i [återställa en säkerhets kopia till SQL-hanterad instans med kommandot Restore](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
- Information om SQL-hanterad instans finns i [Vad är SQL-hanterad instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Information om hur du ansluter appar till SQL-hanterad instans finns i [Anslut program](../azure-sql/managed-instance/connect-application-instance.md).
