---
title: 'Självstudie: Migrera SQL Server online till SQL-hanterad instans'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från SQL Server till en Azure SQL-hanterad instans med hjälp av Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 5585eb210b54dfa016d25c430256508e1b0b9f61
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076895"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Självstudie: Migrera SQL Server till en Azure SQL-hanterad instans online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en SQL Server instans till en [Azure SQL-hanterad instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) med minimal stillestånds tid. För ytterligare metoder som kan kräva viss manuell ansträngning, se artikeln [SQL Server instans migrering till Azure SQL-hanterad instans](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

I den här självstudien migrerar du **Adventureworks2012** -databasen från en lokal instans av SQL Server till en SQL-hanterad instans med minimal stillestånds tid med hjälp av Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb och starta migrering online genom att använda Azure Database Migration Service.
> * Övervaka migreringen.
> * Utför migreringen start punkt när du är klar.

> [!IMPORTANT]
> För online-migrering från SQL Server till SQL-hanterad instans med Azure Database Migration Service måste du ange fullständig säkerhets kopiering av databasen och efterföljande logg säkerhets kopior i SMB-nätverks resursen som tjänsten kan använda för att migrera dina databaser. Azure Database Migration Service initierar inga säkerhets kopior och använder istället befintliga säkerhets kopior, som du kanske redan har som en del av Disaster Recovery-planen för migreringen.
> Se till att du vidtar [säkerhets kopior med alternativet med kontroll Summa](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017). Se också till att du inte lägger till flera säkerhets kopior (t. ex. fullständig och t-logg) i ett enda säkerhets kopierings medium. ta varje säkerhets kopia på en separat säkerhets kopierings fil. Slutligen kan du använda komprimerade säkerhets kopieringar för att minska sannolikheten för problem med att migrera stora säkerhets kopior.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium.

> [!IMPORTANT]
> För en optimal migrering rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som mål databasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

