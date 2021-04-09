---
title: Felsöka med Cloud-Init
description: Felsök etablering av en virtuell Azure-dator med Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 842107245fe26155d53866bf95e11b08d7593ad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582161"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Felsöka VM-etablering med Cloud-Init

Om du har skapat generaliserade anpassade avbildningar med Cloud-Init för att utföra etableringen, men har upptäckt att den virtuella datorn inte skapades korrekt, måste du felsöka dina anpassade avbildningar.

Några exempel på problem med etableringen:
- Den virtuella datorn har fastnat i "skapa" i 40 minuter och den virtuella datorns skapande har marker ATS som misslyckad
- `CustomData` får inte bearbetas
- Den tillfälliga disken kan inte monteras
- Användare skapas inte eller så finns det problem med användar åtkomsten
- Nätverket har inte ställts in korrekt
- Växlings fil eller synkroniseringsfel

Den här artikeln beskriver hur du felsöker Cloud-init. Mer detaljerad information finns i [Cloud-Init djupe](./cloud-init-deep-dive.md).

## <a name="step-1-test-the-deployment-without-customdata"></a>Steg 1: testa distributionen utan att `customData`

Cloud-Init kan acceptera `customData` , som skickas till den när den virtuella datorn skapas. Först bör du se till att detta inte orsakar några problem med distributioner. Försök att konfigurera den virtuella datorn utan att överföra någon konfiguration. Om du hittar den virtuella datorn kan du fortsätta med stegen nedan, om du hittar den konfiguration som du skickar inte går att använda [steg 4](). 

## <a name="step-2-review-image-requirements"></a>Steg 2: granska avbildnings kraven
Den primära orsaken till etablerings felet för den virtuella datorn är operativ system avbildningen uppfyller inte kraven för att köras på Azure. Se till att dina avbildningar är korrekt för beredda innan du försöker etablera dem i Azure. 


Följande artiklar illustrerar stegen för att förbereda olika Linux-distributioner som stöds i Azure:

- [CentOS-baserade distributioner](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES och openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Andra: icke-godkända distributioner](create-upload-generic.md)

För de [Azure Cloud-Init-avbildningar som stöds](./using-cloud-init.md)har Linux-distributionerna redan alla nödvändiga paket och konfigurationer på plats för att tillhandahålla avbildningen i Azure korrekt. Om du upptäcker att den virtuella datorn inte kan skapas från din egen granskade avbildning kan du prova med en Azure Marketplace-avbildning som stöds och som redan har kon figurer ATS för Cloud-Init, med valfri `customData` . Om `customData` fungerar korrekt med en Azure Marketplace-avbildning beror det förmodligen på ett problem med din granskade avbildning.

## <a name="step-3-collect--review-vm-logs"></a>Steg 3: samla in & granska VM-loggar

När den virtuella datorn inte kan etableras visar Azure status för att skapa, i 20 minuter, och startar sedan om den virtuella datorn och väntar ytterligare 20 minuter innan du väljer att avsluta den virtuella dator distributionen, innan du slutligen markerar den som misslyckad, innan du slutligen markerar den med ett `OSProvisioningTimedOut` fel.

När den virtuella datorn körs behöver du loggarna från den virtuella datorn för att förstå varför etableringen misslyckades.  För att förstå varför VM-etableringen misslyckades, stoppa inte den virtuella datorn. Behåll den virtuella datorn som körs. Du måste behålla den felande virtuella datorn i ett körnings tillstånd för att kunna samla in loggar. Använd någon av följande metoder för att samla in loggarna:

