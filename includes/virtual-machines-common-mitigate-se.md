---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: a9146099951aba223a7b201c1613e1ec0ba617d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96509185"
---
**Senaste dokument uppdatering**: 12 November 2019 10:00 PST.

Utlämnande av en [ny klass av processor sårbarheter](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) som kallas spekulativ körning på sidan kanal angrepp har gjort frågor från kunder som vill ha mer klarhet.  

Microsoft har distribuerat åtgärder i alla våra moln tjänster. Den infrastruktur som kör Azure och isolerar kund arbets belastningar från varandra är skyddade. Det innebär att en potentiell angripare som använder samma infrastruktur inte kan attackera ditt program med hjälp av dessa sårbarheter.

Azure använder [underhåll av minnes](../articles/virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#maintenance-that-doesnt-require-a-reboot) bevaran när det är möjligt, för att minimera kund påverkan och eliminera behovet av omstarter. Azure fortsätter att använda dessa metoder när du gör systemomfattande uppdateringar av värden och skyddar våra kunder.

Mer information om hur säkerheten integreras i varje aspekt av Azure finns på webbplatsen för [Azure Security Documentation](../articles/security/index.yml) . 

> [!NOTE] 
> Eftersom det här dokumentet först publicerades har flera varianter av denna sårbarhets klass lämnats. Microsoft fortsätter att vara mycket investering i att skydda våra kunder och tillhandahålla vägledning. Den här sidan kommer att uppdateras när vi fortsätter att publicera ytterligare korrigeringar. 
> 
> Den 12 november 2019 [publicerade Intel](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) en teknisk rekommendation runt Intel® transaktionell TSX (Intel®) transaktion asynkront avbrott (TAA) som tilldelas [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Detta säkerhets problem påverkar Intel® Core®-processorer och Intel® Xeon®-processorer.  Microsoft Azure har släppt uppdateringar av operativ systemet och distribuerar ny mikrokod, eftersom det görs tillgängligt av Intel, genom hela vår flotta för att skydda våra kunder mot dessa nya sårbarheter.   Azure är nära att arbeta med Intel för att testa och validera den nya mikrokoden före den officiella versionen på plattformen. 
>
> **Kunder som kör icke-betrodd kod inom den virtuella datorn** måste vidta åtgärder för att skydda mot dessa säkerhets risker genom att läsa nedan för ytterligare vägledning om alla säkerhets risker för spekulativ körning på sidan (Microsoft Advisors ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)och [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Andra kunder bör utvärdera dessa sårbarheter från ett djupgående perspektiv och fundera över säkerheten och prestanda konsekvenserna av den valda konfigurationen.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Hålla operativ systemen aktuella

Även om en OS-uppdatering inte krävs för att isolera dina program som körs på Azure från andra Azure-kunder, är det alltid en bra idé att hålla din program vara uppdaterad. De senaste säkerhets sammanslagningarna för Windows innehåller lösningar på flera säkerhets risker för spekulativ körning på sidan. På samma sätt har Linux-distributioner släppt flera uppdateringar för att åtgärda dessa sårbarheter. Här är våra rekommenderade åtgärder för att uppdatera operativ systemet:

| Erbjudande | Rekommenderad åtgärd  |
|----------|---------------------|
| Azure Cloud Services  | Aktivera [Automatisk uppdatering](../articles/cloud-services/cloud-services-how-to-configure-portal.md) eller se till att du kör det senaste gäst operativ systemet. |
| Azure-Virtuella Linux-datorer | Installera uppdateringar från operativ system leverantören. Mer information finns i [Linux](#linux) senare i det här dokumentet. |
| Azure-Virtuella Windows-datorer  | Installera den senaste säkerhets uppdateringen.
| Andra Azure PaaS-tjänster | Det krävs ingen åtgärd för kunder som använder dessa tjänster. Azure håller automatiskt dina OS-versioner uppdaterade. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Ytterligare vägledning om du kör obetrodd kod 

Kunder som tillåter ej betrodda användare att köra godtycklig kod kanske vill implementera vissa ytterligare säkerhetsfunktioner i Azure Virtual Machines eller Cloud Services. Dessa funktioner skyddar mot insamlings vektorer för inlämning som har flera sårbarheter för spekulativ körning.

Exempel scenarier där ytterligare säkerhetsfunktioner rekommenderas:

- Du tillåter kod som du inte litar på att köras i den virtuella datorn.  
    - Till *exempel kan en av dina kunder Ladda upp en binärfil eller ett skript som du sedan kör i ditt program*. 
- Du låter användare som du inte litar på Logga in på den virtuella datorn med konton med låg behörighet.   
    - *Du tillåter till exempel att en användare med låg privilegier loggar in på en av dina virtuella datorer med hjälp av fjärr skrivbord eller SSH*.  
- Du tillåter ej betrodda användare åtkomst till virtuella datorer som implementerats via kapslad virtualisering.  
    - *Du kan till exempel kontrol lera Hyper-V-värden, men allokera de virtuella datorerna till obetrodda användare*. 

Kunder som inte implementerar ett scenario som involverar icke-betrodd kod behöver inte aktivera dessa ytterligare säkerhetsfunktioner. 

## <a name="enabling-additional-security"></a>Aktivera ytterligare säkerhet 

Du kan aktivera ytterligare säkerhetsfunktioner i den virtuella datorn eller moln tjänsten om du kör obetrodd kod. Kontrol lera parallellt att operativ systemet är uppdaterat för att aktivera säkerhetsfunktionerna i den virtuella datorn eller moln tjänsten

### <a name="windows"></a>Windows 

Ditt mål operativ system måste vara uppdaterat för att aktivera dessa ytterligare säkerhetsfunktioner. Även om många åtgärder för spekulativ körnings sidans kanal är aktiverade som standard, måste de ytterligare funktioner som beskrivs här vara aktiverade manuellt och kan orsaka en prestanda påverkan. 


**Steg 1: Inaktivera Hyper-Threading på den virtuella datorn** – kunder som kör ej betrodd kod på en virtuell dator med Hyper-Threading måste inaktivera Hyper-Threading eller flytta till en virtuell dator som inte är en Hyper-Thread. Referera till [det här dokumentet](../articles/virtual-machines/acu.md) om du vill ha en lista över virtuella datorer i Hyper-Threading (där förhållandet mellan VCPU och Core är 2:1). Om du vill kontrol lera om den virtuella datorn har aktiverat Hyper-Threading kan du läsa skriptet nedan med hjälp av kommando raden i Windows från den virtuella datorn.

Skriv `wmic` för att ange det interaktiva gränssnittet. Skriv sedan nedan för att visa mängden fysiska och logiska processorer på den virtuella datorn.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Om antalet logiska processorer är större än fysiska processorer (kärnor) är Hyper-Threading aktiverat.  Om du kör en virtuell dator med Hyper-threader kan du [kontakta Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) för att få Hyper-Threading inaktiverat.  När Hyper-Threading har inaktiverats **kräver support en fullständig omstart av datorn**. Se [antalet kärnor](#core-count) för att ta reda på varför ditt virtuella dator kärnor minskar.


**Steg 2**: parallellt med steg 1, följ instruktionerna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) för att kontrol lera att skydd Aktiver ATS med [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell-modulen.

> [!NOTE]
> Om du tidigare har hämtat den här modulen måste du installera den senaste versionen.
>


Utdata från PowerShell-skriptet ska ha nedanstående värden för att validera aktiverade skydd mot följande sårbarheter:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Om utdata visas `MDS mitigation is enabled: False` [kontaktar du Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) för tillgängliga alternativ för att minska.



**Steg 3**: Aktivera stöd för virtuella KVAS-och bti-operativsystem genom att följa anvisningarna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) för att aktivera skydd med hjälp av `Session Manager` register nycklarna. En omstart krävs.


**Steg 4**: för distributioner som använder [kapslad virtualisering](../articles/virtual-machines/windows/nested-virtualization.md) (D3 och E3): de här instruktionerna gäller i den virtuella datorn som du använder som Hyper-V-värd.

1.  Följ anvisningarna i [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) för att aktivera skydd med hjälp av `MinVmVersionForCpuBasedMitigations` register nycklarna.
2.  Ange hypervisor-typen hypervisor-typ `Core` genom att följa anvisningarna [här](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Att aktivera en uppsättning ytterligare säkerhetsfunktioner i kräver att mål operativ systemet är helt uppdaterat. Vissa åtgärder kommer att aktive ras som standard. I följande avsnitt beskrivs de funktioner som är av som standard och/eller som kan vara beroende av maskin varu support (mikrokod). Att aktivera dessa funktioner kan orsaka en prestanda påverkan. Mer information finns i dokumentationen för operativ system leverantören


**Steg 1: Inaktivera Hyper-Threading på den virtuella datorn** – kunder som kör obetrodd kod på en virtuell dator med Hyper-Threading måste inaktivera Hyper-Threading eller flytta till en icke-Hyper-THREADAD virtuell dator.  Referera till [det här dokumentet](../articles/virtual-machines/acu.md) om du vill ha en lista över virtuella datorer i Hyper-Threading (där förhållandet mellan VCPU och Core är 2:1). Om du vill kontrol lera om du kör en virtuell dator med Hyper-Threading kör du `lscpu` kommandot i den virtuella Linux-datorn. 

Om `Thread(s) per core = 2` har Hyper-Threading Aktiver ATS. 

Om `Thread(s) per core = 1` har Hyper-Threading inaktiverats. 

 
Exempel på utdata för en virtuell dator med Hyper-Threading aktiverat: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Om du kör en virtuell dator med Hyper-threader kan du [kontakta Azure-supporten](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) för att få Hyper-Threading inaktiverat.  När Hyper-Threading har inaktiverats **kräver support en fullständig omstart av datorn**. Se [antalet kärnor](#core-count) för att ta reda på varför ditt virtuella dator kärnor minskar.



**Steg 2**: om du vill åtgärda något av de lägre säkerhets riskerna för spekulativ körning på sidan kan du läsa dokumentationen för operativ system leverantören:   
 
- [RedHat och CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Antal kärnor

När en virtuell dator med Hyper-Threading skapas, allokerar Azure 2 trådar per kärna – dessa kallas virtuella processorer. När Hyper-Threading är inaktiverat tar Azure bort en tråd och hämtar enkla trådade kärnor (fysiska kärnor). Förhållandet mellan vCPU och CPU är 2:1, så när Hyper-Threading är inaktiverat verkar antalet processorer i den virtuella datorn ha minskat med hälften. En D8_v3 VM är till exempel en virtuell Hyper-Thread som körs på 8 virtuella processorer (2 trådar per Core x 4 kärnor).  När Hyper-Threading är inaktiverat släpper CPU: er till 4 fysiska kärnor med 1 tråd per kärna. 

## <a name="next-steps"></a>Nästa steg

Den här artikeln ger vägledning för följande spekulativa körnings kanals attacker som påverkar många moderna processorer:

[SPECTRE Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 – BTI (Branch Target insprutning)  
- CVE-2017-5754 – kernel Page Table-isolering (KPTI)
- CVE-2018-3639 – spekulativt lagrings Bypass (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows kernel-information – variant av SPECTRE-variant 1
 
[L1-Terminal-fel (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 – Intel Software Guard-tillägg (Intel SGX)
- CVE-2018-3620 – operativ system (OS) och system hanterings läge (SMM)
- CVE-2018-3646 – påverkan Virtual Machine Manager (VMM)

[Sampling av mikroarkitektur data](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 – mikroarkitektur data sampling, MDSUM (Uncached Memory)
- CVE-2018-12126 – mikroarkitektur för data insamling av mikroarkitektur (MSBDS)
- CVE-2018-12127-mikroarkitektur data sampling (MLPDS)
- CVE-2018-12130-mikroarkitektur data sampling (MFBDS)

Överföring av transaktions tillägg (Intel® TSX) asynkront avbrott:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX transaktion asynkront avbrott (TAA)