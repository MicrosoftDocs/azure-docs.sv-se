---
title: 'Självstudie: Migrera RDS MySQL online till Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från RDS MySQL för att Azure Database for MySQL med hjälp av Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: 377341cc76e1a37d493d66790a1d6d05d010dd5c
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096171"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Självstudier: Migrera RDS MySQL till Azure Database for MySQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaser från en RDS MySQL-instans till [Azure Database for MySQL](../mysql/index.yml) medan käll databasen förblir online under migreringen. Med andra ord kan migreringen uppnås med minimal stillestånds tid för programmet. I den här självstudien migrerar du exempel databasen **anställda** från en instans av RDS MySQL till Azure Database for MySQL med hjälp av aktiviteten online-migrering i Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempel schema med hjälp av mysqldump-och MySQL-verktygen.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskriver hur du utför en online-migrering från en instans av RDS MySQL för att Azure Database for MySQL.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Se till att käll-MySQL-servern kör en MySQL community-version som stöds. För att fastställa versionen av MySQL-instansen, i MySQL-verktyget eller MySQL Workbench, kör du kommandot:

    ```
    SELECT @@version;
    ```

    Mer information finns i artikeln Azure Database for MySQL- [versioner som stöds](../mysql/concepts-supported-versions.md).

* Hämta och installera [MySQL- **anställda** exempel databas](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Skapa en instans av [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md).
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.
* Se till att det virtuella nätverkets regler för nätverks säkerhets grupp inte blockerar den utgående porten 443 av ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera [Windows-brandväggen](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (eller din Linux-brandvägg) för att tillåta åtkomst till databas motorn. Tillåt port 3306 för anslutning för MySQL-server.

> [!NOTE]
> Azure Database for MySQL stöder endast InnoDB-tabeller. Om du vill konvertera ISAM-tabeller till InnoDB kan du läsa artikeln [konvertera tabeller från ISAM till InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Konfigurera AWS RDS MySQL för replikering

1. Om du vill skapa en ny parameter grupp följer du anvisningarna i AWS i artikeln [MySQL-databasens loggfiler](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)i avsnittet **binära loggnings format** .
2. Skapa en ny parameter grupp med följande konfiguration:
    * log_bin = på
    * binlog_format = rad
    * binlog_checksum = ingen
3. Spara den nya parameter gruppen.
4. Koppla den nya parameter gruppen till RDS MySQL-instansen. En omstart kan krävas.
5. När parameter gruppen är på plats ansluter du till MySQL-instansen och [anger BinLog kvarhållning](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) till minst 5 dagar.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>Migrera schemat

1. Extrahera schemat från käll databasen och tillämpa den på mål databasen för att slutföra migreringen av alla databas objekt, till exempel tabell scheman, index och lagrade procedurer.

    Det enklaste sättet att migrera endast schemat är att använda mysqldump med parametern--No-data. Kommandot för att migrera schemat är:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Om du till exempel vill dumpa en schema fil för databasen **anställda** använder du följande kommando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importera schemat till mål tjänsten, som är Azure Database for MySQL. Om du vill återställa schema dumpnings filen kör du följande kommando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Om du till exempel vill importera schemat för den **anställda** databasen:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. Extrahera det externa nyckel skriptet och Lägg till sekundär nyckel skript vid målet (Azure Database for MySQL) genom att köra följande skript i MySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Kör sekundär nyckeln Drop (som är den andra kolumnen) i frågeresultatet för att ta bort sekundär nyckeln.

> [!NOTE]
> Azure DMS stöder inte referens åtgärden CASCADE, som hjälper dig att automatiskt ta bort eller uppdatera en matchande rad i den underordnade tabellen när en rad tas bort eller uppdateras i den överordnade tabellen. Mer information finns i avsnittet referens åtgärder i artikelns [begränsningar för sekundär nyckel](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> Azure DMS kräver att du släpper sekundär nyckel begränsningar i mål databas servern under den inledande data inläsningen och du kan inte använda referens åtgärder. Om din arbets belastning är beroende av att uppdatera en relaterad underordnad tabell via den här referens åtgärden, rekommenderar vi att du utför en [dumpning och återställning](../mysql/concepts-migrate-dump-restore.md) i stället. 

5. Om du har utlösare (infoga eller uppdatera utlösare) i data, kommer den att upprätthålla data integriteten i målet innan data replikeras från källan. Rekommendationen är att inaktivera utlösare i alla tabeller *vid målet* under migreringen och sedan aktivera utlösarna när migreringen är klar.

    Så här inaktiverar du utlösare i mål databasen:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Om det finns instanser av data typen ENUM i alla tabeller, rekommenderar vi att du tillfälligt uppdaterar till data typen "Character varierande" i mål tabellen. När datareplikeringen är klar återställer du data typen till ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service.

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll-MySQL-instansen och mål Azure Database for MySQL instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

6. Välj en pris nivå; Se till att du väljer pris nivån Premium: 4vCores för den här online-migreringen.

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

     ![Hitta din instans av Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **käll Server typ** , väljer **MySQL** och väljer sedan **AzureDbForMySQL** i text rutan **mål server typ** .
5. I avsnittet **Välj typ av aktivitet** väljer du **migrering av data online**.

    > [!IMPORTANT]
    > Se till att välja **migrering av data online**. offline-migrering stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    > [!NOTE]
    > Anteckna de krav som krävs för att konfigurera migrering online i bladet skapa projekt.

## <a name="specify-source-details"></a>Ange källinformation

* På informations skärmen för **migrerings källa** anger du anslutnings information för käll-MySQL-instansen.

   ![Källinformation](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara**, och på skärmen **mål information** anger du anslutnings informationen för mål Azure Database for MySQL servern, som är fördefinierad och har det **anställdas** schema distribuerat med MySQLDump.

    ![Välja mål](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

    ![Aktivitets status-körs](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Under **databas namn** väljer du en annan databas för att komma till migreringsprocessen för **fullständig data inläsning** och **stegvisa data synkronisering** .

    **Fullständig data inläsning** visar statusen för den initiala inläsningen och den **stegvisa datasynkroniseringen** visar status för registrering av ändrings data (CDC).

    ![Inventerings skärm-fullständig data inläsning](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Inventerings skärm – stegvis data synkronisering](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den första fullständiga inläsningen har slutförts markeras databaserna som **klara att start punkt**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Börja klipp ut](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databas migreringen är **slutförd** ansluter du dina program till den nya mål Azure Database for MySQLs databasen.

Online-migreringen av en lokal instans av MySQL till Azure Database for MySQL är klar.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](./dms-overview.md) (Vad är Azure Database Migration Service?).
* Mer information om Azure Database for MySQL finns i artikeln [Vad är Azure Database for MySQL?](../mysql/overview.md).
* Om du har andra frågor kan du skicka ett e-postalias till [Azure Database-migreringen](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
