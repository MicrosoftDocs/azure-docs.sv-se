---
title: Vad är nytt i Azure Site Recovery
description: Innehåller en sammanfattning av nya funktioner och de senaste uppdateringarna i Azure Site Recoverys tjänsten.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 51d54ca4d46af3b9e0746f36114fadc47edd5b1f
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805055"
---
# <a name="whats-new-in-site-recovery"></a>Nyheter i Site Recovery

Tjänsten [Azure Site Recovery](site-recovery-overview.md) uppdateras och förbättras regelbundet. I den här artikeln får du information om de senaste versionerna, nya funktioner och nytt innehåll som hjälper dig att hålla dig uppdaterad. Den här sidan uppdateras regelbundet.

Du kan följa och prenumerera på Site Recovery uppdaterings meddelanden i [Azure updates](https://azure.microsoft.com/updates/?product=site-recovery) -kanalen.

## <a name="supported-updates"></a>Uppdateringar som stöds

För Site Recovery-komponenter stöder vi N-4-versioner, där N är den senaste utgivna versionen. Dessa sammanfattas i tabellen nedan.

**Uppdatera** |  **Enhetlig installation** | **Konfigurations Server för ägg** | **Mobilitets tjänst agent** | **Site Recovery Provider** | **Recovery Services agent**
--- | --- | --- | --- | --- | ---
[Samlad uppdatering 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 9.40.5850.1 | 5.1.6537.0 | 9.40.5850.1 | 5.1.6537.0  | 2.0.9202.0
[Samlad uppdatering 52](https://support.microsoft.com/help/4597409/)  | 9.39.5796.1 | 5.1.6458.0 | 9.39.5796.1 | 5.1.6458.0  | 2.0.9196.0
[Samlad uppdatering 51](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[Samlad uppdatering 50](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[Samlad uppdatering 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0


[Läs mer](service-updates-how-to.md) om installation och stöd för uppdateringar.

## <a name="updates-january-2021"></a>Uppdateringar (januari 2021)

### <a name="update-rollup-53"></a>Samlad uppdatering 53

Samlad [uppdatering 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen. 
**Haveri beredskap för Azure VM** | Replikering för Taggar stöds nu. Alla Taggar som läggs till i virtuella Azure-datorer, diskar och nätverkskort i käll regionen replikeras till datorer i mål regionen.<br/><br/> Virtuella Azure-datorer som kör Debian 10 stöds nu för replikering.
**VMware VM/fysisk haveri beredskap till Azure** | Uppdateringen innehåller loggnings förbättringar för VMware VM-replikering till Azure och förbättrade fel meddelanden.<br/><br/> Virtuella VMware-datorer och fysiska datorer som kör Debian 10 stöds nu för replikering.


## <a name="updates-november-2020"></a>Uppdateringar (november 2020)

### <a name="update-rollup-52"></a>Samlad uppdatering 52

Samlad [uppdatering 52](https://support.microsoft.com/help/4597409/update-rollup-52-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen, inklusive nytt Linux-stöd för mobilitets tjänsten.
**Haveri beredskap för Azure VM** | Stöds nu för virtuella datorer som kör RHEL 8,3 och Oracle Linux 7,9
**VMware VM/fysisk haveri beredskap till Azure** | Stöds nu för virtuella datorer som kör RHEL 8,3, Oracle Linux 7.9/8.3.

## <a name="updates-october-2020"></a>Uppdateringar (oktober 2020)

### <a name="update-rollup-51"></a>Samlad uppdatering 51

Samlad [uppdatering 51](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen, inklusive nytt Linux-stöd för mobilitets tjänsten.

## <a name="updates-september-2020"></a>Uppdateringar (september 2020)

### <a name="update-rollup-50"></a>Samlad uppdatering 50

Samlad [uppdatering 50](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen.

## <a name="updates-august-2020"></a>Uppdateringar (augusti 2020)

### <a name="update-rollup-49"></a>Samlad uppdatering 49

Samlad [uppdatering 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen, inklusive nytt Linux-stöd för mobilitets tjänsten.

## <a name="updates-july-2020"></a>Uppdateringar (juli 2020)

### <a name="update-rollup-48"></a>Samlad uppdatering 48

Samlad [uppdatering 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen.

> [!NOTE]
> Samlad uppdatering 48 har ett känt problem med att aktivera replikering för Linux-datorer som har krypterats med ADE. [Läs mer](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>Samlad uppdatering 47

Samlad [uppdatering 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen.

## <a name="updates-june-2020"></a>Uppdateringar (2020 juni)

### <a name="update-rollup-46"></a>Samlad uppdatering 46

Samlad [uppdatering 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen.

## <a name="updates-march-2020"></a>Uppdateringar (mars 2020)

### <a name="update-rollup-45"></a>Samlad uppdatering 45

Samlad [uppdatering 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar till Site Recovery agenter och leverantörer enligt beskrivningen i den samlade uppdateringen.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen.

## <a name="updates-january-2020"></a>Uppdateringar (januari 2020)

### <a name="update-rollup-44"></a>Samlad uppdatering 44

Samlad [uppdatering 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Det fanns inga uppdateringar för Site Recovery leverantörer och agenter.
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i den samlade uppdateringen.

### <a name="azure-vmware-disaster-recovery"></a>Katastrof återställning i Azure VMware

Azure Virtual Machines stöder nu VM-aktivering för kryptering i vila med Kundhanterade nycklar. [Läs mer](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Samlad uppdatering 43

Samlad [uppdatering 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar av Site Recovery agenter och providers (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)


## <a name="updates-november-2019"></a>Uppdateringar (november 2019)

### <a name="update-rollup-42"></a>Samlad uppdatering 42

Samlad [uppdatering 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar av Site Recovery agenter och providers (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)


### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Nya funktioner för haveri beredskap för virtuella Azure-datorer sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**UEFI** | Site Recovery stöder nu haveri beredskap för virtuella Azure-datorer med UEFI-baserad start arkitektur.
**Linux** | Site Recovery stöder nu virtuella Azure-datorer som kör Linux med Azure Disk Encryption (ADE).
**Generation 2** | Alla virtuella Azure-datorer i generation 2 stöds nu för haveri beredskap.
**Regioner** | Du kan nu aktivera haveri beredskap för virtuella Azure-datorer i Norge geo.

### <a name="vmware-to-azure-disaster-recovery"></a>Haveriberedskap för VMware till Azure

Nya funktioner för haveri beredskap i VMware till Azure sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**UEFI** | Site Recovery stöder nu haveri beredskap för virtuella VMware-datorer med UEFI-baserad start arkitektur.<br/><br/> Operativ system som stöds är Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Uppdatera för att underhålla stack Update/SHA-2

Observera följande för katastrof återställning av virtuella Azure-datorer till en sekundär region eller lokala virtuella VMware-datorer eller fysiska servrar till Azure:

- Från version 9.30.5407.1 av mobilitets tjänst tillägget (för virtuella Azure-datorer) och mobilitets tjänst agenten (för VMware/fysiska datorer) måste en del operativ system köra underhålls stack uppdateringen och SHA-2. Information visas i tabellen nedan.
- Installera uppdateringen och SHA-2 i enlighet med länkat KB. SHA-1 stöds inte från september 2019 och om SHA-2-kod signering inte är aktiverat installeras/uppgraderas inte som förväntat i agent tillägget.
- Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).

**Operativsystem** | **Azure VM** | **Virtuell VMware-dator/fysisk dator**
--- | --- | ---
**Windows 2008 R2 SP1** | [Uppdatering av underhålls stack](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Uppdatering av underhålls stack](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Uppdatering av underhålls stack](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Uppdatering av underhålls stack](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Uppdatering av underhålls stack](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Uppdatering av underhålls stack](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Uppdateringar (oktober 2019)

### <a name="update-rollup-41"></a>Samlad uppdatering 41

Samlad [uppdatering 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar av Site Recovery agenter och providers (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)



### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Nya funktioner för haveri beredskap för virtuella Azure-datorer sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Inställningar för redundanstest** | När du konfigurerar ett redundanstest kan du nu konfigurera inställningarna för den virtuella datorn för redundanstest och nätverket, inklusive IP-adress, NSG, intern belastnings utjämning och den offentliga IP-adressen för varje maskin-NIC. De här inställningarna är valfria och ändrar inte det aktuella beteendet. Om du inte konfigurerar de här inställningarna kan du välja ett Azure VNet vid tidpunkten för redundanstest. [Läs mer](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Återställnings planer** | Återställnings planer är nu begränsade till 100 virtuella datorer för att säkerställa tillförlitlighet för redundans.

### <a name="vmware-to-azure-disaster-recovery"></a>Haveriberedskap för VMware till Azure

Nya funktioner för haveri beredskap i VMware till Azure sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Återställnings planer** | Återställnings planer är nu begränsade till 100 virtuella datorer för att säkerställa tillförlitlighet för redundans.


## <a name="updates-september-2019"></a>Uppdateringar (september 2019)

### <a name="update-rollup-40"></a>Samlad uppdatering 40

Samlad [uppdatering 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar av Site Recovery agenter och providers (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)




### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Nya funktioner för haveri beredskap för virtuella Azure-datorer sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Rensa efter återställning efter fel** | Efter att ha växlat till den sekundära Azure, och sedan växlar tillbaka till den primära regionen, rensar Site Recovery automatiskt datorer i den sekundära regionen. Det finns inget behov av att manuellt ta bort virtuella datorer och nätverkskort.
**Redundanstest behåller IP-adressen** | Du kan nu behålla IP-adressen för den virtuella käll datorn under en haveri beredskap-granskning och välja en statisk IP-adress för ett redundanstest.

### <a name="vmwarephysical-server-disaster-recovery"></a>Katastrof återställning av VMware/fysisk server

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
Nya process Server varningar | Vi har lagt till nya aviseringar för process servern. [Läs mer](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Katastrof återställning för Hyper-V

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
Lagringskonto | Site Recovery stöder nu användning av lagrings konton med brand vägg aktive rad för haveri beredskap för Hyper-V till Azure.  Du kan välja brand Väggs-aktiverade lagrings konton som ett mål konto eller för cachelagring. Om du använder ett brand Väggs konto, se till att du aktiverar alternativet för att tillåta betrodda Microsoft-tjänster.<br/><br/> Detta stöds för virtuella Hyper-V-datorer med eller utan System Center VMM.


## <a name="updates-august-2019"></a>Uppdateringar (augusti 2019)

### <a name="update-rollup-39"></a>Samlad uppdatering 39

Samlad [uppdatering 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar av Site Recovery agenter och providers (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)


### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Nya funktioner för haveri beredskap för virtuella Azure-datorer sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Kryptering utan Azure AD** | Kryptering utan en Azure AD-app stöds nu för Azure VM-replikering till hanterade diskar som kör Windows.
**Nätverks resurser för redundans** | När du växlar över till en annan region kan du nu koppla nätverks resurs inställningar (NSG: er, belastnings utjämning, offentlig IP-adress) till en virtuell dator.

## <a name="updates-july-2019"></a>Uppdateringar (juli 2019)

### <a name="update-rollup-38"></a>Samlad uppdatering 38

Samlad [uppdatering 38](https://support.microsoft.com/help/4513507/) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar av Site Recovery agenter och providers (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)


### <a name="general"></a>Allmänt

Site Recovery stöder nu användning av generell användning v2-lagrings konton för cache Storage eller mål lagring. Endast v1 stöds tidigare.

### <a name="vmware-to-azure-disaster-recovery"></a>Haveriberedskap för VMware till Azure

Nu kan du replikera diskar upp till 8 TB vid replikering till en virtuell Azure-dator med Managed disks.


## <a name="updates-june-2019"></a>Uppdateringar (2019 juni)

### <a name="update-rollup-37"></a>Samlad uppdatering 37

Samlad [uppdatering 37](https://support.microsoft.com/help/4508614/) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | Uppdateringar av Site Recovery agenter och providers (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)


### <a name="vmwarephysical-server-disaster-recovery"></a>Katastrof återställning av VMware/fysisk server

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**GPT-partitioner** | Från Samlad uppdatering 37 och senare (mobilitets tjänstens version 9.25.5241.1) stöds upp till fem GPT-partitioner i UEFI. Före den här uppdateringen stöds fyra.



## <a name="updates-may-2019"></a>Uppdateringar (maj 2019)

### <a name="update-rollup-36"></a>Samlad uppdatering 36

Samlad [uppdatering 36](https://support.microsoft.com/help/4503156) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)

### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Replikera tillagda diskar** | Aktivera replikering för data diskar som har lagts till i en virtuell Azure-dator som redan har Aktiver ATS för haveri beredskap. [Läs mer](azure-to-azure-enable-replication-added-disk.md).
**Automatiska uppdateringar** | När du konfigurerar Automatiska uppdateringar för mobilitets tjänst tillägget som körs på virtuella Azure-datorer som är aktiverade för haveri beredskap, kan du nu välja ett befintligt Automation-konto som ska användas, i stället för att använda standard kontot som skapats av Site Recovery. [Läs mer](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Katastrof återställning av VMware/fysisk server

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Process Server övervakning** | För haveri beredskap för lokala virtuella VMware-datorer och fysiska servrar, övervaka och Felsök problem med processervern med förbättrade Server hälso rapporter och aviseringar. [Läs mer](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Uppdateringar (mars 2019)

### <a name="update-rollup-35"></a>Samlad uppdatering 35

Samlad [uppdatering 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen)
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen)

### <a name="vmwarephysical-server-disaster-recovery"></a>Katastrof återställning av VMware/fysisk server

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Hanterade diskar** | Replikering av lokala virtuella VMware-datorer och fysiska servrar är nu direkt till hanterade diskar i Azure. Lokala data skickas till ett cache Storage-konto i Azure och återställnings punkter skapas i Managed disks på mål platsen. Detta garanterar att du inte behöver hantera flera mål lagrings konton.
**Konfigurationsserver** | Site Recovery stöder nu konfigurations servrar med flera nätverkskort. Lägg till ytterligare nätverkskort i den virtuella Configuration Server-datorn innan du registrerar konfigurations servern i valvet. Om du lägger till efteråt måste du registrera servern i valvet igen.


## <a name="updates-february-2019"></a>Uppdateringar (februari 2019)

### <a name="update-rollup-34"></a>Samlad uppdatering 34

Samlad [uppdatering 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).


### <a name="update-rollup-33"></a>Samlad uppdatering 33

Samlad [uppdatering 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).


### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Nätverksmappning** | För haveri beredskap för virtuella Azure-datorer kan du nu använda alla tillgängliga mål nätverk när du aktiverar replikering.
**Standard SSD** | Nu kan du konfigurera haveri beredskap för virtuella Azure-datorer med hjälp av [standard SSD diskar](../virtual-machines/disks-types.md#standard-ssd).
**Lagringsutrymmen direkt** | Du kan konfigurera haveri beredskap för appar som körs på Azure VM-appar med hjälp av [Lagringsdirigering](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) för hög tillgänglighet.  Användning av Lagringsdirigering (S2D) tillsammans med Site Recovery ger omfattande skydd av virtuella Azure-arbetsbelastningar. S2D låter dig vara värd för ett gäst kluster i Azure. Detta är särskilt användbart när en virtuell dator är värd för ett kritiskt program, till exempel SAP ASCS-lager, SQL Server eller skalbar fil server.


### <a name="vmwarephysical-server-disaster-recovery"></a>Katastrof återställning av VMware/fysisk server

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-BRTFS fil system** | Site Recovery stöder nu replikering av virtuella VMware-datorer med fil systemet BRTFS. Replikering stöds inte om:<br/><br/>-BTRFS fil systemets under volym ändras när replikeringen har Aktiver ATS.<br/><br/>-Fil systemet sprids över flera diskar.<br/><br/>-Fil systemet BTRFS har stöd för RAID.
**Windows Server 2019** | Stöd har lagts till för datorer som kör Windows Server 2019.


## <a name="updates-january-2019"></a>Uppdateringar (januari 2019)


### <a name="accelerated-networking-azure-vms"></a>Accelererat nätverk (virtuella Azure-datorer)

Accelererat nätverk möjliggör SR-IOV (Single root I/O Virtualization) till en virtuell dator, vilket förbättrar nätverkets prestanda. När du aktiverar replikering för en virtuell Azure-dator identifierar Site Recovery om accelererat nätverk är aktiverat. Om så är fallet, efter redundansväxlingen Site Recovery konfigureras automatiskt accelererat nätverk på den virtuella mål repliken Azure VM, för både [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) och [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

[Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Samlad uppdatering 32

Samlad [uppdatering 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).

### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** | Stöd har lagts till för RedHat Workstation 6/7 och nya kernel-versioner för Ubuntu, Debian och SUSE.
**Lagringsutrymmen direkt** | Site Recovery stöder virtuella Azure-datorer med Lagringsdirigering (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Haveri beredskap för virtuella VMware-datorer/fysiska servrar

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** | Stöd har lagts till för redhat Enterprise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6,10 och Oracle Linux 7,6 och nya kernel-versioner för Ubuntu, Debian och SUSE.


### <a name="update-rollup-31"></a>Samlad uppdatering 31

Samlad [uppdatering 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) tillhandahåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).

### <a name="vmware-vmsphysical-servers-replication"></a>Replikering av virtuella VMware-datorer/fysiska servrar

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** |  Support lades till för Oracle Linux 6,8, Oracle Linux 6,9 och Oracle Linux 7,0 med Red Hat-kompatibel kernel och för UEK (unbrytaable Enterprise kernel) version 5.
**LVM** | Stöd har lagts till för LVM-och LVM2-volymer.<br/><br/> /Boot-katalogen på en diskpartition och på LVM-volymer stöds nu.
**Kataloger** | Stöd har lagts till för dessa kataloger som kon figurer ATS som separata partitioner eller fil system som inte är på samma system disk:<br/><br/> /(root),/boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | Stöd har lagts till för dynamiska diskar.
**Redundans** | Förbättrad växlings tid för virtuella VMware-datorer där storvsc och vsbus inte är start driv rutiner.
**UEFI-stöd** | Virtuella Azure-datorer stöder inte start typen UEFI. Nu kan du migrera lokala fysiska servrar med UEFI till Azure med Site Recovery. Site Recovery migrerar servern genom att konvertera start typen till BIOS innan migreringen. Site Recovery tidigare stöd för den här konverteringen endast för virtuella datorer. Support är tillgängligt för fysiska servrar som kör Windows Server 2012 eller senare.

### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** | Support lades till för Oracle Linux 6,8, Oracle Linux 6,9 och Oracle Linux 7,0 med Red Hat-kompatibel kernel och för UEK (unbrytaable Enterprise kernel) version 5.
**Linux-BRTFS fil system** | Stöds för virtuella Azure-datorer.
**Virtuella Azure-datorer i tillgänglighets zoner** | Du kan aktivera replikering till en annan region för virtuella Azure-datorer som distribueras i tillgänglighets zoner. Nu kan du aktivera replikering för en virtuell Azure-dator och ange målet för redundans till en enskild VM-instans, en virtuell dator i en tillgänglighets uppsättning eller en virtuell dator i en tillgänglighets zon. Inställningen påverkar inte replikeringen. [Läs](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) meddelandet.
**Brand Väggs aktive rad lagring (portal/PowerShell)** | Stöd har lagts till för [brand Väggs-aktiverade lagrings konton](../storage/common/storage-network-security.md).<br/><br/> Du kan replikera virtuella Azure-datorer med ohanterade diskar på brand Väggs aktiverade lagrings konton till en annan Azure-region för haveri beredskap.<br/><br/> Du kan använda brand Väggs-aktiverade lagrings konton som mål lagrings konton för ohanterade diskar.<br/><br/> Stöds i portalen och med PowerShell.

## <a name="updates-december-2018"></a>Uppdateringar (december 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatiska uppdateringar för mobilitets tjänsten (virtuella Azure-datorer)

Site Recovery lagt till ett alternativ för automatiska uppdateringar av mobilitets tjänst tillägget. Mobilitets tjänst tillägget installeras på varje virtuell Azure-dator som replikeras av Site Recovery. När du aktiverar replikering väljer du om du vill tillåta Site Recovery hantera uppdateringar av tillägget.

Uppdateringar kräver ingen omstart av datorn och påverkar inte replikeringen. [Läs mer](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Pris kalkylator för haveri beredskap för Azure VM

Haveri beredskap för virtuella Azure-datorer medför kostnader för VM-licensiering, samt nätverks-och lagrings kostnader. Azure tillhandahåller en [pris kalkylator](https://aka.ms/a2a-cost-estimator) som hjälper dig att ta reda på de här kostnaderna. Site Recovery är nu ett [exempel på en pris beräkning](https://aka.ms/a2a-cost-estimator) som visar en exempel distribution som är baserad på en app med tre nivåer med sex virtuella datorer med 12 standard HDD diskar och 6 Premium SSD diskar.

- Exemplet förutsätter en data ändring på 10 GB per dag för standard och 20 GB för Premium.
- För din specifika distribution kan du ändra variablerna för att beräkna kostnaderna.
- Du kan ange antalet virtuella datorer, antalet och typen av hanterade diskar, samt förväntad total data ändrings frekvens för de virtuella datorerna.
- Dessutom kan du använda en komprimerings faktor för att beräkna bandbredds kostnader.

[Läs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) meddelandet.


## <a name="updates-october-2018"></a>Uppdateringar (oktober 2018)

### <a name="update-rollup-30"></a>Samlad uppdatering 30

Samlad [uppdatering 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) tillhandahåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).

### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM
Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Stöd för regioner** | Site Recovery stöd har lagts till för Australien, centrala 1 och Australien, centrala 2.
**Stöd för disk kryptering** | Stöd har lagts till för haveri beredskap för virtuella Azure-datorer som har krypterats med Azure Disk Encryption (ADE) med Azure AD-appen. [Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Disk undantag** | Oinitierade diskar undantas nu automatiskt vid replikering av virtuella Azure-datorer.
**Brand Väggs-aktiverad lagring (PowerShell)** | Stöd har lagts till för [brand Väggs-aktiverade lagrings konton](../storage/common/storage-network-security.md).<br/><br/> Du kan replikera virtuella Azure-datorer med ohanterade diskar på brand Väggs aktiverade lagrings konton till en annan Azure-region för haveri beredskap.<br/><br/> Du kan använda brand Väggs-aktiverade lagrings konton som mål lagrings konton för ohanterade diskar.<br/><br/> Stöds endast med PowerShell.


### <a name="update-rollup-29"></a>Samlad uppdatering 29

Samlad [uppdatering 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).


## <a name="updates-august-2018"></a>Uppdateringar (augusti 2018)

### <a name="update-rollup-28"></a>Samlad uppdatering 28

Samlad [uppdatering 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).

### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM
Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** | Stöd för RedHat Enterprise Linux 6,10; har lagts till CentOS 6,10.<br/><br/>
**Cloud Support** | Katastrof återställning för virtuella Azure-datorer i Tyskland-molnet.
**Katastrof återställning mellan prenumerationer** | Stöd för replikering av virtuella Azure-datorer i en region till en annan region i en annan prenumeration, inom samma Azure Active Directory klient. [Läs mer](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Haveri beredskap för virtuella VMware-datorer/fysiska servrar
Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** | Stöd har lagts till för RedHat Enterprise Linux 6,10, CentOS 6,10.<br/><br/> Linux-baserade virtuella datorer som använder partitionstypen GUID Partition Table (GPT) i bakåtkompatibelt BIOS-kompatibelt läge stöds nu. Mer information hittar du i [vanliga frågor och svar om Azure VM](../virtual-machines/faq-for-disks.md) .
**Haveri beredskap för virtuella datorer efter migrering** | Stöd för att aktivera haveri beredskap till en sekundär region för en lokal virtuell VMware-dator som migrerats till Azure utan att behöva avinstallera mobilitets tjänsten på den virtuella datorn innan du aktiverar replikeringen.
**Windows Server 2008** | Stöd för migrering av datorer som kör Windows Server 2008 R2/2008 64-bitars och 32-bitars.<br/><br/> Endast migrering (replikering och redundans). Failback stöds inte.

## <a name="updates-july-2018"></a>Uppdateringar (juli 2018)

### <a name="update-rollup-27-july-2018"></a>Samlad uppdatering 27 (juli 2018)

Samlad [uppdatering 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) tillhandahåller följande uppdateringar.

**Uppdatera** | **Information**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery agenter och providrar (enligt beskrivningen i den samlade uppdateringen).
**Problem korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (enligt beskrivningen i den samlade uppdateringen).

### <a name="azure-vm-disaster-recovery"></a>Haveri beredskap för Azure VM

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** | Stöd har lagts till för Red Hat Enterprise Linux 7,5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Haveri beredskap för virtuella VMware-datorer/fysiska servrar

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Information**
--- | ---
**Linux-support** | Stöd har lagts till för Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Nästa steg

Håll dig uppdaterad med våra uppdateringar på sidan [Azure-uppdateringar](https://azure.microsoft.com/updates/?product=site-recovery) .