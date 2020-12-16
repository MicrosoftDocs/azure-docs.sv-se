---
title: 'Självstudie: Migrera PostgreSQL till Azure Database for PostgreSQL online via Azure CLI'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från PostgreSQL lokalt till Azure Database for PostgreSQL genom att använda Azure Database Migration Service via CLI.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: fb303054a3026a305831309413c51c061a68c5d6
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608059"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>Självstudie: Migrera PostgreSQL till Azure DB för PostgreSQL online med DMS via Azure CLI

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal PostgreSQL-instans till [Azure Database for PostgreSQL](../postgresql/index.yml) med minimal stillestånds tid. Med andra ord kan migreringen uppnås med minimal stillestånds tid för programmet. I den här självstudien migrerar du exempel databasen för **DVD-hyra** från en lokal instans av postgresql 9,6 till Azure Database for PostgreSQL med hjälp av aktiviteten online-migrering i Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempel schema med hjälp av pg_dump-verktyget.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium. Vi krypterar disk för att förhindra stöld av data under migreringen.

> [!IMPORTANT]
> För en optimal migrering rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som mål databasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Ladda ned och installera [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 eller 10. PostgreSQL-källserverversionen måste vara 9.5.11, 9.6.7, 10 eller senare. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](../postgresql/concepts-supported-versions.md).

    Observera också att mål Azure Database for PostgreSQL versionen måste vara lika med eller senare än den lokala PostgreSQL-versionen. Till exempel kan PostgreSQL 9,6 endast migrera till Azure Database for PostgreSQL 9,6, 10 eller 11, men inte Azure Database for PostgreSQL 9,5.

* [Skapa en instans i Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) eller [skapa en citus-Server (Azure Database for PostgreSQL-Scale Scale)](../postgresql/quickstart-create-hyperscale-portal.md).
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.

