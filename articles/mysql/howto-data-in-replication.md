---
title: Konfigurera data-i replikering – Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar Datareplikering för Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 3c12068c6a2c75c7be8b5572b901a714d397b2ca
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209938"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Så här konfigurerar du Azure Database for MySQL Datareplikering

I den här artikeln beskrivs hur du konfigurerar [datareplikering](concepts-data-in-replication.md) i Azure Database for MySQL genom att konfigurera käll-och replik servrarna. Den här artikeln förutsätter att du har tidigare erfarenhet av MySQL-servrar och-databaser.

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.
>

För att skapa en replik i Azure Database for MySQL-tjänsten synkroniserar [datareplikering](concepts-data-in-replication.md)  data från en lokal MySQL-server lokalt, i virtuella datorer (VM) eller i moln databas tjänster. Datareplikering baseras på den binära loggen (BinLog) fil positions-eller gtid-baserad replikering som är inbyggd i MySQL. Mer information om BinLog-replikering finns i [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Granska [begränsningarna och kraven](concepts-data-in-replication.md#limitations-and-considerations) för datareplikering innan du utför stegen i den här artikeln.

## <a name="1-create-a-azure-database-for-mysql-single-server-to-be-used-as-replica"></a>1. skapa en Azure Database for MySQL enskild server som ska användas som replik

1. Skapa en ny Azure Database for MySQL enskild server (t. ex. "replica.mysql.database.azure.com"). Se [skapa en Azure Database for MySQL-server genom att använda Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) för att skapa servern. Den här servern är replik servern i Datareplikering.

   > [!IMPORTANT]
   > Azure Database for MySQL-servern måste skapas i Generell användning eller minnesoptimerade pris nivåer som datareplikering stöds bara i dessa nivåer.

2. Skapa samma användar konton och motsvarande privilegier

   Användar konton replikeras inte från käll servern till replik servern. Om du planerar att ge användare åtkomst till replik servern måste du skapa alla konton och motsvarande behörigheter manuellt på den nyligen skapade Azure Database for MySQL servern.

3. Lägg till käll serverns IP-adress i replikens brand Väggs regler.

   Uppdatera brandväggsregler med hjälp av [Azure-portalen](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).
   
4. **Valfritt** – om du vill använda [gtid-baserad replikering](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html) från käll servern till Azure Database for MySQL replik Server måste du aktivera följande Server parametrar på Azure Database for MySQL-servern som visas i Portal bilden nedan

   :::image type="content" source="./media/howto-data-in-replication/enable-gtid.png" alt-text="Aktivera gtid på Azure Database for MySQL Server":::

## <a name="2-configure-the-source-mysql-server"></a>2. Konfigurera käll-MySQL-servern

Följande steg förbereder och konfigurerar den MySQL-server som finns lokalt, i en virtuell dator eller databas tjänst som tillhandahålls av andra moln leverantörer för Datareplikering. Den här servern är "källa" i replikering av data.

1. Granska [kraven för käll servern](concepts-data-in-replication.md#requirements) innan du fortsätter.

2. Kontrol lera att käll servern tillåter både inkommande och utgående trafik på port 3306 och att den har en **offentlig IP-adress**, att DNS är offentligt tillgänglig eller har ett fullständigt kvalificerat domän namn (FQDN).

   Testa anslutningen till käll servern genom att försöka ansluta från ett verktyg som till exempel MySQL-kommandoraden på en annan dator eller från den [Azure Cloud Shell](../cloud-shell/overview.md) som är tillgänglig i Azure Portal.

   Om din organisation har strikta säkerhets principer och inte tillåter alla IP-adresser på käll servern att möjliggöra kommunikation från Azure till käll servern kan du använda kommandot nedan för att fastställa IP-adressen för MySQL-servern.

   1. Logga in på din Azure Database for MySQL med ett verktyg som till exempel kommando raden MySQL.

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
   4. Kör följande kommando i Ping-verktyget för att hämta IP-adressen.

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
  
3. Aktivera binär loggning

   Kontrol lera om binär loggning har Aktiver ATS på källan genom att köra följande kommando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) returneras med värdet "på" aktive ras binär loggning på servern.
   
    Om `log_bin` returneras med värdet "off" och käll servern körs lokalt eller på virtuella datorer där du kan komma åt konfigurations filen (My. cnf), kan du följa stegen nedan:
   1. Leta upp MySQL-konfigurationsfilen (My. cnf) på käll servern. Till exempel:/etc/My.cnf
   2. Öppna konfigurations filen för att redigera den och hitta **mysqld** -avsnittet i filen.
   3.  Lägg till följande rad i avsnittet mysqld
   
       ```bash
       log-bin=mysql-bin.log
       ```
   4. Starta om MySQL-källdomänkontrollanten för att ändringarna ska börja gälla.
   5. När servern har startats om kontrollerar du att binär loggning har Aktiver ATS genom att köra samma fråga som tidigare:
   
      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```
   
4. Käll Server inställningar

   Datareplikering kräver `lower_case_table_names` att parametern är konsekvent mellan käll-och replik servrarna. Den här parametern är 1 som standard i Azure Database for MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```
   **Valfritt** – om du vill använda [gtid-baserad replikering](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)måste du kontrol lera om gtid har Aktiver ATS på käll servern. Du kan köra följande kommando mot din käll MySQL-server för att se om gtid läge är aktiverat.
   
   ```sql
   show variables like 'gtid_mode';
   ```
   >[!IMPORTANT]
   > Alla servrar har gtid_mode angetts till standardvärdet. Du behöver inte aktivera gtid på käll-MySQL-servern specifikt för att konfigurera datareplikering. Om gtid redan har Aktiver ATS på käll servern kan du välja att använda gtid-baserad replikering för att konfigurera datareplikering för Azure Database for MySQL en enda server. Du kan använda filbaserad replikering för att konfigurera datareplikering för alla servrar oavsett gtid läges konfiguration på käll servern.


5. Skapa en ny roll för replikering och konfigurera behörighet

   Skapa ett användar konto på käll servern som har kon figurer ATS med behörighet för replikering. Detta kan göras via SQL-kommandon eller ett verktyg som MySQL Workbench. Överväg om du planerar att replikera med SSL eftersom detta måste anges när du skapar användaren. I MySQL-dokumentationen hittar du information om hur du [lägger till användar konton](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) på käll servern.

   I följande kommandon kan den nya replikeringsprincipen som skapas komma åt källan från vilken dator som helst, inte bara den dator som är värd för själva källan. Detta görs genom att ange "syncuser@%" i kommandot CREATE User. Mer information om hur du [anger konto namn](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)finns i MySQL-dokumentationen.

   **SQL-kommando**

   *Replikering med SSL*

   Om du vill kräva SSL för alla användar anslutningar använder du följande kommando för att skapa en användare:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikering utan SSL*

   Om SSL inte krävs för alla anslutningar, använder du följande kommando för att skapa en användare:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Om du vill skapa en replikeringsprincip i MySQL Workbench öppnar du panelen **användare och privilegier** från **hanterings** panelen och väljer sedan **Lägg till konto**.

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Användare och behörigheter":::

   Skriv användar namnet i fältet **inloggnings namn** .

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Synkronisera användare":::

   Välj panelen **administrativa roller** och välj sedan **Replikera slavar** i listan med **globala privilegier**. Välj sedan **Använd** för att skapa en replikeringsprincip.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replikering slav":::

6. Ställ in käll servern på skrivskyddat läge

   Innan du börjar dumpa databasen måste servern placeras i skrivskyddat läge. I skrivskyddat läge kan källan inte bearbeta några Skriv transaktioner. Utvärdera effekten för ditt företag och Schemalägg det skrivskyddade fönstret vid låg belastnings tid om det behövs.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Hämta binär logg fils namn och förskjutning

   Kör [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) kommandot för att fastställa den aktuella binära logg filens namn och offset.

   ```sql
    show master status;
   ```
   Resultatet bör se ut ungefär så här. Glöm inte att anteckna det binära fil namnet, eftersom det används i senare steg.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Huvud status resultat":::
   

## <a name="3-dump-and-restore-source-server"></a>3. dumpa och Återställ käll Server

1. Bestäm vilka databaser och tabeller du vill replikera till Azure Database for MySQL och utför dumpen från käll servern.

    Du kan använda mysqldump för att dumpa databaser från din huvud server. Mer information finns i [dumpa & Restore](concepts-migrate-dump-restore.md). Det behövs ingen dumpning av MySQL-biblioteket och test biblioteket.

2. **Valfritt** – om du vill använda [gtid-baserad replikering](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)måste du identifiera gtid för den senaste transaktionen som körs vid huvud servern. Du kan använda följande kommando för att notera gtid för den senaste transaktionen som kördes på huvud servern.
   ```sql
   show global variables like 'gtid_executed';
   ```
3. Ange att käll servern ska läsa/skriva-läge.

   När databasen har dump ATS ändrar du källans MySQL-server tillbaka till Läs-/skriv läge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställ dumpfilen till en ny server.

   Återställ dumpfilen till servern som skapats i Azure Database for MySQL-tjänsten. Mer information om hur du återställer en dumpfil till en MySQL-server hittar du i [dump & Restore](concepts-migrate-dump-restore.md) . Om dumpfilen är stor laddar du upp den till en virtuell dator i Azure inom samma region som replik servern. Återställ den till Azure Database for MySQL servern från den virtuella datorn.
   
4. **Frivillig** – anteckna gtid på den återställda servern på Azure Database for MySQL för att säkerställa att den är samma som för huvud servern. Du kan använda följande kommando för att notera gtid för det gtid-rensade värdet på Azure Database for MySQL replik servern. Värdet för gtid_purged ska vara detsamma som gtid_executed på huvud servern som anges i steg 2 för gtid-baserad replikering.

   ```sql
   show global variables like 'gtid_purged';
   ```

## <a name="4-link-source-and-replica-servers-to-start-data-in-replication"></a>4. länk käll-och replik servrar för att starta Datareplikering

1. Ange käll Server.

   Alla Datareplikering funktioner utförs med lagrade procedurer. Du hittar alla procedurer på [datareplikering lagrade procedurer](./reference-stored-procedures.md). De lagrade procedurerna kan köras i MySQL-gränssnittet eller MySQL Workbench.

   Om du vill länka två servrar och starta replikering loggar du in på mål replik servern i Azure DB för MySQL-tjänsten och anger den externa instansen som käll Server. Detta görs med hjälp av den `mysql.az_replication_change_master` lagrade proceduren på Azure dB för MySQL-servern.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   **Valfritt** – om du vill använda [gtid-baserad replikering](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)måste du använda följande kommando för att länka de två servrarna
    ```sql
   call mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_ssl_ca>');
   ```

   - master_host: värd namnet för käll servern
   - master_user: användar namn för käll servern
   - master_password: lösen ordet för käll servern
   - master_port: port nummer som käll servern lyssnar efter anslutningar på. (3306 är standard porten som MySQL lyssnar på)
   - master_log_file: det binära logg fils namnet körs inte `show master status`
   - master_log_pos: binär logg position körs `show master status`
   - master_ssl_ca: certifikat utfärdarens kontext. Om du inte använder SSL skickar du en tom sträng.

     Vi rekommenderar att du överför den här parametern i som en variabel. Mer information finns i följande exempel.

   > [!NOTE]
   > Om käll servern finns på en virtuell Azure-dator anger du "Tillåt åtkomst till Azure-tjänster" till "på" för att tillåta käll-och replik servrar att kommunicera med varandra. Den här inställningen kan ändras från **anslutnings säkerhets** alternativen. Mer information finns i [Hantera brand Väggs regler med hjälp av portalen](howto-manage-firewall-using-portal.md) .

   **Exempel**

   *Replikering med SSL*

   Variabeln `@cert` skapas genom att köra följande MySQL-kommandon:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   Replikering med SSL konfigureras mellan en käll server som finns i domänen "companya.com" och en replik server som är värd för Azure Database for MySQL. Den här lagrade proceduren körs på repliken.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replikering utan SSL*

   Replikering utan SSL konfigureras mellan en käll server som finns i domänen "companya.com" och en replik server som är värd för Azure Database for MySQL. Den här lagrade proceduren körs på repliken.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Statisk.

   Om du vill hoppa över replikeringen av vissa tabeller från din huvud Server uppdaterar du `replicate_wild_ignore_table` Server parametern på replik servern. Du kan ange mer än ett tabell mönster med en kommaavgränsad lista.

   Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) .

   Om du vill uppdatera parametern kan du använda [Azure Portal](howto-server-parameters.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).

3. Starta replikering.

   Anropa den `mysql.az_replication_start` lagrade proceduren för att starta replikeringen.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Kontrol lera replikeringsstatus.

   Anropa [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) kommandot på replik servern för att visa replikeringsstatus.

   ```sql
   show slave status;
   ```

   Om statusen `Slave_IO_Running` och `Slave_SQL_Running` är "Ja" och värdet för `Seconds_Behind_Master` är "0" fungerar replikeringen bra. `Seconds_Behind_Master` anger hur sen repliken är. Om värdet inte är "0" innebär det att repliken bearbetar uppdateringar.

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>Andra användbara lagrade procedurer för åtgärder för data i replikering

### <a name="stop-replication"></a>Stoppa replikering

Använd följande lagrade procedur för att stoppa replikeringen mellan käll-och replik servern:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Ta bort replikeringsrelation

Använd följande lagrade procedur om du vill ta bort relationen mellan käll-och replik servern:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Hoppa över replikeringsfel

Använd följande lagrade procedur om du vill hoppa över ett replikeringsfel och tillåta att replikeringen fortsätter:

```sql
CALL mysql.az_replication_skip_counter;
```
 **Valfritt** – om du vill använda [gtid-baserad replikering](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html), använder du följande lagrade procedur för att hoppa över en transaktion

```sql
call mysql. az_replication_skip_gtid_transaction(‘<transaction_gtid>’)
```
Proceduren kan hoppa över transaktionen för den aktuella gtid. Om gtid-formatet inte är rätt eller om gtid-transaktionen redan har körts, kommer proceduren inte att kunna köras. Gtid för en transaktion kan bestämmas genom att parsa den binära loggen för att kontrol lera transaktions händelserna. MySQL tillhandahåller ett verktyg som [mysqlbinlog](https://dev.mysql.com/doc/refman/5.7/en/mysqlbinlog.html) för att parsa binära loggar och visa innehållet i text format som kan användas för att identifiera gtid för transaktionen.

Om du vill hoppa över nästa transaktion efter den aktuella replikeringens position använder du följande kommando för att identifiera gtid för nästa transaktion enligt nedan.

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```
  :::image type="content" source="./media/howto-data-in-replication/show-binary-log.png" alt-text="Visa binära logg resultat":::

## <a name="next-steps"></a>Nästa steg

- Läs mer om [datareplikering](concepts-data-in-replication.md) för Azure Database for MySQL.
