---
title: Fel sökning av virtuella datorer (VM)-certifiering för Azure Marketplace
description: Felsök vanliga problem som rör testning och certifiering av virtuella dator avbildningar för Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 01/18/2021
ms.openlocfilehash: f802292d9723179b36d5291993bd4e07487fe6a8
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567389"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Felsöka virtuell dator certifiering

När du publicerar avbildningen av den virtuella datorn (VM) till Azure Marketplace, verifierar Azure-teamet om det för att säkerställa att det är startbart, säkert och kompatibelt med Azure. Om den virtuella dator avbildningen Miss lyckas med något av testerna med hög kvalitet publiceras den inte. Du får ett fel meddelande som beskriver problemet.

I den här artikeln beskrivs vanliga fel meddelanden under publicering av VM-avbildningar, tillsammans med relaterade lösningar.

> [!NOTE]
> Kontakta [supporten för partner Center](https://aka.ms/marketplacepublishersupport)om du har frågor om den här artikeln eller förslag på förbättringar.

## <a name="vm-extension-failure"></a>Problem med VM-tillägg

Kontrol lera om avbildningen har stöd för VM-tillägg.

Så här aktiverar du VM-tillägg:

1. Välj din virtuella Linux-dator.
1. Gå till **diagnostikinställningar**.
1. Aktivera bas-matriser genom att uppdatera **lagrings kontot**.
1. Välj **Spara**.

   ![Skärm bild som visar hur du aktiverar övervakning på gästnivå.](./media/create-vm/vm-certification-issues-solutions-1.png)

Så här kontrollerar du att VM-tilläggen är korrekt aktiverade:

1. I den virtuella datorn väljer du fliken **VM-tillägg** och kontrollerar sedan statusen för **Linux Diagnostics-tillägget**.
1. Kontrol lera etablerings statusen.

   - Om statusen är *etableringen slutförd* är testerna för tilläggen klara.  
   - Om statusen är *etableringen Miss lyckas* tilläggets test fall och du måste ange den härdade flaggan.

   ![Skärm bild som visar att etableringen har slutförts.](./media/create-vm/vm-certification-issues-solutions-2.png)

   Om det inte går att använda tillägget för virtuell dator kan du läsa mer i [använda Linux-tillägg för att övervaka mått och loggar](../virtual-machines/extensions/diagnostics-linux.md) . Om du inte vill att VM-tillägget ska aktive ras kontaktar du support teamet och ber dem att inaktivera det.

## <a name="vm-provisioning-issue"></a>Problem med VM-etablering

Kontrol lera att du har följt den virtuella dator etablerings processen noggrant innan du skickar in erbjudandet. Information om hur du visar JSON-formatet för etablering av den virtuella datorn finns i [testa en avbildning av en virtuell dator](azure-vm-image-test.md).

Etablerings problem kan omfatta följande fel scenarier:

|Scenario|Fel|Orsak|Lösning|
|---|---|---|---|
|1|Ogiltig virtuell hård disk (VHD)|Om det angivna cookie-värdet i VHD-foten är felaktigt anses den virtuella hård disken vara ogiltig.|Återskapa avbildningen och skicka begäran.|
|2|Ogiltig Blob-typ|VM-etableringen misslyckades eftersom det använda blocket är en Blob-typ i stället för en typ av sida.|Återskapa avbildningen och skicka begäran.|
|3|Tids gräns för etablering eller inte generaliseras korrekt|Det finns ett problem med VM-generalisering.|Återskapa avbildningen med generalisering och skicka begäran.|
|

> [!NOTE]
> Mer information om VM-generalisering finns i:
> - [Linux-dokumentation](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows-dokumentation](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="vhd-specifications"></a>VHD-specifikationer

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie och andra VHD-specifikationer

Strängen "conectix" är en del av VHD-specifikationen. Den definieras som en cookie med 8 byte i VHD-foten som identifierar filens skapare. Alla VHD-filer som skapats av Microsoft har denna cookie.

En VHD-formaterad BLOB måste ha en 512 byte-sidfot i detta format:

|Fält för hård disk foten|Storlek (byte)|
|---|---|
Cookie|8
Funktioner|4
Fil format version|4
Data förskjutning|8
Tidsstämpel|4
Skapare program|4
Creator-version|4
Skapare värd operativ system|4
Ursprunglig storlek|8
Aktuell storlek|8
Disk geometri|4
Disktyp|4
Kontrollsumma|4
Unikt ID|16
Sparat tillstånd|1
Reserverat|427
|

### <a name="vhd-specifications"></a>VHD-specifikationer

Säkerställ att den virtuella hård disken uppfyller följande kriterier för att säkerställa en smidig publicerings upplevelse:

- Cookien innehåller strängen ' conectix '.
- Disk typen är fast.
- Den virtuella hård diskens storlek är minst 20 MB.
- Den virtuella hård disken är justerad. Den virtuella storleken måste vara en multipel av 1 MB.
- Den virtuella hård diskens BLOB-längd är lika med den virtuella storleken och längden på VHD-foten (512).

Hämta [VHD-specifikationen](https://www.microsoft.com/download/details.aspx?id=23850).

## <a name="software-compliance-for-windows"></a>Program varans kompatibilitet för Windows

Om din begäran om Windows-avbildning avvisas på grund av ett problem med program varu kompatibilitet, kan du ha skapat en Windows-avbildning med en installerad SQL Server instans. I stället måste du ta hänsyn till den relevanta SQL Servers versions bas avbildningen från Azure Marketplace.

Skapa inte en egen Windows-avbildning med SQL Server installerad. Använd de godkända SQL Server Base-avbildningarna (Enterprise/standard/Web) från Azure Marketplace.

Om du försöker installera Visual Studio eller någon Office-licensierad produkt kontaktar du support teamet för tidigare godkännande.

Mer information om hur du väljer en godkänd bas finns i [skapa en virtuell dator från en godkänd bas](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Körningen av Toolkit-Testfallet misslyckades

Microsoft Certificate Toolkit kan hjälpa dig att köra test ärenden och kontrol lera att den virtuella hård disken eller avbildningen är kompatibel med Azure-miljön.

Hämta [Microsoft Certificate Toolkit](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Scenarier för Linux-test

I följande tabell visas de Linux-testfall där Toolkit körs. Test verifiering anges i beskrivningen.

|Scenario|Testfall|Beskrivning|
|---|---|---|
|1|Bash-historik|Bash historik-filer bör rensas innan du skapar den virtuella dator avbildningen.|
|2|Linux-agentens version|Azure Linux-agentens 2.2.41 eller senare bör installeras.|
|3|Nödvändiga kernel-parametrar|Verifierar att följande kernel-parametrar har angetts: <br>konsol = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|Byt partition på OS-disk|Kontrollerar att växlings partitionerna inte skapas på OS-disken.|
|5|Rot partition på OS-disk|Skapa en enda rotnod för OS-disken.|
|6|OpenSSL-version|OpenSSL-versionen ska vara v 0.9.8 eller senare.|
|7|Python-version|Python version 2,6 eller senare rekommenderas starkt.|
|8|Klientens Alive-intervall|Ange ClientAliveInterval till 180. Det kan ställas in från 30 till 235 på programmets behov. Om du aktiverar SSH för dina slutanvändare måste det här värdet anges som förklarat.|
|9|Operativsystemarkitektur|Endast 64-bitars operativsystem stöds.|
|10|Automatisk uppdatering|Anger om automatisk uppdatering av Linux-Agent är aktiverat.|
|

### <a name="common-test-case-errors"></a>Vanliga test fall fel

Se följande tabell för vanliga fel som kan uppstå när du kör test fall:

| Scenario | Testfall | Fel | Lösning |
| --- | --- | --- | --- |
| 1 | Test fall för Linux-agentens version | Den lägsta Linux-agentens version är 2.2.41 eller senare. Detta krav har varit obligatoriskt sedan den 1 maj 2020. | Uppdatera versionen av Linux-agenten. Det bör vara 2,241 eller senare. Mer information finns på [sidan om versions uppdatering för Linux-agenten](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Test väska för bash-historik | Ett fel inträffar om storleken på bash-historiken i den skickade avbildningen är större än 1 KB. Storleken är begränsad till 1 KB för att se till att bash-historik filen inte innehåller någon potentiellt känslig information. | Lös genom att montera den virtuella hård disken till en annan fungerande virtuell dator och ändra storleken till 1 KB eller mindre. Ta till exempel bort `.bash` historikfilerna. |
| 3 | Nödvändigt test fall för kernel-parameter | Du får det här felet när värdet för `console` inte är inställt på `ttyS0` . Kontrol lera genom att köra följande kommando: <br /> `cat /proc/cmdline` | Ange värdet för `console` till `ttyS0` och skicka begäran på nytt. |
| 4 | Test fall för ClientAlive-intervall | Om verktygs lådan ger dig ett misslyckat resultat för det här test fallet finns det ett olämpligt värde för `ClientAliveInterval` . | Ange värdet till `ClientAliveInterval` mindre än eller lika med 235 och skicka begäran på nytt. |
|

### <a name="windows-test-cases"></a>Windows-testfall

I följande tabell visas de Windows-testfall där verktygs uppsättningen ska köras, tillsammans med en beskrivning av test verifieringen:

|Scenario |Testfall|Beskrivning|
|---|---|---|
|1|Operativsystemarkitektur|Azure stöder endast 64-bitars operativ system.|
|2|Användar konto beroende|Program körningen bör inte vara beroende av administratörs kontot.|
|3|Redundanskluster|Funktionen kluster för växling vid fel i Windows Server stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|4|IPV6|IPv6 stöds ännu inte i Azure-miljön. Programmet bör inte vara beroende av den här funktionen.|
|5|DHCP|Dynamic Host Configuration Protocol server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|6|Hyper-V|Rollen Hyper-V-Server stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|7|Fjärråtkomst|Fjärråtkomst (Direct Access) Server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|8|Rights Management tjänster|Rights Management tjänster. Server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|9|Windows Deployment Services|Windows Deployment Services. Server rollen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|10|BitLocker-diskkryptering|BitLocker-diskkryptering stöds inte på operativ systemets hård disk, men det kan användas på data diskar.|
|11|Namn server för Internet Storage|Funktionen Internet Storage Name Server stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|12|Multipath I/O|Multipath I/O. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|13|Utjämning av nätverksbelastning|Utjämning av nätverks belastning. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|14|Peer Name Resolution Protocol|Peer Name Resolution Protocol. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|15|SNMP-tjänster|Tjänst funktionen Simple Network Management Protocol (SNMP) stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|16|Windows Internet Name Service|Windows Internet Name Service. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|17|Trådlös LAN-tjänst|Tjänsten Wireless LAN. Den här server funktionen stöds inte ännu. Programmet bör inte vara beroende av den här funktionen.|
|

Om du kommer över eventuella problem med föregående test fall, se kolumnen **Beskrivning** i tabellen för lösningen. Kontakta support teamet om du vill ha mer information.

## <a name="data-disk-size-verification"></a>Verifiering av data disk storlek

Data disk begär Anden med en storlek som är större än 1023 GB (GB) godkänns inte. Den här regeln gäller för både Linux och Windows.

Skicka begäran igen med en storlek som är mindre än eller lika med 1023 GB.

## <a name="os-disk-size-validation"></a>Verifiering av OS-diskens storlek

Se följande regler för begränsningar av storleken på OS-disken. När du skickar en begäran kontrollerar du att storleken på operativ system disken är inom begränsningen för Linux eller Windows.

|Operativsystem|Rekommenderad VHD-storlek|
|---|---|
|Linux|1 GB till 1023 GB|
|Windows|30 GB till 250 GB|
|

Eftersom de virtuella datorerna tillåter åtkomst till det underliggande operativ systemet kontrollerar du att VHD-storleken är tillräckligt stor för den virtuella hård disken. Diskar går inte att utöka utan nedtid. Använd en disk storlek från 30 GB till 50 GB.

|VHD-storlek|Faktisk naturlig storlek|Lösning|
|---|---|---|
|>500 tebibyte (TiB)|saknas|Kontakta support teamet om du vill ha ett undantags godkännande.|
|250-500 TiB|>200-gibibyte (GiB) skillnad från BLOB-storlek|Kontakta support teamet om du vill ha ett undantags godkännande.|
|

> [!NOTE]
> Större disk storlekar medför högre kostnader och leder till en fördröjning under installationen och replikeringen. På grund av denna fördröjning och kostnad kan support teamet söka efter motivering för undantags godkännande.

## <a name="wannacry-patch-verification-test-for-windows"></a>Verifierings test för WannaCry-korrigering för Windows

För att förhindra en potentiell attack som är relaterad till WannaCry virus, se till att alla Windows-avbildningar har uppdaterats med den senaste korrigeringen.

Du kan kontrol lera avbildnings fil versionen från `C:\windows\system32\drivers\srv.sys` eller `srv2.sys` .

I följande tabell visas den lägsta korrigerings versionen av Windows Server:

|Operativsystem|Version|
|---|---|
|Windows servar 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

> [!NOTE]
> Windows Server 2019 saknar nödvändiga versions krav.

## <a name="sack-vulnerability-patch-verification"></a>Bekräftelse av sårbarhets sårbarhets korrigering

När du skickar en Linux-avbildning kan din begäran avvisas på grund av problem med kernel-versionen.

Uppdatera kärnan med en godkänd version och skicka begäran på nytt. Du hittar den godkända kernel-versionen i följande tabell. Versions numret måste vara lika med eller större än det tal som anges här.

Om avbildningen inte är installerad med någon av följande kernel-versioner uppdaterar du den med rätt korrigeringar. Begär det godkännande som krävs från support teamet efter att avbildningen har uppdaterats med de här korrigeringarna:

- CVE – 2019-11477
- CVE – 2019-11478
- CVE – 2019-11479

|OS-familj|Version|Kernel|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0 – 151| 
||14,04 LTS|4.15.0-1049- \* -Azure|
||16,04 LTS|4.15.0 – 1049|
||18,04 LTS|4.18.0 – 1023|
||18,04 LTS|5.0.0 – 1025|
||18,10|4.18.0 – 1023|
||19,04|5.0.0 – 1010|
||19,04|5.3.0 – 1004|
|RHEL och% OS|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7,4|3.10.0-693.50.3|
||7.5|3.10.0-862.34.2|
||7,6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0 – 147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL – SAP 7,4|TBD|
||RHEL – SAP 7,5|TBD|
|SLES|SLES11SP4 (inklusive SAP)|3.0.101-108.95.2|
||SLES12SP1 för SAP|3.12.74-60.64.115.1|
||SLES12SP2 för SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 för SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 för SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 för SAP|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0 – 7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK följer RHEL ovan|
||7,6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|Core-stabila 2079.6.0|4.19.43\*|
||Beta 2135.3.1|4.19.50\*|
||Alpha-2163.2.1|4.19.50\*|
|Debian|Jessie (säkerhet)|3.16.68-2|
||Jessie backports|4.9.168-1 + deb9u3|
||Sträck ut (säkerhet)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, sid (sträck ut backports)|4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Bild storleken måste vara i multipler av megabyte

Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till multipler av 1 megabyte (MB). Om din virtuella hård disk inte följer den rekommenderade virtuella storleken kan din begäran avvisas.

Följ rikt linjerna när du konverterar från en RAW-disk till en virtuell hård disk. Se till att storleken på den råa disken är en multipel av 1 MB. Mer information finns i [information om icke-godkända distributioner](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Åtkomst till virtuell dator nekades

Ett problem med _nekad åtkomst_ för att köra ett test fall på den virtuella datorn kan bero på otillräcklig behörighet.

Kontrol lera att du har aktiverat rätt åtkomst för det konto som självtests fall körs på. Aktivera åtkomst för att köra test ärenden om den inte är aktive rad. Om du inte vill aktivera åtkomst kan du dela resultatet av självtesten med support teamet.

Skicka din begäran med SSH-inaktive rad avbildning för certifierings processen:

1. Kör det [senaste certifierings test verktyget för virtuella Azure-datorer](https://aka.ms/AzureCertificationTestTool) på din avbildning.

2. Utlös ett [support ärende](https://aka.ms/marketplacepublishersupport). Se till att lägga till verktygs rapporten och tillhandahålla erbjudande information:
   - Erbjudandets namn
   - Utgivarnamn
   - Plan-ID/SKU och version

3. Skicka in din begäran om certifiering.

## <a name="download-failure"></a>Nedladdnings problem

I följande tabell finns några problem som kan uppstå när du laddar ned den virtuella dator avbildningen med en URL för signatur för delad åtkomst (SAS).

|Fel|Orsak|Lösning|
|---|---|---|
|Blobben hittades inte|Den virtuella hård disken kan antingen tas bort eller flyttas från den angivna platsen.|| 
|BLOB som används|Den virtuella hård disken används av en annan intern process.|Den virtuella hård disken ska vara i ett använt tillstånd när du laddar ned den med en SAS-URL.|
|Ogiltig SAS-URL|Den tillhör ande SAS-URL: en för den virtuella hård disken är felaktig.|Hämta rätt SAS-URL.|
|Ogiltig signatur|Den tillhör ande SAS-URL: en för den virtuella hård disken är felaktig.|Hämta rätt SAS-URL.|
|Villkorlig HTTP-rubrik|SAS-webbadressen är ogiltig.|Hämta rätt SAS-URL.|
|Ogiltigt VHD-namn|Kontrol lera om det finns specialtecken, till exempel ett procent tecken `%` eller citat tecken `"` , i VHD-namnet.|Byt namn på VHD-filen genom att ta bort specialtecknen.|
|

## <a name="first-1-mb-2048-sectors-each-sector-of-512-bytes-partition"></a>Första 1 MB (2048 sektorer, varje sektor på 512 byte) partition

Om du [skapar en egen avbildning](azure-vm-create-using-own-image.md)ser du till att de första 2048 sektorerna (1 MB) av OS-disken är tomma. Annars fungerar inte publiceringen. Detta krav gäller endast för operativ system diskar (inte data diskar). Om du skapar din avbildning [från en godkänd bas](azure-vm-create-using-approved-base.md)kan du hoppa över det här kravet.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-on-an-empty-vhd"></a>Skapa en 1 MB (2048 sektorer, varje sektor på 512 byte) partition på en tom virtuell hård disk

De här stegen gäller endast för Linux.

1. Skapa alla typer av virtuella Linux-datorer, till exempel Ubuntu,% OS eller andra. Fyll i de obligatoriska fälten och välj **Nästa: diskar >**.

   ![Skärm bild som visar sidan Skapa en virtuell dator med knappen Nästa: diskar kommando markerat.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Skapa en ohanterad disk för den virtuella datorn.

   Använd antingen standardvärdena eller ange ett värde för fält som NIC, NSG och offentlig IP-adress.

   ![Skärm bild av sidan data diskar i skapa ett flöde för virtuella datorer.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. När du har skapat den virtuella datorn väljer du **diskar** i det vänstra fönstret.

   ![Skärm bild som visar hur du väljer diskar för en virtuell dator.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Anslut den virtuella hård disken som en data disk till den virtuella datorn för att skapa en partitionstabell.

   1. Välj **Lägg till DataDisk**  >  **befintlig BLOB**.

      ![Skärm bild som visar hur du lägger till en datadisk till din virtuella hård disk.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Hitta ditt lagrings konto för virtuella hård diskar.
   1. Välj **behållare** och välj sedan din virtuella hård disk.
   1. Välj **OK**.

      ![Skärm bild av sidan bifoga ohanterad disk.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Din virtuella hård disk läggs till som data disk LUN 0.

   1. Starta om den virtuella datorn.

1. När du har startat om den virtuella datorn loggar du in på den virtuella datorn med hjälp av SparaTillFil eller en annan klient och kör `sudo  -i` kommandot för att få rot åtkomst.

   ![Kommando rads skärm för SparaTillFil-klient som visar kommandot sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Skapa en partition på din virtuella hård disk.

   1. Ange `fdisk /dev/sdb` kommando.
   1. Om du vill visa listan över befintliga partitioner från din virtuella hård disk anger du `p` .
   1. Ange `d` om du vill ta bort alla befintliga partitioner som är tillgängliga i din virtuella hård disk. Du kan hoppa över det här steget, om det inte behövs.

      ![Kommando rads skärm för SparaTillFil-klient visar kommandon för att ta bort befintliga partitioner.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Ange för `n` att skapa en ny partition och välj `p` för (primär partition).

   1. Ange 2048 som _första sektor_ värde. Du kan lämna den _sista sektorn_ som standardvärde.

      >[!IMPORTANT]
      >Befintliga data raderas till 2048 sektorer (varje sektor av 512 byte). Säkerhets kopiering av den virtuella hård disken innan du skapar en ny partition.

      ![Kommando rads skärm för SparaTillFil-klient visar kommandon och utdata för raderade data.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Skriv `w` för att bekräfta att partitionen skapas.

      ![Kommando rads skärm för SparaTillFil-klient visar kommandon för att skapa en partition.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. Du kan kontrol lera partitionstabellen genom att köra kommandot `n fdisk /dev/sdb` och skriva `p` . Du ser att partitionen skapas med 2048 offset-värde.

      ![Kommando rads skärm för SparaTillFil-klient visar kommandon för att skapa 2048-förskjutningen.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Koppla från den virtuella hård disken från den virtuella datorn och ta bort den virtuella datorn.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-by-moving-existing-data-on-vhd"></a>Skapa en 1 MB (2048 sektorer, varje sektor på 512 byte) partition genom att flytta befintliga data på den virtuella hård disken

De här stegen gäller endast för Linux.

1. Skapa alla typer av virtuella Linux-datorer, till exempel Ubuntu,% OS eller andra. Fyll i de obligatoriska fälten och välj **Nästa: diskar >**.

   ![Skärm bild som visar sidan Skapa en virtuell dator med knappen Nästa: diskar kommando markerat.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Skapa en ohanterad disk för den virtuella datorn.

   ![Skärm bild av sidan data diskar i skapa ett flöde för virtuella datorer.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Använd antingen standardvärdena eller ange ett värde för fält som NIC, NSG och offentlig IP-adress.

1. När du har skapat den virtuella datorn väljer du **diskar** i det vänstra fönstret.

   ![Skärm bild som visar hur du väljer diskar för en virtuell dator.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Anslut den virtuella hård disken som en data disk till den virtuella datorn för att skapa en partitionstabell.

   1. Anslut den virtuella hård disken som en data disk till den virtuella datorn för att skapa en partitionstabell.

   1. Välj **Lägg till DataDisk**  >  **befintlig BLOB**.

      ![Skärm bild som visar hur du lägger till en datadisk till din virtuella hård disk.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Hitta ditt lagrings konto för virtuella hård diskar.
   1. Välj **behållare** och välj sedan din virtuella hård disk.
   1. Välj **OK**.

      ![Skärm bild av sidan bifoga ohanterad disk.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Din virtuella hård disk läggs till som data disk LUN 0.

   1. Starta om den virtuella datorn.

1. Logga in på den virtuella datorn med SparaTillFil eller någon annan klient och kör `sudo  -i` kommando för att få rot åtkomst.

   ![Kommando rads skärm för SparaTillFil-klient som visar inloggning och sudo-i-kommandot.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Kör kommandot `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`.

   ![Kommando rad för SparaTillFil-klientens kommando rad som visar körningen av kommandona.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Det kan ta lite tid att slutföra kommandot eftersom det beror på diskens storlek.

1. Koppla från den virtuella hård disken från den virtuella datorn och ta bort den virtuella datorn.


## <a name="default-credentials"></a>Standardautentiseringsuppgifter

Skicka aldrig standardautentiseringsuppgifter med den skickade virtuella hård disken. Om du lägger till standardautentiseringsuppgifter blir den virtuella hård disken mer sårbar för säkerhetshot. Skapa i stället dina egna autentiseringsuppgifter när du skickar den virtuella hård disken.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk mappas felaktigt

Ett mappnings problem kan uppstå när en begäran skickas med flera data diskar som inte är i följd. Till exempel måste nummer ordningen för tre data diskar vara *0, 1, 2*. En annan ordning behandlas som ett mappnings problem.

Skicka begäran igen med korrekt ordningsföljd av data diskar.

## <a name="incorrect-os-mapping"></a>Felaktig OS-mappning

När en avbildning skapas, kan den mappas till eller tilldelas fel OS-etikett. Om du till exempel väljer **Windows** som en del av namnet på operativ systemet när du skapar avbildningen bör operativ system disken endast installeras med Windows. Samma krav gäller för Linux.

## <a name="vm-not-generalized"></a>Den virtuella datorn är inte generaliserad

Om alla avbildningar som tas från Azure Marketplace ska återanvändas måste det virtuella operativ systemet vara generaliserat.

- För **Linux** generaliserar följande process en virtuell Linux-dator och distribuerar den igen som en separat virtuell dator.

  I SSH-fönstret anger du följande kommando: `sudo waagent -deprovision+user` .

- För **Windows** generaliserar du Windows-avbildningar med hjälp av `sysreptool` .

  Mer information om `sysreptool` verktyget finns i [Översikt över system förberedelse (Sysprep)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>DataDisk-fel

Använd följande tabell för lösningar på fel som är relaterade till data disken:

|Fel|Orsak|Lösning|
|---|---|---|
|`DataDisk- InvalidUrl:`|Det här felet kan inträffa på grund av en ogiltig Logical Unit Number (LUN) när erbjudandet skickas.|Kontrol lera att LUN-nummersekvensen för data disken är i Partner Center.|
|`DataDisk- NotFound:`|Det här felet kan inträffa på grund av att en datadisk inte finns på en angiven SAS-URL.|Kontrol lera att data disken finns på den angivna SAS-webbadressen.|

## <a name="remote-access-issue"></a>Problem med fjärråtkomst

Du får det här felet om alternativet Remote Desktop Protocol (RDP) inte är aktiverat för Windows-avbildningen.

Aktivera RDP-åtkomst för Windows-avbildningar innan du skickar dem.

## <a name="bash-history-failed"></a>Bash-historik misslyckades

Du ser det här felet om storleken på bash-historiken i den skickade avbildningen är större än 1 KB. Storleken är begränsad till 1 KB för att begränsa filen från att innehålla potentiellt känslig information.

Så här tar du bort bash-historiken:

1. Distribuera den virtuella datorn och välj **kommandot kör** på Azure Portal.

   ![Skärm bild av Azure Portal med alternativet Kör kommando i det vänstra fönstret.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Välj det första alternativet **RunShellScript** och kör sedan kommandot: `cat /dev/null > ~/.bash_history && history -c` .

   ![Skärm bild av sidan Kör kommando skript på Azure Portal.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Starta om den virtuella datorn när kommandot har körts.

1. Generalisera den virtuella datorn, ta avbildningen av den virtuella hård disken och stoppa den virtuella datorn.

1. Skicka den generaliserade avbildningen igen.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Begär ett undantag på VM-avbildningar för urvals test

Utgivare kan begära undantag för några tester som utförs under VM-certifieringen. Undantag anges i sällsynta fall när en utgivare tillhandahåller bevis för att stödja begäran. Certifierings teamet förbehåller sig rätten att neka eller godkänna undantag när som helst.

I det här avsnittet beskrivs allmänna scenarier där utgivare begär ett undantag och hur du begär ett.

### <a name="scenarios-for-exception"></a>Scenarier för undantag

Utgivare begär undantag i allmänhet i följande fall:

- **Undantag för ett eller flera test fall**. Kontakta [partner Center-supporten](https://aka.ms/marketplacepublishersupport) för att begära undantag för test ärenden.

- **Låsning av virtuella datorer/ingen rot åtkomst**. Några få utgivare har scenarier där virtuella datorer måste låsas, eftersom de har program vara som brand väggar som är installerade på den virtuella datorn. I det här fallet laddar du ned [verktyget för certifierad testning](https://aka.ms/AzureCertificationTestTool) och skickar rapporten till [supporten för partner Center](https://aka.ms/marketplacepublishersupport).

- **Anpassade mallar**. Vissa utgivare publicerar VM-avbildningar som kräver en anpassad Azure Resource Manager-mall (ARM) för att distribuera de virtuella datorerna. I det här fallet skickar du de anpassade mallarna på [supporten för partner Center](https://aka.ms/marketplacepublishersupport) så att den kan användas av certifierings teamet för verifiering.

### <a name="information-to-provide-for-exception-scenarios"></a>Information som ska tillhandahållas för undantags scenarier

Kontakta [partner Center-supporten](https://aka.ms/marketplacepublishersupport) om du vill begära ett undantag för något av scenarierna och inkludera följande information:

- **Utgivar-ID**. Ange ditt Publisher-ID för partner Central Portal.
- **Erbjudande-ID/namn**. Ange erbjudande-ID eller namn.
- **SKU/plan-ID**. Skriv plan-ID eller SKU för det virtuella dator erbjudandet.
- **Version**. Ange den version av VM-erbjudandet som kräver ett undantag.
- **Undantags typ**. Välj bland test, låst virtuell dator eller anpassade mallar.
- **Orsak till begäran**. Ta med orsaken till undantags förfrågan, plus all information om test undantag.
- **Tids linje**. Ange slutdatum för ditt undantag.
- **Bifogad fil**. Bifogade viktiga bevis dokument:

  - Bifoga test rapporten för låsta virtuella datorer.
  - Ange den anpassade ARM-mallen som bilaga för anpassade mallar.

  Om du inte kan ta med dessa bilagor nekas din begäran.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Åtgärda ett problem eller en sårbarhet i ett virtuellt dator erbjudande

I det här avsnittet beskrivs hur du anger en ny VM-avbildning när en sårbarhet eller sårbarhet identifieras med en av dina VM-avbildningar. Den gäller endast för virtuella Azure-datorer som publicerats på Azure Marketplace.

> [!NOTE]
> Du kan inte ta bort den senaste virtuella dator avbildningen från en plan eller stoppa – sälja den sista planen för ett erbjudande.

Gör något av följande:

- Om du har en ny VM-avbildning för att ersätta den sårbara VM-avbildningen, se [Ange en fast VM-avbildning](#provide-a-fixed-vm-image).
- Om du inte har en ny VM-avbildning för att ersätta den enda avbildningen av den virtuella datorn i en plan, eller om du är klar med planen, [slutar du att sälja planen](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Om du inte planerar att ersätta den enda avbildningen av den virtuella datorn i erbjudandet rekommenderar vi att du [slutar sälja erbjudandet](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Ange en fast VM-avbildning

För att tillhandahålla en fast VM-avbildning för att ersätta en VM-avbildning som har sårbarhet eller sårbarhet:

1. Ange en ny VM-avbildning för att lösa säkerhets risker eller sårbarhet.
1. Ta bort den virtuella dator avbildningen med säkerhets sårbarhet eller sårbarhet.
1. Publicera om erbjudandet.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Skapa en ny avbildning av virtuella datorer för att lösa säkerhets risker eller sårbarhet

Slutför de här stegen genom att förbereda de tekniska till gångarna för den VM-avbildning som du vill lägga till. Mer information finns i [skapa en virtuell dator med en godkänd bas](azure-vm-create-using-approved-base.md) eller [skapa en virtuell dator med hjälp av en egen avbildning](azure-vm-create-using-own-image.md) och [Generera en SAS-URI för din VM-avbildning](azure-vm-get-sas-uri.md).

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
1. I det vänstra fönstret väljer du **kommersiell Marketplace**-  >  **Översikt**.
1. I kolumnen **erbjudande alias** väljer du erbjudandet.
1. På fliken **plan översikt** i kolumnen **namn** väljer du lämplig plan.
1. På fliken **teknisk konfiguration** , under **VM-avbildningar**, väljer du **+ Lägg till avbildning av virtuell dator**.

   > [!NOTE]
   > Du kan bara lägga till en avbildning av en virtuell dator till en plan i taget. Om du vill lägga till flera avbildningar av virtuella datorer ska du publicera den första innan du lägger till nästa avbildning av virtuella datorer.

1. I rutorna som visas anger du en ny disk version och avbildningen av den virtuella datorn.
1. Välj **Spara utkast**.

Ta sedan bort den virtuella dator avbildningen med säkerhets risken.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Ta bort den virtuella dator avbildningen med säkerhets sårbarhet eller sårbarhet

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. I det vänstra fönstret väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. I kolumnen **erbjudande alias** väljer du erbjudandet.
4. På fliken **plan översikt** i kolumnen **namn** väljer du lämplig plan.
5. På fliken **teknisk konfiguration** , under **VM-avbildningar**, bredvid den VM-avbildning som du vill ta bort, väljer du **ta bort avbildning av virtuell dator**.
6. I dialog rutan väljer du **Fortsätt**.
7. Välj **Spara utkast**.

Publicera sedan erbjudandet igen.

#### <a name="republish-the-offer"></a>Publicera om erbjudandet

1. Välj **Granska och publicera**.
2. Om du behöver ange någon information till certifierings teamet lägger du till den i rutan **kommentarer för certifiering** .
3. Välj **Publicera**.

Information om hur du slutför publicerings processen finns i [Granska och publicera erbjudanden](review-publish-offer.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera egenskaper för VM-erbjudande](azure-vm-create-properties.md)
- [Aktiva marknads förmåner](partner-center-portal/marketplace-rewards.md)
- Kontakta [supporten för partner Center](https://aka.ms/marketplacepublishersupport)om du har frågor eller feedback om förbättringar.