* Se till att dina regler för nätverks säkerhets gruppen (NSG) för virtuella nätverk inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service att få åtkomst till PostgreSQL-servern, som standard är TCP-port 5432.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en [brand Väggs regel](../azure-sql/database/firewall-configure.md) på server nivå för Azure Database for PostgreSQL för att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* Det finns två metoder för att anropa CLI:

  * I det övre högra hörnet av Azure Portal väljer du knappen Cloud Shell:

       ![Cloud Shell-knappen i Azure Portal](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Installera och kör CLI lokalt. CLI 2.0 är kommandoradsverktyget för att hantera Azure-resurser.

       Hämta CLI enligt instruktionerna i artikeln [Installera Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest). Artikeln visas även de plattformar som har stöd för CLI 2.0.

       Om du vill konfigurera Windows-undersystem för Linux (WSL), följer du anvisningarna i [Installationsguide för Windows 10](/windows/wsl/install-win10)

* Aktivera logisk replikering i filen postgresql.config och ange följande parametrar:

  * wal_level = **logical**
  * max_replication_slots = [antal fack], rekommenderar att du ställer in på **fem platser**
  * max_wal_senders = [antalet samtidiga uppgifter] – parametern max_wal_senders anger antal samtidiga aktiviteter som kan köras, rekommenderad inställning är **10 uppgifter**

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

För att slutföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på databasen.

1. Använd pg_dump -s-kommandot för att skapa en schemadumpfil för en databas. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Till exempel, för att dumpa en schemafil dvdrental databas:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Mer information om hur du använder verktyget pg_dump finns i exemplen i självstudien [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Skapa en tom databas i målmiljön, vilken är Azure Database for PostgreSQL.

    Information om hur du ansluter och skapar en databas finns i artikeln [skapa en Azure Database for postgresql-server i Azure Portal](../postgresql/quickstart-create-server-database-portal.md) eller [skapa en Azure Database for PostgreSQL-för-citus-server i Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md).

3. Importera schemat till måldatabasen som du skapade genom att återställa schemadumpfilen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Exempel:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. Kör följande skript i PgAdmin eller i psql för att extrahera skriptet för att släppa sekundärnycklar och skriptet för att lägga till sekundärnycklar vid målet (Azure Database for PostgreSQL).
  
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

    Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.

5. Utlösare i dina data (infoga eller uppdatera utlösare) tillämpar den dataintegritet i målet före replikerade data från källan. Vi rekommenderar att du inaktiverar utlösare i alla tabeller **vid målet** under migreringen och sedan aktiverar utlösarna igen när migreringen är klar.

    Om du vill inaktivera utlösare i måldatabasen använder du följande kommando:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Om det finns en uppräknings data typ i alla tabeller, rekommenderar vi att du uppdaterar den tillfälligt till data typen "Character varierande" i mål tabellen. När replikering av data är klar kan du återställa datatypen till ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Etablera en instans av DMS med hjälp av CLI

1. Installera tillägget dms-synkronisering:
   * Logga in på Azure genom att köra följande kommando:
       ```azurecli
       az login
       ```

   * När du uppmanas, öppna en webbläsare och ange en kod för att autentisera din enhet. Följ anvisningarna som anges.
   * Lägg till dms-tillägget:
       * Om du vill visa tillgängliga tillägg, kör du följande kommando:

           ```azurecli
           az extension list-available –otable
           ```

       * Kör följande kommando för att installera tillägget:

           ```azurecli
           az extension add –n dms-preview
           ```

   * För att verifiera att du har dms-tillägget installerat korrekt, kör du följande kommando:

       ```azurecli
       az extension list -otable
       ```
       Du bör se följande utdata:

       ```output
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

      > [!IMPORTANT]
      > Se till att tilläggs versionen är över 0.11.0.

   * Visa alla kommandon som stöds i DMS genom att köra:

       ```azurecli
       az dms -h
       ```

   * Om du har flera Azure-prenumerationer kör du följande kommando för att välja den prenumeration du vill använda för att etablera en instans av DMS-tjänsten.

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Etablera en instans av DMS genom att köra följande kommando:

   ```azurecli
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Till exempel skapar följande kommando en tjänst i:

   * Plats: USA, östra 2
   * Prenumeration: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Resursgruppsnamn: PostgresDemo
   * DMS-tjänstnamn: PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   Det tar cirka 10–12 minuter att skapa en instans av DMS-tjänsten.

3. För att identifiera IP-adressen för DMS-agenten så att du kan lägga till den i filen Postgres pg_hba.conf, kör du följande kommando:

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Exempel:

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Du bör få ett resultat liknande följande adress: 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. Lägg till IP-adressen för DMS-agenten i Postgres pg_hba.conf-filen.

    * Anteckna DMS IP-adressen när du har slutfört etablering i DMS.
    * Lägg till IP-adressen till pg_hba.conf-filen på källan, på likande sätt som följande post:

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. Skapa sedan ett PostgreSQL-migreringsprojekt genom att köra följande kommando:
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Till exempel skapar följande kommando ett projekt med dessa parametrar:

   * Plats: USA, västra centrala
   * Resursgruppsnamn: PostgresDemo
   * Tjänstnamn: PostgresCLI
   * Projektnamn: PGMigration
   * Källplattform: PostgreSQL
   * Målplattform: AzureDbForPostgreSql

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Skapa en uppgift för migrering av PostgreSQL med följande steg.

    Det här steget inkluderar användning av käll-IP, användar-ID och lösenord, mål-IP-, användar-ID, lösenord och aktivitetstyp för att upprätta en anslutning.

   * Om du vill se en fullständig lista över alternativ, kör du kommandot:

       ```azurecli
       az dms project task create -h
       ```

       För både käll- och målanslutning refererar indataparametern till en json-fil som har objektlistan.

       Formatet för anslutning till JSON-objekt för PostgreSQL-anslutningar.
        
       ```json
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Det finns också en JSON-fil för databas alternativ som visar JSON-objekten. För PostgreSQL visas formatet för JSON-objekt för databasalternativet nedan:

       ```json
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Skapa en json-fil i anteckningar, kopiera följande kommandon och klistra in dem i filen och spara filen på C:\DMS\source.json.

        ```json
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Skapa en annan fil med namnet target.json och spara som C:\DMS\target.json. Inkludera följande kommandon:

       ```json
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Skapa en json-fil för databasalternativ som visar inventeringen som den databas som ska migreras:

       ```json
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Kör följande kommando, som tar in källan, målet och DB-alternativet för json-filer.

       ```azurecli
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     Du har nu har skickat en migreringsuppgift.

7. Om du vill visa förloppet för uppgiften, kör du följande kommando:

   ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   ELLER

    ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. Du kan också fråga efter migrationState från expanderade utdata:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

Det finns flera parametrar som visar förloppet för migrering i utdatafilen. Använd udatafilen nedan som exempel:

  ```output
    "output": [                                 // Database Level
          {
            "appliedChanges": 0,         // Total incremental sync applied after full load
            "cdcDeleteCounter": 0        // Total delete operation  applied after full load
            "cdcInsertCounter": 0,       // Total insert operation applied after full load
            "cdcUpdateCounter": 0,       // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0,     //Number of tables that contain migration error
            "fullLoadLoadingTables": 0,     //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,      //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,           //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",    //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                        // Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",    //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                     //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,            //Volume in Bytes in full load
            "id": "or|inventory|public|actor",
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                       //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                    //Total sync changes that applied after full load
          },
          {                                            //Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                                      // DMS migration task state
        "state": "Running",    //Migration task state – Running means it is still listening to any changes that might come in
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
  ```

## <a name="cutover-migration-task"></a>Snabb migreringsuppgift

Databasen är klar för snabb migrering när fullständig inläsning är klar. Beroende på hur upptagen källservern är med nya transaktioner som kommer, kan DMS-aktiviteten fortfarande tillämpa ändringar när hela inläsningen är klar.

För att säkerställa att alla data har samlats in, verifiera radantal mellan käll- och måldatabaser. Du kan t.ex. använda följande kommando:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0, //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0, //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112, //full load for table 2
```

1. Utför den snabba migreringen för databasen med hjälp av följande kommando:

    ```azurecli
    az dms project task cutover -h
    ```

    Exempel:

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --object-name Inventory
    ```

2. Du kan övervaka förloppet för den snabba migreringen med följande kommando:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```
3. När status för migreringen av **databasen visas,** [återskapar du sekvenser](https://wiki.postgresql.org/wiki/Fixing_Sequences) (om tillämpligt) och ansluter dina program till den nya mål instansen av Azure Database for PostgreSQL.

## <a name="service-project-task-cleanup"></a>Rensa tjänst, projekt, aktivitet

Om du vill avbryta eller ta bort en DMS-aktivitet, ett projekt eller en tjänst, utför du avbrottet i följande ordning:

* Avbryt den pågående uppgiften
* Ta bort uppgiften
* Ta bort projektet
* Ta bort DMS-tjänst

1. Om du vill avbryta en pågående aktivitet använder du följande kommando:

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Om du vill ta bort en pågående aktivitet använder du följande kommando:
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Om du vill avbryta ett pågående projekt använder du följande kommando:
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Om du vill ta bort ett pågående projekt använder du följande kommando:
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Om du vill ta bort en DMS-tjänst använder du följande kommando:

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](./dms-overview.md) (Vad är Azure Database Migration Service?).
* Mer information om Azure Database for PostgreSQL finns i artikeln [Vad är Azure Database for PostgreSQL?](../postgresql/overview.md).