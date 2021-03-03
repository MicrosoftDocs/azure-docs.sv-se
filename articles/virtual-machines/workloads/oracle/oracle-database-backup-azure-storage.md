---
title: Säkerhetskopiera en Oracle Database 19c-databas på en virtuell Azure Linux-dator med RMAN och Azure Storage
description: Lär dig hur du säkerhetskopierar en Oracle Database 19c-databas till Azures moln lagring.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670020"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Säkerhetskopiera och återställa en Oracle Database 19c-databas på en virtuell Azure Linux-dator med Azure Storage

Den här artikeln visar hur du använder Azure Storage som ett medium för att säkerhetskopiera och återställa en Oracle-databas som körs på en virtuell Azure-dator. Du säkerhetskopierar databasen med hjälp av Oracle-RMAN till Azure File Storage som är monterat på den virtuella datorn med SMB-protokollet. Användning av Azure Storage för säkerhets kopierings medier är mycket kostnads effektivt och presterande. För mycket stora databaser är Azure Backup dock en bättre lösning.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Om du vill utföra säkerhets kopierings-och återställnings processen måste du först skapa en virtuell Linux-dator som har en installerad instans av Oracle Database 19c. Marketplace-avbildningen som används för att skapa den virtuella datorn är  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: senaste**. Följ stegen i snabb starten för [Oracle Create Database](./oracle-database-quick-create.md) för att skapa en Oracle-databas för att slutföra den här självstudien.

## <a name="prepare-the-database-environment"></a>Förbereda databas miljön

1. Använd följande kommando om du vill skapa en SSH-session (Secure Shell) med den virtuella datorn. Ersätt IP-adressen och värd namns kombinationen med `publicIpAddress` värdet för den virtuella datorn.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Växla till ***rot*** användaren:
 
   ```bash
   sudo su -
   ```
    
3. Lägg till Oracle-användaren till ***/etc/sudoers*** -filen:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Det här steget förutsätter att du har en Oracle-instans (test) som körs på en virtuell dator med namnet *vmoracle19c*.

   Växla användare till *Oracle* -användare:

   ```bash
   sudo su - oracle
   ```
    
