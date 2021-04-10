---
title: 'Självstudie: Migrera Oracle online till Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från Oracle lokalt eller på virtuella datorer för att Azure Database for PostgreSQL genom att använda Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.subservice: migration-guide
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 2be824c89ac1e6b900dc9b67e046e2be2c7f8919
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562832"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Självstudie: Migrera Oracle till Azure Database for PostgreSQL online med DMS (för hands version)

> [!IMPORTANT]
> Scenario **för Oracle till Azure Database for PostgreSQL** migrering (för närvarande i för hands version) kommer inte längre att vara tillgängligt efter den 1 maj 2021. Vi kommer att fortsätta att tillhandahålla support via alternativa verktyg (till exempel Ora2pg) och tillhandahålla bästa migrering för Oracle till PostgreSQL-migreringar. Metod tips för migrering finns i [Oracle to Azure Database for PostgreSQL migration guide](https://aka.ms/OracletoPGguide). 

Du kan använda Azure Database Migration Service för att migrera databaserna från Oracle-databaser som finns lokalt eller på virtuella datorer för att [Azure Database for PostgreSQL](../postgresql/index.yml) med minimal stillestånds tid. Med andra ord kan du slutföra migreringen med minimal stillestånds tid för programmet. I den här självstudien migrerar du exempel databasen **HR** från en lokal eller virtuell dator instans av Oracle-11g till Azure Database for PostgreSQL med hjälp av aktiviteten online-migrering i Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Utvärdera migreringens ansträngning med ora2pg-verktyget.
> * Migrera exempel schema med verktyget ora2pg.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium.

> [!IMPORTANT]
> För en optimal migrering rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som mål databasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs hur du utför en online-migrering från Oracle till Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Hämta och installera [Oracle 11G version 2 (Standard Edition, Standard Edition One eller Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Ladda ned exempel **HR** -databasen [härifrån.](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)
* Hämta och [Installera ora2pg på antingen Windows eller Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Skapa en instans i Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Anslut till instansen och skapa en databas med hjälp av instruktionen i det här [dokumentet](../postgresql/tutorial-design-database-using-azure-portal.md).
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

  > [!NOTE]
  > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
  >
  > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
  > * Lagrings slut punkt
  > * Service Bus-slutpunkt
  >
  > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.

* Se till att dina regler för nätverks säkerhets gruppen (NSG) för virtuella nätverk inte blockerar den utgående porten 443 för ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service åtkomst till käll-Oracle-servern, som standard är TCP-port 1521.
* När du använder en brand Väggs installation framför dina käll databaser, kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering.
* Skapa en [brand Väggs regel](../azure-sql/database/firewall-configure.md) på server nivå för Azure Database for PostgreSQL att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* Ge åtkomst till käll-Oracle-databaserna.

  > [!NOTE]
  > DBA-rollen krävs för att en användare ska kunna ansluta till Oracle-källan.

  * Arkivera om-loggar krävs för stegvis synkronisering i Azure Database Migration Service för att samla in data ändringar. Följ de här stegen för att konfigurera Oracle-källan:
    * Logga in med SYSDBA-behörighet genom att köra följande kommando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Stäng databas instansen genom att köra följande kommando.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Vänta på bekräftelsen `'ORACLE instance shut down'` .

    * Starta den nya instansen och montera (men öppna inte) databasen för att aktivera eller inaktivera arkivering Bu som kör följande kommando:

      ```
      STARTUP MOUNT;
      ```

      Databasen måste monteras; vänta på bekräftelse ' Oracle-instansen startade '.

    * Ändra läget för databas arkivering genom att köra följande kommando:

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

      Du bör få ett svar `'ARCHIVELOG'` . Om svaret är är `'NOARCHIVELOG'` kravet inte uppfyllt.

  * Aktivera kompletterande loggning för replikering med något av följande alternativ.

    * **Alternativ 1**.
      Ändra den kompletterande loggningen på databas nivå för att avse alla tabeller med PK och unikt index. Identifierings frågan kommer att returneras `'IMPLICIT'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Ändra kompletterande loggning på tabell nivå. Kör bara för tabeller som har data manipulation och inte har PKs eller unika index.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Alternativ 2**.
      Ändra den kompletterande loggningen på databas nivå för att avse alla tabeller och identifierings frågan returnerar `'YES'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Ändra kompletterande loggning på tabell nivå. Följ nedanstående logik om du bara vill köra en instruktion för varje tabell.

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

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Utvärdera ansträngningen för en Oracle att Azure Database for PostgreSQL migrering

Vi rekommenderar att du använder ora2pg för att bedöma den ansträngning som krävs för att migrera från Oracle till Azure Database for PostgreSQL. Använd `ora2pg -t SHOW_REPORT` direktivet för att skapa en rapport som visar alla Oracle-objekt, den uppskattade kostnaden för migrering (i utvecklings dagar) och vissa databas objekt som kan kräva särskild uppmärksamhet som en del av konverteringen.

De flesta kunder kommer att ägna en lång tid att granska utvärderings rapporten och beakta den automatiska och manuella konverteringen.

Information om hur du konfigurerar och kör ora2pg för att skapa en utvärderings rapport finns i avsnittet om att **migrera: utvärdering** i [Oracle för att Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). En utvärderings rapport för ora2pg finns tillgänglig för referens [här](https://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Exportera Oracle-schemat

Vi rekommenderar att du använder ora2pg för att konvertera Oracle-schemat och andra Oracle-objekt (typer, procedurer, funktioner osv) till ett schema som är kompatibelt med Azure Database for PostgreSQL. ora2pg innehåller många direktiv som hjälper dig att fördefiniera vissa data typer. Du kan till exempel använda `DATA_TYPE` direktivet för att ersätta alla siffror (*, 0) med bigint i stället för numeriska (38).

Du kan köra ora2pg för att exportera varje databas objekt i. SQL-filer. Du kan sedan granska. SQL-filerna innan du importerar dem till Azure Database for PostgreSQL med psql eller så kan du köra. SQL-skript i PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Exempel:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Om du vill konfigurera och köra ora2pg för schema konvertering, se avsnittet **migrering: schema och data** i [Oracle för att Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Konfigurera schemat i Azure Database for PostgreSQL

Du kan välja att konvertera Oracle Table-scheman, lagrade procedurer, paket och andra databas objekt så att de blir postgres kompatibla genom att använda ora2pg innan du påbörjar en migrerings-pipeline i Azure Database Migration Service. Se länkarna nedan för att arbeta med ora2pg:

* [Installera ora2pg i Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Cookbook för Oracle till Azure PostgreSQL-migrering](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service kan också skapa tabell schemat PostgreSQL. Tjänsten använder tabell schemat i den anslutna Oracle-källan och skapar ett kompatibelt tabell schema i Azure Database for PostgreSQL. Se till att verifiera och kontrol lera schema formatet i Azure Database for PostgreSQL när Azure Database Migration Service har skapat schemat och flyttat data.

> [!IMPORTANT]
> Azure Database Migration Service skapar bara tabell schemat. andra databas objekt, till exempel lagrade procedurer, paket, index osv., skapas inte.

Se även till att ta bort sekundär nyckeln i mål databasen för att köra den fullständiga belastningen. I avsnittet **migrera exempel schema** i artikeln hittar du ett skript som du kan [använda för att](./tutorial-postgresql-azure-postgresql-online.md) ta bort sekundär nyckeln. Använd Azure Database Migration Service för att köra fullständig belastning och synkronisering.

### <a name="when-the-postgresql-table-schema-already-exists"></a>När PostgreSQL-tabellens schema redan finns

Om du skapar ett PostgreSQL-schema med verktyg som ora2pg innan du påbörjar data förflyttningen med Azure Database Migration Service mappar du käll tabellerna till mål tabellerna i Azure Database Migration Service.

1. När du skapar ett nytt Oracle till Azure Database for PostgreSQL-migreringsjobb uppmanas du att välja mål databas och mål schema i steget Välj scheman. Fyll i mål databasen och mål schemat.

   ![Skärm bild som visar kartan till mål databaserna.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Skärmen **Inställningar för migrering** visar en lista över tabeller i Oracle-källan. Azure Database Migration Service försöker matcha tabeller i käll-och mål tabellerna baserat på tabell namn. Om det finns flera matchande mål tabeller med olika Skift läge kan du välja vilken mål tabell som ska mappas till.

    ![Skärm bild som visar inställningar för migrering.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Om du behöver mappa käll tabell namn till tabeller med olika namn, e-post [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) och vi kan tillhandahålla ett skript för att automatisera processen.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>När tabell schemat PostgreSQL inte finns

Om mål PostgreSQL-databasen inte innehåller någon tabell schema information konverterar Azure Database Migration Service käll schemat och återskapar det i mål databasen. Kom ihåg att Azure Database Migration Service skapar enbart tabell schemat, inte andra databas objekt, till exempel lagrade procedurer, paket och index.
För att Azure Database Migration Service skapa schemat åt dig, måste du se till att mål miljön innehåller ett schema utan befintliga tabeller. Om Azure Database Migration Service identifierar någon tabell förutsätter tjänsten att schemat har skapats av ett externt verktyg, till exempel ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service kräver att alla tabeller skapas på samma sätt, genom att använda antingen Azure Database Migration Service eller ett verktyg som ora2pg, men inte båda.

Så här kommer du igång:

1. Skapa ett schema i mål databasen baserat på dina program krav. Som standard är PostgreSQL tabell schema och kolumn namn lägre bokstäver. Oracle Table schema och kolumner, å andra sidan, är som standard alla versaler.
2. I steget Välj scheman anger du mål databasen och mål schemat.
3. Azure Database Migration Service använder följande omvandlings regler baserat på det schema som du skapar i Azure Database for PostgreSQL:

    Om schema namnet i Oracle-källan och matchar det i Azure Database for PostgreSQL, *skapar Azure Database migration service tabell schemat med samma Skift läge som i målet*.

    Exempel:

    | Käll-Oracle-schema | Mål PostgreSQL Database. schema | DMS skapade schema. tabell. Column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR. public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." LÄNDER "." COUNTRY_ID " |
    | HR | targetHR.HR | "HR". LÄNDER "." COUNTRY_ID " |
    | HR | targetHR.Hr | * Det går inte att mappa blandade fall |

    * Om du vill skapa blandade Skift läges schema och tabell namn i mål PostgreSQL, kontaktar du [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . Vi kan tillhandahålla ett skript för att konfigurera tabell scheman för blandade fall i mål PostgreSQL-databasen.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll-Oracle och mål Azure Database for PostgreSQL instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

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
4. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **käll Server typ** , väljer **Oracle** i text rutan **mål server typ** väljer du **Azure Database for PostgreSQL**.
5. I avsnittet **Välj typ av aktivitet** väljer du **migrering av data online**.

   ![Skapa Database Migration Service-projekt](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

6. Välj **Spara**, anteckna kraven för att kunna använda Azure Database migration service för att utföra en online-migrering och välj sedan **skapa och kör aktivitet**.

## <a name="specify-source-details"></a>Ange källinformation

* På sidan **Lägg till käll information** anger du anslutnings information för käll-Oracle-instansen.

  ![Skärmen Lägg till källinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Ladda upp Oracle OCI-drivrutin

1. Välj **Spara** och på skärmen **Installera OCI-drivrutin** loggar du in på ditt Oracle-konto och laddar ned driv rutins **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37 128 586 byte (s)) (SHA1-kontrollsumma: 865082268) [härifrån](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Ladda ned driv rutinen till en delad mapp.

   Kontrol lera att mappen delas med det användar namn som du angav med minst skrivskyddad åtkomst. Azure Database Migration Service åtkomst och läsningar från resursen för att överföra OCI-drivrutinen till Azure genom att personifiera det användar namn som du anger.

   Det användar namn som du anger måste vara ett Windows-användarkonto.

   ![Installera OCI-drivrutin](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara** och ange sedan anslutnings information för mål Azure Database for PostgreSQL servern på sidan **mål information** , vilket är den företablerade instansen av Azure Database for PostgreSQL som **HR** -schemat har distribuerats till.

    ![Skärmen Målinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Mappa till måldatabaser](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

  Fönstret migrering av aktivitet visas och aktivitetens **status** **initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

     ![Aktivitets status-körs](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Under **databas namn** väljer du en annan databas för att komma till migreringsprocessen för **fullständig data inläsning** och **stegvisa data synkronisering** .

    Fullständig datainläsning visar den initiala inläsningsmigreringsstatus, och Inkrementell datasynkronisering visar CDC-status (change data capture).

     ![Aktivitetsstatus – Fullständig inläsning slutförd](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Aktivitetsstatus – Inkrementell datasynkronisering](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.

   ![Starta snabb](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Välj **Bekräfta** och sedan **Använd**.
4. När status för databas migreringen är **slutförd** ansluter du dina program till den nya mål Azure Database for PostgreSQLs instansen.

 > [!NOTE]
 > Eftersom PostgreSQL som standard har schema. Table. Column i gemener, kan du återställa från versaler till gemener genom att använda skriptet i avsnittet **Konfigurera schemat i Azure Database for PostgreSQL** tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Information om Azure Database Migration Service finns i artikeln [Vad är Azure Database migration service?](./dms-overview.md).
* Mer information om Azure Database for PostgreSQL finns i artikeln [Vad är Azure Database for PostgreSQL?](../postgresql/overview.md).
