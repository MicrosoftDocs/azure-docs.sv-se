---
title: Aktivera Azure Disk Encryption för virtuella Linux-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Linux-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 066fb32dc9da13e8a77660a9144b53675d689f63
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553807"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption för virtuella Linux-datorer 

Azure Disk Encryption hjälper till att skydda dina data så att din verksamhet uppfyller både interna och externa krav och åtaganden. Den använder [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt) funktionen i Linux för att tillhandahålla volym kryptering för operativ system och data diskar för virtuella Azure-datorer (VM) och är integrerat med [Azure Key Vault](../../key-vault/index.yml) för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter.

Azure Disk Encryption är zon elastisk, på samma sätt som Virtual Machines. Mer information finns i [Azure-tjänster som stöder Tillgänglighetszoner](../../availability-zones/az-region.md).

Om du använder [Azure Security Center](../../security-center/index.yml)får du en avisering om du har virtuella datorer som inte är krypterade. Aviseringarna visas med hög allvarlighets grad och rekommendationen är att kryptera de virtuella datorerna.

![Azure Security Center disk krypterings avisering](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta att använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) . 
> - Vissa rekommendationer kan öka användningen av data, nätverk eller beräknings resurser, vilket resulterar i ytterligare licens-eller prenumerations kostnader. Du måste ha en giltig aktiv Azure-prenumeration för att kunna skapa resurser i Azure i de regioner som stöds.