5. Innan du ansluter måste du ange miljövariabeln ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Du bör också lägga till variabeln ORACLE_SID till `oracle` användar `.bashrc` filen för framtida inloggningar med hjälp av följande kommando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Starta Oracle-lyssnaren om den inte redan körs:
    
   ```bash
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

7.  Skapa platsen för snabb återställnings område (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Anslut till databasen:

    ```bash
    sqlplus / as sysdba
    ```

9.  Starta databasen om den inte redan körs:

    ```bash
    SQL> startup
    ```

10. Ange variabler för databas miljön för snabb återställning:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Kontrol lera att databasen är i arkivet logg läge för att aktivera säkerhets kopiering online.
    Kontrol lera logg arkivets status först:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Om i NOARCHIVELOG-läge kör du följande kommandon i SQLPlus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Skapa en tabell för att testa säkerhets kopierings-och återställnings åtgärderna:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Säkerhetskopiera till Azure Files

Utför följande steg för att säkerhetskopiera till Azure Files:

1. Konfigurera Azure File Storage.
1. Montera Azure Storage fil resursen på den virtuella datorn.
1. Säkerhetskopiera databasen.
1. Återställa och återställa databasen.

### <a name="set-up-azure-file-storage"></a>Konfigurera Azure-File Storage

I det här steget ska du säkerhetskopiera Oracle-databasen med Oracle Recovery Manager (RMAN) till Azure File Storage. Azure-filresurser är fullständigt hanterade fil resurser som bor i molnet. De kan nås via SMB-protokollet (Server Message Block) eller NFS-protokollet (Network File System). Det här steget beskriver hur du skapar en fil resurs som använder SMB-protokollet för att montera till den virtuella datorn. Information om hur du monterar med NFS finns i [montera Blob Storage med hjälp av nfs 3,0-protokollet](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

När du monterar Azure Files kommer vi att använda `cache=none` för att inaktivera cachelagring av fil resurs data. Och för att säkerställa att filer som skapas i resursen ägs av Oracle-användaren anger `uid=oracle` du `gid=oinstall` även alternativen och. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konfigurera först ditt lagrings konto.

1. Konfigurera File Storage i Azure Portal

    I Azure Portal väljer du ***+ skapa en resurs** _ och söker efter och väljer _ *_lagrings konto_**
    
    ![Skärm bild som visar var du skapar en resurs och väljer lagrings konto.](./media/oracle-backup-recovery/storage-1.png)
    
2. På sidan Skapa lagrings konto väljer du den befintliga resurs gruppen ***RG-Oracle** _, namnger ditt lagrings konto _*_Oracbkup1_*_ och väljer _*_Storage v2 (generalpurpose v2)_*_ för konto typ. Ändra replikeringen till _*_Lokalt Redundant lagring (LRS)_*_ och ange prestanda till _ *_standard_* *. Kontrol lera att platsen har angetts till samma region som alla andra resurser i resurs gruppen. 
    
    ![Skärm bild som visar var du kan välja den befintliga resurs gruppen.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Klicka på fliken ***Avancerat** _ och under Azure Files ange _*_stora fil resurser_*_ till _ *_aktiverat_* *. Klicka på granska + skapa och klicka sedan på Skapa.
    
    ![Skärm bild som visar var du ställer in stora fil resurser på aktive rad.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. När lagrings kontot har skapats går du till resursen och väljer ***fil resurser***
    
    ![Skärm bild som visar var du kan välja fil resurser.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Klicka på ***+ fil resurs** _ och i det _*_nya fil resurs_*_ bladet namnger du fil resursen _*_orabkup1_*_. Ange _*_kvoten_*_ till _*_10240_*_ GIB och kontrol lera transaktionen som är _*_optimerad_*_ som nivån. Kvoten visar en övre gräns som fil resursen kan växa till. När vi använder standard lagring är resurserna PAYG och de är inte etablerade så att du kan ställa in den på 10 TiB inte kostar mer än vad du använder. Om din säkerhets kopierings strategi kräver mer lagrings utrymme måste du ange kvoten till en lämplig nivå för att lagra alla säkerhets kopior.   När du har slutfört det nya fil resurs bladet klickar du på _ *_skapa_* *.
    
    ![Skärm bild som visar var du kan lägga till en ny fil resurs.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. När du har skapat klickar du på ***orabkup1*** på sidan fil resurs inställningar. 
    Klicka på fliken ***Connect** _ för att öppna bladet Anslut och klicka sedan på fliken _ *_Linux_**. Kopiera de kommandon som finns för att montera fil resursen med SMB-protokoll. 
    
    ![Sidan Lägg till lagrings konto](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera ditt lagrings konto och en fil resurs kör du följande kommandon i Azure CLI.

1. Skapa lagrings kontot i samma resurs grupp och plats som den virtuella datorn:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Skapa fil resurs i lagrings kontot med en kvot på 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Hämta lagrings kontots primära nyckel (KEY1) som behövs när du monterar fil resursen på den virtuella datorn:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Montera Azure Storage fil resursen på den virtuella datorn

1. Skapa monterings punkten:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Konfigurera autentiseringsuppgifter:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Ersätt `<Your Storage Account Key1>` lagrings konto nyckeln som hämtades tidigare, innan du kör följande kommando:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Ändra behörigheter för filen med autentiseringsuppgifter:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Lägg till monteringen i/etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Kör kommandona för att montera Azure Files lagring med SMB-protokoll:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Om du får ett fel som liknar:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   sedan kan CIFS-paketet inte installeras på din Linux-värd. 
   
   Kontrol lera om CIS är installerat genom att köra följande kommando:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Om kommandot inte returnerar några utdata installerar du CIFS-paketet med följande kommando:

   ```bash 
   sudo yum install cifs-utils
   ```

   Kör nu monterings kommandot ovan för att montera Azure Files-lagring.

6. Kontrol lera att fil resursen är korrekt monterad med följande kommando:

   ```bash
   df -h
   ```

   Utdata bör se ut ungefär så här:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Säkerhetskopiera databasen

I det här avsnittet kommer vi att använda Oracle Recovery Manager (RMAN) för att göra en fullständig säkerhets kopia av databasen och arkivera loggar och skriva säkerhets kopian som en säkerhets kopia av Azure-filresursen som monterats tidigare. 

1. Konfigurera RMAN för att säkerhetskopiera till Azure Files monterings punkt:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Eftersom Azure standard-filresurser har en maximal fil storlek på 1 TiB så begränsar vi storleken på RMAN säkerhets kopierings enheter till 1 TiB. (Observera att Premium File-resurser har en maximal fil storleks gräns på 4 TiB. Mer information finns i [Azure Files skalbarhets-och prestanda mål](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Bekräfta konfigurations ändrings informationen:

    ```bash
    RMAN> show all;
    ```

4. Kör nu säkerhets kopieringen. Följande kommando tar en fullständig säkerhets kopia av databasen, inklusive Arkiv logg filen som en säkerhets kopian i komprimerat format:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Nu har du säkerhetskopierat databasen online med Oracle RMAN, med säkerhets kopian i Azure File Storage. Med den här metoden kan du använda funktionerna i RMAN när du lagrar säkerhets kopior i Azure File Storage där de kan nås från andra virtuella datorer, vilket är användbart om du behöver klona databasen.  
    
När du använder RMAN och Azure File Storage för säkerhets kopiering av databasen är det många fördelar, säkerhets kopierings-och återställnings tiden är länkad till databasens storlek, så för stora databaser kan det ta lång tid för dessa åtgärder.  En alternativ mekanism för säkerhets kopiering, som använder Azure Backup programkonsekventa VM-säkerhetskopieringar, använder ögonblicks bild teknik för att utföra säkerhets kopieringar, som har fördelarna med mycket snabba säkerhets kopieringar oavsett databasens storlek. Integrering med Recovery Services Vault ger säker lagring av dina Oracle Database säkerhets kopior i Azures moln lagring som kan nås från andra virtuella datorer och Azure-regioner. 

### <a name="restore-and-recover-the-database"></a>Återställa och återställa databasen

1.  Stäng av Oracle-instansen:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Ta bort datafiles och säkerhets kopiorna:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Följande kommandon använder RMAN för att återställa saknade data och återställa databasen:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Kontrol lera att databas innehållet har återställts fullständigt:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
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