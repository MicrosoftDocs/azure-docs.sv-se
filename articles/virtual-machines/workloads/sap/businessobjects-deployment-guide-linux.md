---
title: SAP BusinessObjects BI Platform-distribution på Azure för Linux | Microsoft Docs
description: Distribuera och konfigurera SAP BusinessObjects BI-plattformen på Azure för Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b94e1f82409da3329eb6d978fa2ae0222928cd97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505944"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Distributionsguide för SAP BusinessObjects BI-plattform för Linux i Azure

Den här artikeln beskriver strategin för att distribuera SAP BOBI-plattformen på Azure för Linux. I det här exemplet konfigureras två virtuella datorer med Premium SSD Managed Disks som installations katalog. Azure Database for MySQL används för CMS-databasen och Azure NetApp Files för fil lagrings servern delas mellan båda servrarna. Standard-Tomcat Java-webbprogram och BI-plattform installeras tillsammans på båda de virtuella datorerna. För att belastningsutjämna användar förfrågningen används Application Gateway som har inbyggda funktioner för TLS/SSL-avlastning.

Den här typen av arkitektur är effektiv för liten distribution eller icke-produktions miljö. För produktions-eller storskalig distribution kan du ha separata värdar för webb program och även ha flera BOBI program värdar som gör att servern kan bearbeta mer information.

