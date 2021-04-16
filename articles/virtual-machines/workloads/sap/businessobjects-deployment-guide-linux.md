---
title: SAP BusinessObjects BI-plattformsdistribution på Azure för Linux-| Microsoft Docs
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
ms.openlocfilehash: b16a2d9f779232e59eb883f6a254be22990f5c78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520028"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Distributionsguide för SAP BusinessObjects BI-plattform för Linux i Azure

I den här artikeln beskrivs strategin för att distribuera SAP BOBI Platform på Azure för Linux. I det här exemplet konfigureras två virtuella datorer Premium SSD Managed Disks som installationskatalog. Azure Database for MySQL används för CMS-databasen och Azure NetApp Files för fildatabasservern delas mellan båda servrarna. Tomcat Java-standardwebbappen och BI Platform-programmet installeras tillsammans på båda virtuella datorerna. För att belastningsutjämna användarbegäran Application Gateway som har inbyggda TLS-/SSL-avlastningsfunktioner.

Den här typen av arkitektur är effektiv för små distributions- eller icke-produktionsmiljöer. För produktion eller storskalig distribution kan du ha separata värdar för webbprogram och kan också ha flera BOBI-programvärdar så att servern kan bearbeta mer information.

![SAP BOBI-distribution på Azure för Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

I det här exemplet används produktversionen nedan och filsystemlayouten

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (version: 8.0.15)
- MySQL C API Connector – libmysqlclient (version: 6.1.11)

| Filsystem        | Description                                                                                                               | Storlek (GB)             | Ägare  | Group  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Filsystemet för installation av SAP BOBI-instans, tomcat-standardwebbprogram och databasdrivrutiner (om det behövs) | Riktlinjer för SAP-storlek | bl1adm | sapsys | Hanterad Premium-disk – SSD |
| /usr/sap/frsinput  | Monteringskatalogen är för delade filer över alla BOBI-värdar som ska användas som indatafildatabaskatalog  | Affärs behov         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Monteringskatalogen är för delade filer över alla BOBI-värdar som ska användas som katalog för utdatafilslager | Affärs behov         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuera en virtuell Linux-dator via Azure Portal

I det här avsnittet skapar vi två virtuella datorer (VM) med linux-operativsystemavbildningen (OS) för SAP BOBI Platform. De avancerade stegen för att skapa Virtual Machines är följande :

1. Skapa en [resursgrupp](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Skapa en [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Använd inte ett enda undernät för alla Azure-tjänster i SAP BI-plattformsdistributionen. Baserat på SAP BI Platform-arkitekturen måste du skapa flera undernät. I den här distributionen skapar vi tre undernät – programundernät, undernät för arkivlager och Application Gateway undernät.
   - I Azure Application Gateway och Azure NetApp Files alltid finnas i separata undernät. Se [Azure Application Gateway](../../../application-gateway/configuration-overview.md) och [riktlinjer för Azure NetApp Files artikeln Nätverksplanering](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) för mer information.

3. Skapa en tillgänglighetsuppsättning.

   - Om du vill uppnå redundans för varje nivå i distribution med flera instanser placerar du virtuella datorer för varje nivå i en tillgänglighetsuppsättning. Se till att du separerar tillgänglighetsuppsättningarna för varje nivå baserat på din arkitektur.

4. Skapa virtuell dator 1 **(azusbosl1).**

   - Du kan antingen använda en anpassad avbildning eller välja en avbildning Azure Marketplace. Se Distribuera [en virtuell dator från Azure Marketplace för SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) eller Distribuera en virtuell dator med en anpassad [avbildning för SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) baserat på dina behov.

5. Skapa virtuell dator 2 **(azusbosl2).**
6. Lägg till Premium SSD disk. Den kommer att användas som SAP BOBI-installationskatalog.

## <a name="provision-azure-netapp-files"></a>Etablera Azure NetApp Files

Innan du fortsätter med konfigurationen för Azure NetApp Files bekanta dig med dokumentationen för Azure [NetApp Files.](../../../azure-netapp-files/azure-netapp-files-introduction.md)

Azure NetApp Files finns i flera [Azure-regioner.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Kontrollera om din valda Azure-region erbjuder Azure NetApp Files.

Använd [Azure NetApp Files tillgänglighet per Azure-region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) för att kontrollera tillgängligheten för Azure NetApp Files per region.

Begär registrering till Azure NetApp Files genom att gå till [Registrera för Azure NetApp Files innan](../../../azure-netapp-files/azure-netapp-files-register.md) du distribuerar Azure NetApp Files.

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files resurser

Följande anvisningar förutsätter att du redan har distribuerat ditt virtuella [Azure-nätverk](../../../virtual-network/virtual-networks-overview.md). De Azure NetApp Files och virtuella datorerna, där Azure NetApp Files-resurserna ska monteras, måste distribueras i samma virtuella Azure-nätverk eller i peer-erade virtuella Azure-nätverk.

1. Om du inte redan har distribuerat resurserna begär du [registrering till Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Skapa ett NetApp-konto i din valda Azure-region genom att följa anvisningarna i [Skapa ett NetApp-konto](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3. Konfigurera en Azure NetApp Files kapacitetspool genom att följa anvisningarna [i Konfigurera en Azure NetApp Files-kapacitetspool](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

   - Sap BI-plattformsarkitekturen som presenteras i den här artikeln  använder en Azure NetApp Files en kapacitetspool på Premium-tjänstnivå. För SAP BI-fillagringsplatsservern på Azure rekommenderar vi att du använder Azure NetApp Files *Premium-* eller [Ultra-servicenivå.](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 

4. Delegera ett undernät till Azure NetApp Files enligt beskrivningen i anvisningarna i [Delegera ett undernät att Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Distribuera Azure NetApp Files genom att följa anvisningarna i [Skapa en NFS-volym för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   ANF-volymen kan distribueras som NFSv3 och NFSv4.1, eftersom båda protokollen stöds för SAP BOBI Platform. Distribuera volymerna i respektive Azure NetApp Files undernät. IP-adresserna för Azure NetApp-volymerna tilldelas automatiskt.

Tänk på att resurserna Azure NetApp Files Azure och de virtuella Azure-datorerna måste finnas i samma virtuella Azure-nätverk eller i peer-ade virtuella Azure-nätverk. Till exempel är azusbobi-frsinput, azusbobi-frsoutput volymnamnen och nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput är filsökvägarna för Azure NetApp Files Volumes.

- Volym azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volym azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Att tänka på

Tänk på följande när du Azure NetApp Files för SAP BOBI Platform File Repository Server:

1. Den minsta kapacitetspoolen är 4 tebibyte (TiB).
2. Den minsta volymstorleken är 100 gibibyte (GiB).
3. Azure NetApp Files och alla virtuella datorer där Azure NetApp Files-volymerna ska monteras måste finnas i samma virtuella Azure-nätverk eller i [peer-ade](../../../virtual-network/virtual-network-peering-overview.md) virtuella nätverk i samma region. Azure NetApp Files åtkomst via VNET-peering i samma region stöds nu. Azure NetApp-åtkomst via global peering stöds inte ännu.
4. Det valda virtuella nätverket måste ha ett undernät som har delegerats till Azure NetApp Files.
5. Med den Azure NetApp Files [exportprincipen](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kan du styra tillåtna klienter, åtkomsttypen (skrivskyddade, skrivskyddade och så vidare).
6. Funktionen Azure NetApp Files är inte zonmedveten ännu. För närvarande distribueras inte funktionen i alla tillgänglighetszoner i en Azure-region. Tänk på de potentiella fördröjningskonsekvenserna i vissa Azure-regioner.
7. Azure NetApp Files kan distribueras som NFSv3- eller NFSv4.1-volymer. Båda protokollen stöds för SAP BI-plattformsprogram.

## <a name="configure-file-systems-on-linux-servers"></a>Konfigurera filsystem på Linux-servrar

Stegen i det här avsnittet använder följande prefix:

**[A]**: Steget gäller för alla värdar

### <a name="format-and-mount-sap-file-system"></a>Formatera och montera SAP-filsystem

1. **[A] Visa** en lista över alla anslutna diskar

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

2. **[A]** Formatera blockenhet för /usr/sap

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A] Skapa** monteringskatalog

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Hämta UUID för blockenhet

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Behåll monteringsposten för filsystemet i /etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** Montera filsystem

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

### <a name="mount-azure-netapp-files-volume"></a>Monteringsvolym Azure NetApp Files monteringsvolym

1. **[A]** Skapa monteringskataloger

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Konfigurera klientoperativsystemet så att det stöder NFSv4.1-montering (gäller endast om du använder **NFSv4.1)**

   Om du använder Azure NetApp Files med NFSv4.1-protokollet kör du följande konfiguration på alla virtuella datorer, där Azure NetApp Files NFSv4.1-volymer måste monteras.

   **Verifiera NFS-domäninställningar**

   Kontrollera att domänen är konfigurerad som standarddomänen Azure NetApp Files det vill  **säga,** defaultv4iddomain.com och mappningen är inställd på **ingen**.

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
   > Se till att ange NFS-domänen i /etc/idmapd.conf på den virtuella datorn så att den matchar standarddomänkonfigurationen på Azure NetApp Files: **defaultv4iddomain.com**. Om det finns ett matchningsfel mellan domänkonfigurationen på NFS-klienten (dvs. den virtuella datorn) och NFS-servern, d.v.s. Azure NetApp-konfigurationen, visas behörigheterna för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som ingen.

   Verifiera `nfs4_disable_idmapping` . Det ska vara inställt på **Y**. Kör monteringskommandot `nfs4_disable_idmapping` för att skapa katalogstrukturen där finns. Du kommer inte att kunna skapa katalogen manuellt under /sys/modules eftersom åtkomst är reserverad för kerneln/drivrutinerna.

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

3. **[A] Lägg** till monteringsposter

   Om du använder NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Om du använder NFSv4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A] Montera** NFS-volymer

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

## <a name="configure-cms-database---azure-database-for-mysql"></a>Konfigurera CMS-databas – Azure Database for MySQL

Det här avsnittet innehåller information om hur du etablerar Azure Database for MySQL med Azure Portal. Den innehåller också anvisningar om hur du skapar CMS- och granskningsdatabaser för SAP BOBI-plattformen och ett användarkonto för att få åtkomst till databasen.

Riktlinjerna gäller endast om du använder Azure DB for MySQL. Information om andra databaser finns i SAP- eller databasspecifik dokumentation.

### <a name="create-an-azure-database-for-mysql"></a>Skapa en Azure-databas för MySQL

Logga in på Azure Portal och följ stegen som anges i den här [snabbstartsguiden för Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Några saker att notera vid etablering Azure Database for MySQL –

1. Välj samma region för den Azure Database for MySQL där dina SAP BI Platform-programservrar körs.

2. Välj en DB-version som stöds baserat [på PAM (Product Availability Matrix) för SAP BI som är](https://support.sap.com/pam) specifik för din SAP BOBI-version. Följ samma kompatibilitetsriktlinjer som gäller för MySQL AB i SAP PAM

3. I "compute+storage" väljer du **Konfigurera server** och väljer lämplig prisnivå baserat på storleksutdata.

4. **Automatisk lagringsdefinering** är aktiverat som standard. Tänk på att [Storage](../../../mysql/concepts-pricing-tiers.md#storage) bara kan skalas upp, inte ned.

5. Som standard är **kvarhållningsperioden sju** dagar, men du kan [välja att konfigurera den](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) i upp till 35 dagar.

6. Säkerhetskopieringar av Azure Database for MySQL är lokalt redundanta som standard, så om du vill  ha serversäkerhetskopior i geo-redundant lagring väljer du Geografiskt redundant från **Redundansalternativ för säkerhetskopiering.**

> [!NOTE]
> Det finns [inte stöd för att ändra redundansalternativ](../../../mysql/concepts-backup.md#backup-redundancy-options) för säkerhetskopiering när servern har skapats.

### <a name="configure-connection-security"></a>Konfigurera anslutningssäkerhet

Som standard skyddas den server som skapas med en brandvägg och är inte tillgänglig offentligt. Om du vill ge åtkomst till det virtuella nätverket där SAP BI Platform-programservrar körs följer du stegen nedan –  

1. Gå till serverresurser i Azure Portal och välj **Anslutningssäkerhet på** menyn till vänster för serverresursen.
2. Välj **Ja** för **Att tillåta åtkomst till Azure-tjänster.**
3. Under VNET-regler väljer du **Lägga till befintligt virtuellt nätverk**. Välj det virtuella nätverket och undernätet för SAP BI Platform-programservern. Du måste också ge åtkomst till Jump Box eller andra servrar där du kan ansluta [MySQL Workbench](../../../mysql/connect-workbench.md) till Azure Database for MySQL. MySQL Workbench används för att skapa CMS- och granskningsdatabasen
4. När virtuella nätverk har lagts till väljer du **Spara**.

### <a name="create-cms-and-audit-database"></a>Skapa CMS och granskningsdatabas

1. Ladda ned och installera MySQL Workbench från [MySQL-webbplatsen](https://dev.mysql.com/downloads/workbench/). Se till att du installerar MySQL Workbench på den server som har åtkomst Azure Database for MySQL.

2. Anslut till servern med MySQL Workbench. Följ anvisningarna i den här [artikeln.](../../../mysql/connect-workbench.md#get-connection-information) Om anslutningstestet lyckas får du följande meddelande :

   ![SQL Workbench-anslutning](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. På fliken SQL-fråga kör du nedanstående fråga för att skapa schemat för CMS- och granskningsdatabasen.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Skapa användarkonto för att ansluta till schemat

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

5. Kontrollera behörigheter och roller för MySQL-användarkontot

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

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Installera MySQL C API-anslutningsprogram (libmysqlclient) på Linux-servern

För att SAP BOBI-programservern ska få åtkomst till databasen krävs databasklient/drivrutiner. MySQL C API Connector för Linux måste användas för åtkomst till CMS- och granskningsdatabaser. ODBC-anslutning till CMS-databasen stöds inte. Det här avsnittet innehåller anvisningar om hur du ställer in MySQL C API Connector på Linux.

1. Se artikeln [MySQL-drivrutiner och](../../../mysql/concepts-compatibility.md) hanteringsverktyg som är kompatibla med Azure Database for MySQL, som beskriver de drivrutiner som är kompatibla med Azure Database for MySQL. Sök efter **drivrutinen MySQL Connector/C (libmysqlclient)** i artikeln.

2. Se den här länken [för](https://downloads.mysql.com/archives/c-c/) att ladda ned drivrutiner.

3. Välj operativsystemet och ladda ned rpm-paketet för delade komponenter för MySQL Connector. I det här exemplet används anslutningsappsversionen mysql-connector-c-shared-6.1.11.

4. Installera anslutningsappen i alla SAP BOBI-programinstanser.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Kontrollera sökvägen för libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Ange LD_LIBRARY_PATH att peka `/usr/lib64` på katalogen för användarkontot som ska användas för installation.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Förberedelse av server

Stegen i det här avsnittet använder följande prefix:

**[A]**: Steget gäller för alla värdar.

1. **[A]** Baserat på versionen av Linux (SLES eller RHEL) måste du ange kernelparametrar och installera nödvändiga bibliotek. Se avsnittet **Systemkrav** i [Installationsguide för Business Intelligence-plattformen för Unix.](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)

2. **[A]** Kontrollera att tidszonen på datorn är korrekt inställd. Se avsnittet [Ytterligare Unix- och Linux-krav](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) i installationsguiden.

3. **[A]** Skapa användarkonto **(bl1** adm) och grupp (sapsys) som programvarans bakgrundsprocesser kan köras under. Använd det här kontot för att köra installationen och programvaran. Kontot kräver inte rotprivilegier.

4. **[A]** Ange en miljö för användarkonto **(bl1** adm) för att använda ett UTF-8-språk som stöds och se till att konsolprogramvaran stöder UTF-8-teckenuppsättningar. För att säkerställa att operativsystemet använder rätt språk anger du miljövariablerna LC_ALL och LANG till önskat språk i användarmiljön **(bl1** adm).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Konfigurera användarkonto (**bl1** adm).

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

6. Ladda ned och extrahera media för SAP BusinessObjects BI-plattformen från SAP Service Marketplace.

## <a name="installation"></a>Installation

Kontrollera språk för användarkonto **bl1** adm på servern

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Gå till media för SAP BusinessObjects BI-plattformen och kör kommandot nedan med **bl1** adm-användare –

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Följ [installationsguiden för SAP BOBI-plattformen](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) för Unix, specifikt för din version. Några saker att notera när du installerar SAP BOBI Platform.

- På **skärmen Konfigurera produktregistrering** kan du antingen använda en tillfällig licensnyckel för SAP BusinessObjects Solutions från SAP Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) eller generera en licensnyckel på SAP Service Marketplace

- På **skärmen Välj installationstyp** väljer du **Fullständig** installation på den första servern (azusbosl1), för annan server (azusbosl2) väljer du **Anpassad/Expandera,** vilket expanderar den befintliga BOBI-konfigurationen.

- På **skärmen Välj standard eller Befintlig** databas väljer du Konfigurera en befintlig **databas,** vilket uppmanar dig att välja CMS och granskningsdatabas. Välj **MySQL som** CMS-databastyp och Granska databastyp.

  Du kan också välja Ingen granskningsdatabas om du inte vill konfigurera granskning under installationen.

- Välj lämpliga alternativ på **skärmen Välj Java Web Application Server** baserat på din SAP BOBI-arkitektur. I det här exemplet har vi valt alternativ 1, som installerar tomcat-servern på samma SAP BOBI Platform.

- Ange CMS-databasinformation i **Konfigurera CMS-databasdatabas – MySQL**. Exempelindata för CMS-databasinformation för Linux-installation. Azure Database for MySQL används på standardport 3306
  
  ![SAP BOBI-distribution på Linux – CMS-databas](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Valfritt) Ange Granskningsdatabasinformation i **Konfigurera databas för granskningsdatabas – MySQL**. Exempelindata för Granska databasinformation för Linux-installation.

  ![SAP BOBI-distribution på Linux – Granska databas](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Slutför installationen genom att följa instruktionerna och ange nödvändiga indata.

För distribution med flera instanser kör du installationsprogrammet på den andra värden (azusbosl2). Under **skärmen Välj installationstyp** väljer du **Anpassad/Expandera,** vilket expanderar den befintliga BOBI-konfigurationen.

I Azure Database for MySQL-erbjudandet används en gateway för att omdirigera anslutningarna till serverinstanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten. I den centrala hanteringskonsolen (CMC) hittar du alltså en annan databasversion som i princip är den version som angetts på gatewayen. Mer [information finns Azure Database for MySQL serverversioner](../../../mysql/concepts-supported-versions.md) som stöds.

![SAP BOBI-distribution på Linux – CMC-inställningar](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Efter installationen

### <a name="tomcat-clustering---session-replication"></a>Tomcat-klustring – sessionsreplikering

Tomcat stöder klustring av två eller flera programservrar för sessionsreplikering och redundans. SAP BOBI-plattformssessioner serialiseras. En användarsession kan sömlöst växla över till en annan instans av tomcat, även om en programserver misslyckas.

Till exempel om en användare är ansluten till en webbserver som misslyckas när användaren navigerar i en mapphierarki i SAP BI-programmet. Med ett korrekt konfigurerat kluster kan användaren fortsätta navigera i mapphierarkin utan att omdirigeras till inloggningssidan.

I SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640)tillhandahålls steg för att konfigurera tomcat-klustring med multicast. Men i Azure stöds inte multicast. För att tomcat-kluster ska fungera i Azure måste du därför använda [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP-anteckning [2764907).](https://launchpad.support.sap.com/#/notes/2764907) Kontrollera [Tomcat-klustring med statiskt medlemskap för SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) på SAP-bloggen för att konfigurera tomcat-kluster i Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Webbnivå för belastningsutjämning för SAP BI-plattformen

I SAP BOBI-distribution med flera instanser körs Java Web Application-servrar (webbnivå) på två eller flera värdar. Om du vill fördela användarbelastningen jämnt mellan webbservrar kan du använda en lastbalanserare mellan slutanvändare och webbservrar. I Azure kan du antingen använda Azure Load Balancer eller Azure Application Gateway för att hantera trafik till dina webbprogramservrar. Information om varje erbjudande förklaras i följande avsnitt.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure-lastbalanserare (nätverksbaserad lastbalanserare)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) är en lastbalanserare med höga prestanda och låg latens layer 4 (TCP, UDP) som distribuerar trafik mellan felfria Virtual Machines. En hälsoavsökning för lastbalanserare övervakar en viss port på varje virtuell dator och distribuerar endast trafik till en eller flera virtuella datorer i drift. Du kan antingen välja en offentlig lastbalanserare eller en intern lastbalanserare beroende på om du vill att SAP BI-plattformen ska vara tillgänglig från Internet eller inte. Dess zonredundant, vilket garanterar hög tillgänglighet Tillgänglighetszoner.

Se avsnittet Internal Load Balancer i bilden nedan där webbprogramservern körs på port 8080, standardport för Tomcat HTTP, som övervakas av hälsoavsökningen. Så alla inkommande begäranden som kommer från slutanvändarna omdirigeras till webbprogramservrarna (azusbosl1 eller azusbosl2) i serverdelspoolen. Lastbalanserare stöder inte TLS/SSL-avslutning (även kallat TLS/SSL-avlastning). Om du använder Azure Load Balancer för att distribuera trafik mellan webbservrar rekommenderar vi att du använder Standard Load Balancer.

> [!NOTE]
> När virtuella datorer utan offentliga IP-adresser placeras i serverpoolen för intern (ingen offentlig IP-adress) Standard Azure-lastbalanserare, kommer det inte att finnas någon utgående Internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter. Mer information om hur du uppnår utgående anslutningar finns i Offentlig slutpunktsanslutning [för Virtual Machines med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet.](high-availability-guide-standard-load-balancer-outbound-connections.md)

![Azure Load Balancer för att balansera trafik mellan webbservrar](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (lastbalanserare för webbprogram)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) tillhandahåller ADC (Application Delivery Controller) som en tjänst, som används för att hjälpa programmet att dirigera användartrafik till en eller flera webbprogramservrar. Den erbjuder olika layer 7-belastningsutjämningsfunktioner som TLS/SSL-avlastning, Web Application Firewall (WAF), cookiebaserad sessionstillhörighet och andra för dina program.

I SAP BI Platform dirigerar Application Gateway programwebbtrafik till de angivna resurserna i en backend-pool – azusbosl1 eller azusbos2. Du tilldelar en lyssnare till porten, skapar regler och lägger till resurser i en backend-pool. I bilden nedan fungerar Application Gateway med en privat IP-adress för klienter (10.31.3.20) som startpunkt för användarna, hanterar inkommande TLS/SSL-anslutningar (HTTPS - TCP/443), dekrypterar TLS/SSL och vidarebefordrar den okrypterade begäran (HTTP - TCP/8080) till servrarna i serverpoolen. Med inbyggd TLS/SSL-avslutningsfunktion behöver vi bara underhålla ett TLS/SSL-certifikat på Application Gateway, vilket förenklar driften.

![Application Gateway för att balansera trafik mellan webbservrar](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Om du Application Gateway för SAP BOBI-webbserver kan du gå till Belastningsutjämning [av SAP BOBI-webbservrar](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) med hjälp Azure Application Gateway på SAP-bloggen.

> [!NOTE]
> Vi rekommenderar att du använder Azure Application Gateway för att belastningsutjämna trafiken till webbservern eftersom den tillhandahåller funktioner som SSL-avlastning, centralisera SSL-hantering för att minska omkostnaderna för kryptering och dekryptering på servern, Round-Robin-algoritmen för att distribuera trafik, Web Application Firewall-funktioner (WAF), hög tillgänglighet och så vidare.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI-plattform – återställning och återställning

Säkerhetskopiering och återställning är en process för att skapa periodiska kopior av data och program på olika platser. Det gör att den kan återställas eller återställas till ett tidigare tillstånd om ursprungliga data eller program förloras eller skadas. Det är också en viktig komponent i alla strategier för haveriberedskap.

För att utveckla en omfattande strategi för säkerhetskopiering och återställning för SAP BOBI Platform identifierar du de komponenter som leder till systemavbrott eller avbrott i programmet. I SAP BOBI Platform är säkerhetskopiering av följande komponenter viktigt för att skydda programmet.

- Installationskatalog för SAP BOBI (Hanterade Premium-diskar)
- Fillagringsplatsserver (Azure NetApp Files eller Azure Premium Files)
- CMS-databas (Azure Database for MySQL eller databas på virtuell Azure-dator)

I följande avsnitt beskrivs hur du implementerar en strategi för säkerhetskopiering och återställning för varje komponent på SAP BOBI Platform.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Säkerhetskopiera & för SAP BOBI-installationskatalogen

I Azure är det enklaste sättet att servera programservrar och alla anslutna diskar att använda [Azure Backup](../../../backup/backup-overview.md) Service. Den tillhandahåller oberoende och isolerade säkerhetskopior för att skydda oavsiktlig destruktion av data på dina virtuella datorer. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalning är enkla, säkerhetskopieringar optimeras och kan enkelt återställas när det behövs.

Som en del av säkerhetskopieringsprocessen tas ögonblicksbilder och data överförs till Recovery Service-valvet utan att det påverkar produktionsarbetsbelastningar. Ögonblicksbilden ger olika konsekvensnivå enligt beskrivningen i artikeln [Konsekvens med ögonblicksbilder.](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) Du kan också välja att säkerhetskopiera en delmängd av datadiskarna på den virtuella datorn med hjälp av selektiva säkerhetskopierings- och återställningsfunktioner för diskar. Mer information finns i dokumentet [Azure VM Backup och](../../../backup/backup-azure-vms-introduction.md) Vanliga frågor och svar – [Säkerhetskopiera virtuella Azure-datorer.](../../../backup/backup-azure-vm-backup-faq.yml)

#### <a name="backup--restore-for-file-repository-server"></a>Säkerhetskopiera & för fildatabasservern

För **Azure NetApp Files** kan du skapa en ögonblicksbild på begäran och schemalägga automatisk ögonblicksbild med hjälp av principer för ögonblicksbilder. Kopior av ögonblicksbilder tillhandahåller en kopia av ANF-volymen vid en tidpunkt. Mer information finns i Hantera [ögonblicksbilder med hjälp av Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**Azure Files** säkerhetskopiering är integrerad [med den Azure Backup](../../../backup/backup-overview.md) tjänsten, som centraliserar säkerhetskopierings- och återställningsfunktionen tillsammans med säkerhetskopiering av virtuella datorer och förenklar driftsarbetet. Mer information finns i [Säkerhetskopiering av Azure-filresurs](../../../backup/azure-file-share-backup-overview.md) och Vanliga [frågor och svar – Säkerhetskopiera Azure Files](../../../backup/backup-azure-files-faq.yml).

#### <a name="backup--restore-for-cms-database"></a>Säkerhetskopiera & för CMS-databas

Azure Database of MySQL är ett DBaaS-erbjudande i Azure, som automatiskt skapar serversäkerhetskopior och lagrar dem i användarkonfigurerad lokalt redundant eller geo-redundant lagring. Azure Database of MySQL säkerhetskopierar datafilerna och transaktionsloggen. Beroende på den maximala lagringsstorleken som stöds tar den antingen fullständiga och differentiella säkerhetskopior (max 4 TB lagringsservrar) eller säkerhetskopiering av ögonblicksbilder (upp till 16 TB maxlagringsservrar). Med dessa säkerhetskopior kan du återställa en server när som helst inom den konfigurerade kvarhållningsperioden för säkerhetskopior. Standardperioden för kvarhållning av säkerhetskopior är sju dagar, som du [kan konfigurera upp](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) till tre dagar om du vill. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

Dessa säkerhetskopierade filer exponeras inte av användaren och kan inte exporteras. Dessa säkerhetskopior kan bara användas för återställningsåtgärder i Azure Database for MySQL. Du kan använda [mysqldump för](../../../mysql/concepts-migrate-dump-restore.md) att kopiera en databas. Mer information finns i [Säkerhetskopiera och återställa i Azure Database for MySQL](../../../mysql/concepts-backup.md).

För databaser som är Virtual Machines kan du använda standardverktyg för säkerhetskopiering [eller Azure Backup](../../../backup/sap-hana-db-about.md) för HANA-databas. Om Azure-tjänsterna och verktygen inte uppfyller dina behov kan du också använda andra säkerhetskopieringsverktyg eller skript för att skapa säkerhetskopiering av diskar.

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI-plattformens tillförlitlighet

SAP BusinessObjects BI Platform innehåller olika nivåer, som är optimerade för specifika uppgifter och åtgärder. När en komponent från en nivå blir otillgänglig blir SAP BOBI-programmet antingen otillgängligt eller så fungerar inte vissa funktioner i programmet. Därför måste du se till att varje nivå är utformad för att vara tillförlitlig för att programmet ska fungera utan avbrott i verksamheten.

Det här avsnittet fokuserar på följande alternativ för SAP BOBI Platform –

- **Hög tillgänglighet:** En plattform med hög tillgänglig tillgång har minst två av allt inom Azure-regionen för att programmet ska fungera om en av servrarna blir otillgänglig.
- **Haveriberedskap:** Det är en process för att återställa dina programfunktioner om det finns oåterkalleliga förluster som hela Azure-regionen blir otillgänglig på grund av en naturåterställning.

Implementeringen av den här lösningen varierar beroende på typen av systemkonfiguration i Azure. Kunden måste därför skräddarsy lösningen för hög tillgänglighet och haveriberedskap baserat på deras affärsbehov.

### <a name="high-availability"></a>Hög tillgänglighet

Hög tillgänglighet avser en uppsättning tekniker som kan minimera IT-avbrott genom att tillhandahålla affärskontinu hos program/tjänster via redundanta, feltoleranta eller redundansskyddade komponenter i samma datacenter. I vårt fall är datacenter inom en Azure-region. Artikeln Arkitektur och scenarier med hög tillgänglighet för SAP ger en inledande inblick i olika tekniker och rekommendationer för hög tillgänglighet i Azure för [SAP-program,](sap-high-availability-architecture-scenarios.md) som kompletterar anvisningarna i det här avsnittet.

Baserat på storleksresultatet av SAP BOBI Platform behöver du utforma landskap och fastställa distributionen av BI-komponenter i Azure Virtual Machines och undernät. Redundansnivån i den distribuerade arkitekturen beror på vilken verksamhet som krävs för mål för återställningstid (RTO) och mål för återställningspunkt (RPO). SAP BOBI Platform innehåller olika nivåer och komponenter på varje nivå bör utformas för att uppnå redundans. Så om en komponent slutar fungera är det få eller inga avbrott i SAP BOBI-programmet. Exempel:

- Redundanta programservrar som BI-programservrar och webbserver
- Unika komponenter som CMS Database, File Repository Server Load Balancer

I följande avsnitt beskrivs hur du uppnår hög tillgänglighet för varje komponent i SAP BOBI Platform.

#### <a name="high-availability-for-application-servers"></a>Hög tillgänglighet för programservrar

För BI- och webbprogramservrar, oavsett om de installeras separat eller tillsammans, behöver inte en specifik lösning med hög tillgänglighet. Du kan uppnå hög tillgänglighet genom redundans, det vill säga genom att konfigurera flera instanser av BI- och webbservrar i olika Azure-Virtual Machines.

För att minska effekten av driftstopp på grund av en eller flera händelser rekommenderar vi att du följer nedanstående praxis för hög tillgänglighet för programservrar som körs på flera virtuella datorer.

- Använd Tillgänglighetszoner för att skydda datacenterfel.
- Konfigurera flera Virtual Machines i en tillgänglighetsuppsättning för redundans.
- Använd Managed Disks för virtuella datorer i en tillgänglighetsuppsättning.
- Konfigurera varje programnivå i separata tillgänglighetsuppsättningar.

Mer information finns i [Hantera tillgängligheten för virtuella Linux-datorer](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>Hög tillgänglighet för CMS-databas

Om du använder tjänsten Azure Database as a Service (DBaaS) för CMS-databasen tillhandahålls ramverket för hög tillgänglighet som standard. Du behöver bara välja den region och tjänst som är inbyggd i funktioner för hög tillgänglighet, redundans och återhämtning utan att du behöver konfigurera några ytterligare komponenter. Mer information om serviceavtalet för det DBaaS-erbjudande som stöds i Azure finns i Hög tillgänglighet [i Azure Database for MySQL](../../../mysql/concepts-high-availability.md) och Hög tillgänglighet [för Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

För annan DBMS-distribution för CMS-databas, se DBMS-distributionsguider för [SAP Workload,](dbms_guide_general.md)som ger insikter om olika DBMS-distributioner och dess metod för att uppnå hög tillgänglighet.

#### <a name="high-availability-for-file-repository-server"></a>Hög tillgänglighet för fildatabasservern

Fillagerserver (FRS) refererar till diskkatalogerna där innehåll som rapporter, universum och anslutningar lagras. Den delas mellan alla programservrar i systemet. Därför måste du se till att den har hög tillgänglig.

I Azure kan du antingen välja [Azure Premium Files](../../../storage/files/storage-files-introduction.md) eller [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) för filresurs som är utformade för att vara mycket tillgängliga och mycket hållbara till sin natur. Mer information finns i [avsnittet Redundans](../../../storage/files/storage-files-planning.md#redundancy) för Azure Files.

> [!NOTE]
> SMB Protocol för Azure Files är allmänt tillgängligt, men NFS-protokollstöd för Azure Files är för närvarande i förhandsversion. Mer information finns i [NFS 4.1-stöd för Azure Files nu är i förhandsversion](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Eftersom den här filresurstjänsten inte är tillgänglig i hela regionen kontrollerar du att du hittar uppdaterad information på webbplatsen Products [available by region](https://azure.microsoft.com/en-us/global-infrastructure/services/) (Produkter som är tillgängliga per region). Om tjänsten inte är tillgänglig i din region kan du skapa en NFS-server där du kan dela filsystemet till SAP BOBI-programmet. Men du måste också tänka på dess höga tillgänglighet.

#### <a name="high-availability-for-load-balancer"></a>Hög tillgänglighet för lastbalanserare

Om du vill distribuera trafik mellan webbservern kan du antingen använda Azure Load Balancer eller Azure Application Gateway. Redundansen för någon av lastbalanserarna kan uppnås baserat på vilken SKU du väljer för distribution.

- För Azure Load Balancer kan redundans uppnås genom att konfigurera Standard Load Balancer klientsidan som zonredundant. Mer information finns i [Standard Load Balancer och Tillgänglighetszoner](../../../load-balancer/load-balancer-standard-availability-zones.md)
- För Application Gateway kan hög tillgänglighet uppnås baserat på vilken typ av nivå som valts under distributionen.
  - v1 SKU stöder scenarier med hög tillgänglighet när du har distribuerat två eller flera instanser. Azure distribuerar dessa instanser mellan uppdaterings- och feldomäner för att säkerställa att alla instanser inte misslyckas samtidigt. Med den här SKU:n kan redundans uppnås inom zonen
  - v2 SKU säkerställer automatiskt att nya instanser sprids över feldomäner och uppdateringsdomäner. Om du väljer zonredundans sprids även de senaste instanserna över tillgänglighetszoner för att erbjuda zonindelade felåter återhämtning. Mer information finns i [Autoskalning och Zonredundant Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Referensarkitektur för hög tillgänglighet för SAP BusinessObjects BI-plattformen

Referensarkitekturen nedan beskriver konfigurationen av SAP BOBI Platform med hjälp av en tillgänglighetsuppsättning, som tillhandahåller redundans och tillgänglighet för virtuella datorer i zonen. Arkitekturen demonstrerar användningen av olika Azure-tjänster som Azure Application Gateway, Azure NetApp Files och Azure Database for MySQL för SAP BOBI Platform som erbjuder inbyggd redundans, vilket minskar komplexiteten med att hantera olika lösningar med hög tillgänglighet.

I bilden nedan belastningsutjämnas den inkommande trafiken (HTTPS – TCP/443) med hjälp av SKU:n Azure Application Gateway v1, som är mycket tillgänglig när den distribueras på två eller flera instanser. Flera instanser av webbserver, hanteringsservrar och bearbetningsservrar distribueras i separata Virtual Machines för att uppnå redundans och varje nivå distribueras i separata tillgänglighetsuppsättningar. Azure NetApp Files har inbyggd redundans i datacenter, så dina ANF-volymer för fillagerservern kommer att ha hög tillgänglig. CMS-databasen etableras på Azure Database for MySQL (DBaaS) som har inbyggd hög tillgänglighet. Mer information finns i [guiden Hög tillgänglighet i Azure Database for MySQL.](../../../mysql/concepts-high-availability.md)

![SAP BusinessObjects BI-plattformsredundans med hjälp av tillgänglighetsuppsättningar](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Arkitekturen ovan ger insikter om hur DU kan distribuera SAP BOBI på Azure. Men det omfattar inte alla möjliga konfigurationsalternativ för SAP BOBI Platform på Azure. Kunden kan skräddarsy sin distribution baserat på deras affärsbehov genom att välja olika produkter/tjänster för olika komponenter som Load Balancer, Fillagerserver och DBMS.

I flera Azure-regioner Tillgänglighetszoner, vilket innebär att den har oberoende strömförsörjning, kylning och nätverk. Det gör att kunden kan distribuera program över två eller tre tillgänglighetszoner. För kunder som vill uppnå hög tillgänglighet över tillgänglighetszoner kan distribuera SAP BOBI Platform mellan tillgänglighetszoner, se till att varje komponent i programmet är zonredundant.

### <a name="disaster-recovery"></a>Haveriberedskap

Instruktionen i det här avsnittet förklarar strategin för att skydda haveriberedskap för SAP BOBI Platform. Det kompletterar dokumentet [Haveriberedskap för SAP,](../../../site-recovery/site-recovery-sap.md) som representerar de primära resurserna för övergripande SAP-haveriberedskapsmetod.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referensarkitektur för haveriberedskap för SAP BusinessObjects BI-plattformen

Den här referensarkitekturen kör distribution av SAP BOBI Platform med redundanta programservrar med flera instanser. För haveriberedskap bör du redundans växla över alla nivåer till en sekundär region. Varje nivå använder olika strategier för att skydda haveriberedskap.

![Haveriberedskap för SAP BusinessObjects BI-plattformen](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Lastbalanserare

Load Balancer används för att distribuera trafik mellan webbprogramservrar för SAP BOBI Platform. Implementera parallell installation av programgatewayen i den sekundära regionen för att uppnå DR för Azure Application Gateway programgateway.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Virtuella datorer som kör webb- och BI-programservrar

Azure Site Recovery-tjänsten kan användas för att replikera Virtual Machines som kör webb- och BI-programservrar i den sekundära regionen. Den replikerar servrarna i den sekundära regionen så att du vid katastrofer och avbrott enkelt kan redundans växla över till den replikerade miljön och fortsätta att arbeta

#### <a name="file-repository-servers"></a>Fildatabasservrar

- **Azure NetApp Files** tillhandahåller NFS- och SMB-volymer, så alla filbaserade kopieringsverktyg kan användas för att replikera data mellan Azure-regioner. Mer information om hur du kopierar ANF-volym i en annan region finns i [Vanliga frågor och svar om Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Du kan använda Azure NetApp Files replikering mellan regioner, som för närvarande är en förhandsversion [som](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) använder NetApp SnapMirror®teknik. Därför skickas endast ändrade block över nätverket i ett komprimerat, effektivt format. Den här egenutvecklade tekniken minimerar mängden data som krävs för att replikera mellan regionerna, vilket sparar kostnader för dataöverföring. Det förkortar också replikeringstiden så att du kan uppnå ett mindre mål för återställningspunkt (RPO). Mer information [finns i Krav och överväganden för](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) att använda replikering mellan regioner.

- **Azure Premium-filer** stöder endast lokalt redundant lagring (LRS) och zonredundant lagring (ZRS). För Dr-strategi för Azure Premium Files kan du använda [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) eller [Azure PowerShell](/powershell/module/az.storage/) för att kopiera dina filer till ett annat lagringskonto i en annan region. Mer information finns i [Haveriberedskap och redundans för lagringskonto](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS-databas

Azure Database for MySQL har flera alternativ för att återställa databasen vid ett eventuellt haveri. Välj ett lämpligt alternativ som passar ditt företag.

- Aktivera skrivskyddade repliker mellan regioner för att förbättra planeringen för affärskontinuum och haveriberedskap. Du kan replikera från källservern till upp till fem repliker. Skrivskyddade repliker uppdateras asynkront med MySQL:s replikeringsteknik för binär logg. Repliker är nya servrar som du hanterar på liknande sätt som vanliga Azure Database for MySQL servrar. Läs mer om skrivskyddade repliker, tillgängliga regioner, begränsningar och redundans från artikeln [om skrivskyddade replikbegrepp.](../../../mysql/concepts-read-replicas.md)

- Använd Azure Database for MySQL geo-återställningsfunktion som återställer servern med geo-redundanta säkerhetskopieringar. Dessa säkerhetskopior är tillgängliga även när den region där servern finns är offline. Du kan återställa från dessa säkerhetskopior till valfri annan region och ta servern online igen.

  > [!NOTE]
  > Geo-återställning är endast möjligt om du har etablerat servern med geo-redundant säkerhetskopieringslagring. Det finns **inte stöd för att ändra redundansalternativ** för säkerhetskopiering när servern har skapats. Mer information finns i artikeln [Redundans för säkerhetskopiering.](../../../mysql/concepts-backup.md#backup-redundancy-options)

Nedan följer rekommendationen för haveriberedskap för varje nivå som används i det här exemplet.

| SAP BOBI-plattformsnivåer   | Rekommendation                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Parallell konfiguration av Application Gateway sekundär region                                                |
| Webbprogramservrar   | Replikera med hjälp av Site Recovery                                                                         |
| BI-programservrar    | Replikera med hjälp av Site Recovery                                                                         |
| Azure NetApp Files        | Filbaserat kopieringsverktyg för att replikera data till sekundär region **eller** ANF-replikering mellan regioner (förhandsversion) |
| Azure Database for MySQL  | Skrivskyddade repliker mellan **regioner eller** Återställ säkerhetskopiering från geo-redundanta säkerhetskopieringar.                             |

## <a name="next-steps"></a>Nästa steg

- [Konfigurera haveriberedskap för en SAP-appdistribution på flera nivåer](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines planering och implementering för SAP](planning-guide.md)
- [Azure Virtual Machines-distribution för SAP](deployment-guide.md)
- [Azure Virtual Machines DBMS-distribution för SAP](./dbms_guide_general.md)
