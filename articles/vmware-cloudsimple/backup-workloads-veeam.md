---
title: Azure VMware-lösning genom CloudSimple-säkerhets kopiering av virtuella arbets belastnings datorer i privata moln med Veeam
description: Beskriver hur du kan säkerhetskopiera virtuella datorer som körs i ett Azure-baserat privat CloudSimple-moln med Veeam B&R 9,5
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be606b3e23a594e67acf3f169d88353403d8577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899345"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Säkerhetskopiera virtuella arbets belastnings datorer på CloudSimple privata moln med Veeam B&R

Den här guiden beskriver hur du kan säkerhetskopiera dina virtuella datorer som körs i ett Azure-baserat privat CloudSimple-moln med Veeam B&R 9,5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Om säkerhets kopierings-och återställnings lösningen för Veeam

Veeam-lösningen innehåller följande komponenter.

**Säkerhets kopierings Server**

Säkerhets kopierings servern är en Windows-Server (VM) som fungerar som kontroll Center för Veeam och utför dessa funktioner: 

* Koordinerar säkerhets kopiering, replikering, verifiering av återställning och återställning av aktiviteter
* Kontrollerar jobb schemaläggning och resurstilldelning
* Gör att du kan konfigurera och hantera infrastruktur komponenter för säkerhets kopiering och ange globala inställningar för infrastrukturen för säkerhets kopiering

**Proxyservrar**

Proxyservrar installeras mellan reserv servern och andra komponenter i säkerhets kopierings infrastrukturen. De hanterar följande funktioner:

* Hämtning av VM-data från produktions lagring
* Komprimering
* Deduplicering
* Kryptering
* Överföring av data till säkerhets kopierings lagrings platsen

**Säkerhets kopierings lagring**

Lagrings platsen för säkerhets kopian är den lagrings plats där Veeam behåller säkerhetskopierade filer, VM-kopior och metadata för replikerade virtuella datorer.  Lagrings platsen kan vara en Windows-eller Linux-server med lokala diskar (eller monterade NFS/SMB) eller en maskin vara för hård disk kopiering.

### <a name="veeam-deployment-scenarios"></a>Veeam distributions scenarier
Du kan använda Azure för att tillhandahålla en lagrings plats för säkerhets kopior och ett lagrings mål för långsiktig säkerhets kopiering och arkivering. All säkerhets kopierings nätverks trafik mellan virtuella datorer i det privata molnet och lagrings platsen för säkerhets kopiering i Azure flyttar över en länk med hög bandbredd och låg latens. Replikeringstrafik mellan regioner överförs till det interna Azure-arbetsplans nätverket, vilket sänker bandbredds kostnaderna för användarna.

**Grundläggande distribution**

För miljöer med mindre än 30 TB som ska säkerhets kopie ras rekommenderar CloudSimple följande konfiguration:

* Veeam backup server och proxy server installerad på samma virtuella dator i det privata molnet.
* En Linux-baserad primär säkerhets kopierings databas i Azure som är konfigurerad som mål för säkerhets kopierings jobb.
* `azcopy` används för att kopiera data från den primära säkerhets kopie databasen till en Azure Blob-behållare som replikeras till en annan region.

![Diagram som visar grundläggande Veeam distributions scenarier.](media/veeam-basicdeployment.png)

**Avancerad distribution**

För miljöer med mer än 30 TB som ska säkerhets kopie ras rekommenderar CloudSimple följande konfiguration:

* En proxyserver per nod i virtuellt San-klustret, som rekommenderas av Veeam.
* Windows-baserad primär säkerhets kopierings lagring i det privata molnet för att cachelagra fem dagars data för snabb återställning.
* Linux backup-lagringsplats i Azure som mål för säkerhets kopierings jobb för längre varaktighets kvarhållning. Den här lagrings platsen ska konfigureras som en lagrings plats för säkerhets kopiering.
* `azcopy` används för att kopiera data från den primära säkerhets kopie databasen till en Azure Blob-behållare som replikeras till en annan region.

![Grundläggande distributions scenarier](media/veeam-advanceddeployment.png)

