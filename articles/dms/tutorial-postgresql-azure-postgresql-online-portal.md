---
title: 'Självstudie: Migrera PostgreSQL till Azure DB för PostgreSQL online via Azure Portal'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från PostgreSQL lokalt till Azure Database for PostgreSQL genom att använda Azure Database Migration Service via Azure Portal.
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
ms.openlocfilehash: d28c45b2d0fc1a123f44020f42c4d2c59c593cb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709928"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Självstudie: Migrera PostgreSQL till Azure DB för PostgreSQL online med DMS via Azure Portal

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal PostgreSQL-instans för att [Azure Database for PostgreSQL](../postgresql/index.yml) med minimal stillestånds tid för programmet. I den här självstudien migrerar du exempel databasen för **DVD-hyra** från en lokal instans av postgresql 9,6 till Azure Database for PostgreSQL med hjälp av aktiviteten online-migrering i Azure Database migration service.

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

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Hämta och installera [postgresql community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 eller 10. Käll PostgreSQL-serverns version måste vara 9,4, 9,5, 9,6, 10 eller 11. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](../postgresql/concepts-supported-versions.md).

    Observera också att mål Azure Database for PostgreSQL versionen måste vara lika med eller senare än den lokala PostgreSQL-versionen. Till exempel kan PostgreSQL 9,6 migrera till Azure Database for PostgreSQL 9,6, 10 eller 11, men inte Azure Database for PostgreSQL 9,5.

* [Skapa en Azure Database for postgresql-server](../postgresql/quickstart-create-server-database-portal.md) eller [skapa en citus-Server (Azure Database for PostgreSQL-Scale Scale)](../postgresql/quickstart-create-hyperscale-portal.md).
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.

* Se till att reglerna för nätverks säkerhets gruppen (NSG) för ditt virtuella nätverk inte blockerar den utgående porten 443 för ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service att få åtkomst till PostgreSQL-servern, som standard är TCP-port 5432.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en [brand Väggs regel](../postgresql/concepts-firewall-rules.md) på server nivå för Azure Database for PostgreSQL för att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* Aktivera logisk replikering i filen postgresql.config och ange följande parametrar:

  * wal_level = **logical**
  * max_replication_slots = [antal fack], rekommenderar att du ställer in på **fem platser**
  * max_wal_senders = [antalet samtidiga uppgifter] – parametern max_wal_senders anger antal samtidiga aktiviteter som kan köras, rekommenderad inställning är **10 uppgifter**

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
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
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
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Extrahera det externa nyckel skriptet genom att lägga till det på målet (Azure Database for PostgreSQL), i PgAdmin eller i psql, kör du följande skript.

   > [!IMPORTANT]
   > Sekundär nycklar i schemat leder till att den inledande inläsningen och den kontinuerliga synkroniseringen av migreringen Miss fungerar.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
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

   ![Visa portalprenumerationer](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. På skärmen **skapa migrerings tjänst** anger du ett namn, en prenumeration, en ny eller en befintlig resurs grupp och platsen för tjänsten.

4. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll PostgreSQL-servern och mål Azure Database for PostgreSQL instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Välj **Granska + skapa** för att skapa tjänsten.

   Skapandet av tjänster slutförs inom cirka 10 till 15 minuter.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. På skärmen **Azure Database migration Services** söker du efter namnet på Azure Database migration service-instansen som du har skapat, väljer instansen och väljer sedan + **nytt migreringsjobb**.

3. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **typ av käll Server** , väljer **postgresql**, i text rutan **mål server typ** , väljer du **Azure Database for PostgreSQL**.

4. I avsnittet **Välj typ av aktivitet** väljer du **migrering av data online**.

    ![Skapa Azure Database Migration Service-projekt](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

5. Välj **Spara**, anteckna kraven för att kunna använda Azure Database migration service migrera data och välj sedan **skapa och köra aktivitet**.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Lägg till käll information** anger du anslutnings information för källan postgresql-instansen.

    ![Skärmen Lägg till källinformation](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **mål information** anger du anslutnings information för citus-servern (Target disscale), som är den företablerade instansen av citus () som schemat för **DVD-hyran** har distribuerats med hjälp av pg_dump.

    ![Skärmen Målinformation](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om mål databasen innehåller samma databas namn som käll databasen Azure Database Migration Service väljer mål databasen som standard.

    ![Skärmen mappa till mål databaser](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Välj **Spara** och godkänn standardvärdena på skärmen **Inställningar för migrering** .

    ![Fönstret inställningar för migrering](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Skärmen migrerings översikt](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Fönstret migrering av aktivitet visas och aktivitetens **status** bör uppdateras för att visa när **säkerhets kopiering pågår**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

     ![Övervaka migreringsprocessen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. När migreringen är klar väljer du en annan databas under **databas namn** för att komma till migreringsprocessen för **fullständig data inläsning** och **stegvis data synkronisering** .

   > [!NOTE]
   > **Fullständig data inläsning** visar statusen för den initiala inläsningen och den **stegvisa datasynkroniseringen** visar status för registrering av ändrings data (CDC).

     ![Fullständig information om data inläsning](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Information om stegvis data synkronisering](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Vänta tills räknaren **väntande ändringar** visar **0** om du vill se till att alla inkommande transaktioner till käll databasen har stoppats markerar du kryss rutan **Bekräfta** och väljer sedan **Använd**.

    ![Slutför start punkt-skärmen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. När status för migreringen av **databasen visas,** [återskapar du sekvenser](https://wiki.postgresql.org/wiki/Fixing_Sequences) (om tillämpligt) och ansluter dina program till den nya mål instansen av Azure Database for PostgreSQL.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](./dms-overview.md) (Vad är Azure Database Migration Service?).
* Mer information om Azure Database for PostgreSQL finns i artikeln [Vad är Azure Database for PostgreSQL?](../postgresql/overview.md).