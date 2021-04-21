---
title: 'Självstudie: Migrera MySQL till Azure Database for MySQL offline med DMS'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en offlinemigrering från MySQL lokalt till Azure Database for MySQL med hjälp av Azure Database Migration Service.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819728"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Självstudie: Migrera MySQL till Azure Database for MySQL offline med DMS

Du kan använda Azure Database Migration Service för att utföra en fullständig databasmigrering på en lokal MySQL-instans för [att Azure Database for MySQL](../mysql/index.yml) med datamigreringskapacitet med hög hastighet. I den här självstudien migrerar vi en exempeldatabas från en lokal instans av MySQL 5.7 till Azure Database for MySQL (v5.7) med hjälp av en offlinemigreringsaktivitet i Azure Database Migration Service. Även om artiklarna förutsätter att källan är en MySQL-databasinstans och att målet är Azure Database for MySQL, kan den användas för att migrera från en Azure Database for MySQL till en annan genom att bara ändra källserverns namn och autentiseringsuppgifter. Dessutom stöds migrering från MySQL-servrar med lägre version (v5.6 och senare) till högre versioner.

> [!IMPORTANT]
> För onlinemigrering kan du använda det här nya erbjudandet tillsammans med [datareplikering](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication). Du kan också använda verktyg med öppen källkod som [MyDumper/MyLoader](https://centminmod.com/mydumper.html) med datareplikering för onlinemigrering. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Migrera databasschema med verktyget mysqldump.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free)
* Ha en lokal MySQL-databas med version 5.7. Om inte laddar du ned och installerar [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.7.
* [Skapa en instans i Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Se artikeln Använda [MySQL Workbench](../mysql/connect-workbench.md) för att ansluta och fråga efter data för information om hur du ansluter och skapar en databas med hjälp av Workbench-programmet. Den Azure Database for MySQL versionen ska vara lika med eller högre än den lokala MySQL-versionen . MySQL 5.7 kan till exempel migrera till Azure Database for MySQL 5.7 eller uppgraderad till 8. 
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning till dina lokala källservrar med hjälp av [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett [virtuellt nätverk finns i Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabbstartsartiklarna med stegvisa detaljer.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av virtuellt nätverkNät lägger du till följande tjänstslutpunkter i undernätet där tjänsten ska [etableras:](../virtual-network/virtual-network-service-endpoints-overview.md)
    >
    > * Måldatabasslutpunkt (till exempel SQL-slutpunkt Cosmos DB slutpunkt och så vidare)
    > * Lagringsslutpunkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig Azure Database Migration Service saknar Internetanslutning.

* Se till att reglerna för nätverkssäkerhetsgruppen för det virtuella nätverket inte blockerar den utgående porten 443 för ServiceTag för ServiceBus, Storage och AzureMonitor. Mer information om NSG-trafikfiltrering för virtuella nätverk finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Öppna Windows-brandväggen för att tillåta anslutningar från Virtual Network för Azure Database Migration Service att få åtkomst till mySQL-källservern, som som standard är TCP-port 3306.
* När du använder en brandväggsinstallation framför dina källdatabaser kan du behöva lägga till brandväggsregler för att tillåta anslutningar från Virtual Network för Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en brandväggsregel på servernivå eller konfigurera [VNET-tjänstslutpunkter](../mysql/howto-manage-vnet-using-portal.md) för måldatabaser Azure Database for MySQL tillåta Virtual Network för Azure Database Migration Service åtkomst till måldatabaserna. [](../azure-sql/database/firewall-configure.md)
* MySQL-källan måste vara på en MySQL Community Edition som stöds. Om du vill kontrollera vilken version av MySQL-instansen du har går du till verktyget MySQL eller MySQL Workbench och kör följande kommando:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL stöder endast InnoDB-tabeller. Om du vill konvertera MyISAM-tabeller till InnoDB kan du läsa artikeln [Konvertera tabeller från MyISAM till InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)
* Användaren måste ha behörighet att läsa data i källdatabasen.

## <a name="migrate-database-schema"></a>Migrera databasschema

För att överföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på måldatabasen. Du kan extrahera schema med mysqldump med parametern `--no-data`. För detta behöver du en dator som kan ansluta till både MySQL-källdatabasen och Azure Database for MySQL.

Om du vill exportera schemat med mysqldump kör du följande kommando:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Exempel:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Kör följande kommando för att importera Azure Database for MySQL målkatalogen:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Exempel:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Om du har främmande nycklar i schemat hanteras den parallella datainläsningen under migreringen av migreringsuppgiften. Du behöver inte släppa främmande nycklar under schemamigrering.

Om du har utlösare i databasen framtvingar den dataintegritet i målet före fullständig datamigrering från källan. Rekommendationen är att inaktivera utlösare för alla tabeller i målet under migreringen och sedan aktivera utlösare när migreringen är klar.

Kör följande skript i MySQL Workbench på måldatabasen för att extrahera släpp-utlösarskriptet och lägga till utlösarskript.

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

Kör den genererade utlösarfrågan (DropQuery-kolumnen) i resultatet för att släppa utlösare i måldatabasen. Frågan om att lägga till utlösare kan sparas för att användas när datamigrering har slutförts.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

Registrering av resursprovidern måste bara göras i varje Azure-prenumeration en gång. Utan registreringen kan du inte skapa en instans av **Azure Database Migration Service**.

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **Resursproviders.**

3. Sök efter migrering och välj sedan Registrera till höger om **Microsoft.DataMigration.**

    ![Registrera resursprovider](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Skapa en Database Migration Service instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj en prisnivå och gå till nätverksskärmen. Offlinemigreringsfunktioner är tillgängliga på prisnivån Standard och Premium.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service grundläggande inställningar](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Välj ett befintligt virtuellt nätverk i listan eller ange namnet på det nya virtuella nätverk som ska skapas. Gå till gransknings- och skapa-skärmen. Du kan också lägga till taggar i tjänsten med hjälp av skärmen taggar.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll-SQL Server och målinstansen Azure SQL Database mål.

    ![Konfigurera Azure Database Migration Service nätverksinställningar](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln Skapa ett virtuellt nätverk [med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

6. Granska konfigurationerna och välj **Skapa** för att skapa tjänsten.
    
    ![Azure Database Migration Service skapa](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.  

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta upp alla instanser av Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Välj din migreringstjänstinstans bland sökresultaten och välj + **Nytt migreringsprojekt**.
    
    ![Skapa ett nytt migreringsprojekt](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. På **sidan** Nytt migreringsprojekt anger du ett namn  för projektet. I rutan Typ av  källserver väljer du **MySQL.** I markeringsrutan Målservertyp väljer du **Azure Database For MySQL** och i rutan Migreringsaktivitetstyp väljer du Förhandsgranskning av **datamigrering. \[ \]**  Välj **Skapa och kör aktivitet**.

    ![Skapa Database Migration Service-projekt](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternativt kan du välja Skapa **endast projekt för att** skapa migreringsprojektet nu och köra migreringen senare.

## <a name="configure-migration-project"></a>Konfigurera migreringsprojekt

1. På skärmen **Välj källa** anger du anslutningsinformationen för MySQL-källinstansen och väljer **Nästa: Välj>>**

    ![Skärmen Lägg till källinformation](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. På skärmen **Välj mål** anger du anslutningsinformationen för målinstansen Azure Database for MySQL och väljer **Nästa: Välj>>**

    ![Skärmen Lägg till målinformation](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. På skärmen **Välj databaser** mappar du käll- och måldatabasen för migrering och väljer **Nästa: Konfigurera migreringsinställningar>>**. Du kan välja alternativet **Make Source Server Readonly** (Gör källservern skrivskyddade) för att göra källan skrivskyddade, men var försiktig så att det här är en inställning på servernivå. Om du väljer det här alternativet anges hela servern till skrivskyddade, inte bara de valda databaserna.
    
    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.
    ![Skärmen Välj databasinformation](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > Även om du kan välja flera databaser i det här steget, men det finns gränser för hur många och hur snabbt databaser kan migreras på det här sättet, eftersom varje databas kommer att dela beräkning. Med standardkonfigurationen för Premium-SKU:n försöker varje migreringsuppgift migrera två tabeller parallellt. Dessa tabeller kan komma från vilken som helst av de valda databaserna. Om det inte är tillräckligt snabbt kan du dela upp databasmigreringsaktiviteter i olika migreringsuppgifter och skala över flera tjänster. Dessutom finns det en gräns på 10 instanser av Azure Database Migration Service per prenumeration per region.
    > Mer detaljerad kontroll över migreringens dataflöde och parallellisering finns i artikeln PowerShell: Kör offlinemigrering från [MySQL-databas](./migrate-mysql-to-azure-mysql-powershell.md) för att Azure Database for MySQL dms

4. På skärmen **Konfigurera migreringsinställningar** väljer du de tabeller som ska ingå i migreringen och **väljer Nästa: Sammanfattning>>**. Om måltabellerna har några data är de inte markerade som standard, men du kan uttryckligen välja dem och de trunkeras innan du påbörjar migreringen.

    ![Skärmen Välj tabeller](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. På skärmen **Sammanfattning** i **textrutan** Aktivitetsnamn anger du ett namn för migreringsaktiviteten och granskar sammanfattningen för att säkerställa att käll- och målinformationen matchar det du angav tidigare.

    ![Sammanfattning av migreringsprojekt](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Välj **Starta migrering.** Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**. Status **ändras** till **Körs när** tabellmigreringarna startar.
 
     ![Köra migrering](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetsskärmen väljer du **Uppdatera** för att uppdatera visningen och ser förloppet för antalet tabeller som har slutförts. 

2. Du kan klicka på databasnamnet på aktivitetsskärmen för att se status för varje tabell när de migreras. Välj **Uppdatera** för att uppdatera visningen. 

     ![Övervaka migrering](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Slutföra migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

    ![Slutföra migrering](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Aktiviteter efter migreringen

Direktmigrering i en offlinemigrering är en programberoende process som ligger utanför omfånget för det här dokumentet, men följande aktiviteter efter migreringen föreskrivs:

1. Skapa inloggningar, roller och behörigheter enligt programkraven.
2. Återskapa alla utlösare på måldatabasen som extraherades under steget före migreringen.
3. Utför en ren testning av programmet mot måldatabasen för att certifiera migreringen. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda Database Migration Service kan du ta bort tjänsten med följande steg:

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta upp alla instanser av DMS](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Välj din migreringstjänstinstans bland sökresultaten och välj Ta **bort tjänst.**
    
    ![Ta bort migreringstjänsten](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. I bekräftelsedialogrutan skriver du namnet på tjänsten i textrutan TYPE THE DATABASE MIGRATION SERVICE NAME (SKRIV **NAMNET PÅ DATABASMIGRERINGSTJÄNSTEN)** och väljer Ta **bort**

    ![Bekräfta borttagning av migreringstjänsten](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför migreringar med DMS finns i artikeln [Vanliga problem – Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Information om hur du felsöker anslutningsproblem för källdatabaser när du använder DMS finns i [artikeln Problem med att ansluta källdatabaser.](./known-issues-troubleshooting-dms-source-connectivity.md)
* Information om Azure Database Migration Service finns i artikeln [Vad är Azure Database Migration Service?](./dms-overview.md).
* Information om Azure Database for MySQL finns i artikeln [Vad är Azure Database for MySQL?](../mysql/overview.md).
* Vägledning om hur du använder DMS via PowerShell finns i artikeln PowerShell: Kör offlinemigrering från [MySQL-databas till Azure Database for MySQL med DMS](./migrate-mysql-to-azure-mysql-powershell.md)