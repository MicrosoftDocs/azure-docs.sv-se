---
title: Säkerhetskopiera och återställa en Oracle Database 19c-databas på en virtuell Azure Linux-dator med Azure Backup
description: Lär dig hur du säkerhetskopierar och återställer en Oracle Database 19c-databas med hjälp av tjänsten Azure Backup.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 90f86a198ad36c2961f77336092d863953ee45ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673888"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Säkerhetskopiera och återställa en Oracle Database 19c-databas på en virtuell Azure Linux-dator med Azure Backup

Den här artikeln visar hur du använder Azure Backup för att ta ögonblicks bilder av de virtuella dator diskarna, som innehåller databasfilerna och snabb återställnings ytan. Med Azure Backup kan du göra fullständiga ögonblicks bilder av ögonblicks bilder, som lagras i [Recovery Services-valvet](../../../backup/backup-azure-recovery-services-vault-overview.md).  Azure Backup innehåller också programkonsekventa säkerhets kopieringar som garanterar att ytterligare korrigeringar inte krävs för att återställa data. Återställning av konsekventa programdata minskar tiden för återställning, så att du snabbt kan återgå till körläge.

> [!div class="checklist"]
>
> * Säkerhetskopiera databasen med programkonsekvent säkerhets kopiering
> * Återställa och återställa databasen från en återställnings punkt
> * Återställa den virtuella datorn från en återställnings punkt

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Om du vill utföra säkerhets kopierings-och återställnings processen måste du först skapa en virtuell Linux-dator som har en installerad instans av Oracle Database 19c. Marketplace-avbildningen som används för att skapa den virtuella datorn är  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: senaste**. Följ stegen i snabb starten för [Oracle Create Database](./oracle-database-quick-create.md) för att skapa en Oracle-databas för att slutföra den här självstudien.


## <a name="prepare-the-environment"></a>Förbereda miljön

Slutför följande steg för att förbereda miljön:

1. Anslut till den virtuella datorn.
1. Förbered databasen.

### <a name="connect-to-the-vm"></a>Anslut till VM:en

1. Använd följande kommando om du vill skapa en SSH-session (Secure Shell) med den virtuella datorn. Ersätt IP-adressen och värd namns kombinationen med `<publicIpAddress>` värdet för den virtuella datorn.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Växla till *rot* användaren:

   ```bash
   sudo su -
   ```
    
1. Lägg till Oracle-användaren till */etc/sudoers* -filen:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Förbered databasen

Det här steget förutsätter att du har en Oracle-instans (*test*) som körs på en virtuell dator med namnet *vmoracle19c*.

1. Växla användare till *Oracle* -användare:
 
   ```bash
    sudo su - oracle
    ```
    
