---
title: Konfigurera data-i replikering – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar Datareplikering i Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 67e4da13d6954342b9979eb57a35c812cb63bb3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665131"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurera Datareplikering i Azure Database for MariaDB

I den här artikeln beskrivs hur du konfigurerar [datareplikering](concepts-data-in-replication.md) i Azure Database for MariaDB genom att konfigurera käll-och replik servrarna. Den här artikeln förutsätter att du har tidigare erfarenhet av MariaDB-servrar och-databaser.

För att skapa en replik i Azure Database for MariaDB-tjänsten synkroniserar [datareplikering](concepts-data-in-replication.md) data från en lokal MariaDB-server lokalt, i virtuella datorer (VM) eller i moln databas tjänster. Datareplikering baseras på positionsbaserad replikering med en binär loggfil (binlog) som är inbyggd i MariaDB. Mer information om BinLog-replikering finns i [Översikt över BinLog-replikering](https://mariadb.com/kb/en/library/replication-overview/).

Granska [begränsningarna och kraven](concepts-data-in-replication.md#limitations-and-considerations) för datareplikering innan du utför stegen i den här artikeln.

> [!NOTE]
> Om käll servern är version 10,2 eller senare rekommenderar vi att du konfigurerar Datareplikering med hjälp av [globalt transaktions-ID](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Skapa en MariaDB-server som ska användas som en replik

1. Skapa en ny Azure Database for MariaDB Server (till exempel replica.mariadb.database.azure.com). Servern är replik servern i Datareplikering.

    Information om hur du skapar servrar finns i [skapa en Azure Database for MariaDB-server med hjälp av Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Du måste skapa Azure Database for MariaDB-servern på Generell användning eller minnesoptimerade pris nivåer.

2. Skapa identiska användar konton och motsvarande privilegier.

    Användar konton replikeras inte från käll servern till replik servern. Om du vill ge användare åtkomst till replik servern måste du manuellt skapa alla konton och motsvarande behörigheter på den nyskapade Azure Database for MariaDB-servern.

3. Lägg till käll serverns IP-adress i replikens brand Väggs regler. 

   Uppdatera brandväggsregler med hjälp av [Azure-portalen](howto-manage-firewall-portal.md) eller [Azure CLI](howto-manage-firewall-cli.md).

## <a name="configure-the-source-server"></a>Konfigurera käll servern

Följande steg förbereder och konfigurerar den MariaDB-server som finns lokalt, i en virtuell dator eller i en moln databas tjänst för Datareplikering. MariaDB-servern är källan i Datareplikering.

1. Granska kraven för den [primära servern](concepts-data-in-replication.md#requirements) innan du fortsätter. 

2. Se till att käll servern tillåter både inkommande och utgående trafik på port 3306 och att käll servern har en **offentlig IP-adress**, att DNS är offentligt tillgänglig eller har ett fullständigt kvalificerat domän namn (FQDN). 

   Testa anslutningen till käll servern genom att försöka ansluta från ett verktyg som till exempel MySQL-kommandoraden på en annan dator eller från den [Azure Cloud Shell](../cloud-shell/overview.md) som är tillgänglig i Azure Portal.

   Om din organisation har strikta säkerhets principer och inte tillåter alla IP-adresser på käll servern att möjliggöra kommunikation från Azure till käll servern kan du använda kommandot nedan för att fastställa IP-adressen för din Azure Database for MariaDB-Server.

   1. Logga in på Azure Database for MariaDB med ett verktyg som MySQL-kommandoraden.
   2. Kör frågan nedan.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Nedan visas några exempel på utdata:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Avsluta från kommando raden MySQL.
   4. Kör följande i Ping-verktyget för att hämta IP-adressen.

      ```bash
      ping <output of step 2b>
      ```

      Exempel:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Konfigurera käll serverns brand Väggs regler för att inkludera föregående stegs IP-adress på port 3306.

   > [!NOTE]
   > Den här IP-adressen kan ändras på grund av underhåll/distributions åtgärder. Den här anslutnings metoden är bara för kunder som inte har råd att tillåta alla IP-adresser på 3306-porten.

3. Aktivera binär loggning.

    Ange följande kommando för att se om binär loggning är aktiverat på den primära:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) returnerar värdet `ON` aktive ras binär loggning på servern.

   Om `log_bin` returnerar värdet `OFF` redigerar du filen **My. cnf** så att den `log_bin=ON` aktiverar binär loggning. Starta om servern så att ändringen börjar gälla.

4. Konfigurera käll Server inställningar.

    Datareplikering kräver att parametern `lower_case_table_names` är konsekvent mellan käll-och replik servrarna. `lower_case_table_names`Parametern anges som `1` standard i Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Skapa en ny replikeringsprincip och konfigurera behörigheter.

   Skapa ett användar konto på käll servern som har kon figurer ATS med behörighet för replikering. Du kan skapa ett konto med hjälp av SQL-kommandon eller MySQL Workbench. Om du planerar att replikera med SSL måste du ange detta när du skapar användar kontot.

   Information om hur du lägger till användar konton på käll servern finns i [MariaDB-dokumentationen](https://mariadb.com/kb/en/library/create-user/).

   Genom att använda följande kommandon kan den nya replikeringsprincipen komma åt källan från vilken dator som helst, inte bara den dator som är värd för själva källan. För den här åtkomsten anger du **syncuser \@ %** i kommandot för att skapa en användare.

   Mer information om MariaDB-dokumentationen finns i [Ange konto namn](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL-kommando**

   - Replikering med SSL

       Om du vill kräva SSL för alla användar anslutningar anger du följande kommando för att skapa en användare:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikering utan SSL

       Om SSL inte krävs för alla anslutningar, anger du följande kommando för att skapa en användare:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Om du vill skapa en replikeringsprincip i MySQL Workbench väljer du **användare och behörigheter** i fönstret **hantering** . Välj sedan **Lägg till konto**.

   ![Användare och behörigheter](./media/howto-data-in-replication/users_privileges.png)

   Ange ett användar namn i fältet **inloggnings namn** .

   ![Synkronisera användare](./media/howto-data-in-replication/syncuser.png)

   Välj panelen **administrativa roller** och välj **replikering slav** i listan över **globala privilegier**. Välj **Använd** för att skapa en replikeringsprincip.

   ![Replikering slav](./media/howto-data-in-replication/replicationslave.png)

6. Ställ in käll servern på skrivskyddat läge.

   Innan du dumpar en databas måste servern placeras i skrivskyddat läge. I skrivskyddat läge kan källan inte bearbeta några Skriv transaktioner. För att undvika affärs påverkan kan du schemalägga det skrivskyddade fönstret under låg belastnings tid.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Hämta den aktuella binära logg filens namn och offset.

   Du kan ta reda på den aktuella binära logg filens namn och förskjutning genom att köra kommandot [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .

   ```sql
   show master status;
   ```

   Resultaten bör likna följande tabell:

   ![Huvud status resultat](./media/howto-data-in-replication/masterstatus.png)

   Anteckna namnet på den binära filen, eftersom det kommer att användas i senare steg.

8. Hämta GTID position (valfritt, krävs för replikering med GTID).

   Kör funktionen [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) för att hämta GTID position för motsvarande BinLog-filnamn och offset.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>Dumpa och Återställ käll servern

1. Dumpa alla databaser från käll servern.

   Använd mysqldump för att dumpa alla databaser från käll servern. Det är inte nödvändigt att dumpa MySQL-biblioteket och test biblioteket.

    Mer information finns i [dumpa och Återställ](howto-migrate-dump-restore.md).

2. Ange att käll servern ska läsa/skriva-läge.

   När databasen har dump ATS ändrar du tillbaka käll MariaDB-servern till läsa/Skriv-läge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställ dumpfilen till den nya servern.

   Återställ dumpfilen till servern som skapats i Azure Database for MariaDB-tjänsten. Se [dump & Restore](howto-migrate-dump-restore.md) for How to Restore dump File to a MariaDB Server.

   Om dumpfilen är stor laddar du upp den till en virtuell dator i Azure inom samma region som replik servern. Återställ den till Azure Database for MariaDB-servern från den virtuella datorn.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>Länka käll-och replik servrar för att starta Datareplikering

1. Ange käll servern.

   Alla Datareplikering funktioner utförs med lagrade procedurer. Du hittar alla procedurer på [datareplikering lagrade procedurer](reference-stored-procedures.md). Lagrade procedurer kan köras i MySQL-gränssnittet eller MySQL Workbench.

   Logga in på mål replik servern i Azure DB for MariaDB-tjänsten för att länka två servrar och starta replikering. Ange sedan den externa instansen som käll Server genom att använda den `mysql.az_replication_change_master` eller `mysql.az_replication_change_master_with_gtid` lagrade proceduren på Azure dB för MariaDB-servern.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   eller

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: värd namnet för käll servern
   - master_user: användar namn för käll servern
   - master_password: lösen ordet för käll servern
   - master_log_file: det binära logg fils namnet körs inte `show master status`
   - master_log_pos: binär logg position körs `show master status`
   - master_gtid_pos: GTID position körs `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: certifikat utfärdarens kontext. Om du inte använder SSL skickar du en tom sträng. *


    * Vi rekommenderar att du skickar parametern master_ssl_ca som en variabel. Mer information finns i följande exempel.

   **Exempel**

   - Replikering med SSL

       Skapa variabeln `@cert` genom att köra följande kommandon:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikering med SSL konfigureras mellan en käll server som finns i domänen companya.com och en replik server som är värd för Azure Database for MariaDB. Den här lagrade proceduren körs på repliken.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - Replikering utan SSL

       Replikering utan SSL konfigureras mellan en käll server som finns i domänen companya.com och en replik server som är värd för Azure Database for MariaDB. Den här lagrade proceduren körs på repliken.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Starta replikering.

   Anropa den `mysql.az_replication_start` lagrade proceduren för att starta replikeringen.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Kontrol lera replikeringsstatus.

   Anropa [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) kommandot på replik servern för att visa replikeringsstatus.

   ```sql
   show slave status;
   ```

   Om `Slave_IO_Running` och `Slave_SQL_Running` är i läget `yes` och värdet för `Seconds_Behind_Master` är `0` , fungerar replikeringen. `Seconds_Behind_Master` anger hur sen repliken är. Om värdet inte `0` anges, bearbetar repliken uppdateringar.

4. Uppdatera motsvarande servervariabler för att göra datareplikering säkrare (krävs endast för replikering utan GTID).

    På grund av en begränsning för ursprunglig replikering i MariaDB måste du ange  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) och [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variabler för replikering utan GTID-scenariot.

    Kontrol lera replik serverns `sync_master_info` och `sync_relay_log_info` variablerna för att kontrol lera att datareplikeringen är stabil och ange variablerna `1` .

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

### <a name="stop-replication"></a>Stoppa replikering

Använd följande lagrade procedur för att stoppa replikeringen mellan käll-och replik servern:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Ta bort replikeringsrelationen

Använd följande lagrade procedur om du vill ta bort relationen mellan käll-och replik servern:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Hoppa över replikeringsfel

Använd följande lagrade procedur om du vill hoppa över ett replikeringsfel och tillåta replikering:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [datareplikering](concepts-data-in-replication.md) för Azure Database for MariaDB.
