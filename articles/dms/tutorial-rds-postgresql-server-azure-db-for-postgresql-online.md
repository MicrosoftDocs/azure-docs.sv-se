---
title: 'Självstudie: Migrera RDS-PostgreSQL online till Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från RDS PostgreSQL till Azure Database for PostgreSQL med hjälp av Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: c2a6a71365b48fa4349306ce632f5762c38dacf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597478"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Självstudie: Migrera RDS-PostgreSQL till Azure DB för PostgreSQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaser från en RDS PostgreSQL-instans till [Azure Database for PostgreSQL](../postgresql/index.yml) medan käll databasen förblir online under migreringen. Med andra ord kan migreringen uppnås med minimal stillestånds tid för programmet. I den här självstudien migrerar du exempel databasen för **DVD-hyra** från en instans av RDS postgresql 9,6 till Azure Database for PostgreSQL med hjälp av aktiviteten online-migrering i Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempel schema med hjälp av pg_dump-verktyget.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.
> * Utför migrering start punkt.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserat på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service. Vi krypterar disk för att förhindra stöld av data under migreringen.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskriver hur du utför en online-migrering från en lokal instans av PostgreSQL för att Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Ladda ned och installera [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 eller 10. PostgreSQL-källserverversionen måste vara 9.5.11, 9.6.7, 10 eller senare. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](../postgresql/concepts-supported-versions.md).

   Observera också att mål Azure Database for PostgreSQL versionen måste vara lika med eller senare än RDS PostgreSQL-versionen. Till exempel kan RDS PostgreSQL 9,6 endast migrera till Azure Database for PostgreSQL 9,6, 10 eller 11, men inte Azure Database for PostgreSQL 9,5.

* Skapa en instans av [Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) eller [Azure Database for PostgreSQL-skala (citus)](../postgresql/quickstart-create-hyperscale-portal.md). Se det här [avsnittet](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) av dokumentet för information om hur du ansluter till postgresql-servern med hjälp av pgAdmin.
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.
* Se till att det virtuella nätverkets regler för nätverks säkerhets grupp inte blockerar den utgående porten 443 av ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service att få åtkomst till PostgreSQL-servern, som standard är TCP-port 5432.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en [brand Väggs regel](../postgresql/concepts-firewall-rules.md) på server nivå för Azure Database for PostgreSQL servern för att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Konfigurera AWS RDS-PostgreSQL för replikering