2. Innan du ansluter måste du ange miljövariabeln ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```

    Du bör också lägga till variabeln ORACLE_SID till `oracle` användar `.bashrc` filen för framtida inloggningar med hjälp av följande kommando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Starta Oracle-lyssnaren om den inte redan körs:

    ```output
    $ lsnrctl start
    ```

    Utdata bör se ut ungefär som följande exempel:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

4.  Skapa platsen för snabb återställnings område (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Anslut till databasen:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Starta databasen om den inte redan körs:

    ```bash
    SQL> startup
    ```

7.  Ange variabler för databas miljön för snabb återställning:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Kontrol lera att databasen är i arkivet logg läge för att aktivera säkerhets kopiering online.

    Kontrol lera logg arkivets status först:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Om den är i NOARCHIVELOG-läge kör du följande kommandon:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Skapa en tabell för att testa säkerhets kopierings-och återställnings åtgärderna:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Konfigurera RMAN att säkerhetskopiera till snabb återställnings ytan på den virtuella dator disken:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Bekräfta konfigurations ändrings informationen:

    ```bash
    RMAN> show all;
    ```    

12.  Kör nu säkerhets kopieringen. Följande kommando tar en fullständig säkerhets kopia av databasen, inklusive Arkiv logg filen som en säkerhets kopian i komprimerat format:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>Använda Azure Backup (för hands version)

Azure Backup tillhandahåller enkla, säkra och kostnadseffektiva lösningar för att säkerhetskopiera dina data och återställa dem från Microsoft Azure-molnet. Med Azure Backup får du oberoende och isolerade säkerhetskopior, vilket skyddar originaldata från att förstöras oavsiktligt. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalbarhet är enkla, säkerhets kopieringar optimeras och du kan enkelt återställa efter behov.

Azure Backup-tjänsten tillhandahåller ett [ramverk](../../../backup/backup-azure-linux-app-consistent.md) för att uppnå program konsekvens under säkerhets kopiering av virtuella Windows-och Linux-datorer för olika program som Oracle, MySQL, Mongo DB och PostGreSQL. Detta innebär att du anropar ett för skript (för att ta bort programmen) innan du tar en ögonblicks bild av diskarna och anropar efter skript (kommandon för att låsa upp programmen) när ögonblicks bilden har slutförts, för att returnera programmen till normalt läge. Exempel på för-skript och post-skript finns på GitHub, och det är ditt ansvar att skapa och underhålla dessa skript.

Nu Azure Backup tillhandahållas ett förbättrat för skript och efter skript ramverk (**som för närvarande finns i för hands version**), där Azure backups tjänsten tillhandahåller paketerade för-skript och post-skript för valda program. Azure Backup användare bara behöver ge programmet ett namn och sedan anropar Azure VM Backup automatiskt de relevanta för hands-och-post-skripten. Paketerade för-skript och post-skript kommer att behållas av Azure Backups teamet, så att användarna kan garantera support, ägarskap och giltighet för dessa skript. För närvarande är de program som stöds för det förbättrade ramverket *Oracle* och *MySQL*.

I det här avsnittet ska du använda Azure Backup Enhanced Framework för att ta programkonsekventa ögonblicks bilder av den virtuella datorn och Oracle-databasen som körs. Databasen kommer att placeras i säkerhets kopierings läge så att en transaktions konsekvent säkerhets kopiering kan ske medan Azure Backup tar en ögonblicks bild av de virtuella dator diskarna. Ögonblicks bilden är en fullständig kopia av lagringen och inte en stegvis eller kopia vid skrivning av ögonblicks bilder, så det är ett effektivt medium att återställa databasen från. Fördelen med att använda Azure Backup programkonsekventa ögonblicks bilder är att de är mycket snabba att ta oavsett hur stor databasen är och en ögonblicks bild kan användas för återställnings åtgärder så fort den tas, utan att behöva vänta på att den överförs till Recovery Services-valvet.

Slutför följande steg för att använda Azure Backup för att säkerhetskopiera databasen:

1. Förbered miljön för en programkonsekvent säkerhets kopiering.
1. Konfigurera programkonsekventa säkerhets kopieringar.
1. Utlös en programkonsekvent säkerhets kopiering av den virtuella datorn.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Förbereda miljön för en programkonsekvent säkerhets kopiering

1. Växla till *rot* användaren:

   ```bash
   sudo su -
   ```

1. Skapa ny säkerhets kopierings användare:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Konfigurera användar miljön för säkerhets kopiering:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Konfigurera extern autentisering för den nya säkerhets kopierings användaren. Säkerhets kopierings användaren måste kunna komma åt databasen med hjälp av extern autentisering, så att det inte går att använda ett lösen ord.

   Växla först tillbaka till *Oracle* -användaren:

   ```bash
   su - oracle
   ```

   Logga in på databasen med SQLPlus och kontrol lera standardinställningarna för extern autentisering:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   Utdata bör se ut som i det här exemplet: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Nu ska du skapa en databas användares *azbackup* autentiserad externt och bevilja sysbackup-behörighet:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Om du får ett fel meddelande `ORA-46953: The password file is not in the 12.2 format.`  när du kör `GRANT` instruktionen följer du dessa steg för att migrera orapwd-filen till 12,2-format:
   >
   > 1. Avsluta SQLPlus.
   > 1. Flytta lösen ords filen med det gamla formatet till ett nytt namn.
   > 1. Migrera lösen ords filen.
   > 1. Ta bort den gamla filen.
   > 1. Kör följande kommando:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Kör `GRANT` åtgärden på nytt i SQLPlus.
   >
   
4. Skapa en lagrad procedur för att logga säkerhets kopierings meddelanden i databasens varnings logg:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Konfigurera programkonsekventa säkerhets kopieringar  

1. Växla till *rot* användaren:

   ```bash
   sudo su -
   ```

2. Sök efter mappen "etc/Azure". Om detta inte finns skapar du den programkonsekventa säkerhets kopierings arbets katalogen:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Sök efter "arbets belastning. conf" i mappen. Om det inte finns skapar du en fil i */etc/Azure* -katalogen med namnet *arbets belastning. conf* med följande innehåll, som måste börja med `[workload]` . Om filen redan finns redigerar du bara fälten så att de matchar följande innehåll. Annars skapar följande kommando filen och fyller i innehållet:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Utlös en programkonsekvent säkerhets kopiering av den virtuella datorn

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Gå till resurs gruppen **RG-Oracle** i Azure Portal och klicka på den virtuella datorn **vmoracle19c**.

2.  På bladet **säkerhets kopia** skapar du ett nytt **Recovery Services valv** i resurs gruppen **RG-Oracle** med name- **valvet**.
    För **Välj säkerhets kopierings princip** använder du **(ny) DailyPolicy**. Om du vill ändra säkerhets kopierings frekvensen eller kvarhållningsintervallet väljer du **skapa en ny princip** i stället.

    ![Lägg till sida i Recovery Services valv](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Fortsätt genom att klicka på **Aktivera säkerhets kopiering**.

    > [!IMPORTANT]
    > När du har klickat på **Aktivera säkerhets kopiering** startar inte säkerhets kopieringen förrän den schemalagda tiden har gått ut. Slutför nästa steg för att konfigurera en omedelbar säkerhets kopiering.

4. På sidan resurs grupp klickar du på ditt nyligen skapade Recovery Services Vault- **valv**. Tips: du kan behöva uppdatera sidan för att se den.

5.  På bladet för att **säkerhetskopiera objekt** under **antal säkerhets kopierings** objekt väljer du antalet säkerhets kopierings objekt.

    ![Informations sida för Recovery Services valv](./media/oracle-backup-recovery/recovery-service-02.png)

6.  På bladet **säkerhets kopierings objekt (virtuell Azure-dator)** klickar du på knappen med tre punkter (**...**) på höger sida av sidan och klickar sedan på **Säkerhetskopiera nu**.

    ![Kommandot Säkerhetskopiera nu Recovery Services valv](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Godkänn värdet Behåll säkerhets kopian som standard och klicka på knappen **OK** . Vänta tills säkerhets kopierings processen har slutförts. 

    Klicka på **säkerhets kopierings jobb** om du vill visa säkerhets kopierings jobbets status.

    ![Jobb sida för Recovery Services valv](./media/oracle-backup-recovery/recovery-service-04.png)

    Säkerhets kopierings jobbets status visas i följande bild:

    ![Jobb sidan Recovery Services valv med status](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Observera att det bara tar några sekunder att köra ögonblicks bilden. det kan ta lite tid att överföra det till valvet, och säkerhets kopierings jobbet slutförs inte förrän överföringen är klar.

8. För en programkonsekvent säkerhets kopiering kan du åtgärda eventuella fel i logg filen. Logg filen finns på/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Skapa ett Recovery Services-valv:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Aktivera säkerhets kopierings skydd för den virtuella datorn:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Utlös en säkerhets kopiering för att köra nu i stället för att vänta på att säkerhets kopieringen ska utlösas enligt standardschemat (5 AM UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Du kan övervaka förloppet för säkerhets kopierings jobbet med hjälp av `az backup job list` och `az backup job show` .

---

## <a name="recovery"></a>Återställning

Slutför följande steg för att återställa databasen:

1. Ta bort databasfilerna.
1. Generera ett återställnings skript från Recovery Services-valvet.
1. Montera återställnings punkten.
1. Utför återställning.

### <a name="remove-the-database-files"></a>Ta bort databasfilerna 

Senare i den här artikeln får du lära dig hur du testar återställnings processen. Innan du kan testa återställnings processen måste du ta bort databasfilerna.

1.  Stäng av Oracle-instansen:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Ta bort datafiles och säkerhets kopiorna:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Generera ett återställnings skript från Recovery Services-valvet

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I Azure Portal söker du efter objektet *valv* Recovery Services valv och markerar det.

    ![Säkerhets kopierings objekt för Recovery Services valv](./media/oracle-backup-recovery/recovery-service-06.png)

2. På bladet **Översikt** väljer du **säkerhets kopierings objekt** och den valda **virtuella Azure-datorn**, som ska innehålla Anon-noll säkerhets kopierings objekt i listan.

    ![Recovery Services valv antal säkerhets kopierings objekt för virtuella Azure-datorer](./media/oracle-backup-recovery/recovery-service-07.png)

3. På sidan säkerhets kopierings objekt (Azure Virtual Machines) visas din VM- **vmoracle19c** . Klicka på ellipsen till höger för att öppna menyn och välj **fil återställning**.

    ![Skärm bild av sidan Recovery Services valv fil återställning](./media/oracle-backup-recovery/recovery-service-08.png)

4. I fönstret **fil återställning (för hands version)** klickar du på **Hämta skript**. Spara sedan filen Download (. py) i en mapp på klient datorn. Ett lösen ord skapas för körning av skriptet. Kopiera lösen ordet till en fil för senare användning. 

    ![Hämta alternativ för att spara skript fil](./media/oracle-backup-recovery/recovery-service-09.png)

5. Kopiera. py-filen till den virtuella datorn.

    I följande exempel visas hur du använder ett säkert kopierings kommando (SCP) för att flytta filen till den virtuella datorn. Du kan också kopiera innehållet till Urklipp och sedan klistra in innehållet i en ny fil som har kon figurer ATS på den virtuella datorn.

    > [!IMPORTANT]
    > I följande exempel ser du till att du uppdaterar IP-adress och mappegenskaper. Värdena måste mappas till den mapp där filen sparas.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa återställnings punkter för den virtuella datorn använder du AZ backup List List List. I det här exemplet väljer vi den senaste återställnings punkten för den virtuella datorn med namnet myVM som skyddas i myRecoveryServicesVault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Hämta skriptet som ansluter, eller monterar, återställningspunkten till den virtuella datorn med az backup restore files mount-rp. I följande exempel hämtas skriptet för den virtuella datorn med namnet myVM som är skyddad i myRecoveryServicesVault.

Ersätt myRecoveryPointName med namnet på den återställningspunkt som du fick med det tidigare kommandot:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Skriptet laddas ned och ett lösenord visas som i följande exempel:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Kopiera. py-filen till den virtuella datorn.

I följande exempel visas hur du använder ett säkert kopierings kommando (SCP) för att flytta filen till den virtuella datorn. Du kan också kopiera innehållet till Urklipp och sedan klistra in innehållet i en ny fil som har kon figurer ATS på den virtuella datorn.

> [!IMPORTANT]
> I följande exempel ser du till att du uppdaterar IP-adress och mappegenskaper. Värdena måste mappas till den mapp där filen sparas.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Montera återställnings punkten

1. Skapa en återställnings monterings punkt och kopiera skriptet till den.

    I följande exempel skapar du en */restore* -katalog för ögonblicks bilden att montera till, flyttar filen till katalogen och ändrar filen så att den ägs av rot användaren och den körbara filen har gjorts.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Kör nu skriptet för att återställa säkerhets kopian. Du uppmanas att ange det lösen ord som genererades i Azure Portal. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    I följande exempel visas vad du bör se när du har kört föregående skript. När du uppmanas att fortsätta anger du **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. Åtkomst till monterade volymer bekräftas.

    Avsluta genom att ange **q** och sedan söka efter de monterade volymerna. Om du vill skapa en lista över tillagda volymer går du till kommando tolken och anger **DF-h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Utföra en återställning

1. Kopiera de saknade säkerhetskopierade filerna tillbaka till snabb återställnings ytan:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Följande kommandon använder RMAN för att återställa saknade data och återställa databasen:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Kontrol lera att databas innehållet har återställts fullständigt:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Demontera återställnings punkten.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    I Azure Portal på bladet **fil återställning (för hands version)** klickar du på **demontera diskar**.

    ![Demontera diskar, kommando](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Du kan också demontera återställnings volymerna genom att köra python-skriptet igen med alternativet **-Clean** .

## <a name="restore-the-entire-vm"></a>Återställa hela den virtuella datorn

I stället för att återställa de borttagna filerna från Recovery Services-valven kan du återställa hela den virtuella datorn.

Slutför följande steg för att återställa hela den virtuella datorn:

1. Stoppa och ta bort vmoracle19c.
1. Återställa den virtuella datorn.
1. Ange den offentliga IP-adressen.
1. Anslut till den virtuella datorn.
1. Starta databasen för att montera steget och utföra återställningen.

### <a name="stop-and-delete-vmoracle19c"></a>Stoppa och ta bort vmoracle19c

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I Azure Portal går du till den virtuella datorn **vmoracle19c** och väljer sedan **stoppa**.

1. När den virtuella datorn inte längre körs väljer du **ta bort** och sedan **Ja**.

   ![Valv borttagnings kommando](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Stoppa och frigöra den virtuella datorn:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Ta bort den virtuella datorn. Ange "y" när du uppmanas till detta:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Återställa den virtuella datorn

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Skapa ett lagrings konto för mellanlagring i Azure Portal.

   1. I Azure Portal väljer du **+ skapa en resurs** och söker efter och väljer **lagrings konto**.
    
      ![Skärm bild som visar var du skapar en resurs.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. På sidan Skapa lagrings konto väljer du den befintliga resurs gruppen **RG-Oracle**, namnger ditt lagrings konto **Oracrestore** och väljer **Storage v2 (generalpurpose v2)** för konto typ. Ändra replikeringen till **Lokalt Redundant lagring (LRS)** och Ställ in prestanda som **standard**. Kontrol lera att platsen har angetts till samma region som alla andra resurser i resurs gruppen. 
    
      ![Sidan Lägg till lagrings konto](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Klicka på granska + skapa och klicka sedan på Skapa.

2. I Azure Portal söker du efter objektet *valv* Recovery Services valv och klickar på det.

    ![Säkerhets kopierings objekt för Recovery Services valv](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  På bladet **Översikt** väljer du **säkerhets kopierings objekt** och den valda **virtuella Azure-datorn**, som ska innehålla Anon-noll säkerhets kopierings objekt i listan.

    ![Recovery Services valv antal säkerhets kopierings objekt för virtuella Azure-datorer](./media/oracle-backup-recovery/recovery-service-07.png)

4.  På sidan säkerhets kopierings objekt (Azure Virtual Machines) visas en lista över din VM- **vmoracle19c** . Klicka på namnet på den virtuella datorn.

    ![Sidan Återställ virtuell dator](./media/oracle-backup-recovery/recover-vm-02.png)

5.  På bladet **vmoracle19c** väljer du en återställnings punkt som har en enhetlig typ av **program konsekvent** och klickar på ellipsen (**...**) till höger för att öppna menyn.  På menyn klickar du på **Återställ virtuell dator**.

    ![Återställ VM-kommando](./media/oracle-backup-recovery/recover-vm-03.png)

6.  På bladet **Återställ virtuell dator** väljer du **Skapa ny** och **Skapa ny virtuell dator**. Ange namnet på den virtuella datorn **vmoracle19c** och välj VNet- **vmoracle19cVNET** så fylls under nätet automatiskt för dig baserat på ditt VNet-val. Processen för att återställa en virtuell dator kräver ett Azure Storage-konto i samma resurs grupp och region. Du kan välja det lagrings konto **orarestore** du installerar tidigare.

    ![Återställ konfigurations värden](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Om du vill återställa den virtuella datorn klickar du på knappen **Återställ** .

8.  Visa status för återställnings processen genom att klicka på **jobb** och sedan på **säkerhets kopierings jobb**.

    ![Status kommando för säkerhets kopierings jobb](./media/oracle-backup-recovery/recover-vm-05.png)

    Klicka på återställnings åtgärden **pågår** för att visa status för återställnings processen:

    ![Status för återställnings processen](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera ditt lagrings konto och en fil resurs kör du följande kommandon i Azure CLI.

1. Skapa lagrings kontot i samma resurs grupp och plats som den virtuella datorn:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Hämta listan över återställnings punkter som är tillgängliga. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Återställ återställnings punkten till lagrings kontot. Ersätt `<myRecoveryPointName>` med en återställnings punkt från listan som genererades i föregående steg:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Hämta information om återställnings jobbet. Följande kommando hämtar mer information om det återställda återställda jobbet, inklusive dess namn, som krävs för att hämta mall-URI: n. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   Resultatet kommer att se ut ungefär så här `(Note down the name of the restore job)` :

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Hämta information om den URI som ska användas för att återskapa den virtuella datorn. Ersätt återställnings jobb namnet från föregående steg för `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   Utdata ser ut ungefär så här:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Mallnamnet, som är i slutet av mallens BLOB-URI, som i det här exemplet är `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` och namnet på BLOB-behållaren, som finns i `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` listan. 

   Använd de här värdena i följande kommando för att tilldela variabler som ska förberedas för att skapa den virtuella datorn. En SAS-nyckel skapas för lagrings containern med 30 minuters varaktighet.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Distribuera nu mallen för att skapa den virtuella datorn.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Du uppmanas att ange ett namn för den virtuella datorn.

