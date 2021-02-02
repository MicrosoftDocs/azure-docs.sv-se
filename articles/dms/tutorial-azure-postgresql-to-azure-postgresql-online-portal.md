---
title: 'Självstudie: Migrera Azure DB för PostgreSQL till Azure DB för PostgreSQL online via Azure Portal'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från en Azure DB för PostgreSQL till en annan Azure Database for PostgreSQL genom att använda Azure Database Migration Service via Azure Portal.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 545359cd68c18fea8bf1369a57226d024489f293
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258939"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Självstudie: Migrera/uppgradera Azure DB för PostgreSQL – en server till Azure DB för PostgreSQL – en server online med DMS via Azure Portal

Du kan använda Azure Database Migration Service för att migrera databaserna från en [Azure Database for PostgreSQL-enskild server](../postgresql/overview.md#azure-database-for-postgresql---single-server) instans till samma eller en annan version av Azure Database for PostgreSQL-enskild Server instans eller Azure Database for PostgreSQL-flexibel server med minimal stillestånds tid. I den här självstudien migrerar du exempel databasen för **DVD-hyra** från en Azure Database for PostgreSQL v10 till Azure Database for PostgreSQL-en server med hjälp av aktiviteten online-migrering i Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempel schema med hjälp av pg_dump-verktyget.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb i Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.
> * Utför migrering start punkt.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium. Vi krypterar disk för att förhindra stöld av data under migreringen

> [!IMPORTANT]
> För en optimal migrering rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som mål databasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

> [!IMPORTANT]
> Migrering från Azure Database for PostgreSQL stöds för PostgreSQL version 10 och senare. Du kan också använda den här självstudien för att migrera från en Azure Database for PostgreSQL instans till en annan Azure Database for PostgreSQL instans eller citus-instans.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Kontrol lera [status för migrerings scenarier som stöds av Azure Database migration service](./resource-scenario-status.md) för kombinationer av migrering och version som stöds. 
* En befintlig [Azure Database for PostgreSQL](../postgresql/index.yml) version 10 och senare instans med **DVD-hyres-** databasen. 

    Observera också att mål Azure Database for PostgreSQL versionen måste vara lika med eller senare än den lokala PostgreSQL-versionen. Till exempel kan PostgreSQL 10 migrera till Azure Database for PostgreSQL 10 eller 11, men inte Azure Database for PostgreSQL 9,6.

* [Skapa en Azure Database for postgresql-server](../postgresql/quickstart-create-server-database-portal.md) eller [skapa en citus-Server (Azure Database for PostgreSQL-Scale Scale)](../postgresql/quickstart-create-hyperscale-portal.md) som mål databas servern för att migrera data till.
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modellen. Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

* Se till att reglerna för nätverks säkerhets gruppen (NSG) för ditt virtuella nätverk inte blockerar följande utgående kommunikations portar till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Skapa en [brand Väggs regel](../azure-sql/database/firewall-configure.md) på server nivå för Azure Database for PostgreSQL källa för att tillåta Azure Database migration service åtkomst till käll databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* Skapa en [brand Väggs regel](../azure-sql/database/firewall-configure.md) på server nivå för Azure Database for PostgreSQL Target för att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* [Aktivera logisk replikering](../postgresql/concepts-logical.md) i Azure dB för postgresql-källan. 
* Ange följande Server parametrar i Azure Database for PostgreSQL-instansen som används som källa:

  * max_replication_slots = [antal fack], rekommenderar inställningen till **tio platser**
  * max_wal_senders = [antalet samtidiga uppgifter] – parametern max_wal_senders anger antal samtidiga aktiviteter som kan köras, rekommenderad inställning är **10 uppgifter**

> [!NOTE]
> Ovanstående Server parametrar är statiska och kräver en omstart av Azure Database for PostgreSQL-instansen för att de ska börja gälla. Mer information om växling av Server parametrar finns i [konfigurera Azure Database for postgresql server parametrar](../postgresql/howto-configure-server-parameters-using-portal.md).

> [!IMPORTANT]
> Alla tabeller i den befintliga databasen behöver en primär nyckel för att säkerställa att ändringar kan synkroniseras till mål databasen.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

För att slutföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på databasen.

1. Använd pg_dump -s-kommandot för att skapa en schemadumpfil för en databas.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Om du till exempel vill skapa en schema dumpnings fil för **dvdrental** -databasen:

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Mer information om hur du använder verktyget pg_dump finns i exemplen i självstudien [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Skapa en tom databas i målmiljön, vilken är Azure Database for PostgreSQL.

    Information om hur du ansluter och skapar en databas finns i artikeln [skapa en Azure Database for postgresql-server i Azure Portal](../postgresql/quickstart-create-server-database-portal.md) eller [skapa en Azure Database for PostgreSQL-för-citus-server i Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md).

    > [!NOTE]
    > En instans av Azure Database for PostgreSQL-Scale (citus) har endast en databas: **citus**.

3. Importera schemat till måldatabasen som du skapade genom att återställa schemadumpfilen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Exempel:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Extrahera det externa nyckel skriptet genom att lägga till det på målet (Azure Database for PostgreSQL), i PgAdmin eller i psql, kör du följande skript.

   > [!IMPORTANT]
   > Sekundär nycklar i schemat leder till att den inledande inläsningen och den kontinuerliga synkroniseringen av migreringen Miss fungerar.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.

6. Kör skriptet nedan om du vill inaktivera utlösare i mål databasen.

   > [!IMPORTANT]
   > Utlösare (infoga eller uppdatera) i data upprätthåller data integriteten i målet före de data som replikeras från källan. Därför rekommenderar vi att du inaktiverar utlösare i alla tabeller **vid målet** under migreringen, och sedan aktiverar utlösarna igen när migreringen är klar.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. På skärmen **skapa migrerings tjänst** anger du ett namn, en prenumeration, en ny eller en befintlig resurs grupp och platsen för tjänsten.

4. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll PostgreSQL-servern och mål Azure Database for PostgreSQL instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Välj **Granska + skapa** för att skapa tjänsten.

   Skapandet av tjänster slutförs inom cirka 10 till 15 minuter.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. På skärmen **Azure Database migration Services** söker du efter namnet på Azure Database migration service-instansen som du har skapat, väljer instansen och väljer sedan + **nytt migreringsjobb**.

3. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **typ av käll Server** , väljer **postgresql**, i text rutan **mål server typ** , väljer du **Azure Database for PostgreSQL**.
    > [!NOTE]
    > Välj **postgresql** i **käll Server typen** även om käll servern är en **Azure Database for PostgreSQL** instans.  

4. I avsnittet **Välj typ av aktivitet** väljer du **migrering av data online**.

    ![Skapa Azure Database Migration Service-projekt](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

5. Välj **Spara**, anteckna kraven för att kunna använda Azure Database migration service migrera data och välj sedan **skapa och köra aktivitet**.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Lägg till käll information** anger du anslutnings information för källan postgresql-instansen.

    ![Skärmen Lägg till källinformation](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Du kan hitta information som "Server namn", "server port", "databas namn" osv. i **Azure Database for PostgreSQL** -portalen.

2. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **mål information** anger du anslutnings information för citus-servern (Target disscale), som är den företablerade instansen av citus () som schemat för **DVD-hyran** har distribuerats med hjälp av pg_dump.

    ![Skärmen Målinformation](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Du kan migrera från en Azure Database for PostgreSQL instans till en annan Azure Database for PostgreSQL instans av en server eller till en citus-Server (storskalig).

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Skärmen mappa till mål databaser](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Välj **Spara** och godkänn standardvärdena på skärmen **Inställningar för migrering** .

    ![Fönstret inställningar för migrering](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Skärmen migrerings översikt](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

Fönstret migrering av aktivitet visas och aktivitetens **status** bör uppdateras för att visa när **säkerhets kopiering pågår**. Följande fel kan uppstå när du uppgraderar från Azure DB för PostgreSQL 9,5 eller 9,6:

**Ett scenario rapporterade ett okänt fel. 28000: ingen pg_hba. conf-post för replikeringsanslutning från värden "40.121.141.121", användare "SR"**

Detta beror på att PostgreSQL inte har rätt behörighet för att skapa nödvändiga artefakter för logiska replikeringar. Om du vill aktivera nödvändiga behörigheter kan du göra följande:

1. Öppna inställningarna för "anslutnings säkerhet" för den Azure DB for PostgreSQL-server som du försöker migrera/uppgradera från.
2. Lägg till en ny brand Väggs regel med ett namn som slutar med "_replrule" och Lägg till IP-adressen från fel meddelandet i fälten Start-IP och slut-IP-adress. Exempel på ovanstående fel –
> Brand Väggs regelns namn = sr_replrule; Start-IP = 40.121.141.121; Slut-IP = 40.121.141.121

3. Klicka på Spara och låt ändringen slutföras. 
4. Gör om DMS-aktivitet. 

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

     ![Övervaka migreringsprocessen](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. När migreringen är klar väljer du en annan databas under **databas namn** för att komma till migreringsprocessen för **fullständig data inläsning** och **stegvis data synkronisering** .

   > [!NOTE]
   > **Fullständig data inläsning** visar statusen för den initiala inläsningen och den **stegvisa datasynkroniseringen** visar status för registrering av ändrings data (CDC).

     ![Fullständig information om data inläsning](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Information om stegvis data synkronisering](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Vänta tills räknaren **väntande ändringar** visar **0** om du vill se till att alla inkommande transaktioner till käll databasen har stoppats markerar du kryss rutan **Bekräfta** och väljer sedan **Använd**.

    ![Slutför start punkt-skärmen](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. När status för migreringen av **databasen visas,** [återskapar du sekvenser](https://wiki.postgresql.org/wiki/Fixing_Sequences) (om tillämpligt) och ansluter dina program till den nya mål instansen av Azure Database for PostgreSQL.
 
> [!NOTE]
> Azure Database Migration Service kan användas för att utföra större versions uppgraderingar med minskad stillestånds tid i Azure Database for PostgreSQL-enskild server. Först konfigurerar du en mål databas med önskad högre PostgreSQL-version, nätverks inställningar och-parametrar. Sedan kan du starta migreringen till mål databaserna med hjälp av proceduren som beskrivs ovan. När du har start punkt till mål databas servern kan du uppdatera din program anslutnings sträng så att den pekar mot mål databas servern. 

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](./dms-overview.md) (Vad är Azure Database Migration Service?).
* Mer information om Azure Database for PostgreSQL finns i artikeln [Vad är Azure Database for PostgreSQL?](../postgresql/overview.md).