Du kan lära dig grunderna i Azure Disk Encryption för Linux på bara några minuter med [skapa och kryptera en virtuell Linux-dator med Azure CLI snabb start](disk-encryption-cli-quickstart.md) eller [skapa och kryptera en virtuell Linux-dator med Azure PowerShell snabb start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Virtuella datorer och operativ system som stöds

### <a name="supported-vms"></a>Virtuella datorer som stöds

Virtuella Linux-datorer är tillgängliga i [flera storlekar](../sizes.md). Azure Disk Encryption stöds på virtuella datorer i generation 1 och generation 2. Azure Disk Encryption är också tillgängligt för virtuella datorer med Premium Storage.

Se [storlekar för virtuella Azure-datorer utan lokal temporär disk](../azure-vms-no-temp-disk.md).

Azure Disk Encryption är inte heller tillgängligt på virtuella datorer med [Basic, A-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/)eller på virtuella datorer som inte uppfyller de minsta minnes kraven:

| Virtuell dator | Minsta minnes krav |
|--|--|
| Virtuella Linux-datorer vid endast kryptering av data volymer| 2 GB |
| Virtuella Linux-datorer vid kryptering av både data-och OS-volymer och där roten (/) fil systemets användning är 4 GB eller mindre | 8 GB |
| Virtuella Linux-datorer vid kryptering av både data-och OS-volymer och där roten (/) fil systemets användning är större än 4 GB | Rot fil systemets användning * 2. Till exempel kräver en 16 GB rot fil system användning minst 32 GB RAM-minne |

När operativ systemets disk krypterings process har slutförts på virtuella Linux-datorer kan den virtuella datorn konfigureras för att köras med mindre minne.

Mer undantag finns i [Azure Disk Encryption: scenarier som inte stöds](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Operativsystem som stöds

Azure Disk Encryption stöds på en delmängd av [Azure-godkända Linux-distributioner](endorsed-distros.md), som i sig är en delmängd av alla möjliga distributioner av Linux-servrar.

![Venndiagram av Linux Server-distributioner som stöder Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Linux Server-distributioner som inte har godkänts av Azure stöder inte Azure Disk Encryption. för de som har påtecknats har endast följande distributioner och versioner stöd Azure Disk Encryption:


| Publisher | Erbjudande | SKU | URN | Volym typ som stöds för kryptering |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18,04 – LTS | Kanoniskt: UbuntuServer: 18.04-LTS: senaste | Operativ system och data disk |
| Canonical | Ubuntu 18.04 | 18,04 – DAGLIGEN – LTS | Kanoniskt: UbuntuServer: 18.04-DAILY-LTS: senaste | Operativ system och data disk |
| Canonical | Ubuntu 16.04 | 16,04 – DAGLIGEN – LTS | Kanoniskt: UbuntuServer: 16.04-DAILY-LTS: senaste | Operativ system och data disk |
| Canonical | Ubuntu 14.04.5</br>[med Azures justerade kernel uppdaterat till 4,15 eller senare](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Kanoniskt: UbuntuServer: 14.04.5-LTS: senaste | Operativ system och data disk |
| Canonical | Ubuntu 14.04.5</br>[med Azures justerade kernel uppdaterat till 4,15 eller senare](disk-encryption-troubleshooting.md) | 14.04.5 – DAGLIGA LTS | Kanoniskt: UbuntuServer: 14.04.5-DAILY-LTS: senaste | Operativ system och data disk |
| Redhat | RHEL 8 – LVM | 8-LVM | RedHat: RHEL: 8-LVM: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 8,2 | 8,2 | RedHat: RHEL: 8.2: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 8,1 | 8.1 | RedHat: RHEL: 8.1: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7 – LVM | 7-LVM | RedHat: RHEL: 7-LVM: 7.8.2020111201 | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7,8 | 7,8 | RedHat: RHEL: 7,8: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7,7 | 7.7 | RedHat: RHEL: 7.7: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7,6 | 7.6 | RedHat: RHEL: 7.6: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7.5 | 7.5 | RedHat: RHEL: 7.5: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7,4 | 7,4 | RedHat: RHEL: 7.4: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7,3 | 7.3 | RedHat: RHEL: 7.3: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 7,2 | 7.2 | RedHat: RHEL: 7.2: senaste | Operativ system och data disk (se OBS! nedan) |
| Redhat | RHEL 6,8 | 6.8 | RedHat: RHEL: 6,8: senaste | Data disk (se OBS! nedan) |
| Redhat | RHEL 6,7 | 6.7 | RedHat: RHEL: 6,7: senaste | Data disk (se OBS! nedan) |
| OpenLogic | CentOS 8 – LVM | 8-LVM | OpenLogic: CentOS-LVM: 8-LVM: senaste | Operativ system och data disk |
| OpenLogic | CentOS 8,2 | 8_2 | OpenLogic: CentOS: 8_2: senaste | Operativ system och data disk |
| OpenLogic | CentOS 8,1 | 8_1 | OpenLogic: CentOS: 8_1: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7 – LVM | 7-LVM | OpenLogic: CentOS-LVM: 7-LVM: 7.8.2020111100 | Operativ system och data disk |
| OpenLogic | CentOS 7,8 | 7,8 | OpenLogic: CentOS: 7_8: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7,7 | 7.7 | OpenLogic: CentOS: 7.7: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7,6 | 7.6 | OpenLogic: CentOS: 7.6: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic: CentOS: 7.5: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7.4 | 7,4 | OpenLogic: CentOS: 7.4: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7,3 | 7.3 | OpenLogic: CentOS: 7.3: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7,2 n | 7,2 n | OpenLogic: CentOS: 7,2 n: senaste | Operativ system och data disk |
| OpenLogic | CentOS 7,1 | 7.1 | OpenLogic: CentOS: 7.1: senaste | Endast datadisk |
| OpenLogic | CentOS 7,0 | 7.0 | OpenLogic: CentOS: 7.0: senaste | Endast datadisk |
| OpenLogic | CentOS 6,8 | 6.8 | OpenLogic: CentOS: 6,8: senaste | Endast datadisk |
| SUSE | openSUSE 42,3 | 42,3 | SUSE: openSUSE: 42.3: senaste | Endast datadisk |
| SUSE | SLES 12 – SP4 | 12 – SP4 | SUSE: SLES: 12-SP4: senaste | Endast datadisk |
| SUSE | SLES HPC 12-SP3 | 12 – SP3 | SUSE: SLES-HPC: 12-SP3: senaste | Endast datadisk |

> [!NOTE]
> Den nya Azure Disk Encryption-implementeringen stöds för RHEL OS och datadisk för RHEL7 avbildningar enligt principen betala per användning.  
>
> ADE stöds också för RHEL-egna prenumerations guld bilder, men endast **efter** att prenumerationen har registrerats. Mer information finns i [Red Hat Enterprise Linux guld-avbildningar för prenumerationer i Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Ytterligare krav för virtuell dator

Azure Disk Encryption kräver att dm-crypt-och vfat-modulerna finns i systemet. Om du tar bort eller inaktiverar vfat från standard avbildningen så förhindras systemet från att läsa nyckel volymen och hämta den nyckel som behövs för att låsa upp diskarna vid efterföljande omstarter. System härdnings steg som tar bort vfat-modulen från systemet eller tvingar fram expandering av OS-mountpoints/-mappar på data enheter är inte kompatibla med Azure Disk Encryption. 

Innan du aktiverar kryptering måste data diskarna som ska krypteras anges korrekt i/etc/fstab. Använd alternativet "nomissation" när du skapar poster och välj ett beständigt block enhets namn (som enhets namn i formatet "/dev/sdX" kan inte associeras med samma disk i omstarter, särskilt efter kryptering. mer information om det här problemet finns i: [Felsöka Linux VM enhets namn ändringar](../troubleshooting/troubleshoot-device-names-problems.md)).

Kontrol lera att/etc/fstab-inställningarna är korrekt konfigurerade för montering. Om du vill konfigurera dessa inställningar kör du kommandot Mount-a eller startar om den virtuella datorn och utlöser ommonteringen på det sättet. När den är klar kontrollerar du utdata från kommandot lsblk för att kontrol lera att enheten fortfarande är monterad. 

- Om/etc/fstab-filen inte monterar enheten korrekt innan du aktiverar krypteringen kan Azure Disk Encryption inte montera den på rätt sätt.
- Azure Disk Encryption processen flyttar monterings informationen från/etc/fstab och till en egen konfigurations fil som en del av krypterings processen. Larm inte för att se posten som saknas från/etc/fstab när data enhets krypteringen har slutförts.
- Innan du startar kryptering måste du stoppa alla tjänster och processer som kan skrivas till monterade data diskar och inaktivera dem, så att de inte startar om automatiskt efter en omstart. Dessa kan hålla filerna öppna på dessa partitioner, vilket förhindrar krypterings proceduren att ommontera dem, vilket orsakar att krypteringen Miss lyckas. 
- Efter omstarten tar det tid för Azure Disk Encryption processen att montera de nya krypterade diskarna. De blir inte omedelbart tillgängliga efter en omstart. Processen behöver tid för att starta, låsa upp och sedan montera de krypterade enheterna innan de är tillgängliga för andra processer att komma åt. Den här processen kan ta mer än en minut efter omstart beroende på systemets egenskaper.

Här är ett exempel på kommandon som används för att montera data diskarna och skapa nödvändiga/etc/fstab-poster:

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Nätverkskrav

Om du vill aktivera funktionen Azure Disk Encryption måste de virtuella Linux-datorerna uppfylla följande konfigurations krav för nätverks slut punkt:
  - Om du vill hämta en token för att ansluta till ditt nyckel valv måste den virtuella Linux-datorn kunna ansluta till en Azure Active Directory-slutpunkt, \[ login.microsoftonline.com \] .
  - Om du vill skriva krypterings nycklarna till ditt nyckel valv måste den virtuella Linux-datorn kunna ansluta till Key Vault-slutpunkten.
  - Den virtuella Linux-datorn måste kunna ansluta till en Azure Storage-slutpunkt som är värd för Azure Extension-lagringsplatsen och ett Azure Storage-konto som är värd för VHD-filerna.
  -  Om säkerhets principen begränsar åtkomsten från virtuella Azure-datorer till Internet kan du matcha föregående URI och konfigurera en speciell regel för att tillåta utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brand vägg](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Lagrings krav för krypterings nyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt nyckel valv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologi
I följande tabell definieras några vanliga termer som används i dokumentationen för Azure Disk Encryption:

| Terminologi | Definition |
| --- | --- |
| Azure Key Vault | Key Vault är en kryptografisk nyckel hanterings tjänst som är baserad på FIPS-validerade (Federal Information Processing Standards) moduler för maskin varu säkerhet. Dessa standarder hjälper till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerat för att hantera och administrera Azure-resurser från kommando raden.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) är det Linux-baserade, transparenta disk krypterings systemet som används för att aktivera disk kryptering på virtuella Linux-datorer. |
| Nyckel krypterings nyckel (KEK) | Den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller figursätta hemligheten. Du kan ange en HSM-skyddad nyckel eller en skyddad nyckel för program vara. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdletar](/powershell/azure/). |


## <a name="next-steps"></a>Nästa steg

- [Snabb start – skapa och kryptera en virtuell Linux-dator med Azure CLI ](disk-encryption-cli-quickstart.md)
- [Snabb start – skapa och kryptera en virtuell Linux-dator med Azure PowerShell](disk-encryption-powershell-quickstart.md) 
- [Azure Disk Encryption-scenarier på virtuella Linux-datorer](disk-encryption-linux.md)
- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md)
