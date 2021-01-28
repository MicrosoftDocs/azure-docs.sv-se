---
title: Skapa en Oracle-databas på en virtuell Azure-dator | Microsoft Docs
description: Få snabbt en Oracle Database 12C-databas igång i Azure-miljön.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: d16153a7dc9a3164a5127b80a474bf9c398684ac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945139"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Skapa en Oracle Database på en virtuell Azure-dator

Den här guiden beskriver hur du använder Azure CLI för att distribuera en virtuell Azure-dator från [Galleri avbildningen för Oracle Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) för att skapa en Oracle 12C-databas. När servern har distribuerats ansluter du via SSH för att kunna konfigurera Oracle-databasen. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Använd kommandot [AZ VM Create](/cli/azure/vm) för att skapa en virtuell dator (VM). 

Följande exempel skapar en virtuell dator med namnet `myVM`. Det skapar också SSH-nycklar, om de inte redan finns på en standard nyckel plats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

När du har skapat den virtuella datorn visar Azure CLI information som liknar följande exempel. Anteckna värdet för `publicIpAddress` . Du använder den här adressen för att få åtkomst till den virtuella datorn.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt IP-adressen med `publicIpAddress` värdet för den virtuella datorn.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Skapa databasen

Oracle-programvaran är redan installerad på Marketplace-avbildningen. Skapa en exempel databas på följande sätt. 

1.  Växla till *Oracle* -användaren och starta sedan Oracle-lyssnaren:

    ```bash
    $ sudo -su oracle
    $ lsnrctl start
    ```

    De utdata som genereras liknar följande:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```
2. Skapa en data katalog för Oracle-datafilerna

    ```bash
        mkdir /u01/app/oracle/oradata
    ```

3.  Skapa databasen:

    ```bash
    dbca -silent \
           -createDatabase \
           -templateName General_Purpose.dbc \
           -gdbname cdb1 \
           -sid cdb1 \
           -responseFile NO_VALUE \
           -characterSet AL32UTF8 \
           -sysPassword OraPasswd1 \
           -systemPassword OraPasswd1 \
           -createAsContainerDatabase true \
           -numberOfPDBs 1 \
           -pdbName pdb1 \
           -pdbAdminPassword OraPasswd1 \
           -databaseType MULTIPURPOSE \
           -automaticMemoryManagement false \
           -storageType FS \
           -datafileDestination "/u01/app/oracle/oradata/" \
           -ignorePreReqs
    ```

    Det tar några minuter att skapa databasen.

    Du ser utdata som liknar följande:

    ```output
        Copying database files
        1% complete
        2% complete
        8% complete
        13% complete
        19% complete
        27% complete
        Creating and starting Oracle instance
        29% complete
        32% complete
        33% complete
        34% complete
        38% complete
        42% complete
        43% complete
        45% complete
        Completing Database Creation
        48% complete
        51% complete
        53% complete
        62% complete
        70% complete
        72% complete
        Creating Pluggable Databases
        78% complete
        100% complete
        Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
    ```

4. Ange Oracle-variabler

    Innan du ansluter måste du ange två miljövariabler: *ORACLE_HOME* och *ORACLE_SID*.

    ```bash
        ORACLE_SID=cdb1; export ORACLE_SID
    ```

    Du kan också lägga till ORACLE_HOME och ORACLE_SID variabler i. bashrc-filen. Detta skulle spara miljövariablerna för framtida inloggningar. Bekräfta att följande instruktioner har lagts till i `~/.bashrc` filen med valfritt redigerings program.

    ```bash
    # Add ORACLE_SID. 
    export ORACLE_SID=cdb1 
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

3. Öppna behållaren PDB1 om den inte redan är öppen, men kontrol lera statusen:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    De utdata som genereras liknar följande:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Om OPEN_MODE för `PDB1` inte är skrivskyddad, kör du följande kommandon för att öppna PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Du måste ange `quit` för att avsluta SQLPlus-sessionen och skriva `exit` för att logga ut från Oracle-användaren.

## <a name="automate-database-startup-and-shutdown"></a>Automatisera start och avstängning av databasen

Oracle-databasen startar som standard inte automatiskt när du startar om den virtuella datorn. Om du vill konfigurera Oracle-databasen så att den startar automatiskt måste du först logga in som rot. Skapa och uppdatera vissa systemfiler.

1. Logga in som rot

    ```bash
    sudo su -
    ```

2.  Använd din favorit redigerare, redigera filen `/etc/oratab` och ändra standardinställningen `N` till `Y` :

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Skapa en fil med namnet `/etc/init.d/dbora` och klistra in följande innehåll:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
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

## <a name="open-ports-for-connectivity"></a>Öppna portar för anslutning

Den sista aktiviteten är att konfigurera vissa externa slut punkter. Om du vill konfigurera den Azure-nätverks säkerhets grupp som skyddar den virtuella datorn måste du först avsluta SSH-sessionen på den virtuella datorn (bör ha startats av SSH vid omstart i föregående steg). 

1.  Om du vill öppna slut punkten som du använder för att fjärrans luta till Oracle-databasen skapar du en regel för nätverks säkerhets grupper med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule) enligt följande: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Öppna slut punkten som du använder för att få åtkomst till Oracle EM Express genom att skapa en regel för nätverks säkerhets grupper med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule) enligt följande:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Om det behövs kan du hämta den offentliga IP-adressen för den virtuella datorn igen med [AZ Network Public-IP Visa](/cli/azure/network/public-ip) enligt följande:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Anslut EM Express från din webbläsare. Kontrol lera att webbläsaren är kompatibel med EM Express (Flash-installation krävs): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Du kan logga in med hjälp av **sys** -kontot och markera kryss rutan **som SYSDBA** . Använd det lösen ord **OraPasswd1** som du angav under installationen. 

![Skärm bild av sidan för Oracle OEM Express-inloggning](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med att utforska din första Oracle-databas på Azure och den virtuella datorn inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Oracle-lösningar på Azure](./oracle-overview.md). 

Prova att [Installera och konfigurera Oracle-guiden för automatisk lagrings hantering](configure-oracle-asm.md) .
