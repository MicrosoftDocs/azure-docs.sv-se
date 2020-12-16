---
title: 'Självstudie: Migrera MySQL online till Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från MySQL lokalt för att Azure Database for MySQL med hjälp av Azure Database Migration Service.
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
ms.openlocfilehash: 41c0bd23bbd2d69506a979c5a36ac40f73258f2c
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605521"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Självstudie: Migrera MySQL till Azure Database for MySQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal MySQL-instans till [Azure Database for MySQL](../mysql/index.yml) med minimal stillestånds tid. Du kan med andra ord migrera med minimal stilleståndstid i programmet. I den här självstudien migrerar du exempel databasen **anställda** från en lokal instans av MySQL 5,7 till Azure Database for MySQL med hjälp av en online-migrering i Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempelschemat med verktyget mysqldump.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium.

> [!IMPORTANT]
> För en optimal migrering rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som mål databasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

> [!NOTE]
> Kompensations fri kommunikation
>
> Microsoft stöder en mängd olika och införlivande miljöer. Den här artikeln innehåller referenser till ordet _slav_. Microsofts [stil guide för en kostnads fri kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) känner igen detta som ett undantags ord. Ordet används i den här artikeln för konsekvens eftersom det är det ord som visas i program varan. När program varan har uppdaterats för att ta bort ordet uppdateras den här artikeln som en justering.
>


## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Ladda ned och installera [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 eller 5.7. Den lokala MySQL-versionen måste överensstämma med Azure Database for MySQL-versionen. Exempelvis kan MySQL 5.6 endast migreras till Azure Database for MySQL 5.6 och inte uppgraderat till 5.7. Migreringar till eller från MySQL 8,0 stöds inte. Migreringar till eller från MySQL 8,0 stöds inte.
* [Skapa en instans i Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Läs artikeln [Använda MySQL Workbench för att ansluta och fråga efter data](../mysql/connect-workbench.md) om du vill ha information om hur du ansluter och skapar en databas med Azure-portalen.  
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av Virtual networkNet lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.

* Se till att de virtuella nätverkets säkerhets grupp regler inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service åtkomst till käll-MySQL-servern som standard är TCP-port 3306.
* När du använder en brand Väggs installation framför dina käll databaser, kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering.
* Skapa en [brand Väggs regel](../azure-sql/database/firewall-configure.md) på server nivå för Azure Database for MySQL att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* MySQL-källan måste vara på en MySQL Community Edition som stöds. Om du vill kontrollera vilken version av MySQL-instansen du har går du till verktyget MySQL eller MySQL Workbench och kör följande kommando:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL stöder endast InnoDB-tabeller. Om du vill konvertera MyISAM-tabeller till InnoDB kan du läsa artikeln [Konvertera tabeller från MyISAM till InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Aktivera binär loggning i filen my.ini (Windows) eller my.cnf (Unix) i källdatabasen med följande konfiguration:

  * **server_id** = 1 eller större (endast relevant för MySQL 5.6)
  * **logg-bin** = \<path> (gäller endast för MySQL 5,6)    Till exempel: log-bin = E:\ MySQL_logs \BinLog
  * **binlog_format** = rad
  * **Expire_logs_days** = 5 (vi rekommenderar att inte använda noll, gäller endast MySQL 5,6)
  * **Binlog_row_image** = fullständig (endast relevant för MySQL 5.6)
  * **log_slave_updates** = 1

* Användaren måste ha rollen ReplicationAdmin med följande behörigheter:

  * **REPLIKERINGSKLIENT** – Krävs endast för ändringsbearbetningsuppgifter. Med andra ord kräver inte uppgifter för fullständig inläsning detta privilegium.
  * **REPLIKERINGSREPLIK** – Krävs endast för ändringsbearbetningsuppgifter. Med andra ord kräver inte uppgifter för fullständig inläsning detta privilegium.
  * **SUPER** – Krävs endast i versioner som är äldre än MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

För att slutföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på databasen. Du kan extrahera schema med mysqldump med parametern `--no-data`.

Förutsatt att du har MySQL- **anställda** exempel databas i det lokala systemet, är kommandot för schema migrering med mysqldump:

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

Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen.  Kör följande skript i MySQL Workbench för att extrahera skriptet för att ta bort sekundär nyckel och lägga till ett sekundär nyckel skript.

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
> Azure DMS stöder inte referens åtgärden CASCADE, som hjälper dig att automatiskt ta bort eller uppdatera en matchande rad i den underordnade tabellen när en rad tas bort eller uppdateras i den överordnade tabellen. Mer information finns i avsnittet referens åtgärder i artikelns [begränsningar för sekundär nyckel](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> Azure DMS kräver att du släpper sekundär nyckel begränsningar i mål databas servern under den inledande data inläsningen och du kan inte använda referens åtgärder. Om din arbets belastning är beroende av att uppdatera en relaterad underordnad tabell via den här referens åtgärden, rekommenderar vi att du utför en [dumpning och återställning](../mysql/concepts-migrate-dump-restore.md) i stället. 


> [!IMPORTANT]
> Om du importerar data med hjälp av en säkerhets kopia tar du bort kommandona skapa avskärmar manuellt eller genom att använda kommandot--Skip-definar när du utför en mysqldump. Definar kräver Super-behörighet för att skapa och är begränsad i Azure Database for MySQL.

Om du har en utlösare i data (infogning eller uppdaterings utlösare), kommer den att upprätthålla data integriteten i målet före replikerade data från källan. Rekommendationen är att inaktivera utlösare i alla tabeller för målet under migreringen och sedan aktivera utlösare när migreringen är klar.

Om du vill inaktivera utlösare i mål databasen använder du följande kommando:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll SQL Server och mål Azure SQL Database instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. På skärmen **Azure Database migration Services** söker du efter namnet på Azure Database migration service-instansen som du skapade och väljer sedan instansen.

     ![Hitta din instans av Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, I textrutan **Typ av källserver** väljer du **MySQL**, i textrutan **Målservertyp** väljer du **AzureDbForMySQL**.
5. I avsnittet **Välj aktivitetstyp** väljer du **Online-datamigrering**

    ![Skapa Database Migration Service-projekt](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

6. Välj **Spara**, notera kraven för att använda DMS för att migrera data och välj sedan **Skapa och kör aktivitet**.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Lägg till källinformation** anger du anslutningsinformationen för MySQL-källinstansen.

    ![Skärmen Lägg till källinformation](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara** och ange på sidan **Målinformation** anslutningsinformationen för Azure Database for MySQL-målservern, som är den företablerade instansen av Azure Database for MySQL som schemat **Anställda**-distribuerades till med mysqldump.

    ![Skärmen Målinformation](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Mappa till måldatabaser](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Du kan välja flera databaser i det här steget, men varje instans av Azure Database Migration Service stöder upp till fyra databaser för samtidig migrering. Det finns också en gräns på två instanser av Azure Database Migration Service per region i en prenumeration. Om du till exempel har 40 databaser att migrera kan du bara migrera åtta av dem samtidigt, och endast om du har skapat två instanser av Azure Database Migration Service.

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Fönstret migrering av aktivitet visas och aktivitetens **status** **initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

     ![Aktivitetsstatus – slutförd](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Under **Databasnamn** väljer du en specifik databas för att komma till migreringsstatus för åtgärderna **Fullständig datainläsning** och **Inkrementell datasynkronisering**.

    Fullständig datainläsning visar den initiala inläsningsmigreringsstatus, och Inkrementell datasynkronisering visar CDC-status (change data capture).

     ![Aktivitetsstatus – Fullständig inläsning slutförd](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Aktivitetsstatus – Inkrementell datasynkronisering](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Starta snabb](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya Azure SQL-måldatabasen.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for MySQL finns i artikeln [Known issues and workarounds with Azure Database for MySQL online migrations](known-issues-azure-mysql-online.md) (Kända problem och lösningar med Azure Database for MySQL-onlinemigreringar).
* Mer information om Azure Database Migration Service finns i artikeln [Vad är Azure Database migration service?](./dms-overview.md).
* Mer information om Azure Database for MySQL finns i artikeln [Vad är Azure Database for MySQL?](../mysql/overview.md).