I föregående bild ser du att backup proxy är en virtuell dator med snabb åtkomst till virtuella arbets belastnings diskar i virtuellt San-datalagret. Veeam använder virtuella installations program för säkerhets kopiering för virtuellt San.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Krav för Veeam-lösning på CloudSimple

Veeam-lösningen kräver att du gör följande:

* Ange dina egna Veeam-licenser.
* Distribuera och hantera Veeam för att säkerhetskopiera arbets belastningar som körs i det privata CloudSimple-molnet.

Den här lösningen ger dig fullständig kontroll över Veeam säkerhets kopierings verktyg och erbjuder valet att använda det interna Veeam-gränssnittet eller Veeam vCenter-plugin-programmet för att hantera säkerhets kopierings jobb för virtuella datorer.

Om du är en befintlig Veeam-användare kan du hoppa över avsnittet om Veeam-lösnings komponenter och direkt fortsätta till [Veeam distributions scenarier](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Installera och konfigurera Veeam-säkerhetskopieringar i ditt CloudSimple-privata moln

I följande avsnitt beskrivs hur du installerar och konfigurerar en Veeam backup-lösning för ditt CloudSimple-privata moln.

Distributions processen består av följande steg:

1. [vCenter-gränssnitt: Konfigurera infrastruktur tjänster i ditt privata moln](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple-portalen: Konfigurera privata moln nätverk för Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple-portalen: eskalera privilegier](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure Portal: Anslut ditt virtuella nätverk till det privata molnet](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure Portal: skapa en säkerhets kopierings lagrings plats i Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure Portal: Konfigurera Azure Blob Storage för långsiktig data kvarhållning](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter-gränssnitt för privat moln: installera Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam-konsol: Konfigurera Veeam säkerhets kopiering & återställnings program](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple-portalen: Konfigurera Veeam-åtkomst och ta bort behörigheter för att eskalera](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Innan du börjar

Följande krävs innan du börjar Veeam-distributionen:

* En Azure-prenumeration som ägs av dig
* En tidigare skapad Azure-resurs grupp
* Ett virtuellt Azure-nätverk i din prenumeration
* Ett Azure Storage-konto
* Ett [privat moln](create-private-cloud.md) som skapats med CloudSimple-portalen.  

Följande objekt behövs under implementerings fasen:

* VMware-mallar för Windows för att installera Veeam (till exempel Windows Server 2012 R2-64-bitars avbildning)
* Ett tillgängligt VLAN har identifierats för säkerhets kopierings nätverket
* CIDR för det undernät som ska tilldelas till säkerhets kopierings nätverket
* Veeam 9,5 U3-installerbar media (ISO) överfört till virtuellt San-datalagret för det privata molnet

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter-gränssnitt: Konfigurera infrastruktur tjänster i ditt privata moln

Konfigurera infrastruktur tjänster i det privata molnet för att göra det enkelt att hantera dina arbets belastningar och verktyg.

* Du kan lägga till en extern identitetsprovider enligt beskrivningen i [Konfigurera vCenter identitets källor för att använda Active Directory](set-vcenter-identity.md) om något av följande gäller:

  * Du vill identifiera användare från din lokala Active Directory (AD) i ditt privata moln.
  * Du vill konfigurera en AD i ditt privata moln för alla användare.
  * Du vill använda Azure AD.
* Om du vill tillhandahålla IP-adressering, IP-adress hantering och namn matchnings tjänster för dina arbets belastningar i det privata molnet konfigurerar du en DHCP-och DNS-server enligt beskrivningen i [Konfigurera DNS och DHCP-program och arbets belastningar i ditt CloudSimple privata moln](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple privat moln: Konfigurera privata moln nätverk för Veeam

Få åtkomst till CloudSimple-portalen för att konfigurera nätverk för privat moln för Veeam-lösningen.

Skapa ett VLAN för det säkerhetskopierade nätverket och tilldela det till en under näts CIDR. Instruktioner finns i [skapa och hantera VLAN/undernät](create-vlan-subnet.md).

Skapa brand Väggs regler mellan hanterings under nätet och säkerhets kopierings nätverket för att tillåta nätverks trafik på portar som används av Veeam. Se Veeam-ämnet [som använder portarna](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Anvisningar om hur du skapar brand Väggs regler finns i [Konfigurera brand Väggs tabeller och regler](firewall.md).

Följande tabell innehåller en port lista.

| Ikon | Beskrivning | Ikon | Beskrivning |
| ------------ | ------------- | ------------ | ------------- |
| Säkerhets kopierings Server  | vCenter  | HTTPS/TCP  | 443 |
| Säkerhets kopierings Server <br> *Krävs för distribution av Veeam backup & Replication Components* | Säkerhetskopiera proxy  | TCP/UDP  | 135, 137 till 139 och 445 |
    | Säkerhets kopierings Server   | DNS  | UDP  | 53  | 
    | Säkerhets kopierings Server   | Veeam uppdaterings aviserings Server  | TCP  | 80  | 
    | Säkerhets kopierings Server   | Veeam licens uppdaterings Server  | TCP  | 443  | 
    | Säkerhetskopiera proxy   | vCenter |   |   | 
    | Säkerhetskopiera proxy  | Säkerhets kopiering av Linux-lagringsplats   | TCP  | 22  | 
    | Säkerhetskopiera proxy  | Lagrings plats för Windows säkerhets kopiering  | TCP  | 49152 – 65535   | 
    | Säkerhets kopierings lagring  | Säkerhetskopiera proxy  | TCP  | 2500 – 5000  | 
    | Käll databas för säkerhets kopiering<br> *Används för säkerhets kopierings jobb*  | Mål databas för säkerhets kopiering  | TCP  | 2500 – 5000  | 

Skapa brand Väggs regler mellan arbets belastnings under nätet och säkerhets kopierings nätverket enligt beskrivningen i [Konfigurera brand Väggs tabeller och regler](firewall.md).  För program medveten säkerhets kopiering och återställning måste [ytterligare portar](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) öppnas på de virtuella arbets belastningarna som är värdar för specifika program.

Som standard tillhandahåller CloudSimple en 1Gbps ExpressRoute-länk. För större miljö storlekar kan en länk med högre bandbredd krävas. Kontakta Azure-supporten om du vill ha mer information om länkar med högre bandbredd.

Om du vill fortsätta med installationen behöver du en verifierings nyckel och en URI för peer-kretsen och åtkomst till din Azure-prenumeration.  Den här informationen finns på sidan Virtual Network anslutning på CloudSimple-portalen. Instruktioner finns i [Hämta peering-information för Azure Virtual Network till CloudSimple-anslutning](virtual-network-connection.md). [Kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)om du har problem med att hämta informationen.

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple privat moln: eskalera behörigheter för cloudowner

Standard användaren "cloudowner" har inte tillräckliga privilegier i det privata molnet vCenter för att installera VEEAM, så användarens vCenter-privilegier måste eskaleras. Mer information finns i [eskalera privilegier](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure Portal: Anslut ditt virtuella nätverk till det privata molnet

Anslut ditt virtuella nätverk till det privata molnet genom att följa anvisningarna i [Azure Virtual Network-anslutning med ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure Portal: skapa en säkerhets kopia av en VM-lagringsplats

1. Skapa en standard-D2 v3-virtuell dator med (2 virtuella processorer och 8 GB minne).
2. Välj den CentOS 7,4-baserade avbildningen.
3. Konfigurera en nätverks säkerhets grupp (NSG) för den virtuella datorn. Kontrol lera att den virtuella datorn inte har en offentlig IP-adress och inte går att komma åt från det offentliga Internet.
4. Skapa ett användar namn och ett lösen ord baserat användar konto för den nya virtuella datorn. Instruktioner finns i [skapa en virtuell Linux-dator i Azure Portal](../virtual-machines/linux/quick-create-portal.md).
5. Skapa 1x512 GiB standard-HDD och koppla den till den virtuella lagrings platsen.  Instruktioner finns i [så här ansluter du en hanterad datadisk till en virtuell Windows-dator i Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Skapa en xfs-volym på den hanterade disken](https://www.digitalocean.com/docs/volumes/how-to/). Logga in på den virtuella datorn med de tidigare nämnda autentiseringsuppgifterna. Kör följande skript för att skapa en logisk volym, Lägg till disken i den, skapa en XFS-filsystem- [partition](https://www.digitalocean.com/docs/volumes/how-to/partition/) och [montera](https://www.digitalocean.com/docs/volumes/how-to/mount/) partitionen under/backup1-sökvägen.

    Exempelskript:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Exponera/backup1 som en NFS-monterings punkt för den Veeam backup-server som körs i det privata molnet. Mer information finns i artikeln om digital oceanen [så här konfigurerar du en NFS-montering på CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Använd det här NFS-resurs namnet när du konfigurerar säkerhets kopierings lagrings platsen i Veeam backup server.

8. Konfigurera filtrerings regler i NSG för säkerhets kopian av den virtuella datorns lagrings plats för att uttryckligen tillåta all nätverks trafik till och från den virtuella datorn.

> [!NOTE]
> Veeam backup & Replication använder SSH-protokollet för att kommunicera med Linux-säkerhetskopiering och kräver SCP-verktyget på Linux-databaser. Kontrol lera att SSH-daemonen är korrekt konfigurerad och att SCP är tillgängligt på Linux-värden.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Konfigurera Azure Blob Storage för långsiktig data kvarhållning

1. Skapa ett allmänt lagrings konto (GPv2) av standard typ och en BLOB-behållare enligt beskrivningen i Microsoft video [komma igång med Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Skapa en Azure Storage-behållare enligt beskrivningen i referensen för att [skapa behållare](/rest/api/storageservices/create-container) .
2. Hämta `azcopy` kommando rads verktyget för Linux från Microsoft. Du kan använda följande kommandon i bash-gränssnittet i CentOS 7,5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Använd `azcopy` kommandot för att kopiera säkerhetskopierade filer till och från BLOB-behållaren.  Mer information finns i [överföra data med AZCopy i Linux](../storage/common/storage-use-azcopy-v10.md) för detaljerade kommandon.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter-konsol för privat moln: installera Veeam B&R

Anslut till vCenter från ditt privata moln för att skapa ett Veeam-tjänstkonto, installera Veeam B&R 9,5 och konfigurera Veeam med hjälp av tjänst kontot.

1. Skapa en ny roll med namnet "Veeam backup Role" och tilldela den nödvändiga behörighet som rekommenderas av Veeam. Mer information finns i Veeam-ämnet [nödvändiga behörigheter](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Skapa en ny grupp för "Veeam User Group" i vCenter och tilldela den rollen Veeam säkerhets kopiering.
3. Skapa ett nytt "Veeam Service Account"-användare och Lägg till det i användar gruppen "Veeam".

    ![Skapa ett Veeam-tjänstkonto](media/veeam-vcenter01.png)

4. Skapa en distribuerad port grupp i vCenter med säkerhets kopierings nätverkets VLAN. Mer information finns i VMware-videon [skapa en distribuerad port grupp i vSphere-webbklienten](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Skapa de virtuella datorerna för Veeam-säkerhetskopiering och proxyservrar i vCenter enligt [system kraven för Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Du kan använda Windows 2012 R2 eller Linux. Mer information finns i [krav för att använda säkerhets kopierings databaser för Linux](https://www.veeam.com/kb2216).
6. Montera den installerbara Veeam ISO som en CDROM-enhet i den virtuella datorn med Veeam backup server.
7. Använd en RDP-session på Windows 2012 R2-datorn (målet för Veeam-installationen), [Installera Veeam B&R 9.5 U3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) i en virtuell Windows 2012 R2-dator.
8. Hitta den interna IP-adressen för den virtuella dator som Veeam säkerhets kopierings Server och konfigurera IP-adressen så att den är statisk i DHCP-servern. De exakta stegen som krävs för detta beror på DHCP-servern. Som exempel förklarar artikel <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statiska DHCP-mappningar</a> i netgrind hur du konfigurerar en DHCP-server med en PFSense-router.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam-konsol: installera Veeam för säkerhets kopiering och återställning av program vara

Konfigurera säkerhets kopierings-och återställnings program för Veeam med Veeam-konsolen. Mer information finns i [Veeam Backup & Replication v9-installation and Deployment](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Lägg till VMware vSphere som en hanterad server miljö. När du uppmanas till det anger du autentiseringsuppgifterna för Veeam-tjänstkontot som du skapade i början av [vCenter-konsolen för privat moln: installera Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Använd standardinställningar för belastnings kontroll och avancerade standardinställningar.
    * Ange att monterings Server platsen ska vara säkerhets kopierings servern.
    * Ändra säkerhets kopierings platsen för Veeam-servern till fjärrlagringsplatsen.

2. Lägg till Linux-servern i Azure som säkerhets kopierings lagrings plats.

    * Använd standardinställningar för belastnings kontroll och avancerade inställningar. 
    * Ange att monterings Server platsen ska vara säkerhets kopierings servern.
    * Ändra säkerhets kopierings platsen för Veeam-servern till fjärrlagringsplatsen.

3. Aktivera kryptering av konfigurations säkerhets kopiering med **start> inställningar för säkerhets kopiering av konfiguration**.

4. Lägg till en virtuell Windows Server-dator som proxyserver för VMware-miljön. Kryptera säkerhetskopierade data via kabeln med hjälp av trafik regler för en proxyserver.

5. Konfigurera säkerhets kopierings jobb.
    * Om du vill konfigurera säkerhets kopierings jobb följer du anvisningarna i [skapa ett säkerhets kopierings jobb](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Aktivera kryptering av säkerhetskopieringsfiler under **Avancerade inställningar > lagring**.

6. Konfigurera säkerhets kopierings jobb.

    * Om du vill konfigurera säkerhets kopierings jobb följer du anvisningarna i videon [skapa ett säkerhets kopierings jobb](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Aktivera kryptering av säkerhetskopieringsfiler under **Avancerade inställningar > lagring**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple-portalen: Konfigurera Veeam-åtkomst och ta bort behörigheter för att eskalera
Skapa en offentlig IP-adress för Veeam-säkerhets kopierings-och återställnings servern. Anvisningar finns i [tilldela offentliga IP-adresser](public-ips.md).

Skapa en brand Väggs regel med hjälp av om du vill att Veeam ska kunna skapa en utgående anslutning till Veeam-webbplatsen för att ladda ned uppdateringar/korrigeringar på TCP-port 80. Instruktioner finns i [Konfigurera brand Väggs tabeller och regler](firewall.md).

För att ta bort privilegier, se [aveskalerade privilegier](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Referenser

### <a name="cloudsimple-references"></a>CloudSimple-referenser

* [Skapa ett privat moln](create-private-cloud.md)
* [Skapa och hantera VLAN/undernät](create-vlan-subnet.md)
* [vCenter-identitets källor](set-vcenter-identity.md)
* [Installation av DNS-och DHCP-arbetsbelastning](dns-dhcp-setup.md)
* [Eskalera privilegier](escalate-privileges.md)
* [Konfigurera brandväggstabeller och -regler](firewall.md)
* [Behörigheter för privata moln](learn-private-cloud-permissions.md)
* [Allokera offentliga IP-adresser](public-ips.md)

### <a name="veeam-references"></a>Veeam-referenser

* [Använda portar](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Behörigheter som krävs](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [System krav](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Installerar Veeam backup & Replication](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Nödvändiga moduler och behörigheter för stöd för flera OS-FLR och lagrings platser för Linux](https://www.veeam.com/kb2216)
* [Veeam backup & Replication v9-installation and Deployment-video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 skapa ett säkerhets kopierings jobb – video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 skapa ett säkerhets kopierings jobb – video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure-referenser

* [Konfigurera en virtuell nätverksgateway för ExpressRoute med hjälp av Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Ansluta ett VNet till en krets-annan prenumeration](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Skapa en virtuell Linux-dator i Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Så här ansluter du en hanterad datadisk till en virtuell Windows-dator i Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Komma igång med Azure Storage-video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Skapa container](/rest/api/storageservices/create-container)
* [Överföra data med AzCopy i Linux](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>VMware-referenser

* [Skapa en distribuerad port grupp i vSphere-webb klienten – video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Andra referenser

* [Skapa en XFS-volym på den hanterade disken – RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Konfigurera en NFS-montering på CentOS 7-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Konfigurera DHCP-servern-netgrind](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