- [Seriekonsol](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- [Aktivera startdiagnostik](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics) innan du skapar den virtuella datorn och [Visa](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics) dem under starten.

- [Kör AZ VM Repair](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands) för att ansluta och montera OS-disken, vilket gör att du kan samla in dessa loggar:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Om du vill starta inledande fel sökning börjar du med Cloud-Init-loggarna och förstår var felet inträffade och använder sedan de andra loggarna för att få fram en djupgående och ger ytterligare insikter. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Serie-/start loggar

I alla loggar börjar du söka efter "misslyckades", "varning", "varna", "ERR", "Error", "ERROR". Inställning av konfiguration som ignorerar Skift läges känsliga sökningar rekommenderas. 

> [!TIP]
> Om du felsöker en anpassad avbildning bör du överväga att lägga till en användare under avbildningen. Om etableringen inte kan ställa in administratörs användaren kan du fortfarande logga in på operativ systemet.

## <a name="analyzing-the-logs"></a>Analysera loggarna

Här är mer information om vad du kan söka efter i varje Cloud-Init-logg.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Som standard skrivs alla Cloud-Init-händelser med prioriteten fel sökning eller högre `/var/log/cloud-init.log` . Detta ger utförliga loggar för alla händelser som inträffade under initieringen av Cloud-init. 

Exempel:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


När du har hittat ett fel eller en varning läser du bakåt i Cloud-Init-loggen för att ta reda på vad Cloud-Init försökte innan det nådde felet eller varningen. I många fall kommer Cloud-Init att köra OS-kommandon eller utföra etablerings åtgärder före felet, vilket kan ge insikter om varför fel visas i loggarna. I följande exempel visas att Cloud-Init försökte montera en enhet direkt innan den påträffade ett fel.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Om du har åtkomst till den [seriella konsolen](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)kan du försöka köra kommandot igen som Cloud-Init försökte köra.

Loggningen för `/var/log/cloud-init.log` kan också konfigureras om i/etc/cloud/cloud.cfg.d/05_logging. cfg. Mer information om Cloud-Init-loggning finns i dokumentationen om [Cloud-Init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

Du kan hämta information från `stdout` och `stderr` under [stegen i Cloud-Init](cloud-init-deep-dive.md). Detta omfattar vanligt vis information om routningstabell, nätverksinformation, information om verifiering av SSH-värdnamn `stdout` och `stderr` för varje steg i Cloud-Init, tillsammans med tidsstämpeln för varje steg. Om du vill kan du `stderr` `stdout` Konfigurera om loggningen från `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Serie-/start loggar 

Cloud-Init har flera beroenden, de dokumenteras i de nödvändiga förutsättningarna för avbildningar på Azure, till exempel nätverk, lagring, möjlighet att montera en ISO och montera och formatera den temporära disken. Alla dessa kan utlösa fel och leda till att Cloud-Init Miss lyckas. Om den virtuella datorn exempelvis inte kan hämta ett DHCP-lån kommer Cloud-Init att Miss Miss läge.

Om du fortfarande inte kan isolera varför Cloud-Init inte kunde etableras måste du förstå vilka Cloud-Init-steg och när moduler körs. Mer information finns i [simhopp djupare i Cloud-Init](cloud-init-deep-dive.md) .


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Steg 4: Undersök varför konfigurationen inte används
Alla fel i Cloud-Init resulterar i ett oåterkalleligt etablerings fel. Om du till exempel använder `runcmd` modulen i en Cloud-Init-konfiguration kommer en kod som inte är noll från det kommando som körs att leda till att den virtuella dator etableringen Miss fungerar. Detta beror på att den körs efter grundläggande etablerings funktioner som sker i de första tre stegen i Cloud-init. Om du vill felsöka varför konfigurationen inte tillämpades granskar du loggarna i steg 3 och Cloud-Init-moduler manuellt. Exempel:

- `runcmd` – körs skripten utan fel? Kör konfigurationen manuellt från terminalen för att säkerställa att de körs som förväntat.
- Installerar paket – har den virtuella datorn åtkomst till paket arkiven?
- Du bör också kontrol lera den `customData` data konfiguration som tillhandahölls för den virtuella datorn. den finns i `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Nästa steg

Om du fortfarande inte kan isolera varför Cloud-Init inte har kört konfigurationen måste du titta närmare på vad som händer i varje Cloud-Init-steg och när moduler körs. Mer information finns i [simhopp djupare i Cloud-Init-konfigurationen](./cloud-init-deep-dive.md) .