---
title: 'Självstudie: Migrera Oracle online till Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en onlinemigrering från Oracle lokalt eller på virtuella datorer till Azure Database for PostgreSQL med hjälp av Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 76ab2204a81d3cbfb559bfa4591f1f332b1707ee
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388082"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Självstudie: Migrera Oracle till Azure Database for PostgreSQL online med DMS (förhandsversion)

> [!IMPORTANT]
> **Migreringsscenariot Oracle to Azure Database for PostgreSQL** (för närvarande i förhandsversion) kommer inte längre att vara tillgängligt efter den 1 maj 2021. Vi fortsätter att ge stöd via alternativa verktyg (till exempel Ora2pg) och ger den bästa migreringsupplevelsen för migrering från Oracle till PostgreSQL. Metodtips för migrering finns i [Migreringsguiden för Oracle till Azure Database for PostgreSQL](https://aka.ms/OracletoPGguide). 

Du kan använda Azure Database Migration Service för att migrera databaserna från Oracle-databaser som finns lokalt eller på virtuella datorer för [att Azure Database for PostgreSQL](../postgresql/index.yml) med minimal avbrottstid. Med andra ord kan du slutföra migreringen med minimal avbrottstid till programmet. I den här självstudien migrerar du HR-exempeldatabasen från en lokal eller virtuell datorinstans av Oracle 11g till Azure Database for PostgreSQL med hjälp av onlinemigreringsaktiviteten i Azure Database Migration Service. 

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Utvärdera migreringen med hjälp av ora2pg-verktyget.
> * Migrera exempelschemat med verktyget ora2pg.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Om Azure Database Migration Service utföra en onlinemigrering måste du skapa en instans baserat på prisnivån Premium.

> [!IMPORTANT]
> För en optimal migreringsupplevelse rekommenderar Microsoft att du skapar en instans Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskriver hur du utför en onlinemigrering från Oracle till Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Ladda ned och [installera Oracle 11g Version 2 (Standard Edition, Standard Edition One eller Enterprise Edition).](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* Ladda ned **HR-exempeldatabasen** [härifrån](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Ladda ned [och installera ora2pg på Windows eller Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Skapa en instans i Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Anslut till instansen och skapa en databas med hjälp av instruktionen i det här [dokumentet](../postgresql/tutorial-design-database-using-azure-portal.md).
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning till dina lokala källservrar med hjälp av [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett [virtuellt nätverk finns i Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabbstartsartiklarna med stegvisa detaljer.

  > [!NOTE]
  > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänstslutpunkter i undernätet där tjänsten ska [etableras:](../virtual-network/virtual-network-service-endpoints-overview.md)
  >
  > * Måldatabasslutpunkt (till exempel SQL-slutpunkt Cosmos DB slutpunkt och så vidare)
  > * Lagringsslutpunkt
  > * Service Bus-slutpunkt
  >
  > Den här konfigurationen är nödvändig Azure Database Migration Service saknar Internetanslutning.

* Kontrollera att dina regler för nätverkssäkerhetsgrupp för virtuellt nätverk (NSG) inte blockerar den utgående porten 443 för ServiceTag för ServiceBus, Storage och AzureMonitor. Mer information om NSG-trafikfiltrering för virtuella nätverk finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen så att Azure Database Migration Service åtkomst till Oracle-källservern, som som standard är TCP-port 1521.
* När du använder en brandväggsinstallation framför dina källdatabaser kan du behöva lägga till brandväggsregler för att tillåta Azure Database Migration Service åtkomst till källdatabaserna för migrering.
* Skapa en brandväggsregel [på servernivå](../azure-sql/database/firewall-configure.md) för Azure Database for PostgreSQL tillåta Azure Database Migration Service åtkomst till måldatabaserna. Ange undernätsintervallet för det virtuella nätverk som används för Azure Database Migration Service.
* Ge åtkomst till Oracle-källdatabaserna.

  > [!NOTE]
  > DBA-rollen krävs för att en användare ska kunna ansluta till Oracle-källan.

  * Arkiv gör om loggar krävs för inkrementell synkronisering i Azure Database Migration Service att samla in dataändring. Följ dessa steg för att konfigurera Oracle-källan:
    * Logga in med SYSDBA-behörighet genom att köra följande kommando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Stäng av databasinstansen genom att köra följande kommando.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Vänta tills bekräftelsen är `'ORACLE instance shut down'` .

    * Starta den nya instansen och montera (men öppna inte) databasen för att aktivera eller inaktivera arkivering genom att köra följande kommando:

      ```
      STARTUP MOUNT;
      ```

      Databasen måste monteras. vänta på bekräftelse på att "Oracle-instansen har startats".

    * Ändra databasarkiveringsläget genom att köra följande kommando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Öppna databasen för normal drift genom att köra följande kommando:

      ```
      ALTER DATABASE OPEN;
      ```

      Du kan behöva starta om för att ARC-filen ska visas.

    * Verifiera genom att köra följande kommando:

      ```
      SELECT log_mode FROM v$database;
      ```

      Du bör få ett svar `'ARCHIVELOG'` . Om svaret är `'NOARCHIVELOG'` uppfylls inte kravet.

  * Aktivera kompletterande loggning för replikering med något av följande alternativ.

    * **Alternativ 1.**
      Ändra kompletterande loggning på databasnivå för att täcka alla tabeller med PK och unikt index. Identifieringsfrågan returnerar `'IMPLICIT'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Ändra tilläggsloggning på tabellnivå. Körs endast för tabeller som har datamanipulering och som inte har några PK:er eller unika index.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Alternativ 2.**
      Ändra kompletterande loggning på databasnivå för att täcka alla tabeller, och identifieringsfrågan returnerar `'YES'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Ändra tilläggsloggning på tabellnivå. Följ logiken nedan om du bara vill köra en -instruktion för varje tabell.

      Om tabellen har en primär nyckel:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Om tabellen har ett unikt index:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Annars kör du följande kommando:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Verifiera genom att köra följande kommando:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Du bör få ett svar `'YES'` .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Utvärdera arbetet för ett Oracle för att Azure Database for PostgreSQL migrering

Vi rekommenderar att du använder ora2pg för att utvärdera det arbete som krävs för att migrera från Oracle till Azure Database for PostgreSQL. Använd direktivet för att skapa en rapport som visar alla Oracle-objekt, den uppskattade migreringskostnaden (under utvecklardagar) och vissa databasobjekt som kan kräva särskild uppmärksamhet som en del `ora2pg -t SHOW_REPORT` av konverteringen.

De flesta kunder kommer att ägna mycket tid åt att granska utvärderingsrapporten och överväga den automatiska och manuella konverteringen.

Information om hur du konfigurerar och kör ora2pg för att skapa en utvärderingsrapport finns i avsnittet **Premigration: Assessment** i [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). En exempelutvärderingsrapport för ora2pg finns tillgänglig som referens [här.](https://ora2pg.darold.net/report.html)

## <a name="export-the-oracle-schema"></a>Exportera Oracle-schemat

Vi rekommenderar att du använder ora2pg för att konvertera Oracle-schemat och andra Oracle-objekt (typer, procedurer, funktioner osv.) till ett schema som är kompatibelt med Azure Database for PostgreSQL. ora2pg innehåller många direktiv som hjälper dig att definiera vissa datatyper i förväg. Du kan till exempel använda direktivet för att ersätta `DATA_TYPE` alla NUMBER(*,0) med bigint i stället för NUMERIC(38).

Du kan köra ora2pg för att exportera vart och ett av databasobjekten i SQL-filer. Du kan sedan granska .sql-filerna innan du importerar dem Azure Database for PostgreSQL med psql eller så kan du köra . SQL-skript i PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Exempel:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Information om hur du konfigurerar och kör ora2pg för schemakonvertering finns i avsnittet **Migrering: Schema och data** i [Oracle för att Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Konfigurera schemat i Azure Database for PostgreSQL

Du kan välja att konvertera Oracle-tabellscheman, lagrade procedurer, paket och andra databasobjekt så att postgres blir kompatibla med ora2pg innan du startar en migreringspipeline i Azure Database Migration Service. Se länkarna nedan för hur du arbetar med ora2pg:

* [Installera ora2pg i Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle till Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service också skapa PostgreSQL-tabellschemat. Tjänsten har åtkomst till tabellschemat i den anslutna Oracle-källan och skapar ett kompatibelt tabellschema i Azure Database for PostgreSQL. Se till att validera och kontrollera schemaformatet i Azure Database for PostgreSQL när Azure Database Migration Service har skapat schemat och flyttat data.

> [!IMPORTANT]
> Azure Database Migration Service skapar bara tabellschemat. andra databasobjekt, till exempel lagrade procedurer, paket, index osv. skapas inte.

Se även till att släppa främmande nyckeln i måldatabasen för att den fullständiga inläsningen ska köras. Se avsnittet **Migrera exempelschemat** i artikeln här [för ett](./tutorial-postgresql-azure-postgresql-online.md) skript som du kan använda för att ta bort främmande nyckeln. Använd Azure Database Migration Service för att köra för fullständig inläsning och synkronisering.

### <a name="when-the-postgresql-table-schema-already-exists"></a>När tabellschemat för PostgreSQL redan finns

Om du skapar ett PostgreSQL-schema med hjälp av verktyg som ora2pg innan du startar dataförflyttningen med Azure Database Migration Service mappar du källtabellerna till måltabellerna i Azure Database Migration Service.

1. När du skapar ett nytt Oracle Azure Database for PostgreSQL skapa ett migreringsprojekt uppmanas du att välja måldatabas och målschema i steget Välj scheman. Fyll i måldatabasen och målschemat.

   ![Skärmbild som visar Mappa till måldatabaser.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Skärmen **Migreringsinställningar** visar en lista över tabeller i Oracle-källan. Azure Database Migration Service försöker matcha tabeller i källan och måltabellerna baserat på tabellnamn. Om det finns flera matchande måltabeller med olika höljen kan du välja vilken måltabell du ska mappa till.

    ![Skärmbild som visar migreringsinställningar.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Om du behöver mappa källtabellnamn till tabeller med olika namn kan du skicka e-post och vi kan ange [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) ett skript för att automatisera processen.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>När postgreSQL-tabellschemat inte finns

Om PostgreSQL-måldatabasen inte innehåller någon tabellschemainformation Azure Database Migration Service konverterar källschemat och återskapar det i måldatabasen. Kom ihåg Azure Database Migration Service bara skapar tabellschemat, inte andra databasobjekt, till exempel lagrade procedurer, paket och index.
Om du Azure Database Migration Service skapa schemat åt dig måste du se till att målmiljön innehåller ett schema utan befintliga tabeller. Om Azure Database Migration Service identifierar någon tabell förutsätter tjänsten att schemat har skapats av ett externt verktyg, till exempel ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service kräver att alla tabeller skapas på samma sätt, med hjälp av antingen Azure Database Migration Service eller ett verktyg som ora2pg, men inte båda.

Så här kommer du igång:

1. Skapa ett schema i måldatabasen baserat på dina programkrav. PostgreSQL-tabellschemat och kolumnnamnen är gemener som standard. Oracle-tabellschema och kolumner är å andra sidan som standard i versaler.
2. I steget Välj scheman anger du måldatabasen och målschemat.
3. Baserat på det schema som du skapar Azure Database for PostgreSQL Azure Database Migration Service följande transformeringsregler:

    Om schemanamnet i Oracle-källan och matchar det i Azure Database for PostgreSQL skapar Azure Database Migration Service tabellschemat med samma fall som *i målet*.

    Exempel:

    | Oracle-källschema | Måldatabas för PostgreSQL.Schema | DMS skapade schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." LÄNDER"." COUNTRY_ID" |
    | HR | targetHR.HR | "HR"." LÄNDER"." COUNTRY_ID" |
    | HR | targetHR.Hr | *Det går inte att mappa blandade ärenden |

    *Om du vill skapa scheman för blandat fall och tabellnamn i PostgreSQL-målet kontaktar du [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . Vi kan tillhandahålla ett skript för att konfigurera schema för tabeller med blandat fall i PostgreSQL-måldatabasen.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan Registrera till höger om **Microsoft.DataMigration.**

    ![Registrera resursprovider](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till Oracle-källan och målinstansen Azure Database for PostgreSQL mål.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln Skapa ett virtuellt nätverk [med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

    ![Hitta din instans av Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du ett namn för projektet. I **textrutan** Typ av källserver väljer du **Oracle.** I textrutan **Målservertyp** väljer **du Azure Database for PostgreSQL**.
5. I avsnittet **Välj typ av aktivitet** väljer du **Online-datamigrering.**

   ![Skapa Database Migration Service-projekt](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativt kan du välja Skapa **endast projekt för att** skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **Spara,** notera kraven för att använda Azure Database Migration Service för att utföra en onlinemigrering och välj **sedan Skapa och kör aktivitet.**

## <a name="specify-source-details"></a>Ange källinformation

* På skärmen **Lägg till källinformation** anger du anslutningsinformationen för Oracle-källinstansen.

  ![Skärmen Lägg till källinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Ladda upp Oracle OCI-drivrutin

1. Välj **Spara.** På skärmen Installera **OCI-drivrutin** loggar du sedan in på ditt Oracle-konto och laddar ned drivrutinen **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37 128 586 byte(s)) (SHA1-kontrollsumma: 865082268) [härifrån.](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)
2. Ladda ned drivrutinen till en delad mapp.

   Kontrollera att mappen delas med det användarnamn som du har angett med minsta skrivskyddade åtkomst. Azure Database Migration Service åtkomst till och läsningar från resursen för att ladda upp OCI-drivrutinen till Azure genom att personifiera det användarnamn som du anger.

   Användarnamnet du anger måste vara ett Windows-användarkonto.

   ![Installera OCI-drivrutin](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara.** På  skärmen Målinformation anger du anslutningsinformationen för mål-Azure Database for PostgreSQL-servern, som är den före etablerade instansen av Azure Database for PostgreSQL som **HR-schemat** har distribuerats till.

    ![Skärmen Målinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

  Migreringsaktivitetsfönstret visas och **Status** för aktiviteten **initierar**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

     ![Aktivitetsstatus – körs](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Under **Databasnamn väljer** du en specifik databas för att komma till migreringsstatusen för åtgärder **för fullständig datainläsning** och **inkrementell datasynkronisering.**

    Fullständig datainläsning visar den initiala inläsningsmigreringsstatus, och Inkrementell datasynkronisering visar CDC-status (change data capture).

     ![Aktivitetsstatus – Fullständig inläsning slutförd](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Aktivitetsstatus – Inkrementell datasynkronisering](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.

   ![Starta snabb](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Välj **Bekräfta** och sedan **Använd**.
4. När status för databasmigrering **visar Slutförd** ansluter du dina program till den nya Azure Database for PostgreSQL instansen.

 > [!NOTE]
 > Eftersom PostgreSQL som standard har schema.table.column i gemener kan du återgå från versaler till gemener med hjälp av skriptet i avsnittet Konfigurera **schemat i Azure Database for PostgreSQL** tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Mer information om Azure Database Migration Service finns i artikeln [Vad är Azure Database Migration Service?](./dms-overview.md).
* Information om Azure Database for PostgreSQL finns i artikeln [Vad är Azure Database for PostgreSQL?](../postgresql/overview.md).
