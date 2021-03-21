---
title: Skapa en Oracle-databas på en virtuell Azure-dator | Microsoft Docs
description: Få snabbt en Oracle Database 12C-databas igång i Azure-miljön.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 8964248bb23b2b615c7e73e26d730fbd79b4e9e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184465"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Skapa en Oracle Database på en virtuell Azure-dator

Den här guiden beskriver hur du använder Azure CLI för att distribuera en virtuell Azure-dator från [Galleri avbildningen för Oracle Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) för att skapa en Oracle 19c-databas. När servern har distribuerats ansluter du via SSH för att kunna konfigurera Oracle-databasen. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resurs grupp med namnet *RG-Oracle* på den *östra* platsen.

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Använd kommandot [AZ VM Create](/cli/azure/vm) för att skapa en virtuell dator (VM). 

Följande exempel skapar en virtuell dator med namnet `vmoracle19c`. Det skapar också SSH-nycklar, om de inte redan finns på en standard nyckel plats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

När du har skapat den virtuella datorn visar Azure CLI information som liknar följande exempel. Anteckna värdet för `publicIpAddress` . Du använder den här adressen för att få åtkomst till den virtuella datorn.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Skapa och ansluta en ny disk för Oracle-datadatafiler och FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Öppna portar för anslutning
I den här uppgiften måste du konfigurera vissa externa slut punkter för databas lyssnaren och EM Express som ska användas genom att konfigurera den Azure-nätverks säkerhets grupp som skyddar den virtuella datorn. 

1. Om du vill öppna slut punkten som du använder för att fjärrans luta till Oracle-databasen, skapar du en regel för nätverks säkerhets grupper på följande sätt:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Öppna slut punkten som du använder för att få åtkomst till Oracle EM Express genom att skapa en regel för nätverks säkerhets grupper med AZ Network NSG Rule Create enligt följande:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. Om det behövs kan du hämta den offentliga IP-adressen för den virtuella datorn igen med AZ Network Public-IP Visa enligt följande:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Förbered VM-miljön

1. Anslut till VM:en

   Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt IP-adressen med `publicIpAddress` värdet för den virtuella datorn.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Växla till rot användaren

   ```bash
   sudo su -
   ```

3. Sök efter senast skapade disk enhet som vi ska formatera för användning av Oracle-datadelar

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   Utdata ser ut ungefär så här:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Formatera enheten. 
   Som rot användare som körs delvis på enheten 
   
   Skapa först en disk etikett:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Skapa sedan en primär partition som sträcker sig över hela disken:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Kontrol lera sedan enhets informationen genom att skriva ut dess metadata:
   ```bash
   parted /dev/sdc print
   ```
   Utdata bör se ut ungefär så här:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Skapa ett fil system på enhetens partition

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Skapa en monterings punkt
   ```bash
   mkdir /u02
   ```

7. Montera disken

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Ändra behörigheter för monterings punkten

   ```bash
   chmod 777 /u02
   ```

9. Lägg till monteringen i/etc/fstab-filen. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Uppdatera ***/etc/hosts*** -filen med den offentliga IP-adressen och värd namnet.

    Ändra den ***offentliga IP-adressen och VMName*** så att de motsvarar dina faktiska värden:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Uppdatera hostname-filen
    
    Använd följande kommando för att lägga till den virtuella datorns domän namn i **/etc/hostname** -filen. Detta förutsätter att du har skapat en resurs grupp och en virtuell dator i regionen **östra** :
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Öppna brand Väggs portar
    
    Eftersom SELinux är aktiverat som standard på Marketplace-avbildningen måste vi öppna brand väggen för trafik för databasens lyssnings port 1521 och Enterprise Manager Express-port 5502. Kör följande kommandon som rot användare:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Skapa databasen

Oracle-programvaran är redan installerad på Marketplace-avbildningen. Skapa en exempel databas på följande sätt. 

1.  Växla till **Oracle** -användaren:

    ```bash
    sudo su - oracle
    ```
2. Starta databas lyssnaren

   ```bash
   lsnrctl start
   ```
   De utdata som genereras liknar följande:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Skapa en data katalog för Oracle-datafilerna:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Kör skapande assistenten för databaser:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    Det tar några minuter att skapa databasen.

    Du ser utdata som liknar följande:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Ange Oracle-variabler

    Innan du ansluter måste du ange miljövariabeln *ORACLE_SID*:

    ```bash
        export ORACLE_SID=test
    ```

    Du bör också lägga till variabeln ORACLE_SID till `oracle` användar `.bashrc` filen för framtida inloggningar med hjälp av följande kommando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-anslutning

För ett GUI-verktyg som du kan använda för att utforska-databasen konfigurerar du Oracle EM Express. Om du vill ansluta till Oracle EM Express måste du först konfigurera porten i Oracle. 

1. Anslut till din databas med SQLPlus:

    ```bash
    sqlplus sys as sysdba
    ```

2. När du är ansluten ställer du in port 5502 för EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Anslut EM Express från din webbläsare. Kontrol lera att webbläsaren är kompatibel med EM Express (Flash-installation krävs): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Du kan logga in med hjälp av **sys** -kontot och markera kryss rutan **som SYSDBA** . Använd det lösen ord **OraPasswd1** som du angav under installationen. 

    ![Skärm bild av sidan för Oracle OEM Express-inloggning](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatisera start och avstängning av databasen

Oracle-databasen startar som standard inte automatiskt när du startar om den virtuella datorn. Om du vill konfigurera Oracle-databasen så att den startar automatiskt måste du först logga in som rot. Skapa och uppdatera vissa systemfiler.

1. Logga in som rot

    ```bash
    sudo su -
    ```

2.  Kör följande kommando för att ändra den automatiska start flaggan från `N` till `Y` i `/etc/oratab` filen:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Skapa en fil med namnet `/etc/init.d/dbora` och klistra in följande innehåll:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Ändra behörigheter för filer med *chmod* enligt följande:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Skapa symboliska länkar för start och avstängning enligt följande:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Om du vill testa ändringarna startar du om den virtuella datorn:

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med att utforska din första Oracle-databas på Azure och den virtuella datorn inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skyddar din databas i Azure med [strategier för Oracle-säkerhetskopiering](./oracle-database-backup-strategies.md)

Lär dig mer om andra [Oracle-lösningar på Azure](./oracle-overview.md). 

Prova att [Installera och konfigurera Oracle-guiden för automatisk lagrings hantering](configure-oracle-asm.md) .