![SAP BOBI-distribution på Azure för Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

I det här exemplet används under produkt version och fil systemets layout

- SAP BusinessObjects Platform 4,3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (version: 8.0.15)
- MySQL C API-koppling – libmysqlclient (version: 6.1.11)

| Filsystem        | Beskrivning                                                                                                               | Storlek (GB)             | Ägare  | Group  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Fil systemet för installation av SAP BOBI-instans, standard webb program för Tomcat och databas driv rutiner (om det behövs) | Rikt linjer för SAP-storlek | bl1adm | sapsys | Hanterad Premium disk – SSD |
| /usr/sap/frsinput  | Monterings katalogen är för delade filer över alla BOBI-värdar som ska användas som indatafilens lagrings katalog  | Affärs behov         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Monterings katalogen är för delade filer över alla BOBI-värdar som ska användas som utgående fil lagrings katalog | Affärs behov         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuera en virtuell Linux-dator via Azure Portal

I det här avsnittet ska vi skapa två virtuella datorer med operativ system avbildningen Linux (OS) för SAP BOBI-plattformen. De övergripande stegen för att skapa Virtual Machines är följande:

1. Skapa en [resurs grupp](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Skapa en [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Använd inte ett enda undernät för alla Azure-tjänster i SAP BI Platform-distribution. Baserat på SAP BI Platform Architecture måste du skapa flera undernät. I den här distributionen ska vi skapa tre undernät – program under nätet, fil lagrings platsens undernät och Application Gateway under nätet.
   - I Azure måste Application Gateway och Azure NetApp Files alltid finnas i ett separat undernät. Se [Azure Application Gateway](../../../application-gateway/configuration-overview.md) och [rikt linjer för Azure NetApp Files nätverks planerings](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) artikel för mer information.

3. Skapa en tillgänglighets uppsättning.

   - Placera virtuella datorer för varje nivå i en tillgänglighets uppsättning för att uppnå redundans för varje nivå i distributionen av flera instanser. Se till att separera tillgänglighets uppsättningarna för varje nivå baserat på din arkitektur.

4. Skapa virtuell dator 1 **(azusbosl1).**

   - Du kan antingen använda en anpassad avbildning eller välja en avbildning från Azure Marketplace. Läs om hur du [distribuerar en virtuell dator från Azure Marketplace för SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) eller [distribuerar en virtuell dator med en anpassad avbildning för SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) utifrån dina behov.

5. Skapa virtuell dator 2 **(azusbosl2).**
6. Lägg till en Premium SSD disk. Den kommer att användas som SAP-BOBI installations katalog.

## <a name="provision-azure-netapp-files"></a>Etablera Azure NetApp Files

Innan du fortsätter med installationen av Azure NetApp Files kan du bekanta dig med dokumentationen om Azure [NetApp-filer](../../../azure-netapp-files/azure-netapp-files-introduction.md).

Azure NetApp Files finns i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Kontrol lera om din valda Azure-region erbjuder Azure NetApp Files.

Använd sidan [Azure NetApp Files tillgänglighet för Azure-region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) för att kontrol lera tillgängligheten för Azure NetApp Files efter region.

Begär onboarding till Azure NetApp Files genom att gå till [Registrera dig för Azure NetApp Files-instruktioner](../../../azure-netapp-files/azure-netapp-files-register.md) innan du distribuerar Azure NetApp Files.

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files-resurser

Följande instruktioner förutsätter att du redan har distribuerat ditt [virtuella Azure-nätverk](../../../virtual-network/virtual-networks-overview.md). Azure NetApp Files resurser och virtuella datorer, där Azure NetApp Files resurserna ska monteras, måste distribueras i samma virtuella Azure-nätverk eller i peer-anslutna virtuella Azure-nätverk.

1. Om du inte redan har distribuerat resurserna begär du [att registrera dig för att Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Skapa ett NetApp-konto i din valda Azure-region genom att följa instruktionerna i [skapa ett NetApp-konto](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3. Konfigurera en pool för Azure NetApp Files kapacitet genom att följa anvisningarna i [Konfigurera en Azure NetApp Files kapacitets pool](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

   - I den plattforms arkitektur för SAP BI som presenteras i den här artikeln används en pool för Azure NetApp Files kapacitet på *Premium* service-nivå. För SAP BI-fillagringsplatsen på Azure rekommenderar vi att du använder en Azure NetApp Files *Premium* -eller *Ultra* [service-nivå](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

4. Delegera ett undernät till Azure NetApp Files, enligt beskrivningen i instruktionerna i [delegera ett undernät till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Distribuera Azure NetApp Files-volymer genom att följa anvisningarna i [skapa en NFS-volym för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   ANF-volymen kan distribueras som NFSv3 och NFSv 4.1, eftersom båda protokollen stöds för SAP BOBI-plattformen. Distribuera volymerna i respektive Azure NetApp Files-undernät. IP-adresserna för Azure NetApp-volymerna tilldelas automatiskt.

Tänk på att Azure NetApp Files-resurser och de virtuella Azure-datorerna måste finnas i samma virtuella Azure-nätverk eller i peer-nätverk med peer-nätverk. Till exempel azusbobi-frsinput, azusbobi-frsoutput är volym namnen och nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput är fil Sök vägarna för Azure NetApp Files volymerna.

- Volym azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volym azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Att tänka på

När du skapar din Azure NetApp Files för SAP BOBI Platform File lagrings Server, bör du vara medveten om följande överväganden:

1. Den minsta kapacitets poolen är 4 tebibyte (TiB).
2. Den minsta volym storleken är 100 gibibyte (GiB).
3. Azure NetApp Files och alla virtuella datorer där Azure NetApp Files volymer ska monteras måste finnas i samma virtuella Azure-nätverk eller i peer-anslutna [virtuella nätverk](../../../virtual-network/virtual-network-peering-overview.md) i samma region. Azure NetApp Files åtkomst över VNET-peering i samma region stöds nu. Azure NetApp Access över global peering stöds inte ännu.
4. Det valda virtuella nätverket måste ha ett undernät som är delegerat till Azure NetApp Files.
5. Med Azure NetApp Files [export policy](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kan du kontrol lera tillåtna klienter, åtkomst typ (Läs-och Skriv behörighet, skrivskyddad och så vidare).
6. Azure NetApp Files funktionen är inte Zone-medveten än. Funktionen distribueras för närvarande inte i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner.
7. Azure NetApp Files volymer kan distribueras som NFSv3-eller NFSv 4.1-volymer. Båda protokollen stöds för SAP BI-plattforms program.

## <a name="configure-file-systems-on-linux-servers"></a>Konfigurera fil system på Linux-servrar

Stegen i det här avsnittet använder följande prefix:

**[A]**: steget gäller alla värdar

### <a name="format-and-mount-sap-file-system"></a>Formatera och montera SAP-fil system

1. **[A]** lista alla anslutna diskar

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** format Blocks enhet för/usr/SAP

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** skapa monterings katalog

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Hämta UUID för block enhet

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** underhåll fil systemets monterings post i/etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** montera fil system

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Montera Azure NetApp Files volym

1. **[A]** skapa monterings kataloger

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** konfigurera klientens operativ system så att det stöder Nfsv 4.1 Mount **(gäller endast om du använder nfsv 4.1)**

   Om du använder Azure NetApp Files volymer med NFSv 4.1-protokollet kör du följande konfiguration på alla virtuella datorer, där Azure NetApp Files 4.1-volymer måste monteras.

   **Verifiera inställningar för NFS-domän**

   Kontrol lera att domänen är konfigurerad som standard Azure NetApp Files domän som är,  **defaultv4iddomain.com** och mappningen är inställd på **ingen**.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Se till att ange NFS-domänen i/etc/idmapd.conf på den virtuella datorn så att den matchar standard domän konfigurationen på Azure NetApp Files: **defaultv4iddomain.com**. Om det finns ett matchnings fel mellan domän konfigurationen på NFS-klienten (dvs. den virtuella datorn) och NFS-servern, d.v.s. Azure NetApp-konfigurationen, så visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som ingen.

   Verifiera `nfs4_disable_idmapping` . Den måste anges till **Y**. Kör monterings kommandot för att skapa en katalog struktur där `nfs4_disable_idmapping` finns. Du kan inte skapa katalogen manuellt under/sys/modules eftersom åtkomst är reserverad för kernel/driv rutiner.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Lägg till monterings poster

   Om du använder NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Om du använder NFSv 4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** montera NFS-volymer

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Konfigurera CMS-databasen – Azure Database for MySQL

Det här avsnittet innehåller information om hur du etablerar Azure Database for MySQL med hjälp av Azure Portal. Den innehåller också instruktioner om hur du skapar CMS-och gransknings databaser för SAP BOBI-plattformen och ett användar konto för att komma åt databasen.

Rikt linjerna gäller endast om du använder Azure DB för MySQL. Mer information om andra databaser finns i SAP eller databas-Specific Documentation.

### <a name="create-an-azure-database-for-mysql"></a>Skapa en Azure Database för MySQL

Logga in på Azure Portal och följ stegen som beskrivs i den här [snabb starts guiden för Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Några punkter att observera vid etablering Azure Database for MySQL –

1. Välj samma region för Azure Database for MySQL där dina SAP BI-plattformens program servrar körs.

2. Välj en DB-version som stöds baserat på [Product Availability Matrix (PAM) för SAP BI](https://support.sap.com/pam) som är speciellt för din SAP Bobi-version. Följ samma kompatibilitetsinställningar som de som har åtgärd ATS för MySQL AB i SAP PAM

3. I "Compute + Storage" väljer du **Konfigurera Server** och väljer lämplig pris nivå baserat på storleken på utdata.

4. Automatisk **utökning av lagring** är aktiverat som standard. Tänk på att [lagringen](../../../mysql/concepts-pricing-tiers.md#storage) bara kan skalas upp, inte nedåt.

5. Som standard är **säkerhets kopierings perioden** sju dagar, men du kan [också konfigurera](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) den upp till 35 dagar.

6. Säkerhets kopieringar av Azure Database for MySQL är lokalt redundanta som standard, så om du vill ha Server säkerhets kopior i Geo-redundant lagring väljer du **geografiskt redundant** från **alternativ för reserv redundans**.

> [!NOTE]
> Det finns inte stöd för att ändra [redundans alternativen för säkerhets kopiering](../../../mysql/concepts-backup.md#backup-redundancy-options) efter att servern har skapats.

### <a name="configure-connection-security"></a>Konfigurera anslutningssäkerhet

Som standard skyddas servern som skapats med en brand vägg och är inte tillgänglig offentligt. Följ stegen nedan för att ge åtkomst till det virtuella nätverket där SAP BI Platform-programservrar körs.  

1. Gå till server resurser i Azure Portal och välj **anslutnings säkerhet** från menyn till vänster för Server resursen.
2. Välj **Ja** för att **tillåta åtkomst till Azure-tjänster**.
3. Under VNET-regler väljer du **Lägg till befintligt virtuellt nätverk**. Välj det virtuella nätverket och under nätet för SAP BI Platform Application Server. Du måste också ge åtkomst till hopp Box eller andra servrar där du kan ansluta [MySQL Workbench](../../../mysql/connect-workbench.md) till Azure Database for MySQL. MySQL Workbench används för att skapa CMS-databasen och gransknings databasen
4. När virtuella nätverk har lagts till väljer du **Spara**.

### <a name="create-cms-and-audit-database"></a>Skapa CMS och gransknings databas

1. Hämta och installera MySQL Workbench från [MySQL-webbplatsen](https://dev.mysql.com/downloads/workbench/). Kontrol lera att du installerar MySQL Workbench på den server som har åtkomst till Azure Database for MySQL.

2. Anslut till servern med hjälp av MySQL Workbench. Följ anvisningarna i den här [artikeln](../../../mysql/connect-workbench.md#get-connection-information). Om anslutnings testet lyckas visas följande meddelande:

   ![SQL Workbench-anslutning](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Kör följande fråga på fliken SQL-fråga för att skapa schemat för CMS och gransknings databasen.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Skapa ett användar konto för att ansluta till schemat

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Kontrol lera privilegier och roller för MySQL-användarkontot

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Installera MySQL C API Connector (libmysqlclient) på Linux-Server

För att SAP BOBI program Server ska kunna komma åt databasen kräver det att det krävs databas klient/driv rutiner. MySQL C API-anslutning för Linux måste användas för att få åtkomst till CMS-och gransknings databaser. ODBC-anslutning till CMS-databasen stöds inte. Det här avsnittet innehåller anvisningar om hur du konfigurerar MySQL C API Connector på Linux.

1. Se de [MySQL-drivrutiner och hanterings verktyg som är kompatibla med Azure Database for MySQL](../../../mysql/concepts-compatibility.md) artikel, som beskriver de driv rutiner som är kompatibla med Azure Database for MySQL. Sök efter driv rutin för **MySQL Connector/C (libmysqlclient)** i artikeln.

2. Använd den här [länken](https://downloads.mysql.com/archives/c-c/) för att ladda ned driv rutiner.

3. Välj operativ system och ladda ned det delade komponent rpm-paketet för MySQL Connector. I det här exemplet används MySQL-Connector-c-Shared-6.1.11 Connector-version.

4. Installera anslutnings programmet i alla SAP BOBI-programinstanser.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Kontrol lera sökvägen till libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Ange LD_LIBRARY_PATH för att peka på `/usr/lib64` katalog för det användar konto som ska användas för installationen.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Server förberedelse

Stegen i det här avsnittet använder följande prefix:

**[A]**: steget gäller alla värdar.

1. **[A]** baserat på varianten av Linux (SLES eller RHEL), måste du ange kernel-parametrar och installera nödvändiga bibliotek. Se avsnittet **system krav** i [installations guiden för Business Intelligence-plattform för UNIX](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** kontrol lera att tids zonen på din dator är korrekt inställd. Se [avsnittet ytterligare UNIX-och Linux-krav](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) i installations guiden för.

3. **[A]** skapa ett användar konto (**bl1** ADM) och grupp (sapsys) som program varans bakgrunds processer kan köras under. Använd det här kontot för att köra installationen och köra program varan. Kontot kräver inte rot privilegier.

4. **[A]** Ange användar konto (**bl1** ADM)-miljö för att använda ett UTF-8-språkområde som stöds och se till att konsol programmet stöder UTF-8-teckenuppsättningar. För att säkerställa att operativ systemet använder rätt språk uppsättning, ställer du in de LC_ALL-och LANG-miljövariablerna på din önskade språkvariant i din (**bl1** ADM) användar miljö.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** konfigurera användar konto (**bl1** ADM).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Ladda ned och extrahera media för SAP BusinessObjects BI Platform från SAP Service Marketplace.

## <a name="installation"></a>Installation

Kontrol lera språk för användar kontot **bl1** adm på servern

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Gå till media för SAP BusinessObjects BI-plattformen och kör nedanstående kommando med **bl1** ADM-användare –

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Följ installations guiden för [SAP Bobi-plattformen](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) för UNIX, som är speciell för din version. Några saker att tänka på när du installerar SAP BOBI-plattformen.

- På sidan **Konfigurera produkt registrering** kan du antingen använda temporär licens nyckel för SAP BusinessObjects-lösningar från SAP NOTE [1288121](https://launchpad.support.sap.com/#/notes/1288121) eller generera licens nyckel i SAP Service Marketplace

- På sidan **Välj Installations typ** väljer du **fullständig** installation på första servern (azusbosl1) för annan server (Azusbosl2) och väljer **anpassad/expandera** som kommer att expandera den befintliga Bobi-installationen.

- På skärmen **Välj standard eller befintlig databas** väljer du **Konfigurera en befintlig databas**, där du uppmanas att välja CMS och gransknings databas. Välj **MySQL** för CMS-databas typ och gransknings databas typ.

  Du kan också välja ingen gransknings databas om du inte vill konfigurera granskning under installationen.

- Välj lämpliga alternativ på **sidan Välj Java-webbserver** som baseras på din SAP Bobi-arkitektur. I det här exemplet har vi valt alternativ 1, som installerar Tomcat-servern på samma SAP BOBI-plattform.

- Ange information om CMS-databasen i **Konfigurera CMS-lagringsplatsen databas-MySQL**. Exempel på indata för CMS-databas information för Linux-installation. Azure Database for MySQL används på standard porten 3306
  
  ![SAP BOBI-distribution på Linux-CMS-databasen](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Valfritt Ange information om gransknings databas i **Konfigurera granskning av databas-MySQL**. Exempel på indata för granskning av databas information för Linux-installation.

  ![SAP BOBI-distribution på Linux – granska databas](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Följ anvisningarna och ange nödvändiga indata för att slutföra installationen.

Kör installations programmet på den andra värden (azusbosl2) för distribution av flera instanser. På skärmen **Välj Installations typ** väljer du **anpassad/utöka** som utökar den befintliga Bobi-installationen.

I Azure Database for MySQL-erbjudandet används en gateway för att omdirigera anslutningarna till Server instanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten. I den centrala Management Console (CMC) kommer du att hitta olika databas versioner som i princip är den version som finns på gatewayen. Mer information finns i kontrol lera [Azure Database for MySQL Server-versioner som stöds](../../../mysql/concepts-supported-versions.md) .

![Distribution av SAP-BOBI på Linux-CMC-inställningar](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Efter installation

### <a name="tomcat-clustering---session-replication"></a>Tomcat klustring-session-replikering

Tomcat stöder klustring av två eller flera program servrar för replikering och redundans av sessioner. SAP BOBI Platform-sessioner serialiseras, en användarsession kan växlas över sömlöst till en annan instans av Tomcat, även om en program server kraschar.

Till exempel om en användare är ansluten till en webb server som Miss lyckas när användaren navigerar i en mapphierarki i SAP BI-program. Med ett korrekt konfigurerat kluster kan användaren fortsätta navigera i mapphierarkin utan att behöva omdirigeras till inloggnings sidan.

I SAP anmärkning [2808640](https://launchpad.support.sap.com/#/notes/2808640), tillhandahålls steg för att konfigurera Tomcat-klustring med hjälp av multicast. Men i Azure stöds inte multicast. För att Tomcat-kluster ska fungera i Azure måste du använda [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP NOTE [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Kontrol lera [Tomcat-klustring med statiskt medlemskap för SAP BUSINESSOBJECTS bi Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) på SAP-bloggen för att konfigurera Tomcat-kluster i Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Belastnings Utjämnings webb nivå för SAP BI-plattform

I SAP BOBI distribution av flera instanser körs Java-webbprogram-servrar (webb nivå) på två eller flera värdar. Om du vill distribuera användar belastningen jämnt på webb servrar kan du använda en belastningsutjämnare mellan slutanvändare och webb servrar. I Azure kan du antingen använda Azure Load Balancer eller Azure Application Gateway för att hantera trafik till dina webb program servrar. Information om varje erbjudande beskrivs i följande avsnitt.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (nätverks baserad belastningsutjämnare)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) är en högpresterande belastningsutjämnare med låg latens Layer 4 (TCP, UDP) som distribuerar trafiken mellan friska Virtual Machines. En belastnings Utjämnings hälso avsökning övervakar en specifik port på varje virtuell dator och distribuerar bara trafik till en eller flera virtuella datorer. Du kan antingen välja en offentlig belastningsutjämnare eller en intern belastningsutjämnare beroende på om du vill att SAP BI-plattformen ska vara tillgänglig från Internet eller inte. Zonen är redundant och säkerställer hög tillgänglighet för Tillgänglighetszoner.

Se det interna Load Balancer avsnittet nedan där webb program servern körs på Port 8080, standard HTTP-port för Tomcat, som kommer att övervakas av hälso avsökningen. Alla inkommande begär Anden som kommer från slutanvändare kommer att omdirigeras till webb program servrarna (azusbosl1 eller azusbosl2) i backend-poolen. Belastningsutjämnaren har inte stöd för TLS/SSL-avslutning (kallas även TLS/SSL-avlastning). Om du använder Azure Load Balancer för att distribuera trafik mellan webb servrar rekommenderar vi att du använder Standard Load Balancer.

> [!NOTE]
> När virtuella datorer utan offentliga IP-adresser placeras i backend-poolen för intern (ingen offentlig IP-adress) standard Azure-belastningsutjämnare, kommer det inte att finnas någon utgående Internet anslutning, om inte ytterligare konfiguration utförs för att tillåta routning till offentliga slut punkter. Mer information om hur du uppnår utgående anslutningar finns i Översikt över [offentliga slut punkter för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Azure Load Balancer att balansera trafik mellan webb servrar](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (belastnings utjämning för webb program)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) tillhandahåller program leverans kontroll (ADC) som en tjänst, som används för att hjälpa till att dirigera användar trafik till en eller flera webb program servrar. Det erbjuder olika belastnings Utjämnings funktioner i Layer 7 som TLS/SSL-avlastning, brand vägg för webbaserade program (WAF), cookie-baserad sessionsgräns och andra för dina program.

I SAP BI-plattformen dirigerar Application Gateway program webb trafik till de angivna resurserna i en backend-pool – azusbosl1 eller azusbos2. Du tilldelar en lyssnare till port, skapar regler och lägger till resurser i en backend-pool. I bilden nedan fungerar Application Gateway med privat klient delens IP-adress (10.31.3.20) som start punkt för användarna, hanterar inkommande TLS/SSL-anslutningar (HTTPS-TCP/443), dekrypterar TLS/SSL och skickar den okrypterade begäran (HTTP-TCP/8080) till servrarna i backend-poolen. Med inbyggd TLS/SSL-terminering behöver vi bara underhålla ett TLS/SSL-certifikat på Application Gateway, vilket fören klar åtgärder.

![Application Gateway att balansera trafik mellan webb servrar](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Om du vill konfigurera Application Gateway för SAP BOBI-webbservern kan du referera till [belastnings utjämning SAP Bobi-webbservrar med Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) på SAP-bloggen.

> [!NOTE]
> Vi rekommenderar att du använder Azure Application Gateway för att belastningsutjämna trafiken till webb servern eftersom den tillhandahåller funktioner som SSL-avlastning, centralisera SSL-hantering för att minska belastningen på kryptering och dekryptering på servern, Round-Robin algoritmen för att distribuera trafik, brand vägg för webbaserade program (WAF), hög tillgänglighet och så vidare.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI-plattform – säkerhetskopiera och Återställ

Säkerhets kopiering och återställning är en process för att skapa regelbundna kopior av data och program till separata platser. Det kan återställas eller återställas till ett tidigare tillstånd om ursprungliga data eller program går förlorade eller skadas. Det är också en viktig komponent i en affärs katastrof återställnings strategi.

För att utveckla omfattande säkerhets kopierings-och återställnings strategi för SAP BOBI-plattformen, identifiera de komponenter som leder till system drift avbrott eller avbrott i programmet. I SAP BOBI-plattformen är säkerhets kopiering av följande komponenter avgörande för att skydda programmet.

- Installations katalog för SAP-BOBI (hanterade Premium diskar)
- Fil lagrings Server (Azure NetApp Files eller Azure Premium-filer)
- CMS-databas (Azure Database for MySQL eller databas på virtuell Azure-dator)

I följande avsnitt beskrivs hur du implementerar säkerhets kopierings-och återställnings strategin för varje komponent på SAP BOBI-plattformen.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Säkerhets kopiering & återställning för SAP-BOBI installations katalog

I Azure är det enklaste sättet att säkerhetskopiera program servrar och alla anslutna diskar genom att använda [Azure Backup](../../../backup/backup-overview.md) -tjänsten. Den tillhandahåller oberoende och isolerade säkerhets kopior för att skydda oavsiktligt förstöring av data på dina virtuella datorer. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalning är enkla, säkerhets kopieringar optimeras och kan enkelt återställas vid behov.

Som en del av säkerhets kopieringen tas ögonblicks bilden och data överförs till Recovery Service-valvet utan påverkan på produktions arbets belastningar. Ögonblicks bilden ger olika konsekvens nivåer enligt beskrivningen i artikeln om [ögonblicks bilder](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) . Du kan också välja att säkerhetskopiera en delmängd av data diskarna i VM med hjälp av selektiva diskar säkerhets kopierings-och återställnings funktioner. Mer information finns i dokumentet om [säkerhets kopiering av virtuella Azure-datorer](../../../backup/backup-azure-vms-introduction.md) och [FAQ – säkerhetskopiera virtuella Azure-datorer](../../../backup/backup-azure-vm-backup-faq.yml).

#### <a name="backup--restore-for-file-repository-server"></a>Säkerhets kopiering & återställning för fil lagrings Server

För **Azure NetApp Files** kan du skapa en ögonblicks bild på begäran och schemalägga automatisk ögonblicks bild med hjälp av ögonblicks bilds principer. Ögonblicks bilds kopior ger en tidpunkts kopia av din ANF-volym. Mer information finns i [Hantera ögonblicks bilder med hjälp av Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**Azure Files** Backup är integrerat med Native [Azure Backup](../../../backup/backup-overview.md) -tjänsten, som centraliserar säkerhets kopierings-och återställnings funktionen tillsammans med säkerhets kopiering av virtuella datorer och fören klar driften. Mer information finns i [säkerhets kopiering av Azure-filresurser](../../../backup/azure-file-share-backup-overview.md) och [faq – säkerhetskopiera Azure Files](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>Säkerhetskopiera & återställning för CMS-databasen

Azure Database of MySQL är ett DBaaS erbjudande i Azure, som automatiskt skapar Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Azure Database of MySQL tar säkerhets kopior av datafilerna och transaktions loggen. Beroende på den maximala lagrings storleken som stöds, är det antingen fullständiga och differentiella säkerhets kopieringar (4 TB max lagrings servrar) eller säkerhets kopiering av ögonblicks bilder (upp till 16 TB max lagrings servrar). Med dessa säkerhets kopieringar kan du återställa en server vid varje tidpunkt inom din konfigurerade kvarhållningsperiod för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopior är sju dagar, vilket du kan välja att [Konfigurera](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) upp till tre dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

De här säkerhetskopierade filerna är inte användar-exponerade och kan inte exporteras. Dessa säkerhets kopior kan bara användas för återställnings åtgärder i Azure Database for MySQL. Du kan använda [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) för att kopiera en databas. Mer information finns i [säkerhets kopiering och återställning i Azure Database for MySQL](../../../mysql/concepts-backup.md).

Om databasen är installerad på Virtual Machines kan du använda standard verktyg för säkerhets kopiering eller [Azure Backup](../../../backup/sap-hana-db-about.md) för Hana-databas. Även om Azure-tjänsterna och-verktygen inte uppfyller ditt krav kan du använda andra verktyg för säkerhets kopiering eller skript för att skapa säkerhets kopior av diskar.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Tillförlitlighet för SAP BusinessObjects BI-plattformen

SAP BusinessObjects BI-plattformen innehåller olika nivåer, som är optimerade för vissa uppgifter och åtgärder. När en komponent från en annan nivå blir otillgänglig blir SAP BOBI-programmet antingen otillgängligt eller så fungerar inte vissa funktioner i programmet. Därför måste du se till att varje nivå är utformad för att vara tillförlitlig för att hålla program driften utan avbrott i verksamheten.

Det här avsnittet fokuserar på följande alternativ för SAP BOBI-plattform –

- **Hög tillgänglighet:** En hög tillgänglig plattform har minst två av allt i Azure-regionen för att hålla programmet i drift om en av servrarna blir otillgänglig.
- **Haveri beredskap:** Det är en process för att återställa dina program funktioner om det förekommer katastrof förlust som hela Azure-regionen blir otillgänglig på grund av en natur katastrof.

Implementeringen av den här lösningen varierar beroende på system konfigurationens natur i Azure. Kunden behöver skräddarsy hög tillgänglighet och katastrof återställnings lösning baserat på deras affärs behov.

### <a name="high-availability"></a>Hög tillgänglighet

Hög tillgänglighet syftar på en uppsättning tekniker som kan minimera IT-avbrott genom att tillhandahålla affärs kontinuitet för program/tjänster via redundanta, feltoleranta eller redundansväxlings-skyddade komponenter i samma data Center. I vårt fall finns data centren inom en Azure-region. Artikeln [hög tillgänglighets arkitektur och scenarier för SAP](sap-high-availability-architecture-scenarios.md) ger en inledande insikt om olika tekniker och rekommendationer för hög tillgänglighet som erbjuds på Azure för SAP-program, som kommer att följa anvisningarna i det här avsnittet.

Baserat på storleks resultatet av SAP BOBI-plattformen måste du utforma liggande och fastställa distributionen av BI-komponenter i Azure Virtual Machines och undernät. Nivån av redundans i den distribuerade arkitekturen beror på det nödvändiga målet för återställnings tid för företag (RTO) och återställnings punkt mål. SAP BOBI-plattformen innehåller olika nivåer och komponenter på varje nivå för att uppnå redundans. Om en komponent Miss lyckas finns det ingen anledning att störa ditt SAP BOBI-program. Exempel:

- Redundanta program servrar som BI-programservrar och webb server
- Unika komponenter som CMS-databasen, fil lagrings Server Load Balancer

I följande avsnitt beskrivs hur du uppnår hög tillgänglighet för varje komponent i SAP BOBI-plattformen.

#### <a name="high-availability-for-application-servers"></a>Hög tillgänglighet för program servrar

För BI-och webb program servrar, oavsett om de är installerade separat eller tillsammans, behöver ingen specifik lösning för hög tillgänglighet. Du kan uppnå hög tillgänglighet genom redundans, det vill säga genom att konfigurera flera instanser av BI-och webb servrar i olika Azure-Virtual Machines.

För att minska effekten av drift stopp på grund av en eller flera händelser, är det lämpligt att följa övningen för hög tillgänglighet för program servrarna som körs på flera virtuella datorer.

- Använd Tillgänglighetszoner för att skydda data Center problem.
- Konfigurera flera Virtual Machines i en tillgänglighets uppsättning för redundans.
- Använd Managed Disks för virtuella datorer i en tillgänglighets uppsättning.
- Konfigurera varje program nivå i separata tillgänglighets uppsättningar.

Mer information finns i [Hantera tillgängligheten för virtuella Linux-datorer](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>Hög tillgänglighet för CMS-databasen

Om du använder Azure Database as a Service (DBaaS)-tjänsten för CMS-databasen tillhandahålls ramverket för hög tillgänglighet som standard. Du behöver bara välja den region och tjänst som är av hög tillgänglighet, redundans och återhämtnings kapacitet utan att du behöver konfigurera några ytterligare komponenter. Mer information om SLA för DBaaS-erbjudande som stöds på Azure finns [i hög tillgänglighet i Azure Database for MySQL](../../../mysql/concepts-high-availability.md) och [hög tillgänglighet för Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

För andra DBMS-distributioner för CMS-databasen hänvisar du till [DBMS-distributions guider för SAP-arbetsbelastningar](dbms_guide_general.md), som ger insikt om olika DBMS-distributioner och dess metoder för att uppnå hög tillgänglighet

#### <a name="high-availability-for-file-repository-server"></a>Hög tillgänglighet för fil lagrings Server

Fil databas servern (FRS) refererar till disk katalogerna där innehåll som rapporter, universum och anslutningar lagras. Den delas mellan alla program servrar i systemet. Så du måste se till att den har hög tillgänglighet.

På Azure kan du antingen välja [Azure Premium-filer](../../../storage/files/storage-files-introduction.md) eller [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) för fil resurs som har utformats för hög tillgänglighet och hög tålighet. Mer information finns i avsnittet om [redundans](../../../storage/files/storage-files-planning.md#redundancy) för Azure Files.

> [!NOTE]
> SMB-protokollet för Azure Files är allmänt tillgängligt, men NFS-protokollet stöd för Azure Files är för närvarande en för hands version. Mer information finns [i stöd för NFS 4,1 för Azure Files nu i för hands version](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Eftersom den här fil resurs tjänsten inte är tillgänglig i alla regioner, se till att det finns [produkter som är tillgängliga efter regions](https://azure.microsoft.com/en-us/global-infrastructure/services/) plats för att ta reda på uppdaterad information. Om tjänsten inte är tillgänglig i din region kan du skapa en NFS-server som du kan använda för att dela fil systemet till SAP BOBI-programmet. Men du måste också tänka på dess hög tillgänglighet.

#### <a name="high-availability-for-load-balancer"></a>Hög tillgänglighet för belastningsutjämnare

Om du vill distribuera trafik mellan webb servrar kan du antingen använda Azure Load Balancer eller Azure Application Gateway. Redundans för någon av belastningsutjämnaren kan uppnås baserat på den SKU du väljer för distribution.

- För Azure Load Balancer kan redundans uppnås genom att konfigurera Standard Load Balancer-frontend som zon-redundant. Mer information finns i [standard Load Balancer och Tillgänglighetszoner](../../../load-balancer/load-balancer-standard-availability-zones.md)
- För Application Gateway kan hög tillgänglighet uppnås baserat på vilken typ av nivå som valts under distributionen.
  - v1 SKU stöder scenarier med hög tillgänglighet när du har distribuerat två eller fler instanser. Azure distribuerar dessa instanser över uppdaterings-och fel domäner för att säkerställa att instanserna inte fungerar samtidigt. Så med denna SKU kan redundans uppnås inom zonen
  - v2-SKU ser automatiskt till att nya instanser sprids mellan fel domäner och uppdaterings domäner. Om du väljer zon redundans sprids även de nyaste instanserna över tillgänglighets zoner för att erbjuda zonindelade-felåterhämtning. Mer information finns i [autoskalning och Zone-redundant Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Referens arkitektur med hög tillgänglighet för SAP BusinessObjects BI-plattformen

Referens arkitekturen nedan beskriver installationen av SAP BOBI-plattformen med hjälp av tillgänglighets uppsättning, som ger VM-redundans och tillgänglighet i zonen. Arkitekturen demonstrerar användningen av olika Azure-tjänster som Azure Application Gateway, Azure NetApp Files och Azure Database for MySQL för SAP BOBI-plattformen som erbjuder inbyggd redundans, vilket minskar komplexiteten med att hantera olika lösningar för hög tillgänglighet.

I bilden nedan är inkommande trafik (HTTPS-TCP/443) belastningsutjämnad med Azure Application Gateway v1 SKU, som är hög tillgänglig vid distribution på två eller fler instanser. Flera instanser av webb server, hanterings servrar och bearbetnings servrar distribueras i separata Virtual Machines för att uppnå redundans och varje nivå distribueras i separata tillgänglighets uppsättningar. Azure NetApp Files har inbyggd redundans i Data Center, så att ANF-volymerna för fil lagrings servern är hög tillgängliga. CMS-databasen är etablerad på Azure Database for MySQL (DBaaS) med hög tillgänglighet. Mer information finns [i hög tillgänglighet i Azure Database for MySQL](../../../mysql/concepts-high-availability.md) guide.

![SAP BusinessObjects BI Platform-redundans med tillgänglighets uppsättningar](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Ovanstående arkitektur ger insikt om hur SAP BOBI-distribution på Azure kan göras. Men det beskriver inte alla möjliga konfigurations alternativ för SAP BOBI-plattformen på Azure. Kunden kan skräddarsy sin distribution utifrån deras affärs behov genom att välja olika produkter/tjänster för olika komponenter som Load Balancer, fil lagrings Server och DBMS.

I flera Azure-regioner erbjuds Tillgänglighetszoner vilket innebär att den har oberoende utbud av ström källor, kylning och nätverk. Det gör det möjligt för kunden att distribuera program över två eller tre tillgänglighets zoner. För kunder som vill uppnå hög tillgänglighet i AZs kan du distribuera SAP BOBI-plattformen över tillgänglighets zoner och se till att varje komponent i programmet är zon-redundant.

### <a name="disaster-recovery"></a>Haveriberedskap

I instruktionen i det här avsnittet beskrivs strategin för att tillhandahålla haveri beredskaps skydd för SAP BOBI-plattformen. Det kompletterar [haveri beredskap för SAP](../../../site-recovery/site-recovery-sap.md) -dokument, som representerar de primära resurserna för övergripande SAP haveri beredskap.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referens arkitektur för haveri beredskap för SAP BusinessObjects BI-plattformen

Den här referens arkitekturen kör distribution med flera instanser av SAP BOBI-plattformen med redundanta program servrar. Vid haveri beredskap bör du redundansväxla alla nivåer till en sekundär region. Varje nivå använder en annan strategi för att tillhandahålla katastrof återställnings skydd.

![Katastrof återställning för SAP BusinessObjects BI-plattformen](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Lastbalanserare

Load Balancer används för att distribuera trafik mellan webb program servrar i SAP BOBI-plattformen. För att uppnå DR för Azure Application Gateway implementerar du parallellt installationen av Application Gateway på den sekundära regionen.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Virtuella datorer som kör webb-och BI-programservrar

Azure Site Recovery-tjänsten kan användas för att replikera Virtual Machines som kör webb-och BI-programservrar i den sekundära regionen. Den replikerar servrarna i den sekundära regionen så att du enkelt kan redundansväxla till din replikerade miljö när haverier och avbrott uppstår och fortsätta att arbeta

#### <a name="file-repository-servers"></a>Fil lagrings servrar

- **Azure NetApp Files** tillhandahåller NFS-och SMB-volymer så att alla filbaserade kopierings verktyg kan användas för att replikera data mellan Azure-regioner. Mer information om hur du kopierar ANF volym i en annan region finns i [vanliga frågor och svar om Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Du kan använda Azure NetApp Files replikering mellan regioner, som för närvarande är en för [hands version](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) som använder NetApp SnapMirror® Technology. Endast ändrade block skickas över nätverket i ett komprimerat, effektivt format. Den här tillverkarspecifika tekniken minimerar mängden data som krävs för att replikera över regionerna, vilket sparar kostnader för data överföring. Den förkortar också replikerings tiden så att du kan få ett mindre återställnings punkt mål (återställnings punkt mål). Mer information hittar du i [krav och överväganden för att använda replikering över flera regioner](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) .

- **Azure Premium-filer** har endast stöd för lokalt REDUNDANT (LRS) och Zone-redundant lagring (ZRS). I DR-strategin för Azure Premium-filer kan du använda [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) eller [Azure PowerShell](/powershell/module/az.storage/) för att kopiera filer till ett annat lagrings konto i en annan region. Mer information finns i [haveri beredskap och redundans för lagrings konton](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS-databas

Azure Database for MySQL tillhandahåller flera alternativ för att återställa databasen om det förekommer en katastrof. Välj lämpligt alternativ som passar ditt företag.

- Aktivera Läs repliker över flera regioner för att förbättra verksamhets kontinuiteten och Disaster Recovery-planeringen. Du kan replikera från käll servern till upp till fem repliker. Läs repliker uppdateras asynkront med MySQL: s binära logg teknik för replikering. Repliker är nya servrar som du hanterar precis som vanliga Azure Database for MySQL-servrar. Läs mer om Läs repliker, tillgängliga regioner, begränsningar och hur du växlar över från artikeln om att [läsa repliker](../../../mysql/concepts-read-replicas.md).

- Använd Azure Database for MySQL geo-Restore-funktionen som återställer servern med geo-redundanta säkerhets kopieringar. Dessa säkerhets kopior är tillgängliga även när den region där din server finns är offline. Du kan återställa från dessa säkerhets kopior till en annan region och ta servern online igen.

  > [!NOTE]
  > Geo-återställning är bara möjlig om du har upprättat servern med Geo-redundant lagring av säkerhets kopior. Det finns inte stöd för att ändra **redundans alternativen för säkerhets kopiering** efter att servern har skapats. Mer information finns i artikeln om [reserv-redundans](../../../mysql/concepts-backup.md#backup-redundancy-options) .

Följande är en rekommendation för haveri beredskap för varje nivå som används i det här exemplet.

| Plattforms nivåer för SAP BOBI   | Rekommendation                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Parallell installation av Application Gateway i sekundär region                                                |
| Webb program servrar   | Replikera med hjälp av Site Recovery                                                                         |
| BI-programservrar    | Replikera med hjälp av Site Recovery                                                                         |
| Azure NetApp Files        | Filbaserat kopierings verktyg för att replikera data till sekundär region **eller** ANF mellan regioner (för hands version) |
| Azure Database for MySQL  | Kors region Läs repliker **eller** Återställ säkerhets kopior från geo-redundanta säkerhets kopieringar.                             |

## <a name="next-steps"></a>Nästa steg

- [Konfigurera katastrof återställning för en distribution med flera nivåer av SAP-appar](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines planera och implementera SAP](planning-guide.md)
- [Azure Virtual Machines distribution för SAP](deployment-guide.md)
- [Azure Virtual Machines DBMS-distribution för SAP](./dbms_guide_general.md)