---

### <a name="set-the-public-ip-address"></a>Ange den offentliga IP-adressen

När den virtuella datorn har återställts bör du tilldela om den ursprungliga IP-adressen till den nya virtuella datorn.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  I Azure Portal går du till den virtuella datorn **vmoracle19c**. Du märker att den har tilldelats en ny offentlig IP-adress och ett nätverkskort som liknar vmoracle19c-NIC-XXXXXXXXXXXX, men inte har någon DNS-adress. När den ursprungliga virtuella datorn togs bort behålls den offentliga IP-adressen och NIC-kortet och nästa steg kommer att återansluta dem till den nya virtuella datorn.

    ![Lista över offentliga IP-adresser](./media/oracle-backup-recovery/create-ip-01.png)

2.  Stoppa den virtuella datorn

    ![Skapa IP-adress](./media/oracle-backup-recovery/create-ip-02.png)

3.  Gå till **nätverk**

    ![Associera IP-adress](./media/oracle-backup-recovery/create-ip-03.png)

4.  Klicka på **bifoga nätverks gränssnitt**, Välj det ursprungliga nätverkskortet * * vmoracle19cVMNic, vilket den ursprungliga offentliga IP-adressen fortfarande är kopplad till och klicka på **OK**

    ![Välj resurs typ och NIC-värden](./media/oracle-backup-recovery/create-ip-04.png)

