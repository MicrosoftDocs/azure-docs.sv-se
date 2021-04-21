---
title: 'Självstudie: Migrera MySQL online till Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en onlinemigrering från MySQL lokalt till Azure Database for MySQL med hjälp av Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818198"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Självstudie: Migrera MySQL till Azure Database for MySQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal MySQL-instans [till Azure Database for MySQL](../mysql/index.yml) med minimal avbrottstid. Du kan med andra ord migrera med minimal stilleståndstid i programmet. I den här självstudien migrerar du exempeldatabasen **Anställda** från en lokal instans av MySQL 5.7 till Azure Database for MySQL med hjälp av en onlinemigreringsaktivitet i Azure Database Migration Service.

> [!IMPORTANT]
> Onlinemigreringsscenariot "MySQL till Azure Database for MySQL" ersätts med ett parallelliserat offlinemigreringsscenario med hög performantitet den 1 juni 2021. För onlinemigrering kan du använda det här nya erbjudandet tillsammans med [datareplikering.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Du kan också använda verktyg med öppen källkod som [MyDumper/MyLoader](https://centminmod.com/mydumper.html) med datareplikering för onlinemigrering. 

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempelschemat med verktyget mysqldump.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Om Azure Database Migration Service utföra en onlinemigrering måste du skapa en instans baserat på prisnivån Premium.

> [!IMPORTANT]
> För en optimal migreringsupplevelse rekommenderar Microsoft att du skapar en instans Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

> [!NOTE]
> Biasfri kommunikation
>
> Microsoft har stöd för en miljö med mångfald och inkludering. Den här artikeln innehåller referenser till ordet _slave ._ Microsofts [stilguide för biasfri kommunikation identifierar](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) detta som ett exkluderingsord. Ordet används i den här artikeln för konsekvens eftersom det för närvarande är ordet som visas i programvaran. När programvaran uppdateras för att ta bort ordet uppdateras den här artikeln så att den är i linje.
>


## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Ladda ned och installera [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 eller 5.7. Den lokala MySQL-versionen måste överensstämma med Azure Database for MySQL-versionen. Exempelvis kan MySQL 5.6 endast migreras till Azure Database for MySQL 5.6 och inte uppgraderat till 5.7. Migreringar till eller från MySQL 8.0 stöds inte.
* [Skapa en instans i Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Se artikeln Använda [MySQL Workbench](../mysql/connect-workbench.md) för att ansluta och fråga efter data för information om hur du ansluter och skapar en databas med hjälp av Workbench-programmet.  
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till dina lokala källservrar med hjälp av [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt [nätverk finns i Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabbstartsartiklarna med stegvisa detaljer.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av virtuellt nätverkNät lägger du till följande tjänstslutpunkter i det undernät där tjänsten ska [etableras:](../virtual-network/virtual-network-service-endpoints-overview.md)
    >
    > * Måldatabasslutpunkt (till exempel SQL-slutpunkt, Cosmos DB slutpunkt och så vidare)
    > * Lagringsslutpunkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig Azure Database Migration Service saknar Internetanslutning.

* Kontrollera att reglerna för nätverkssäkerhetsgruppen för det virtuella nätverket inte blockerar den utgående porten 443 för ServiceTag för ServiceBus, Storage och AzureMonitor. Mer information om filtrering av nätverkssäkerhetsgrupper för virtuella nätverk finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen så att Azure Database Migration Service åtkomst till mySQL-källservern, som som standard är TCP-port 3306.
* När du använder en brandväggsinstallation framför dina källdatabaser kan du behöva lägga till brandväggsregler för att tillåta Azure Database Migration Service åtkomst till källdatabaserna för migrering.
* Skapa en brandväggsregel [på servernivå](../azure-sql/database/firewall-configure.md) för Azure Database for MySQL tillåta Azure Database Migration Service åtkomst till måldatabaserna. Ange undernätsintervallet för det virtuella nätverk som används för Azure Database Migration Service.
* MySQL-källan måste vara på en MySQL Community Edition som stöds. Om du vill kontrollera vilken version av MySQL-instansen du har går du till verktyget MySQL eller MySQL Workbench och kör följande kommando:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL stöder endast InnoDB-tabeller. Om du vill konvertera MyISAM-tabeller till InnoDB kan du läsa artikeln [Konvertera tabeller från MyISAM till InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Aktivera binär loggning i filen my.ini (Windows) eller my.cnf (Unix) i källdatabasen med följande konfiguration:

  * **server_id** = 1 eller större (endast relevant för MySQL 5.6)
  * **log-bin** = \<path> (endast relevant för MySQL 5.6)    Exempel: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = rad
  * **Expire_logs_days** = 5 (vi rekommenderar att du inte använder noll; endast relevant för MySQL 5.6)
  * **Binlog_row_image** = fullständig (endast relevant för MySQL 5.6)
  * **log_slave_updates** = 1

* Användaren måste ha rollen ReplicationAdmin med följande behörigheter:

  * **REPLIKERINGSKLIENT** – Krävs endast för ändringsbearbetningsuppgifter. Med andra ord kräver inte uppgifter för fullständig inläsning detta privilegium.
  * **REPLIKERINGSREPLIK** – Krävs endast för ändringsbearbetningsuppgifter. Med andra ord kräver inte uppgifter för fullständig inläsning detta privilegium.
  * **SUPER** – Krävs endast i versioner som är äldre än MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

För att slutföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på databasen. Du kan extrahera schema med mysqldump med parametern `--no-data`.

Förutsatt att du har **exempeldatabasen** MySQL Employees i det lokala systemet är kommandot för att göra schemamigrering med mysqldump:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Exempel:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Om du vill importera schemat till Azure Database for MySQL-mål kör du följande kommando:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Exempel:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen.  Kör följande skript i MySQL Workbench för att extrahera drop-foreign key-skriptet och lägga till ett främmande nyckelskript.

```sql
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

Kör drop-sekundärnyckeln (som är den andra kolumnen) i frågeresultatet till drop-sekundärnyckeln.

> [!NOTE]
> Azure DMS stöder inte CASCADE-referensåtgärden, som hjälper till att automatiskt ta bort eller uppdatera en matchande rad i den underordnade tabellen när en rad tas bort eller uppdateras i den överordnade tabellen. Mer information finns i MySQL-dokumentationen i avsnittet Referensåtgärder i artikeln [OM BEGRÄNSNINGAR FÖR FRÄMMANDE NYCKEL.](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)
> Azure DMS kräver att du släpper begränsningar för externa nycklar i måldatabasservern under den inledande databelastningen och att du inte kan använda referensåtgärder. Om din arbetsbelastning är beroende av att uppdatera en relaterad underordnad tabell via den här referensåtgärden rekommenderar vi att du i stället utför [en dump och återställning.](../mysql/concepts-migrate-dump-restore.md) 


> [!IMPORTANT]
> Om du importerar data med hjälp av en säkerhetskopia tar du bort CREATE DEFINER-kommandona manuellt eller med kommandot --skip-definer när du utför en mysqldump. DEFINER kräver superprivilegier för att skapa och är begränsad i Azure Database for MySQL.

Om du har utlösare i databasen framtvingar den dataintegritet i målet före fullständig datamigrering från källan. Rekommendationen är att inaktivera utlösare för alla tabeller i målet under migreringen och sedan aktivera utlösare när migreringen är klar.

Kör följande skript i MySQL Workbench på måldatabasen för att extrahera släpp-utlösarskriptet och lägga till utlösarskriptet.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Kör den genererade utlösarfrågan (DropQuery-kolumnen) i resultatet för att släppa utlösare i måldatabasen. Lägg till utlösarfrågan kan sparas för att användas när datamigrering har slutförts.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **Resursproviders.**

    ![Visa resursprovidrar](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan Registrera till höger om **Microsoft.DataMigration.**

    ![Registrera resursprovider](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Skapa en Database Migration Service instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj en prisnivå och gå till nätverksskärmen. Offlinemigreringsfunktioner är tillgängliga på prisnivån Standard och Premium.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service grundläggande inställningar](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Välj ett befintligt virtuellt nätverk i listan eller ange namnet på det nya virtuella nätverk som ska skapas. Gå till skärmen Granska + skapa. Du kan också lägga till taggar i tjänsten med hjälp av taggar-skärmen.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll-SQL Server och målinstansen Azure SQL Database målinstansen.

    ![Konfigurera Azure Database Migration Service nätverksinställningar](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln Skapa ett virtuellt nätverk med hjälp [av Azure Portal](../virtual-network/quick-create-portal.md).

6. Granska konfigurationerna och välj **Skapa** för att skapa tjänsten.
    
    ![Azure Database Migration Service skapa](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.  

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta upp alla instanser av Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Välj din migreringstjänstinstans bland sökresultaten och välj + **Nytt migreringsprojekt**.
    
    ![Skapa ett nytt migreringsprojekt](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. På **sidan** Nytt migreringsprojekt anger du ett namn  för projektet. I rutan Typ av  källserver väljer du **MySQL.** I rutan  Typ av målserver väljer du **Azure Database For MySQL** och i urvalsrutan Migreringsaktivitetstyp väljer du **Online-datamigrering.** Välj **Skapa och kör aktivitet**.

    ![Skapa Database Migration Service-projekt](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternativt kan du välja Skapa **endast projekt för att** skapa migreringsprojektet nu och köra migreringen senare.

## <a name="configure-migration-project"></a>Konfigurera migreringsprojekt

1. På skärmen **Välj källa** anger du anslutningsinformationen för MySQL-källinstansen och väljer **Nästa: Välj>>**

    ![Skärmen Lägg till källinformation](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. På skärmen **Välj mål** anger du anslutningsinformationen för målinstansen Azure Database for MySQL och väljer **Nästa: Välj databaser>>**

    ![Skärmen Lägg till målinformation](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. På skärmen **Välj databaser** mappar du käll- och måldatabasen för migrering och väljer **Nästa: Konfigurera migreringsinställningar>>**. Du kan välja alternativet **Make Source Server Readonly** (Gör källservern skrivskyddade) för att göra källan skrivskyddade, men var försiktig så att det här är en inställning på servernivå. Om du väljer det här alternativet ställs hela servern in på skrivskydd, inte bara de valda databaserna.
    
    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.
    ![Skärmen Välj databasinformation](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Du kan välja flera databaser i det här steget, men varje instans av Azure Database Migration Service stöder upp till 4 databaser för samtidig migrering. Det finns också en gräns på 10 instanser av Azure Database Migration Service per prenumeration per region. Om du till exempel har 80 databaser att migrera kan du migrera 40 av dem till samma region samtidigt, men bara om du har skapat 10 instanser av Azure Database Migration Service.

4. På skärmen **Konfigurera migreringsinställningar** väljer du de tabeller som ska ingå i migreringen och **väljer Nästa: Sammanfattning>>**. Om måltabellerna har några data är de inte markerade som standard, men du kan uttryckligen välja dem och de trunkeras innan du påbörjar migreringen.

    ![Skärmen Välj tabeller](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. På skärmen **Sammanfattning** i **textrutan** Aktivitetsnamn anger du ett namn för migreringsaktiviteten och granskar sammanfattningen för att säkerställa att käll- och målinformationen matchar det du angav tidigare.

    ![Sammanfattning av migreringsprojekt](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Välj **Starta migrering.** Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**. **Status ändras** till **Körs när** tabellmigreringarna startar.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

     ![Aktivitetsstatus – slutförd](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. Under **Databasnamn** väljer du en specifik databas för att komma till migreringsstatus för åtgärderna **Fullständig datainläsning** och **Inkrementell datasynkronisering**.

    Fullständig datainläsning visar den initiala inläsningsmigreringsstatus, och Inkrementell datasynkronisering visar CDC-status (change data capture).

     ![Aktivitetsstatus – Fullständig inläsning slutförd](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Aktivitetsstatus – Inkrementell datasynkronisering](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Starta snabb](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya Azure SQL-måldatabasen.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for MySQL finns i artikeln [Known issues and workarounds with Azure Database for MySQL online migrations](known-issues-azure-mysql-online.md) (Kända problem och lösningar med Azure Database for MySQL-onlinemigreringar).
* Information om Azure Database Migration Service finns i artikeln [Vad är Azure Database Migration Service?](./dms-overview.md).
* Information om Azure Database for MySQL finns i artikeln [Vad är Azure Database for MySQL?](../mysql/overview.md).
