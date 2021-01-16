---
title: Konfigurera data-i replikering – Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar Datareplikering för Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 22974a47a6b1e9d49e5055a85f46286497cfe149
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250540"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Så här konfigurerar du Azure Database for MySQL Datareplikering

I den här artikeln beskrivs hur du konfigurerar [datareplikering](concepts-data-in-replication.md) i Azure Database for MySQL genom att konfigurera käll-och replik servrarna. Den här artikeln förutsätter att du har tidigare erfarenhet av MySQL-servrar och-databaser.

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.
>

För att skapa en replik i Azure Database for MySQL-tjänsten synkroniserar [datareplikering](concepts-data-in-replication.md)  data från en lokal MySQL-server lokalt, i virtuella datorer (VM) eller i moln databas tjänster. Datareplikering baseras på positionsbaserad replikering med en binär loggfil (binlog) som är inbyggd i MySQL. Mer information om BinLog-replikering finns i [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Granska [begränsningarna och kraven](concepts-data-in-replication.md#limitations-and-considerations) för datareplikering innan du utför stegen i den här artikeln.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Skapa en MySQL-server som ska användas som replik

1. Skapa en ny Azure Database for MySQL Server

   Skapa en ny MySQL-server (t. ex. "replica.mysql.database.azure.com"). Se [skapa en Azure Database for MySQL-server genom att använda Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) för att skapa servern. Den här servern är replik servern i Datareplikering.

   > [!IMPORTANT]
   > Azure Database for MySQL-servern måste skapas i Generell användning eller minnesoptimerade pris nivåer.
   >

2. Skapa samma användar konton och motsvarande privilegier

   Användar konton replikeras inte från käll servern till replik servern. Om du planerar att ge användare åtkomst till replik servern måste du skapa alla konton och motsvarande behörigheter manuellt på den nyligen skapade Azure Database for MySQL servern.

3. Lägg till käll serverns IP-adress i replikens brand Väggs regler.

   Uppdatera brandväggsregler med hjälp av [Azure-portalen](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

## <a name="configure-the-source-server"></a>Konfigurera käll servern

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

      Här är några exempel:

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

   Om `log_bin` returneras med värdet "off", aktiverar du binär loggning genom att redigera din. cnf-fil så att `log_bin=ON` och startar om servern för att ändringen ska börja gälla.

4. Käll Server inställningar

   Datareplikering kräver `lower_case_table_names` att parametern är konsekvent mellan käll-och replik servrarna. Den här parametern är 1 som standard i Azure Database for MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

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

## <a name="dump-and-restore-source-server"></a>Dumpa och återställa käll servern

1. Bestäm vilka databaser och tabeller du vill replikera till Azure Database for MySQL och utför dumpen från käll servern.

    Du kan använda mysqldump för att dumpa databaser från din huvud server. Mer information finns i [dumpa & Restore](concepts-migrate-dump-restore.md). Det behövs ingen dumpning av MySQL-biblioteket och test biblioteket.

2. Ange att käll servern ska läsa/skriva-läge.

   När databasen har dump ATS ändrar du källans MySQL-server tillbaka till Läs-/skriv läge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställ dumpfilen till en ny server.

   Återställ dumpfilen till servern som skapats i Azure Database for MySQL-tjänsten. Mer information om hur du återställer en dumpfil till en MySQL-server hittar du i [dump & Restore](concepts-migrate-dump-restore.md) . Om dumpfilen är stor laddar du upp den till en virtuell dator i Azure inom samma region som replik servern. Återställ den till Azure Database for MySQL servern från den virtuella datorn.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Länka käll-och replik servrar för att starta Datareplikering

1. Ange käll Server.

   Alla Datareplikering funktioner utförs med lagrade procedurer. Du hittar alla procedurer på [datareplikering lagrade procedurer](./reference-stored-procedures.md). De lagrade procedurerna kan köras i MySQL-gränssnittet eller MySQL Workbench.

   Om du vill länka två servrar och starta replikering loggar du in på mål replik servern i Azure DB för MySQL-tjänsten och anger den externa instansen som käll Server. Detta görs med hjälp av den `mysql.az_replication_change_master` lagrade proceduren på Azure dB för MySQL-servern.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: värd namnet för käll servern
   - master_user: användar namn för käll servern
   - master_password: lösen ordet för käll servern
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

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

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

## <a name="next-steps"></a>Nästa steg

- Läs mer om [datareplikering](concepts-data-in-replication.md) för Azure Database for MySQL.