5.  Nu måste du koppla från NÄTVERKSKORTet som skapades med återställnings åtgärden för den virtuella datorn eftersom det är konfigurerat som det primära gränssnittet. Klicka på **Koppla från nätverks gränssnittet** och välj det nya nätverkskortet som liknar **vmoracle19c-NIC-xxxxxxxxxxxx** och klicka sedan på **OK**

    ![Skärm bild som visar var du väljer koppla från nätverks gränssnittet.](./media/oracle-backup-recovery/create-ip-05.png)
    
    Den återskapade virtuella datorn kommer nu att ha det ursprungliga NÄTVERKSKORTet, som är associerat med reglerna för den ursprungliga IP-adressen och nätverks säkerhets gruppen
    
    ![IP-adress värde](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Gå tillbaka till **översikten** och klicka på **Starta** 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Stoppa och frigöra den virtuella datorn:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Visa en lista över den aktuella, återställa genererade virtuella dator nätverkskort

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   Utdata ser ut ungefär så här, som visar den återställnings genererade NIC-namnet som `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Bifoga ursprungligt nätverkskort, som ska ha namnet `<VMName>VMNic` , i det här fallet `vmoracle19cVMNic` . Den ursprungliga offentliga IP-adressen är fortfarande kopplad till det här NÄTVERKSKORTet och kommer att återställas till den virtuella datorn när NÄTVERKSKORTet är anslutet. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Koppla från den genererade återställnings kortet

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Starta den virtuella datorn:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Använd följande skript för att ansluta till den virtuella datorn:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Starta databasen för att montera steget och utföra återställningen

1. Du kanske upptäcker att instansen körs eftersom den automatiska starten har försökt starta databasen vid start av virtuella datorer. Databasen kräver dock att återställningen är endast i monterings skedet, så en förberedande avstängning körs först.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Kontrol lera att databas innehållet har återställts:

    ```bash
    SQL> select * from scott.scott_table;
    ```

Säkerhets kopiering och återställning av Oracle Database 19c-databasen på en virtuell Azure Linux-dator har nu slutförts.

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

När du inte längre behöver den virtuella datorn kan du använda följande kommando för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Nästa steg

[Självstudie: skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)

[Utforska Azure CLI-exempel för VM-distribution](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)