> [!IMPORTANT]
> Minska varaktigheten för migreringen så mycket som möjligt för att minimera risken för avbrott som orsakas av omkonfiguration av en instans eller planerat underhåll. Om en sådan händelse inträffar startar migreringen från början. I händelse av planerat underhåll finns en respitperiod på 36 timmar innan migreringsprocessen startas om.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs en online-migrering från SQL Server till en SQL-hanterad instans. En offline-migrering finns i [migrera SQL Server till en SQL-hanterad instans offline med DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Lär dig mer om nätverkstopologier för migrering av SQL-hanterade instanser med hjälp av Azure Database migration service](./resource-network-topologies.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.
    >
    >Om du inte har plats-till-plats-anslutning mellan det lokala nätverket och Azure, eller om det finns begränsad bandbredd för plats-till-plats-anslutning, bör du överväga att använda Azure Database Migration Service i hybrid läge (för hands version). Hybrid läge utnyttjar en lokal migrering av arbetare tillsammans med en instans av Azure Database Migration Service som körs i molnet. Information om hur du skapar en instans av Azure Database Migration Service i hybrid läge finns i artikeln [skapa en instans av Azure Database migration service i hybrid läge med hjälp av Azure Portal](./quickstart-create-data-migration-service-portal.md).

    > [!IMPORTANT]
    > För det lagrings konto som används som en del av migreringen måste du antingen:
    > * Välj att tillåta alla nätverk att komma åt lagrings kontot.
    > * Aktivera [under näts delegering](../virtual-network/manage-subnet-delegation.md) i mi-undernät och uppdatera brand Väggs reglerna för lagrings kontot för att tillåta det här under nätet.

* Se till att det virtuella nätverkets regler för nätverks säkerhets grupp inte blockerar den utgående porten 443 av ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera din [Windows-brandvägg för källdatabasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service åtkomst till käll SQL Server, vilket som standard är TCP-port 1433. Om standard instansen lyssnar på någon annan port lägger du till den i brand väggen.
* Om du kör flera namngivna SQL Server instanser med dynamiska portar kanske du vill aktivera tjänsten SQL Browser och tillåta åtkomst till UDP-port 1434 genom brand väggarna så att Azure Database Migration Service kan ansluta till en namngiven instans på käll servern.
* Om du använder en brand Väggs installation framför dina käll databaser kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering, samt filer via SMB-port 445.
* Skapa en SQL-hanterad instans genom att följa informationen i artikeln [skapa en SQL-hanterad instans i Azure Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Se till att de inloggningar som används för att ansluta till käll SQL Server och att den SQL-hanterade instansen är medlemmar i Server rollen sysadmin.
* Ange en SMB-nätverks resurs som innehåller alla säkerhets kopior av databasen och de efterföljande säkerhetskopieringsfilerna för transaktions loggen, som Azure Database Migration Service kan använda för migrering av databasen.
* Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
* Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage behållare för återställnings åtgärden.
* Skapa ett Azure Active Directory program-ID som genererar den program-ID-nyckel som Azure Database Migration Service kan använda för att ansluta till målets hanterade instans i Azure Database och Azure Storage behållare. Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md).

  > [!NOTE]
  > Azure Database Migration Service kräver behörigheten deltagare för prenumerationen för angivet program-ID. Du kan också skapa anpassade roller som ger de angivna behörigheterna som Azure Database Migration Service kräver. Steg-för-steg-anvisningar om hur du använder anpassade roller finns i artikeln [anpassade roller för SQL Server till migrering av SQL-hanterade instanser online](./resource-custom-roles-sql-db-managed-instance.md).

* Skapa eller anteckna **Standard Performance-nivå** och Azure Storage-konto, som DMS-tjänsten kan överföra de säkerhetskopierade databasfilerna till och använda för att migrera databaser.  Se till att skapa Azure Storage kontot i samma region som Azure Database Migration Services instansen skapas.

  > [!NOTE]
  > När du migrerar en databas som skyddas av [Transparent datakryptering](../azure-sql/database/transparent-data-encryption-tde-overview.md) till en hanterad instans med hjälp av online-migrering måste motsvarande certifikat från den lokala eller virtuella Azure VM SQL Server-instansen migreras innan databasen återställs. Detaljerade anvisningar finns i [Migrera ett TDE-certifikat till en hanterad instans](../azure-sql/database/transparent-data-encryption-tde-overview.md).

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure Portal väljer du + **skapa en resurs**, söker efter **Azure Database migration service** och väljer sedan **Azure Database migration service** i list rutan.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa ett.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll SQL Server och mål SQL-hanterad instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

    Mer information finns i artikeln [nätverks topologier för migrering av SQL-hanterad instans med hjälp av Azure Database migration service](./resource-network-topologies.md).

6. Välj en SKU på Premium-prisnivån.

    > [!NOTE]
    > Onlinemigreringar stöds bara när du använder Premium-nivån.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skapa DMS-tjänst](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. På **Azure Database Migration Service-sidan** söker du efter namnet på instansen som du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **typ av käll server** , väljer **SQL Server** i text rutan **mål server typ** , väljer **Azure SQL-hanterad instans**, och väljer sedan **typ av aktivitet**, Välj **data migration online**.

   ![Skapa Azure Database Migration Service-projekt](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktions miljö eller på servrar som är anslutna till Internet.

   ![Källinformation](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Välj **Spara**.

4. På sidan **Välj källdatabaser**, välj databsen **Adventureworks2012** för migrering.

   ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Om du använder SQL Server Integration Services (SSIS) stöder inte DMS för närvarande migrering av katalog databasen för SSIS-projekt/-paket (SSISDB) från SQL Server till SQL-hanterad instans. Du kan dock etablera SSIS i Azure Data Factory (ADF) och distribuera om dina SSIS-projekt/-paket till mål-SSISDB som hanteras av SQL-hanterad instans. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](./how-to-migrate-ssis-packages.md) (Migrera SQL Server Integration Services-paket till Azure).

5. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **information om migrerings mål** anger du det **program-ID** och den **nyckel** som DMS-instansen kan använda för att ansluta till mål instansen av SQL-hanterad instans och det Azure Storage kontot.

    Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md).

2. Välj den **prenumeration** som innehåller mål instansen av SQL-hanterad instans och välj sedan mål instansen.

    Om du inte redan har etablerat SQL-hanterad instans väljer du [länken](../azure-sql/managed-instance/instance-create-quickstart.md) för att hjälpa dig att etablera instansen. När den SQL-hanterade instansen är klar går du tillbaka till det här projektet för att utföra migreringen.

3. Ange **SQL-användare** och **lösen ord** för att ansluta till SQL-hanterad instans.

    ![Välja mål](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Välj **Spara**.

## <a name="select-source-databases"></a>Välj källdatabaser

1. På sidan **Välj källdatabaser** väljer du de källdatabaser som du vill migrera.

    ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Välj **Spara**.

## <a name="configure-migration-settings"></a>Konfigurera migreringsinställningar

1. På sidan **Konfigurera migreringsinställningar** anger du följande information:

    | Parameter | Beskrivning |
    |--------|---------|
    |**SMB-nätverksplatsresurs** | Den lokala SMB-nätverksanslutningen eller Azure-filresursen som innehåller fullständiga säkerhets kopior av databasen och säkerhets kopior av transaktions loggen som Azure Database Migration Service kan använda för migrering. Tjänstkontot som kör en SQL Server-källinstansen måste ha läs-/skrivbehörighet på den här nätverksresursen. Ange ett fullständigt domännamn eller IP-adresser för servern i nätverksresursen, till exempel \\\servernamn.domännamn.com\säkerhetskopieringsmapp eller \\\IP-adress\säkerhetskopieringsmapp. För bättre prestanda rekommenderar vi att du använder en separat mapp för varje databas som ska migreras. Du kan ange sökvägen till fil resursen på databas nivå genom att använda alternativet **Avancerade inställningar** . Om du har problem med att ansluta till SMB-resursen, se [SMB-resurs](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity). |
    |**Användarnamn** | Kontrollera att Windows-användaren har fullständig kontrollbehörighet på nätverksresursen du har angett ovan. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage behållare för återställnings åtgärden. Om du använder Azure-filresurs använder du pended för lagrings konto namn med AZURE \ som användar namn. |
    |**Lösenord** | Lösenordet för användaren. Om du använder Azure-filresurs använder du en lagrings konto nyckel som lösen ord. |
    |**Prenumeration på Azure Storage-kontot** | Välj den prenumeration som innehåller Azure Storage-kontot. |
    |**Azure Storage konto** | Välj det Azure Storage-konto som DMS kan överföra de säkerhetskopierade filerna från SMB-nätverksresursen till och använda för databasmigrering.  Vi rekommenderar att du väljer Storage-kontot i samma region som DMS-tjänsten för bästa möjliga prestanda vid filöverföring. |

    ![Konfigurera migreringsinställningar](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Om Azure Database Migration Service visar felet "Systemfel 53" eller "systemfel 57" kan det bero på att Azure Database Migration Service inte kan komma åt Azure-filresursen. Om du stöter på något av dessa fel kan du bevilja åtkomst till lagrings kontot från det virtuella nätverket med hjälp av instruktionerna [här](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Om loopback-kontrollen är aktive rad och käll SQL Server och fil resursen finns på samma dator kommer källan inte att kunna komma åt filerna esursna med FQDN. Åtgärda problemet genom att inaktivera funktionen för loopback-kontroll med hjälp av instruktionerna [här](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Välj **Spara**.

## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. Granska och verifiera informationen som är kopplad till migreringsprojektet.

    ![Sammanfattning av migreringsprojekt](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Köra och övervaka migreringen

1. Välj **Kör migrering**.

2. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen.

   ![Migreringsaktivitet pågår](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Du kan expandera databaserna och inloggningskategorierna ytterligare för att övervaka migreringsstatusen för respektive serverobjekt.

   ![Status för migrerings aktivitet](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga säkerhets kopieringen av databasen har återställts på mål instansen av SQL-hanterad instans, är databasen tillgänglig för att utföra en migrering start punkt.

1. När du är redo att slutföra databasmigreringen online väljer du **Starta snabb**.

2. Stoppa all inkommande trafik till källdatabaser.

3. Ta [säkerhetskopia av slutlogg], gör säkerhetskopian tillgänglig i SMB-nätverksresursen och vänta sedan tills säkerhetskopieringen av den sista transaktionsloggen har återställts.

    I det här läget visas **Väntande ändringar** med värdet 0.

4. Välj **Bekräfta** och sedan **Använd**.

    ![Förbereda för att slutföra startpunkt](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Efter start punkt kan tillgänglighet för SQL-hanterad instans med Affärskritisk tjänst nivå bara ta betydligt längre tid än Generell användning som tre sekundära repliker måste dirigeras för gruppen med hög tillgänglighet för AlwaysOn. Den här åtgärdens storlek beror på data storleken. mer information finns i [varaktighet för hanterings åtgärder](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. När status för databas migreringen är **slutförd** ansluter du dina program till den nya mål instansen av SQL-hanterad instans.

    ![Startpunkt slutförd](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Nästa steg

* En själv studie kurs som visar hur du migrerar en databas till SQL-hanterad instans med hjälp av kommandot T-SQL Restore finns i [återställa en säkerhets kopia till SQL-hanterad instans med kommandot Restore](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* Information om SQL-hanterad instans finns i [Vad är SQL-hanterad instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Information om hur du ansluter appar till SQL-hanterad instans finns i [Anslut program](../azure-sql/managed-instance/connect-application-instance.md).