1. Om du vill skapa en ny parameter grupp följer du anvisningarna från AWS i artikeln [arbeta med databas parameter grupper](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Använd huvud användar namnet för att ansluta till källan från Azure Database Migration Service. Om du använder ett annat konto än huvud användar kontot måste kontot ha rds_superuser-rollen och rds_replication-rollen. Rds_replication rollen ger behörighet att hantera logiska platser och strömma data med logiska platser.
3. Skapa en ny parameter grupp med följande konfiguration:

    a. Ange parametern rds.logical_replication i din DB-parameter grupp till 1.

    b. max_wal_senders = [antal samtidiga aktiviteter] – parametern max_wal_senders anger antalet samtidiga aktiviteter som kan köras, rekommenderar 10 aktiviteter.

    c. max_replication_slots – = [antal platser], rekommenderar att du anger fem platser.

4. Koppla parameter gruppen som du skapade till RDS PostgreSQL-instansen.

## <a name="migrate-the-schema"></a>Migrera schemat

1. Extrahera schemat från käll databasen och tillämpa den på mål databasen för att slutföra migreringen av alla databas objekt, till exempel tabell scheman, index och lagrade procedurer.

    Det enklaste sättet att migrera endast schemat är att använda pg_dump med alternativet-s. Mer information finns i [exemplen](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) i självstudien om postgres pg_dump.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Om du till exempel vill dumpa en schema fil för **dvdrental** -databasen använder du följande kommando:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Skapa en tom databas i mål tjänsten, som är Azure Database for PostgreSQL. Information om hur du ansluter och skapar en databas finns i någon av följande artiklar:

    * [Skapa en Azure Database for PostgreSQL-server med hjälp av Azure Portal](../postgresql/quickstart-create-server-database-portal.md)
    * [Skapa en Azure Database for PostgreSQL-storskalig-Server (citus) med hjälp av Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md)

3. Importera schemat till mål tjänsten, som är Azure Database for PostgreSQL. Om du vill återställa schema dumpnings filen kör du följande kommando:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Exempel:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. Extrahera det externa nyckel skriptet och Lägg till sekundär nyckel skript vid målet (Azure Database for PostgreSQL) genom att köra följande skript i PgAdmin eller i psql:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Kör sekundär nyckeln Drop (som är den andra kolumnen) i frågeresultatet för att ta bort sekundär nyckeln.

6. Om du har utlösare (infoga eller uppdatera utlösare) i data, kommer den att upprätthålla data integriteten i målet innan data replikeras från källan. Rekommendationen är att inaktivera utlösare i alla tabeller *vid målet* under migreringen och sedan aktivera utlösarna när migreringen är klar.

    Så här inaktiverar du utlösare i mål databasen:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name,' ON ', event_object_table, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service.

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll PostgreSQL-instansen och mål Azure Database for PostgreSQL instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

6. Välj en pris nivå; Se till att du väljer pris nivån Premium: 4vCores för den här online-migreringen.

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. På skärmen **Azure Database migration Services** söker du efter namnet på Azure Database migration service-instansen som du skapade, väljer instansen och väljer sedan + **nytt migreringsjobb**.
3. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **typ av käll Server** , väljer **AWS RDS för postgresql** och väljer sedan **Azure Database for PostgreSQL** i text rutan **mål server typ** .
4. I avsnittet **Välj typ av aktivitet** väljer du **migrering av data online**.

    > [!IMPORTANT]
    > Se till att välja **migrering av data online**. offline-migrering stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

5. Välj **Spara**.

6. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    > [!NOTE]
    > Anteckna de krav som krävs för att konfigurera migrering online i bladet skapa projekt.

## <a name="specify-source-details"></a>Ange källinformation

* På sidan **Lägg till käll information** anger du anslutnings information för källan postgresql-instansen.

   ![Källinformation](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara**. på skärmen **mål information** anger du sedan anslutnings information för mål Azure Database for PostgreSQL servern, som är fördefinierad och har schemat för **DVD-hyresering** distribuerat med pg_dump.

    ![Målinformation](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

    ![Aktivitets status-körs](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Under **databas namn** väljer du en annan databas för att komma till migreringsprocessen för **fullständig data inläsning** och **stegvisa data synkronisering** .

    **Fullständig data inläsning** visar statusen för den initiala inläsningen och den **stegvisa datasynkroniseringen** visar status för registrering av ändrings data (CDC).

    ![Inventerings skärm-fullständig data inläsning](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Inventerings skärm – stegvis data synkronisering](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den första fullständiga inläsningen har slutförts markeras databaserna som **klara att start punkt**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Vänta tills räknaren **väntande ändringar** visar **0** om du vill se till att alla inkommande transaktioner till käll databasen har stoppats markerar du kryss rutan **Bekräfta** och väljer sedan **Använd**.

    ![Slutför start punkt-skärmen](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. När status för databas migreringen är **slutförd** ansluter du dina program till den nya mål Azure Database for PostgreSQLs databasen.

Online-migreringen av en lokal instans av RDS-PostgreSQL till Azure Database for PostgreSQL har slutförts.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](./dms-overview.md) (Vad är Azure Database Migration Service?).
* Mer information om Azure Database for PostgreSQL finns i artikeln [Vad är Azure Database for PostgreSQL?](../postgresql/overview.md).
* Om du har andra frågor kan du skicka ett e-postalias till [Azure Database-migreringen](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
