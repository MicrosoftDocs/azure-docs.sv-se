---
title: Support mat ris för att flytta virtuella Azure-datorer till en annan region med Azure Resource-arbetskraft
description: Granska support för att flytta virtuella Azure-datorer mellan regioner med Azure Resource-arbetskraft.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 3022b2d4954ffaef71e17ed28dd9b6f141d4da70
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980498"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Stöd för att flytta virtuella Azure-datorer mellan Azure-regioner

Den här artikeln sammanfattar support och förutsättningar när du flyttar virtuella datorer och relaterade nätverks resurser i Azure-regioner med resurs förflyttning.

> [!IMPORTANT]
> Azure Resource-arbetskraften är för närvarande en för hands version.


## <a name="windows-vm-support"></a>Stöd för virtuella Windows-datorer

Resurs förflyttning har stöd för virtuella Azure-datorer som kör dessa Windows-operativsystem.

**Operativsystem** | **Information**
--- | ---
Windows Server 2019 | Stöds för Server Core, server med Skriv bords miljö.
Windows Server 2016  | Server Core som stöds, server med Skriv bords miljö.
Windows Server 2012 R2 | Stöds.
Windows Server 2012 | Stöds.
Windows Server 2008 R2 med SP1/SP2 | Stöds.<br/><br/> För datorer som kör Windows Server 2008 R2 med SP1/SP2 måste du installera en uppdatering för Windows [servicing stack Update (självbetjänings)](https://support.microsoft.com/help/4490628) och [SHA-2](https://support.microsoft.com/help/4474419).  SHA-1 stöds inte från september 2019 och om SHA-2-kod signering inte är aktiverat installeras/uppgraderas inte som förväntat i agent tillägget. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Stöds.
Windows 8,1 (x64) | Stöds.
Windows 8 (x64) | Stöds.
Windows 7 (x64) med SP1 och senare | Installera en Windows [servicing stack Update (självbetjänings)](https://support.microsoft.com/help/4490628) och [SHA-2-uppdatering](https://support.microsoft.com/help/4474419) på datorer som kör Windows 7 med SP1.  SHA-1 stöds inte från september 2019 och om SHA-2-kod signering inte är aktiverat kommer steget "förberedelse" inte att lyckas. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Stöd för virtuella Linux-datorer

Resurs flyttning har stöd för virtuella Azure-datorer som kör dessa Linux-operativsystem.

**Operativsystem** | **Information**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0, 8,1
Ubuntu 14,04 LTS-Server | [Kernel-versioner som stöds](#supported-ubuntu-kernel-versions)
Ubuntu 16,04 LTS-Server | [Kernel-version som stöds](#supported-ubuntu-kernel-versions)<br/><br/> Ubuntu-servrar som använder lösenordsbaserad autentisering och inloggning, och paketet Cloud-Init för att konfigurera virtuella datorer i molnet, kan ha lösenordsbaserad inloggning inaktive rad på redundansväxling (beroende på konfigurationen för Cloud-Init). Lösenordsbaserade inloggningar kan återaktiveras på den virtuella datorn genom att återställa lösen ordet från support > fel sökning > Inställningar-menyn (av den misslyckade virtuella datorn i Azure Portal.
Ubuntu 18,04 LTS-Server | [Kernel-version som stöds](#supported-ubuntu-kernel-versions).
Debian 7 | [Kernel-versioner som stöds](#supported-debian-kernel-versions).
Debian 8 | [Kernel-versioner som stöds](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Kernel-versioner som stöds](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 och 15 SP1. [(Kernel-versioner som stöds)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | INSTALLERAS
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Köra Red Hat-kompatibel kernel eller Enterprise kernel release 3, 4 & 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Ubuntu kernel-versioner som stöds

**Frisläpp** | **Kernelversion** 
--- | --- 
14,04 LTS |  3.13.0-24-genered to 3.13.0-170-Generic,<br/>3.16.0-25-genered to 3.16.0-77-Generic,<br/>3.19.0-18-genered till 3.19.0-80-genered,<br/>4.2.0-18-genered to 4.2.0-42-Generic,<br/>4.4.0-21-genered to 4.4.0-148-genered,<br/>4.15.0 – 1023 – Azure till 4.15.0 – 1045 – Azure 
16,04 LTS |  4.4.0-21-genered to 4.4.0-171-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-74-generic<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1066-Azure
18,04 LTS | 4.15.0-20-Generic to 4.15.0-74-generic </br> 4.18.0-13-Generic to 4.18.0-25-genered </br> 5.0.0-15-genered to 5.0.0-37-generisk </br> 5.3.0-19-Generic to 5.3.0-24-genered </br> 4.15.0-1009 – Azure till 4.15.0-1037 – Azure </br> 4.18.0-1006 – Azure till 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure till 5.0.0-1028 – Azure </br> 5.3.0-1007-Azure till 5.3.0-1009 – Azure


### <a name="supported-debian-kernel-versions"></a>Debian Kernel-versioner som stöds 

**Frisläpp** |  **Kernelversion** 
--- |  --- 
Debian 7 |  3.2.0 – 4-amd64 till 3.2.0-6-amd64, 3.16.0 -0. bpo. 4-amd64 
Debian 8 |  3.16.0 – 4-amd64 till 3.16.0-10-amd64, 4.9.0 -0. bpo. 4-amd64 till 4.9.0 -0. bpo. 11-amd64 
Debian 8 |  3.16.0 – 4-amd64 till 3.16.0-10-amd64, 4.9.0 -0. bpo. 4-amd64 till 4.9.0 -0. bpo. 9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>SUSE Linux Enterprise Server 12 kernel-versioner som stöds 

**Frisläpp** | **Kernelversion** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Alla [aktie-och SUSE 12 SP1-, SP2-, SP3-och SP4-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4,7-Azure till 4.4.180-4.31 – Azure</br>4.12.14-6.3 – Azure till 4.12.14-6.34 – Azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>SUSE Linux Enterprise Server 15 kernel-versioner som stöds

**Frisläpp** | **Kernelversion** |
--- |  --- |
SUSE Linux Enterprise Server 15 och 15 SP1 |  Alla börs-och 15-kernels och 15 kärnor stöds.</br></br> 4.12.14 – 5.5 – Azure till 4.12.14 – 8.22 – Azure |

## <a name="supported-linux-file-systemguest-storage"></a>Linux-filsystem/gäst lagring som stöds

* Fil system: ext3, ext4, XFS, BTRFS
* Volym hanterare: LVM2
* Program vara för flera sökvägar: enhets mappning


## <a name="supported-vm-compute-settings"></a>Inställningar för VM-beräkning som stöds

**Inställning** | **Support** | **Information**
--- | --- | ---
Storlek | Valfri storlek på virtuella Azure-datorer med minst två processor kärnor och 1 GB RAM | Verifiera [storleken på virtuella Azure-datorer](../virtual-machines/sizes-general.md).
Tillgänglighetsuppsättningar | Stöds | Stöds.
Tillgänglighetszoner | Stöds | Stöds, beroende på stöd för mål region.
Azure Gallery-avbildningar (publicerat av Microsoft) | Stöds | Stöds om den virtuella datorn körs på ett operativ system som stöds.
Azure Gallery-avbildningar (publicerat av tredje part)  | Stöds | Stöds om den virtuella datorn körs på ett operativ system som stöds.
Anpassade avbildningar (publicerat av tredje part)| Stöds | Stöds om den virtuella datorn körs på ett operativ system som stöds.
Virtuella datorer som använder Site Recovery | Stöds inte | Flytta resurser mellan regioner för virtuella datorer med Site Recovery på Server delen. Om du redan använder Site Recovery inaktiverar du replikering och startar sedan förberedelse processen.
Azure RBAC-principer | Stöds inte | Azure-rollbaserad åtkomst kontroll (Azure RBAC) principer för virtuella datorer kopieras inte till den virtuella datorn i mål regionen.
Tillägg | Stöds inte | Tillägg kopieras inte till den virtuella datorn i mål regionen. Installera dem manuellt när flyttningen är klar.


## <a name="supported-vm-storage-settings"></a>Inställningar för VM-lagring som stöds

Den här tabellen sammanfattade stödet för den virtuella Azure OS-disken, data disken och den temporära disken. Det är viktigt att Observera de virtuella datorernas disk gränser och mål för [hanterade diskar](../virtual-machines/disks-scalability-targets.md) för att undvika prestanda problem.

> [!NOTE]
> Storleken på den virtuella mål datorn måste vara lika med eller större än den virtuella käll datorn. De parametrar som används för verifiering är: antal data diskar, antal nätverkskort, tillgängliga processorer, minne i GB. Om det sn't ett fel utfärdas.


**Komponent** | **Support** | **Information**
--- | --- | ---
Maximal storlek för OS-disk | 2048 GB | [Läs mer](../virtual-machines/managed-disks-overview.md) om VM-diskar.
Tillfällig disk | Stöds inte | Den tillfälliga disken är alltid exkluderad från förberedelse processen.<br/><br/> Lagra inte beständiga data på den temporära disken. [Läs mer](../virtual-machines/managed-disks-overview.md#temporary-disk).
Maximal storlek för data disk | 8192 GB för Managed disks
Minsta storlek för data disk |  2 GB för hanterade diskar |
Högsta antal data diskar | Upp till 64, i enlighet med stöd för en speciell storlek på virtuell Azure-dator | [Läs mer](../virtual-machines/sizes.md) om storlekar på virtuella datorer.
Ändrings takt för data disk | Högst 10 MBps per disk för Premium Storage. Högst 2 Mbit/s per disk för standard lagring. | Om genomsnitts data ändrings takten på disken kontinuerligt är högre än det högsta antalet kommer förberedelsen inte att fångas upp.<br/><br/>  Men om det maximala värdet överskrids, kan förberedelsen fångas upp, men du kan se något fördröjda återställnings punkter.
Data disk (standard lagrings konto) | Stöds inte. | Ändra lagrings typen till den hanterade disken och försök sedan att flytta den virtuella datorn.
Data disk (Premium Storage-konto) | Stöds inte | Ändra lagrings typen till den hanterade disken och försök sedan att flytta den virtuella datorn.
Hanterad disk (standard) | Stöds  |
Hanterad disk (Premium) | Stöds |
Standard SSD | Stöds |
Generation 2 (UEFI-start) | Stöds
Lagrings konto för startdiagnostik | Stöds inte | Aktivera det igen när du har flyttat den virtuella datorn till mål regionen.
Virtuella datorer med Azure Disk Encryption aktiverat | Stöds | [Läs mer](tutorial-move-region-encrypted-virtual-machines.md)
Virtuella datorer som använder kryptering på Server sidan med kundhanterad nyckel | Stöds | [Läs mer](tutorial-move-region-encrypted-virtual-machines.md)

### <a name="limits-and-data-change-rates"></a>Begränsningar och data ändrings takt

I följande tabell sammanfattas de gränser som baseras på våra tester. Dessa beskriver inte alla möjliga kombinationer av program-I/O. De faktiska resultaten varierar beroende på din program-I/O-mix. Det finns två gränser att överväga, data omsättning per disk och per virtuell dator data omsättning.

**Lagrings mål** | **Genomsnittligt käll disk-I/O** |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total käll disk data omsättning per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |    336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk

## <a name="supported-vm-networking-settings"></a>Nätverks inställningar för virtuella datorer som stöds

**Inställning** | **Support** | **Information**
--- | --- | ---
NIC | Stöds | Ange en befintlig resurs i mål regionen eller skapa en ny resurs under förberedelse processen. 
Intern lastbalanserare | Stöds | Ange en befintlig resurs i mål regionen eller skapa en ny resurs under förberedelse processen.  
Offentlig lastbalanserare | Stöds | Ange en befintlig resurs i mål regionen eller skapa en ny resurs under förberedelse processen.  
Offentlig IP-adress | Stöds | Ange en befintlig resurs i mål regionen eller skapa en ny resurs under förberedelse processen.<br/><br/> Den offentliga IP-adressen är landsspecifika och kommer inte att behållas i mål regionen efter flytten. Tänk på detta när du ändrar nätverks inställningar (inklusive belastnings Utjämnings regler) på mål platsen.
Nätverkssäkerhetsgrupp | Stöds | Ange en befintlig resurs i mål regionen eller skapa en ny resurs under förberedelse processen.  
Reserverad (statisk) IP-adress | Stöds | Du kan för närvarande inte konfigurera den här. Standardvärdet är källans värde. <br/><br/> Om NÄTVERKSKORTet på den virtuella käll datorn har en statisk IP-adress och mål under nätet har samma IP-adress, tilldelas den virtuella mål datorn.<br/><br/> Om mål under nätet inte har samma IP-adress kommer initieringen av den virtuella datorn att Miss förflyttningen.
Dynamisk IP-adress | Stöds | Du kan för närvarande inte konfigurera den här. Standardvärdet är källans värde.<br/><br/> Om NÄTVERKSKORTet på källan har dynamisk IP-adressering, är NÄTVERKSKORTet på den virtuella mål datorn också dynamiskt som standard.
IP-konfigurationer | Stöds | Du kan för närvarande inte konfigurera den här. Standardvärdet är källans värde.

## <a name="outbound-access-requirements"></a>Krav för utgående åtkomst

Virtuella Azure-datorer som du vill flytta behöver utgående åtkomst.


### <a name="url-access"></a>URL-åtkomst

 Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till följande URL: er:

**Namn** | **Offentligt Azure-moln** | **Information** 
--- | --- | --- 
Storage | `*.blob.core.windows.net`  | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. 
Azure Active Directory | `login.microsoftonline.com`  | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. 
Replikering | `*.hypervrecoverymanager.windowsazure.com` | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. 
Service Bus | `*.servicebus.windows.net` | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. 

## <a name="nsg-rules"></a>NSG-regler
Om du använder en regel för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar skapar du dessa [service tag-](../virtual-network/service-tags-overview.md) regler. Varje regel ska tillåta utgående åtkomst på HTTPS (443).
- Skapa en regel för lagrings tag i käll regionen.
- Skapa en *AzureSiteRecovery* tag-regel för att tillåta åtkomst till tjänsten Site Recovery i vilken region som helst. Den här taggen har beroenden för dessa taggar, så du måste skapa regler för dem för att:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Vi rekommenderar att du testar regler i en miljö som inte är för produktion. [Granska några exempel](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Nästa steg

Försök att [flytta en virtuell Azure-dator](tutorial-move-region-virtual-machines.md) till en annan region med resurs förflyttning.
