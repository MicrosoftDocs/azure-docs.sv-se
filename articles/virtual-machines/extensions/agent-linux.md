---
title: Översikt över Azure Linux VM-agent
description: Lär dig hur du installerar och konfigurerar Linux-agenten (waagent) för att hantera den virtuella datorns interaktion med Azure Fabric Controller.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 10/17/2016
ms.openlocfilehash: e8851ddd5211536394614727d990a2b52d32bfcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565385"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Förstå och använda Azure Linux-agenten

Microsoft Azure Linux-agenten (waagent) hanterar Linux & FreeBSD-etablering och virtuell dator interaktion med Azure Fabric-styrenheten. Utöver Linux-agenten som tillhandahåller etablerings funktioner kan Azure också välja att använda Cloud-Init för vissa Linux-operativ system. Linux-agenten tillhandahåller följande funktioner för Linux-och FreeBSD IaaS-distributioner:

> [!NOTE]
> Mer information finns i [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Avbildnings etablering**
  
  * Skapa ett användar konto
  * Konfigurera SSH-autentiseringstyper
  * Distribution av offentliga SSH-nycklar och nyckel par
  * Ange värd namnet
  * Publicera värd namnet på plattforms-DNS
  * Rapportering av finger avtryck för SSH-värdnamnet till plattformen
  * Resurs disk hantering
  * Formatera och montera resurs disken
  * Konfigurera växlings utrymme
* **Nätverk**
  
  * Hanterar vägar för att förbättra kompatibilitet med plattforms-DHCP-servrar
  * Säkerställer stabiliteten för nätverks gränssnittets namn
* **Kernel**
  
  * Konfigurerar virtuell NUMA (inaktivera för kernel-<`2.6.37` )
  * Använder Hyper-V-entropi för/dev/Random
  * Konfigurerar SCSI-tidsgräns för rot enheten (som kan vara fjärran sluten)
* **Diagnostik**
  
  * Omdirigering av konsol till den seriella porten
* **SCVMM-distributioner**
  
  * Identifierar och startar VMM-agenten för Linux när den körs i en System Center Virtual Machine Manager 2012 R2-miljö
* **VM-tillägg**
  
  * Inmatnings komponent som skapats av Microsoft och partners till virtuella Linux-datorer (IaaS) för att aktivera automatisering av program vara och konfiguration
  * Referens implementering för VM-tillägg på [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikation
Informations flödet från plattformen till agenten sker via två kanaler:

* En ansluten DVD-skiva för IaaS-distributioner. Den här DVD-skivan innehåller en OVF-kompatibel konfigurations fil som innehåller all etablerings information förutom faktiska SSH-nyckelpar.
* En TCP-slutpunkt visar en REST API som används för att hämta distributions-och Topology-konfigurationen.

## <a name="requirements"></a>Krav
Följande system har testats och är kända för att fungera med Azure Linux-agenten:

> [!NOTE]
> Den här listan kan skilja sig från den officiella listan över [distributioner som stöds](../linux/endorsed-distros.md).
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6,7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12,3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Andra system som stöds:

* FreeBSD 10 + (Azure Linux-agenten v 2.0.10 +)

Linux-agenten är beroende av vissa system paket för att fungera korrekt:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Fil Systems verktyg: sfdisk, fdisk, mkfs, delvis
* Lösen ords verktyg: chpasswd, sudo
* Verktyg för text bearbetning: sed, grep
* Nätverks verktyg: IP-Route
* Kernel-stöd för att montera UDF-filsystem.

Se till att den virtuella datorn har åtkomst till IP-168.63.129.16. Mer information finns i [Vad är IP-168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="installation"></a>Installation
Installation med hjälp av ett RPM-eller DEB-paket från distributionens paket lagring är den bästa metoden för att installera och uppgradera Azure Linux-agenten. Alla godkända [distributions leverantörer](../linux/endorsed-distros.md) integrerar Azure Linux Agent-paketet i sina avbildningar och databaser.

Läs dokumentationen i [Azure Linux-agentens lagrings platsen på GitHub](https://github.com/Azure/WALinuxAgent) för avancerade installations alternativ, till exempel att installera från källan eller till anpassade platser eller prefix.

## <a name="command-line-options"></a>Command-Line alternativ
### <a name="flags"></a>Flagg
* Verbose: öka utförligheten för angivet kommando
* tvinga: hoppa över interaktiv bekräftelse för vissa kommandon

### <a name="commands"></a>Kommandon
* hjälp: visar de kommandon och flaggor som stöds.
* avetablera: försök att rensa systemet och gör det lämpligt för att etablera. Följande åtgärd tar bort:
  
  * Alla SSH-värd nycklar (om etableringen. RegenerateSshHostKeyPair är "y" i konfigurations filen)
  * Namnserver-konfiguration i/etc/resolv.conf
  * Rot lösen ord från/etc/Shadow (om etableringen. DeleteRootPassword är "y" i konfigurations filen)
  * Cachelagrade DHCP-klient lån
  * Återställer värd namnet till localhost. localdomain

> [!WARNING]
> Avetablering garanterar inte att avbildningen är klar med all känslig information och lämplig för omdistribution.
> 
> 

* avetablera + användare: utför allt inetablering (ovan) och tar också bort det senast etablerade användar kontot (hämtas från/var/lib/waagent) och tillhör ande data. Den här parametern är när du avetablerar en avbildning som tidigare har inträffat på Azure så att den kan fångas och återanvändas.
* version: visar versionen av waagent
* serialconsole: konfigurerar GRUB för att markera ttyS0 (den första seriella porten) som start konsol. Detta säkerställer att kernel start-loggar skickas till den seriella porten och görs tillgänglig för fel sökning.
* daemon: kör waagent som en daemon för att hantera interaktion med plattformen. Det här argumentet anges till waagent i waagent init-skriptet.
* Starta: kör waagent som bakgrunds process

## <a name="configuration"></a>Konfiguration
En konfigurations fil (/etc/waagent.conf) styr åtgärder för waagent. Följande visar en exempel konfigurations fil:

```config
Provisioning.Enabled=y
Provisioning.DeleteRootPassword=n
Provisioning.RegenerateSshHostKeyPair=y
Provisioning.SshHostKeyPairType=rsa
Provisioning.MonitorHostName=y
Provisioning.DecodeCustomData=n
Provisioning.ExecuteCustomData=n
Provisioning.AllowResetSysUser=n
Provisioning.PasswordCryptId=6
Provisioning.PasswordCryptSaltLength=10
ResourceDisk.Format=y
ResourceDisk.Filesystem=ext4
ResourceDisk.MountPoint=/mnt/resource
ResourceDisk.MountOptions=None
ResourceDisk.EnableSwap=n
ResourceDisk.SwapSizeMB=0
LBProbeResponder=y
Logs.Verbose=n
OS.RootDeviceScsiTimeout=300
OS.OpensslPath=None
HttpProxy.Host=None
HttpProxy.Port=None
AutoUpdate.Enabled=y
```

Följande olika konfigurations alternativ beskrivs. Konfigurations alternativen är av tre typer. Boolesk, sträng eller heltal. De booleska konfigurations alternativen kan anges som "y" eller "n". Det särskilda nyckelordet "ingen" kan användas för vissa konfigurations poster av sträng typ som följande information:

**Etablering. aktive rad:**  
```txt
Type: Boolean  
Default: y
```
Detta gör att användaren kan aktivera eller inaktivera etablerings funktionerna i agenten. Giltiga värden är "y" eller "n". Om etableringen är inaktiverat bevaras SSH-värden och användar nycklar i avbildningen och all konfiguration som anges i Azures etablerings-API ignoreras.

> [!NOTE]
> Parametern är som `Provisioning.Enabled` standard "n" på Ubuntu Cloud-avbildningar som använder Cloud-Init för etablering.
> 
> 

**Etablering. DeleteRootPassword:**  
```txt
Type: Boolean  
Default: n
```
Om detta anges raderas rot lösen ordet i/etc/Shadow-filen under etablerings processen.

**Etablering. RegenerateSshHostKeyPair:**  
```txt
Type: Boolean  
Default: y
```
Om detta anges tas alla nyckel par för SSH-värden (ECDSA, DSA och RSA) bort under etablerings processen från/etc/ssh/. Och ett enda nytt nyckel par skapas.

Krypterings typen för det nya nyckel paret kan konfigureras av etableringen. SshHostKeyPairType-posten. Vissa distributioner återskapar SSH-nyckelpar för alla saknade krypterings typer när SSH-daemonen startas om (till exempel vid en omstart).

**Etablering. SshHostKeyPairType:**  
```txt
Type: String  
Default: rsa
```
Detta kan anges till en typ av krypteringsalgoritm som stöds av SSH daemon på den virtuella datorn. De värden som stöds vanligt vis är "RSA", "DSA" och "ECDSA". "putty.exe" i Windows stöder inte "ECDSA". Om du tänker använda putty.exe på Windows för att ansluta till en Linux-distribution använder du exempelvis "RSA" eller "DSA".

**Etablering. MonitorHostName:**  
```txt
Type: Boolean  
Default: y
```
Om det här alternativet är inställt övervakar waagent den virtuella Linux-datorn för hostname-ändringar (som returneras av kommandot "hostname") och automatiskt uppdaterar nätverks konfigurationen i avbildningen för att avspegla ändringen. För att push-överföra namn ändras till DNS-servrarna startas nätverk om på den virtuella datorn. Detta resulterar i en kort förlust av Internet anslutning.

**Etablering. DecodeCustomData**  
```txt
Type: Boolean  
Default: n
```
Om det här värdet anges avkodas waagent CustomData från base64.

**Provisioning.ExecuteCustomData**  
```txt
Type: Boolean  
Default: n
```
Om det här värdet anges kör waagent CustomData efter etableringen.

**Etablering. AllowResetSysUser**
```txt
Type: Boolean
Default: n
```
Med det här alternativet kan du återställa sys-användarens lösen ord. Standardvärdet är inaktiverat.

**Etablering. PasswordCryptId**  
```txt
Type: String  
Default: 6
```
Algoritm som används av crypt vid generering av lösen ords-hash.  
 1-MD5  
 2a – blowfish  
 5-SHA-256  
 6-SHA-512  

**Etablering. PasswordCryptSaltLength**  
```txt
Type: String  
Default: 10
```
Längden på det slumpmässiga salt som används när lösen ordets hash genereras.

**ResourceDisk. format:**  
```txt
Type: Boolean  
Default: y
```
Om den här inställningen är aktive rad kommer resurs disken som tillhandahålls av plattformen att formateras och monteras av waagent om den fil Systems typ som användaren begär i "ResourceDisk. filesystem" är något annat än "NTFS". En enda partition av typen Linux (83) görs tillgänglig på disken. Den här partitionen är inte formaterad om den kan monteras.

**ResourceDisk. filesystem:**  
```txt
Type: String  
Default: ext4
```
Anger fil Systems typ för resurs disken. Vilka värden som stöds varierar beroende på Linux-distribution. Om strängen är X, så mkfs. X måste finnas på Linux-avbildningen. SLES 11-bilder bör normalt använda "ext3". FreeBSD-bilder ska använda ' UFS2 ' här.

**ResourceDisk. monterings punkt:**  
```txt
Type: String  
Default: /mnt/resource 
```
Anger sökvägen till den plats där resurs disken monteras. Resurs disken är en *temporär* disk och kan tömmas när den virtuella datorn har avetablerats.

**ResourceDisk.MountOptions**  
```txt
Type: String  
Default: None
```
Anger monterings alternativ för diskar som ska skickas till kommandot Mount-o. Det här är en kommaavgränsad lista med värden, t. ex. 'nodev,nosuid'. Mer information finns i montera (8).

**ResourceDisk.EnableSwap:**  
```txt
Type: Boolean  
Default: n
```
Om den anges skapas en växlings fil (/swapfile) på resurs disken och läggs till i system växlings utrymmet.

**ResourceDisk.SwapSizeMB:**  
```txt
Type: Integer  
Default: 0
```
Växlings filens storlek i megabyte.

**Loggar. Verbose:**  
```txt
Type: Boolean  
Default: n
```
Om det är inställt, ökar loggens utförlighet. Waagent loggar till/var/log/waagent.log och använder system logrotate-funktionen för att rotera loggar.

**Granskning. EnableRDMA**  
```txt
Type: Boolean  
Default: n
```
Om det här alternativet anges försöker agenten installera och sedan läsa in en RDMA-kernel-drivrutin som matchar versionen av den inbyggda program varan på den underliggande maskin varan.

**Granskning. RootDeviceScsiTimeout:**  
```txt
Type: Integer  
Default: 300
```
Den här inställningen konfigurerar SCSI-tidsgräns på några sekunder av OS-disken och data enheterna. Om den inte anges används systemets standardinställningar.

**Granskning. OpensslPath:**  
```txt
Type: String  
Default: None
```
Den här inställningen kan användas för att ange en alternativ sökväg för den openssl-binärfil som ska användas för kryptografiska åtgärder.

**HttpProxy. Host, HttpProxy. port**  
```txt
Type: String  
Default: None
```
Om den är inställd använder agenten denna proxyserver för att få åtkomst till Internet. 

**AutoUpdate. Enabled**
```txt
Type: Boolean
Default: y
```
Aktivera eller inaktivera automatisk uppdatering för bearbetning av mål tillstånd; Standardvärdet är aktiverat.



## <a name="ubuntu-cloud-images"></a>Ubuntu moln avbildningar
Ubuntu Cloud-avbildningar använder [Cloud-Init](https://launchpad.net/ubuntu/+source/cloud-init) för att utföra många konfigurations uppgifter som annars skulle hanteras av Azure Linux-agenten. Följande skillnader gäller:

* **Etableringen. aktiverade** standardinställningar till "n" på Ubuntu Cloud-avbildningar som använder Cloud-Init för att utföra etablerings uppgifter.
* Följande konfigurations parametrar har ingen inverkan på Ubuntu Cloud-avbildningar som använder Cloud-Init för att hantera resurs disk och växlings utrymme:
  
  * **ResourceDisk. format**
  * **ResourceDisk. filesystem**
  * **ResourceDisk. monterings punkt**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Mer information finns i följande resurser för att konfigurera resurs diskens monterings punkt och växlings utrymme på Ubuntu Cloud-avbildningar under etableringen:
  
  * [Ubuntu wiki: Konfigurera Swap-partitioner](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Mata in anpassade data i en virtuell Azure-dator](../windows/tutorial-automate-vm-deployment.md)