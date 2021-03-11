---
title: Uppdatera Azure Linux-agenten från GitHub
description: Lär dig hur du uppdaterar Azure Linux-agenten för din virtuella Linux-dator i Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 08/02/2017
ms.openlocfilehash: a274435d6e0fc32bdf5b2ab04702ed971d7c5175
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558992"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Så här uppdaterar du Azure Linux-agenten på en virtuell dator

Om du vill uppdatera [Azure Linux-agenten](https://github.com/Azure/WALinuxAgent) på en virtuell Linux-dator i Azure måste du redan ha:

- En virtuell Linux-dator som körs i Azure.
- En anslutning till den virtuella Linux-datorn med SSH.

Du bör alltid söka efter ett paket i Linux distribution-lagringsplatsen först. Det är möjligt att paketet är tillgängligt kanske inte är den senaste versionen, men om du aktiverar AutoUpdate ser Linux-agenten alltid den senaste uppdateringen. Om du har problem med att installera från paket chefer bör du söka efter support från distribution-leverantören.

> [!NOTE]
> Mer information finns i avsnittet [om signerade Linux-distributioner på Azure](../linux/endorsed-distros.md)

Kontrol lera [lägsta versions stöd för virtuella dator agenter i Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) innan du fortsätter.


## <a name="ubuntu"></a>Ubuntu

Kontrol lera den aktuella paket versionen

```bash
apt list --installed | grep walinuxagent
```

Uppdatera paket-cache

```bash
sudo apt-get -qq update
```

Installera den senaste paket versionen

```bash
sudo apt-get install walinuxagent
```

Se till att automatisk uppdatering är aktiverat. Kontrol lera först om den är aktive rad:

```bash
cat /etc/waagent.conf
```

Hitta AutoUpdate. enabled. Om du ser dessa utdata är de aktiverade:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Starta om waagengt-tjänsten för 14,04

```bash
initctl restart walinuxagent
```

Starta om waagent-tjänsten för 16,04/17,04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

Kontrol lera den aktuella paket versionen

```bash
sudo yum list WALinuxAgent
```

Kontrol lera tillgängliga uppdateringar

```bash
sudo yum check-update WALinuxAgent
```

Installera den senaste paket versionen

```bash
sudo yum install WALinuxAgent
```

Se till att automatisk uppdatering är aktiverat 

Kontrol lera först om den är aktive rad:

```bash
cat /etc/waagent.conf
```

Hitta AutoUpdate. enabled. Om du ser dessa utdata är de aktiverade:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Starta om waagent-tjänsten

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

Kontrol lera den aktuella paket versionen

```bash
sudo yum list WALinuxAgent
```

Kontrol lera tillgängliga uppdateringar

```bash
sudo yum check-update WALinuxAgent
```

Installera den senaste paket versionen

```bash
sudo yum install WALinuxAgent  
```

Se till att automatisk uppdatering är aktiverat. Kontrol lera först om den är aktive rad:

```bash
cat /etc/waagent.conf
```

Hitta AutoUpdate. enabled. Om du ser dessa utdata är de aktiverade:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Starta om waagent-tjänsten

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

Kontrol lera den aktuella paket versionen

```bash
zypper info python-azure-agent
```

Kontrol lera tillgängliga uppdateringar. I ovanstående utdata visas om paketet är uppdaterat.

Installera den senaste paket versionen

```bash
sudo zypper install python-azure-agent
```

Se till att automatisk uppdatering är aktiverat 

Kontrol lera först om den är aktive rad:

```bash
cat /etc/waagent.conf
```

Hitta AutoUpdate. enabled. Om du ser dessa utdata är de aktiverade:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Starta om waagent-tjänsten

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

Kontrol lera den aktuella paket versionen

```bash
zypper info python-azure-agent
```

Kontrol lera tillgängliga uppdateringar

I utdata från ovanstående visar det att paketet är uppdaterat, om paketet är uppdaterat.

Installera den senaste paket versionen

```bash
sudo zypper install python-azure-agent
```

Se till att automatisk uppdatering är aktiverat 

Kontrol lera först om den är aktive rad:

```bash
cat /etc/waagent.conf
```

Hitta AutoUpdate. enabled. Om du ser dessa utdata är de aktiverade:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Starta om waagent-tjänsten

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "Sträck ut"

Kontrol lera den aktuella paket versionen

```bash
dpkg -l | grep waagent
```

Uppdatera paket-cache

```bash
sudo apt-get -qq update
```

Installera den senaste paket versionen

```bash
sudo apt-get install waagent
```

Aktivera automatisk uppdatering av agent den här versionen av Debian saknar version >= 2.0.16, så den automatiska uppdateringen är inte tillgänglig för den. Utdata från kommandot ovan visar dig om paketet är uppdaterat.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "Sträck ut"

Kontrol lera den aktuella paket versionen

```bash
apt list --installed | grep waagent
```

Uppdatera paket-cache

```bash
sudo apt-get -qq update
```

Installera den senaste paket versionen

```bash
sudo apt-get install waagent
```

Se till att automatisk uppdatering aktive ras först, kontrol lera om den är aktive rad:

```bash
cat /etc/waagent.conf
```

Hitta AutoUpdate. enabled. Om du ser dessa utdata är de aktiverade:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 och Oracle Linux 7

För Oracle Linux kontrollerar du att `Addons` lagrings platsen är aktive rad. Välj att redigera filen `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) eller `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux) och ändra raden `enabled=0` till `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i den här filen.

Om du vill installera den senaste versionen av Azure Linux-agenten skriver du:

```bash
sudo yum install WALinuxAgent
```

Om du inte hittar lagrings platsen för tillägg kan du enkelt lägga till dessa rader i slutet av din. lagrings platsen-fil enligt din Oracle Linux-version:

För Oracle Linux 6 virtuella datorer:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

För Oracle Linux 7 virtuella datorer:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Skriv sedan:

```bash
sudo yum update WALinuxAgent
```

Detta är vanligt vis allt du behöver, men om du av någon anledning behöver installera den https://github.com direkt använder du följande steg.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Uppdatera Linux-agenten när det inte finns något agent paket för distribution

Installera wget (det finns vissa distributioner som inte installeras som standard, till exempel Red Hat, CentOS och Oracle Linux version 6,4 och 6,5) genom att skriva `sudo yum install wget` på kommando raden.

### <a name="1-download-the-latest-version"></a>1. Ladda ned den senaste versionen
Öppna [versionen av Azure Linux-agenten i GitHub](https://github.com/Azure/WALinuxAgent/releases) på en webb sida och ta reda på det senaste versions numret. (Du kan hitta din aktuella version genom att skriva `waagent --version` .)

För version 2.2. x eller senare, skriver du:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Följande rad använder version 2.2.0 som exempel:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Installera Azure Linux-agenten

För version 2.2. x använder du: du kan behöva installera paketet `setuptools` först – se [här](https://pypi.python.org/pypi/setuptools). Kör sedan:

```bash
sudo python setup.py install
```

Se till att automatisk uppdatering är aktiverat. Kontrol lera först om den är aktive rad:

```bash
cat /etc/waagent.conf
```

Hitta AutoUpdate. enabled. Om du ser dessa utdata är de aktiverade:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Så här aktiverar du körning:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Starta om waagent-tjänsten
För de flesta Linux-distributioner:

```bash
sudo service waagent restart
```

För Ubuntu använder du:

```bash
sudo service walinuxagent restart
```

För kärnor använder du:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. bekräfta versionen av Azure Linux-agenten
    
```bash
waagent -version
```

För Core, kanske inte kommandot ovan fungerar.

Du kommer att se att Azure Linux-agentens version har uppdaterats till den nya versionen.

Mer information om Azure Linux-agenten finns i [README för Azure Linux-agenten](https://github.com/Azure/WALinuxAgent).
