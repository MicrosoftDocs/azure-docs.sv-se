---
title: Support mat ris för haveri beredskap i Azure VM med Azure Site Recovery
description: Sammanfattar stöd för haveri beredskap för virtuella Azure-datorer till en sekundär region med Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.openlocfilehash: 8e0d47395e4658d531ac6e9d31117edc062351c0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312305"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Supportmatris för haveriberedskap för virtuella Azure-datorer mellan Azure-regioner

Den här artikeln sammanfattar support och krav för haveri beredskap för virtuella Azure-datorer från en Azure-region till en annan med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .


## <a name="deployment-method-support"></a>Stöd för distributions metoder

**Distribution** |  **Support**
--- | ---
**Azure-portalen** | Stöds.
**PowerShell** | Stöds. [Läs mer](azure-to-azure-powershell.md)
**REST-API** | Stöds.
**CLI** | Stöds för närvarande inte


## <a name="resource-support"></a>Resursstöd

**Resurs åtgärd** | **Information**
--- | ---
**Flytta valv över resurs grupper** | Stöds inte
**Flytta beräknings-/lagrings-/nätverks resurser över resurs grupper** | Stöds inte.<br/><br/> Om du flyttar en virtuell dator eller tillhör ande komponenter, till exempel lagring/nätverk när den virtuella datorn har repliker ATS, måste du inaktivera och sedan återaktivera replikering för den virtuella datorn.
**Replikera virtuella Azure-datorer från en prenumeration till en annan för haveri beredskap** | Stöds inom samma Azure Active Directory-klient.
**Migrera virtuella datorer mellan regioner inom de geografiska kluster som stöds (inom och över prenumerationer)** | Stöds inom samma Azure Active Directory-klient.
**Migrera virtuella datorer inom samma region** | Stöds inte.
**Dedikerade Azure-värdar** | Stöds inte.

## <a name="region-support"></a>Stöd för regioner

Du kan replikera och återställa virtuella datorer mellan två regioner i samma geografiska kluster. Geografiska kluster definieras för att hålla data latens och suveränitet i åtanke.


**Geografiskt kluster** | **Azure-regioner**
-- | --
Vägnar | Östra Kanada, centrala Kanada, södra centrala USA, västra centrala USA, östra USA, östra USA 2, västra USA, västra USA 2, centrala USA, norra centrala USA
Europa | Storbritannien, västra, Storbritannien, södra, Nord Europa, Västeuropa, västra Europa, västra Sydafrika, norra Afrika, östra USA, centrala Frankrike, Schweiz, norra, Tyskland, västra centrala
Asien | Södra Indien, centrala Indien, västra Indien, Sydostasien, Asien, östra, Östra Japan, västra Japan, centrala Korea, centrala Korea, södra Korea
Australien    | Australien, östra, Australien, sydöstra, Australien, centrala, Australien, centrala 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, USA DOD öst, USA DOD Central
Tyskland    | Tyskland, centrala, Tyskland nordöstra
Kina | Kina, östra, Kina, norra, Kina North2, Kina östra
Begränsade regioner som är reserverade för haveri beredskap i landet |Schweiz, västra som är reserverad för Schweiz, norra, Frankrike, som är avsedd för centrala Frankrike, Förenade Arabemiraten Central restricted för Förenade Arabemiraten norra kunder, Norge väst för östra Norge-kunder

>[!NOTE]
>
> - För **södra Brasilien** kan du replikera och redundansväxla till dessa regioner: södra centrala USA, västra centrala USA, östra USA, östra USA 2, västra USA, västra USA 2 och norra centrala USA.
> - Södra Brasilien kan endast användas som käll region från vilka virtuella datorer kan replikeras med hjälp av Site Recovery. Den kan inte fungera som mål region. Detta beror på fördröjnings problem på grund av geografiska avstånd. Observera att om du växlar över från Brasilien, södra som käll region till ett mål, stöds failback till södra Brasilien från mål regionen.
> - Du kan arbeta i regioner som du har lämplig åtkomst till.
> - Om den region där du vill skapa ett valv inte visas kontrollerar du att prenumerationen har åtkomst till att skapa resurser i den regionen.
> - Om du inte kan se en region i ett geografiskt kluster när du aktiverar replikering, se till att din prenumeration har behörighet att skapa virtuella datorer i den regionen.



## <a name="cache-storage"></a>Cachelagring

I den här tabellen sammanfattas stödet för cache Storage-kontot som används av Site Recovery under replikeringen.

**Inställning** | **Support** | **Information**
--- | --- | ---
Generell användning v2-lagrings konton (frekvent och låg frekvent nivå) | Stöds | Användning av GPv2 rekommenderas inte eftersom transaktionskostnader för v2 är betydligt högre än v1-lagrings konton.
Premium Storage | Stöds inte | Standard lagrings konton används för cachelagring för att hjälpa till att optimera kostnaderna.
Azure Storage brand väggar för virtuella nätverk  | Stöds | Om du använder en brand vägg som är aktive rad för cache-lagring eller mål lagrings konto, se till att du [tillåter betrodda Microsoft-tjänster](../storage/common/storage-network-security.md#exceptions).<br></br>Se också till att du tillåter åtkomst till minst ett undernät för det virtuella käll nätverket.

I tabellen nedan visas gränserna för antalet diskar som kan replikeras till ett enda lagrings konto.

**Typ av lagrings konto**    |    **Omsättning = 4 Mbit/s per disk**    |    **Omsättning = 8 Mbit/s per disk**
---    |    ---    |    ---
V1 lagrings konto    |    300 diskar    |    150 diskar
V2-lagrings konto    |    750 diskar    |    375 diskar

Vartefter den genomsnittliga omsättningen på diskarna ökar är antalet diskar som ett lagrings konto har stöd för. Tabellen ovan kan användas som en vägledning för att fatta beslut om antalet lagrings konton som måste tillhandahållas.

Observera att de begränsningar som anges ovan är begränsade till Azure till Azure och zon för DR-scenarier. 

## <a name="replicated-machine-operating-systems"></a>Replikerade dator operativ system

Site Recovery stöder replikering av virtuella Azure-datorer som kör operativ systemen som anges i det här avsnittet. Observera att om en redan replikerande dator sedan uppgraderas (eller nedgraderas) till en annan större kernel måste du inaktivera replikering och återaktivera replikering efter uppgraderingen.

### <a name="windows"></a>Windows


**Operativsystem** | **Information**
--- | ---
Windows Server 2019 | Stöds för Server Core, server med Skriv bords miljö.
Windows Server 2016  | Server Core som stöds, server med Skriv bords miljö.
Windows Server 2012 R2 | Stöds.
Windows Server 2012 | Stöds.
Windows Server 2008 R2 med SP1/SP2 | Stöds.<br/><br/> Från version [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av mobilitets tjänst tillägget för virtuella Azure-datorer måste du installera en Windows [servicing stack Update-uppdatering (självbetjänings)](https://support.microsoft.com/help/4490628) och [SHA-2](https://support.microsoft.com/help/4474419) på datorer som kör Windows Server 2008 R2 SP1/SP2.  SHA-1 stöds inte från september 2019 och om SHA-2-kod signering inte är aktiverat installeras/uppgraderas inte som förväntat i agent tillägget. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Stöds.
Windows 8,1 (x64) | Stöds.
Windows 8 (x64) | Stöds.
Windows 7 (x64) med SP1 och senare | Från version [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av mobilitets tjänst tillägget för virtuella Azure-datorer måste du installera en Windows [servicing stack Update-uppdatering (självbetjänings)](https://support.microsoft.com/help/4490628) och [SHA-2](https://support.microsoft.com/help/4474419) på datorer som kör Windows 7 med SP1.  SHA-1 stöds inte från september 2019 och om SHA-2-kod signering inte är aktiverat installeras/uppgraderas inte som förväntat i agent tillägget. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Operativsystem** | **Information**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609/), 8,3,,, [,](https://support.microsoft.com/help/4597409/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10 </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), [7,9 före-ga-versionen](https://support.microsoft.com/help/4578241/), 7,9 ga-version stöds från korrigerings filen för snabb korrigering * * i 9,37. * * </br> 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609), [8,3](https://support.microsoft.com/help/4597409/)
Ubuntu 14,04 LTS-Server | Innehåller stöd för alla 14,04. *x* -versioner; [Kernel-versioner som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Ubuntu 16,04 LTS-Server | Innehåller stöd för alla 16,04. *x* -versioner; [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servrar som använder lösenordsbaserad autentisering och loggar in och Cloud-Init-paketet för att konfigurera virtuella datorer i molnet kan ha lösenordsbaserad inloggning inaktive rad vid redundansväxling (beroende på cloudinit-konfigurationen). Lösenordsbaserade inloggningar kan återaktiveras på den virtuella datorn genom att återställa lösen ordet från support > fel sökning > Inställningar-menyn (av den misslyckade virtuella datorn i Azure Portal.
Ubuntu 18,04 LTS-Server | Innehåller stöd för alla 18,04. *x* -versioner; [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servrar som använder lösenordsbaserad autentisering och loggar in och Cloud-Init-paketet för att konfigurera virtuella datorer i molnet kan ha lösenordsbaserad inloggning inaktive rad vid redundansväxling (beroende på cloudinit-konfigurationen). Lösenordsbaserade inloggningar kan återaktiveras på den virtuella datorn genom att återställa lösen ordet från support > fel sökning > Inställningar-menyn (av den misslyckade virtuella datorn i Azure Portal.
Ubuntu 20,04 LTS-Server | Innehåller stöd för alla 20,04. *x* -versioner; [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Innehåller stöd för alla 7. *x* versioner som [stöds kernel-versioner](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Innehåller stöd för alla 8. *x* versioner som [stöds kernel-versioner](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Innehåller stöd för 9,1 till 9,13. Debian 9,0 stöds inte. [Kernel-versioner som stöds](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Kernel-versioner som stöds](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(kernel-versioner som stöds)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(kernel-versioner som stöds)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | INSTALLERAS<br/><br/> Uppgradering av replikering av datorer från SP3 till SP4 stöds inte. Om en replikerad dator har uppgraderats måste du inaktivera replikering och återaktivera replikering efter uppgraderingen.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, [7,5, 7,6](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,7](https://support.microsoft.com/help/4597409), [](https://support.microsoft.com/help/4573888/) [7,8](https://support.microsoft.com/help/4573888/), 7,9, 8,0, [8,1 (med](https://support.microsoft.com/help/4573888/) Red Hat-kompatibel kernel eller Enterprise kernel release 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8,1 (som körs på alla UEK-kerneler och RedHat kernel <= 3.10.0-1062. * stöds i [9,35](https://support.microsoft.com/help/4573888/). Stöd för rest av RedHat-kärnan är tillgängligt i [9,36](https://support.microsoft.com/help/4578241/))

> [!NOTE]
> För Linux-versioner stöder Azure Site Recovery inte anpassade OS-avbildningar. Endast de lager kerneler som ingår i distributionens lägre versions version/uppdatering stöds.

* * Obs! Om du vill ha stöd för de senaste Linux-kernelerna inom 15 dagars lansering, Azure Site Recovery samla in snabb korrigerings korrigering ovanpå den senaste mobilitets agent versionen. Den här korrigeringen är insamlad i mellan två större versioner. Följ anvisningarna i [den här artikeln](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)om du vill uppdatera till den senaste versionen av mobilitets agenten (inklusive snabb korrigerings korrigering). Den här korrigeringen är för närvarande distribuerad för mobilitets agenter som används i Azure till Azure DR-scenario.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ubuntu kernel-versioner som stöds för Azure Virtual Machines

**Frisläpp** | **Mobilitets tjänst version** | **Kernelversion** |
--- | --- | --- |
14,04 LTS | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24-genered to 3.13.0-170-Generic,<br/>3.16.0-25-genered to 3.16.0-77-Generic,<br/>3.19.0-18-genered till 3.19.0-80-genered,<br/>4.2.0-18-genered to 4.2.0-42-Generic,<br/>4.4.0-21-genered to 4.4.0-148-genered,<br/>4.15.0 – 1023 – Azure till 4.15.0 – 1045 – Azure |
|||
16,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-genered to 4.4.0-201-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-generisk till 4.15.0-133-generisk<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1106 – Azure <br/> 4.4.0-203-Generic, 4.4.0-204-Generic, 4.4.0-206-Generic, 4.15.0-136-Generic, 4.15.0-137-Generic, 4.15.0-139-Generic, 4.15.0-140-Generic, 4.15.0-1108-Azure, 4.15.0-1109-Azure, 4.15.0-1110-Azure, 4.15.0-1111-Azure till 9,41 snabb korrigering * *|
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0 – 21-genered to 4.4.0-197-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-Generic to 4.15.0-128-genered<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1102 – Azure </br> 4.15.0-132-genered, 4.4.0-200-genered, 4.15.0-1106-Azure, 4.15.0-133-Generic, 4.4.0-201-Generic till 9,40 snabb korrigering * *|
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-genered to 4.4.0-194-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-123-generic<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1098 – Azure </br> 4.4.0-197-Generic, 4.15.0-126-Generic, 4.15.0-128-genered, 4.15.0-1100-Azure, 4.15.0-1102-Azure till 9,39 snabb korrigerings korrigering * *|
16,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-genered to 4.4.0-190-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-118-generic<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1096 – Azure </br> 4.4.0-193-genered, 4.15.0-120-Generic, 4.15.0-122-Generic, 4.15.0-1098-Azure till 9,38 snabb korrigerings korrigering * *|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-genered to 4.4.0-189-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-115-generic<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1093 – Azure </br> 4.4.0-190-generisk, 4.15.0-117-generisk, 4.15.0-118-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure till 9,37 snabb korrigerings korrigering * *|
|||
18,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-Generic to 4.15.0-135-generic </br> 4.18.0-13-Generic to 4.18.0-25-genered </br> 5.0.0-15-genered to 5.0.0-65-generic </br> 5.3.0-19-genered to 5.3.0-70-generic </br> 5.4.0 – 37-generisk till 5.4.0-59-generic</br> 5.4.0-60-genered to 5.4.0-65-generic </br> 4.15.0-1009 – Azure till 4.15.0-1106 – Azure </br> 4.18.0-1006 – Azure till 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure till 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure till 5.3.0-1035 – Azure </br> 5.4.0-1020 – Azure till 5.4.0 – 1039 – Azure </br> 4.15.0-136-generisk, 4.15.0-137-Generic, 4.15.0-139-Generic, 4.15.0-140-Generic, 5.3.0-72-Generic, 5.4.0-66-Generic, 5.4.0-67-genered, 5.4.0-70-genered, 4.15.0-1108-Azure, 4.15.0-1111-Azure, 5.4.0-1040-Azure, 5.4.0-1041-Azure, 5.4.0-1043-Azure, 4.15.0-1109-Azure, 4.15.0-1110-Azure till 9,41 snabb korrigerings korrigering * *|
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-Generic to 4.15.0-129-generic </br> 4.18.0-13-Generic to 4.18.0-25-genered </br> 5.0.0-15-genered till 5.0.0-63-generic </br> 5.3.0-19-genered to 5.3.0-69-generic </br> 5.4.0 – 37-generisk till 5.4.0-59-generic</br> 4.15.0-1009 – Azure till 4.15.0-1103 – Azure </br> 4.18.0-1006 – Azure till 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure till 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure till 5.3.0-1035 – Azure </br> 5.4.0-1020 – Azure till 5.4.0-1035 – Azure </br> 4.15.0-1104-Azure, 4.15.0-130-Generic, 4.15.0-132-genered, 5.4.0-1036-Azure, 5.4.0-60-Generic, 5.4.0-62-Generic, 4.15.0-1106-Azure, 4.15.0-134-Generic, 4.15.0-135-genered, 5.4.0-1039-Azure, 9,40 5.4.0|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-Generic to 4.15.0-123-generic </br> 4.18.0-13-Generic to 4.18.0-25-genered </br> 5.0.0-15-genered till 5.0.0-63-generic </br> 5.3.0-19-genered to 5.3.0-69-generic </br> 5.4.0 – 37-generisk till 5.4.0-53-generic</br> 4.15.0-1009 – Azure till 4.15.0 – 1099 – Azure </br> 4.18.0-1006 – Azure till 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure till 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure till 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure till 5.4.0-1031-Azure </br> 4.15.0-124-generisk, 5.4.0-54-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 4.15.0-1100-Azure, 4.15.0-126-genered, 4.15.0-128-genered, 5.4.0-58-Generic, 4.15.0-1102-Azure, 5.4.0-1034 – Azure till 9,39 snabb korrigering * *|
18,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-Generic to 4.15.0-118-generic </br> 4.18.0-13-Generic to 4.18.0-25-genered </br> 5.0.0-15-genered to 5.0.0-61-generic </br> 5.3.0-19-genered to 5.3.0-67-generic </br> 5.4.0 – 37-generisk till 5.4.0-48-genered</br> 4.15.0-1009 – Azure till 4.15.0-1096 – Azure </br> 4.18.0-1006 – Azure till 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure till 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure till 5.3.0-1035 – Azure </br> 5.4.0-1020-Azure till 5.4.0-tjänste – Azure </br> 4.15.0-121-Generic, 4.15.0-122-Generic, 5.0.0-62-Generic, 5.3.0-68-Generic, 5.4.0-51-Generic, 5.4.0-52-Generic, 4.15.0-1099-Azure, 5.4.0-1031-Azure till 9,38 snabb korrigering * *|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-Generic to 4.15.0-115-generic </br> 4.18.0-13-Generic to 4.18.0-25-genered </br> 5.0.0-15-Generic to 5.0.0-60-generic </br> 5.3.0-19-genered to 5.3.0-66-generic </br> 5.4.0 – 37-generisk till 5.4.0-45-genered</br> 4.15.0-1009 – Azure till 4.15.0-1093 – Azure </br> 4.18.0-1006 – Azure till 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure till 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure till 5.3.0-1035 – Azure </br> 5.4.0-1020 – Azure till 5.4.0-1023 – Azure</br> 4.15.0-117-Generic, 4.15.0-118-Generic, 5.0.0-61-Generic, 5.3.0-67-genered, 5.4.0-47-Generic, 5.4.0-48-genered, 4.15.0-1095-Azure, 4.15.0-1096-Azure, 5.4.0-1025-Azure, 5.4.0-– Azure till 9,37 snabb korrigerings korrigering * *|
|||
20,04 LTS |[9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0 – 26-Generic to 5.4.0-65 </br> -Generic 5.4.0-1010 – Azure till 5.4.0-1039 – Azure </br> 5.8.0-29-Generic to 5.8.0-43-generic </br> 5.4.0-66-Generic, 5.4.0-67-Generic, 5.4.0-70-genered, 5.8.0-44-Generic, 5.8.0-45-genered, 5.8.0-48-Generic, 5.4.0-1040-Azure, 5.4.0-1041-Azure, 5.4.0-1043-Azure till 9,41 snabb korrigerings korrigering * *|
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0 – 26-Generic to 5.4.0-59 </br> -Generic 5.4.0-1010 – Azure till 5.4.0-1035 – Azure </br> 5.8.0-29-Generic to 5.8.0-34-generic </br> 5.4.0-1036-Azure, 5.4.0-60-generisk, 5.4.0-62-Generic, 5.8.0-36-Generic, 5.8.0-38-genered, 5.4.0-1039-Azure, 5.4.0-64-Generic, 5.4.0-65-Generic, 5.8.0-40-genered, 5.8.0-41-Generic till 9,40 snabb korrigering * *|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0 – 26-Generic to 5.4.0-53 </br> -Generic 5.4.0-1010-Azure till 5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034 – Azure till 9,39 snabb korrigerings korrigering * *
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0 – 26-Generic to 5.4.0-53 </br> -Generic 5.4.0-1010-Azure till 5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034 – Azure till 9,39 snabb korrigerings korrigering * *
20,04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0 – 26-Generic to 5.4.0-48 </br> -Generic 5.4.0-1010-Azure till 5.4.0-tjänsteoch Azure </br> 5.4.0-51-Generic, 5.4.0-52-Generic, 5.8.0-23-genered, 5.8.0-25-genered, 5.4.0-1031-Azure till 9,38 snabb korrigerings korrigering * *
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0 – 26-Generic to 5.4.0-45 </br> -Generic 5.4.0-1010 – Azure till 5.4.0-1023 – Azure </br> 5.4.0-47-Generic, 5.4.0-48-genered, 5.4.0-1025-Azure, 5.4.0-– Azure till 9,37 snabb korrigerings korrigering * *

* * Obs! Om du vill ha stöd för de senaste Linux-kernelerna inom 15 dagars lansering, Azure Site Recovery samla in snabb korrigerings korrigering ovanpå den senaste mobilitets agent versionen. Den här korrigeringen är insamlad i mellan två större versioner. Följ anvisningarna i [den här artikeln](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)om du vill uppdatera till den senaste versionen av mobilitets agenten (inklusive snabb korrigerings korrigering). Den här korrigeringen är för närvarande distribuerad för mobilitets agenter som används i Azure till Azure DR-scenario.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debian Kernel-versioner som stöds för Azure Virtual Machines

**Frisläpp** | **Mobilitets tjänst version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) , [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0 – 4-amd64 till 3.2.0-6-amd64, 3.16.0 -0. bpo. 4-amd64 |
|||
Debian 8 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0 – 4-amd64 till 3.16.0-11-amd64, 4.9.0 -0. bpo. 4-amd64 till 4.9.0 -0. bpo. 11-amd64 |
|||
Debian 9,1 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 till 4.9.0-14-amd64 </br> 4.19.0 -0. bpo. 1-amd64 till 4.19.0 -0. bpo. 14-amd64 </br> 4.19.0 -0. bpo. 1 – Cloud-amd64 till 4.19.0 -0. bpo. 14 – Cloud-amd64 </br> 4.9.0-15-amd64, 4.19.0 -0. bpo. 16-amd64, 4.19.0 -0. bpo. 16-Cloud-amd64 till 9,41 snabb korrigerings korrigering * *
Debian 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 till 4.9.0-14-amd64 </br> 4.19.0 -0. bpo. 1-amd64 till 4.19.0 -0. bpo. 13-amd64 </br> 4.19.0 -0. bpo. 1 – Cloud-amd64 till 4.19.0 -0. bpo. 13-Cloud-amd64 
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 till 4.9.0-14-amd64 </br> 4.19.0 -0. bpo. 1-amd64 till 4.19.0 -0. bpo. 12-amd64 </br> 4.19.0 -0. bpo. 1 – Cloud-amd64 till 4.19.0 -0. bpo. 12-Cloud-amd64 </br> 4.19.0 -0. bpo. 13-amd64, 4.19.0 -0. bpo. 13-Cloud-amd64 till 9,39 snabb korrigering * *</br> 
Debian 9,1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 till 4.9.0-13-amd64 </br> 4.19.0 -0. bpo. 1-amd64 till 4.19.0 -0. bpo. 11-amd64 </br> 4.19.0 -0. bpo. 1 – Cloud-amd64 till 4.19.0 -0. bpo. 11-Cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0 -0. bpo. 12-amd64, 4.19.0 -0. bpo. 12-Cloud-amd64 till 9,38 snabb korrigering * *
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 till 4.9.0-13-amd64, 4.19.0 -0. bpo. 6-amd64 till 4.19.0 -0. bpo. 10-amd64, 4.19.0 -0. bpo. 6 – Cloud-amd64 till 4.19.0 -0. bpo. 10 – Cloud-amd64
|||
Debian 10 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64 till 4.19.0-14-amd64 </br> 4.19.0 – 6 – Cloud-amd64 till 4.19.0-14-Cloud-amd64 </br> 5.8.0 -0. bpo. 2-amd64 </br> 5.8.0 -0. bpo. 2 – Cloud-amd64 </br> 4.19.0-10-Cloud-amd64, 4.19.0-16-amd64, 4.19.0-16-Cloud-amd64 till 9,41 snabb korrigering * *
Debian 10 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 till 4.19.0-13-amd64 </br> 4.19.0 – 6 – Cloud-amd64 till 4.19.0-13-Cloud-amd64 </br> 5.8.0 -0. bpo. 2-amd64 </br> 5.8.0 -0. bpo. 2 – Cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Stöd för SUSE Linux Enterprise Server 12 kernel-versioner för Azure Virtual Machines

**Frisläpp** | **Mobilitets tjänst version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Alla [aktie-och SUSE 12 SP1-, SP2-, SP3-, SP4-och SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4,7-Azure till 4.4.180-4.31 – Azure</br>4.12.14-6.3 – Azure till 4.12.14-6.43 – Azure </br> 4.12.14-16,7 – Azure till 4.12.14-16.44 – Azure </br> 4.12.14-16.47 – Azure till 9,41 snabb korrigerings korrigering * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Alla [aktie-och SUSE 12 SP1-, SP2-, SP3-, SP4-och SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4,7-Azure till 4.4.180-4.31 – Azure</br>4.12.14-6.3 – Azure till 4.12.14-6.43 – Azure </br> 4.12.14-16,7 – Azure till 4.12.14-16.38 – Azure </br> 4.12.14-16.41 – Azure till 9,40 snabb korrigerings korrigering * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Alla [aktie-och SUSE 12 SP1-, SP2-, SP3-, SP4-och SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4,7-Azure till 4.4.180-4.31 – Azure</br>4.12.14-6.3 – Azure till 4.12.14-6.43 – Azure </br> 4.12.14-16,7 – Azure till 4.12.14-16.34 – Azure </br> 4.12.14-16.38 – Azure till 9,39 snabb korrigerings korrigering * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Alla [aktie-och SUSE 12 SP1-, SP2-, SP3-, SP4-och SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4,7-Azure till 4.4.180-4.31 – Azure</br>4.12.14-6.3 – Azure till 4.12.14-6.43 – Azure </br> 4.12.14-16,7 – Azure till 4.12.14-16.28 – Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,37](https://support.microsoft.com/help/4582666/) | Alla [aktie-och SUSE 12 SP1-, SP2-, SP3-, SP4-och SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4,7-Azure till 4.4.180-4.31 – Azure</br>4.12.14-6.3 – Azure till 4.12.14-6.43 – Azure </br> 4.12.14-16,7 – Azure till 4.12.14-16.22 – Azure </br> 4.12.14-16.25 – Azure, 4.12.14-16.28 – Azure till 9,37 snabb korrigerings korrigering * *|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server 15 kernel-versioner som stöds för Azure Virtual Machines

**Frisläpp** | **Mobilitets tjänst version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Som standard stöds alla [aktier i lager SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14 – 5.5 – Azure till 4.12.14 – 5.47 – Azure </br></br> 4.12.14 – 8.5 – Azure till 4.12.14 – 8.55 – Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5 – Azure till 5.3.18-18.35 – Azure </br> 5.3.18-18.38 – Azure till 9,41 snabb korrigerings korrigering * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Som standard stöds alla [aktier i lager SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14 – 5.5 – Azure till 4.12.14 – 5.47 – Azure </br></br> 4.12.14 – 8.5 – Azure till 4.12.14 – 8.58 – Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5 – Azure till 5.3.18-18.29 – Azure </br> 5.3.18-18.32 – Azure, 4.12.14-8.58 – Azure till 9,40 snabb korrigerings korrigering * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Som standard stöds alla [aktier i lager SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14 – 5.5 – Azure till 4.12.14 – 5.47 – Azure </br></br> 4.12.14 – 8.5 – Azure till 4.12.14 – 8.47 – Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5 – Azure till 5.3.18-18.21 – Azure </br> 4.12.14-8.52-Azure, 5.3.18-18.24-Azure, 4.12.14-8.55-Azure, 5.3.18-18.29-Azure till 9,39 snabb korrigerings korrigering * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Som standard stöds alla [aktier i lager SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14 – 5.5 – Azure till 4.12.14 – 5.47 – Azure </br></br> 4.12.14 – 8.5 – Azure till 4.12.14 – 8.44 – Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5 – Azure till 5.3.18-18.18 – Azure </br> 4.12.14-8.47 – Azure, 5.3.18-18.21 – Azure till 9,38 snabb korrigerings korrigering * *
SUSE Linux Enterprise Server 15 och 15 SP1 | [9,37](https://support.microsoft.com/help/4582666/)  | Som standard stöds alla [aktier i lager SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14 – 5.5 – Azure till 4.12.14 – 5.47 – Azure </br></br> 4.12.14 – 8.5 – Azure till 4.12.14 – 8.38 – Azure </br> 4.12.14-8.41 – Azure, 4.12.14-8.44 – Azure till 9,37 snabb korrigerings korrigering * *


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikerade datorer – Linux-filsystem/gäst lagring

* Fil system: ext3, ext4, XFS, BTRFS
* Volym hanterare: LVM2

> [!NOTE]
> Program vara för flera sökvägar stöds inte.


## <a name="replicated-machines---compute-settings"></a>Replikerade datorer – beräknings inställningar

**Inställning** | **Support** | **Information**
--- | --- | ---
Storlek | Valfri storlek på virtuella Azure-datorer med minst 2 processor kärnor och 1 GB RAM | Verifiera [storleken på virtuella Azure-datorer](../virtual-machines/sizes.md).
Tillgänglighetsuppsättningar | Stöds | Om du aktiverar replikering för en virtuell Azure-dator med standard alternativen skapas en tillgänglighets uppsättning automatiskt, baserat på käll regions inställningarna. Du kan ändra de här inställningarna.
Tillgänglighetszoner | Stöds |
Hybrid användnings förmånen (hubb) | Stöds | Om den virtuella käll datorn har en nav-licens aktive rad, används även HUB-licensen i en redundanstest eller redundansväxling av den virtuella datorn.
Skalningsuppsättningar för virtuella datorer | Stöds inte |
Azure Gallery-bilder – Microsoft publicerat | Stöds | Stöds om den virtuella datorn körs på ett operativ system som stöds.
Azure Gallery-avbildningar – tredje part publicerad | Stöds | Stöds om den virtuella datorn körs på ett operativ system som stöds.
Anpassade avbildningar – tredje part publicerad | Stöds | Stöds om den virtuella datorn körs på ett operativ system som stöds.
Virtuella datorer som migrerats med Site Recovery | Stöds | Om en virtuell VMware-dator eller en fysisk dator har migrerats till Azure med hjälp av Site Recovery måste du avinstallera den äldre versionen av mobilitets tjänsten som körs på datorn och starta om datorn innan du replikerar den till en annan Azure-region.
Azure RBAC-principer | Stöds inte | Azure-rollbaserad åtkomst kontroll (Azure RBAC) principer för virtuella datorer replikeras inte till den virtuella redundansväxlingen i mål regionen.
Tillägg | Stöds inte | Tillägg replikeras inte till den virtuella redundansväxlingen i mål regionen. Den måste installeras manuellt efter redundansväxlingen.
Placerings grupper för närhet | Stöds | Virtuella datorer som finns inuti en närhets placerings grupp kan skyddas med hjälp av Site Recovery.
Taggar  | Stöds | Användardefinierade taggar som används på virtuella käll datorer överförs till virtuella mål datorer efter redundanstest eller redundans. Taggar på VM: ar replikeras en gång var 24: e timme så länge som de virtuella datorerna finns i mål regionen.


## <a name="replicated-machines---disk-actions"></a>Replikerade datorer – disk åtgärder

**Åtgärd** | **Information**
-- | ---
Ändra storlek på disk på replikerad virtuell dator | Stöds på den virtuella käll datorn före redundans. Du behöver inte inaktivera/återaktivera replikering.<br/><br/> Om du ändrar den virtuella käll datorn efter redundansväxlingen, fångas inte ändringarna.<br/><br/> Om du ändrar disk storleken på den virtuella Azure-datorn efter redundansväxlingen, registreras inte ändringarna av Site Recovery och återställningen görs till den ursprungliga virtuella dator storleken.
Lägga till en disk till en replikerad virtuell dator | Stöds
Offlineändringar av skyddade diskar | Att koppla från diskar och göra offlineändringar till dem kräver att en fullständig omsynkronisering utlöses.

## <a name="replicated-machines---storage"></a>Replikerade datorer – lagring

Den här tabellen sammanfattade stödet för den virtuella Azure OS-disken, data disken och den temporära disken.

- Det är viktigt att Observera de virtuella datorernas disk gränser och mål för [hanterade diskar](../virtual-machines/disks-scalability-targets.md) för att undvika prestanda problem.
- Om du distribuerar med standardinställningarna skapar Site Recovery automatiskt diskar och lagrings konton baserat på käll inställningarna.
- Om du anpassar, se till att du följer rikt linjerna.

**Komponent** | **Support** | **Information**
--- | --- | ---
Maximal storlek för OS-disk | 2048 GB | [Läs mer](../virtual-machines/managed-disks-overview.md) om VM-diskar.
Tillfällig disk | Stöds inte | Den tillfälliga disken är alltid exkluderad från replikering.<br/><br/> Lagra inte beständiga data på den temporära disken. [Läs mer](../virtual-machines/managed-disks-overview.md).
Maximal storlek för data disk | 32 TB för Managed disks<br></br>4 TB för ohanterade diskar|
Minsta storlek för data disk | Ingen begränsning för ohanterade diskar. 2 GB för hanterade diskar |
Högsta antal data diskar | Upp till 64, i enlighet med stöd för en speciell storlek på virtuell Azure-dator | [Läs mer](../virtual-machines/sizes.md) om storlekar på virtuella datorer.
Ändrings takt för data disk | Högst 20 Mbit/s per disk för Premium Storage. Högst 2 Mbit/s per disk för standard lagring. | Om genomsnitts data ändrings takten på disken kontinuerligt är högre än det högsta antalet kommer replikering inte att fångas upp.<br/><br/>  Men om det maximala värdet överskrids sporadisk, kan replikeringen fångas upp, men du kan se något fördröjda återställnings punkter.
Data disk-standard lagrings konto | Stöds |
Data disk – Premium Storage-konto | Stöds | Om en virtuell dator har diskar som sprids över Premium-och standard lagrings konton kan du välja ett annat mål lagrings konto för varje disk, så att du har samma lagrings konfiguration i mål regionen.
Hanterad disk – standard | Stöds i Azure-regioner där Azure Site Recovery stöds. |
Hanterad disk – Premium | Stöds i Azure-regioner där Azure Site Recovery stöds. |
Standard SSD | Stöds |
Redundans | LRS och GRS stöds.<br/><br/> ZRS stöds inte.
Kylning och frekvent lagring | Stöds inte | VM-diskar stöds inte på låg frekvent lagring
Lagringsutrymmen | Stöds |
Lagrings gränssnitt för NVMe | Stöds inte
Kryptering i rest (SSE) | Stöds | SSE är standardinställningen på lagrings konton.
Kryptering i vilo läge (CMK) | Stöds | Både program-och HSM-nycklar stöds för hanterade diskar
Dubbel kryptering i vila | Stöds | Läs mer om regioner som stöds för [Windows](../virtual-machines/disk-encryption.md) och [Linux](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) för Windows OS | Stöds för virtuella datorer med hanterade diskar. | Virtuella datorer med ohanterade diskar stöds inte. <br/><br/> HSM-skyddade nycklar stöds inte. <br/><br/> Kryptering av enskilda volymer på en enskild disk stöds inte. |
Azure Disk Encryption (ADE) för Linux OS | Stöds för virtuella datorer med hanterade diskar. | Virtuella datorer med ohanterade diskar stöds inte. <br/><br/> HSM-skyddade nycklar stöds inte. <br/><br/> Kryptering av enskilda volymer på en enskild disk stöds inte. <br><br> Känt problem med att aktivera replikering. [Läs mer.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
Nyckel rotation för SAS | Stöds inte | Om SAS-nyckeln för lagrings konton roteras måste kunden inaktivera och återaktivera replikering. |
Cachelagring av värden | Stöds
Frekvent tillägg    | Stöds | Att aktivera replikering för en datadisk som du lägger till i en replikerad virtuell Azure-dator stöds för virtuella datorer som använder hanterade diskar. <br/><br/> Det går bara att lägga till en hård disk i taget till en virtuell Azure-dator. Det finns inte stöd för att lägga till flera diskar parallellt. |
Snabb borttagnings disk    | Stöds inte | Om du tar bort datadisk på den virtuella datorn måste du inaktivera replikeringen och aktivera replikeringen igen för den virtuella datorn.
Uteslut disk | Hjälp. Du måste använda [PowerShell](azure-to-azure-exclude-disks.md) för att konfigurera. |    Temporära diskar undantas som standard.
Lagringsutrymmen direkt  | Stöds för kraschbaserade återställnings punkter. Programmets konsekventa återställnings punkter stöds inte. |
Skalbar fil Server  | Stöds för kraschbaserade återställnings punkter. Programmets konsekventa återställnings punkter stöds inte. |
DRBD | Diskar som ingår i en DRBD-installation stöds inte. |
LRS | Stöds |
GRS | Stöds |
RA-GRS | Stöds |
ZRS | Stöds inte |
Kylning och frekvent lagring | Stöds inte | Virtuella dator diskar stöds inte i låg frekvent lagring och snabb lagring
Azure Storage brand väggar för virtuella nätverk  | Stöds | Aktivera [Tillåt betrodda Microsoft-tjänster](../storage/common/storage-network-security.md#exceptions)om du begränsar åtkomsten till virtuella nätverk till lagrings konton.
Generell användning v2-lagrings konton (både frekvent och låg frekvent nivå) | Stöds | Transaktions kostnader ökar avsevärt jämfört med generell användning v1-lagrings konton
Generation 2 (UEFI-start) | Stöds
NVMe-diskar | Stöds inte
Delade diskar i Azure | Stöds inte
Alternativ för säker överföring | Stöds
Skriv Accelerator aktiverade diskar | Stöds inte
Taggar  | Stöds | Användardefinierade Taggar replikeras var 24: e timme.

>[!IMPORTANT]
> För att undvika prestanda problem måste du kontrol lera att du följer skalbarhets-och prestanda mål för virtuella datorer för [hanterade diskar](../virtual-machines/disks-scalability-targets.md). Om du använder standardinställningarna skapar Site Recovery de nödvändiga diskarna och lagrings kontona baserat på käll konfigurationen. Om du anpassar och väljer dina egna inställningar följer du diskens skalbarhet och prestanda mål för dina virtuella käll datorer.

## <a name="limits-and-data-change-rates"></a>Begränsningar och data ändrings takt

I följande tabell sammanfattas Site Recovery gränser.

- Dessa gränser baseras på våra tester, men det är självklart inte alla möjliga kombinationer av program I/O.
- De faktiska resultaten kan variera beroende på appens I/O-mix.
- Det finns två gränser att överväga, data omsättning per disk och data omsättning per virtuell dator.
- Den aktuella gränsen för data omsättning per virtuell dator är 54 MB/s, oavsett storlek.

**Lagrings mål** | **Genomsnittligt käll disk-I/O** |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total käll disk data omsättning per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |    336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk

## <a name="replicated-machines---networking"></a>Replikerade datorer – nätverk
**Inställning** | **Support** | **Information**
--- | --- | ---
NIC | Maximalt antal som stöds för en angiven storlek på virtuell Azure-dator | Nätverkskort skapas när den virtuella datorn skapas under redundansväxling.<br/><br/> Antalet nätverkskort på den virtuella redundansväxlingen är beroende av antalet nätverkskort på den virtuella käll datorn när replikering har Aktiver ATS. Om du lägger till eller tar bort ett nätverkskort efter att ha aktiverat replikering, påverkar det inte antalet nätverkskort på den replikerade virtuella datorn efter redundansväxlingen. <br/><br/> Ordningen på nätverkskort efter redundansväxlingen är inte garanterat densamma som den ursprungliga ordningen. <br/><br/> Du kan byta namn på nätverkskort i mål regionen baserat på organisationens namngivnings konventioner. Att byta namn på nätverkskort stöds med PowerShell.
Internet-lastbalanserare | Stöds inte | Du kan konfigurera offentliga/Internet-belastningsutjämnare i den primära regionen. Offentliga/Internet-belastningsutjämnare stöds dock inte av Azure Site Recovery i DR-regionen.
Intern belastningsutjämnare | Stöds | Koppla den förkonfigurerade belastningsutjämnaren med hjälp av ett Azure Automation-skript i en återställnings plan.
Offentlig IP-adress | Stöds | Koppla en befintlig offentlig IP-adress till NÄTVERKSKORTet. Du kan också skapa en offentlig IP-adress och associera den med NÄTVERKSKORTet med hjälp av ett Azure Automation-skript i en återställnings plan.
NSG på nätverkskort | Stöds | Koppla NSG till NÄTVERKSKORTet med hjälp av ett Azure Automation-skript i en återställnings plan.
NSG i undernät | Stöds | Koppla NSG till under nätet med hjälp av ett Azure Automation-skript i en återställnings plan.
Reserverad (statisk) IP-adress | Stöds | Om NÄTVERKSKORTet på den virtuella käll datorn har en statisk IP-adress, och mål under nätet har samma IP-adress, tilldelas den misslyckade över VM.<br/><br/> Om mål under nätet inte har samma IP-adress är en av de tillgängliga IP-adresserna i under nätet reserverad för den virtuella datorn.<br/><br/> Du kan också ange en fast IP-adress och undernät i inställningar för **replikerade objekt**,  >    >  **beräknings-och nätverks**  >  **nätverks gränssnitt**.
Dynamisk IP-adress | Stöds | Om NÄTVERKSKORTet på källan har dynamisk IP-adressering, är NÄTVERKSKORTet på den misslyckade virtuella datorn också dynamiskt som standard.<br/><br/> Du kan ändra detta till en fast IP-adress om det behövs.
Flera IP-adresser | Stöds inte | När du växlar över en virtuell dator som har ett nätverkskort med flera IP-adresser behålls bara den primära IP-adressen för NÄTVERKSKORTet i käll regionen. Om du vill tilldela flera IP-adresser kan du lägga till virtuella datorer i en [återställnings plan](recovery-plan-overview.md) och bifoga ett skript för att tilldela ytterligare IP-adresser till planen, eller så kan du göra ändringen manuellt eller med ett skript efter redundansväxlingen.
Traffic Manager     | Stöds | Du kan förkonfigurera Traffic Manager så att trafiken dirigeras till slut punkten i käll regionen regelbundet och till slut punkten i mål regionen i händelse av redundans.
Azure DNS | Stöds |
Anpassad DNS    | Stöds |
Oautentiserad proxy | Stöds | [Läs mer](./azure-to-azure-about-networking.md)
Autentiserad proxy | Stöds inte | Om den virtuella datorn använder en autentiserad proxy för utgående anslutningar kan den inte replikeras med hjälp av Azure Site Recovery.
VPN plats-till-plats-anslutning till lokal plats<br/><br/>(med eller utan ExpressRoute)| Stöds | Se till att UDR och NSG: er har kon figurer ATS på ett sådant sätt att den Site Recovery trafiken inte dirigeras till lokalt. [Läs mer](./azure-to-azure-about-networking.md)
VNET-till-VNET-anslutning    | Stöds | [Läs mer](./azure-to-azure-about-networking.md)
Tjänstslutpunkter för virtuellt nätverk | Stöds | Om du begränsar det virtuella nätverkets åtkomst till lagrings konton måste du se till att de betrodda Microsoft-tjänsterna har åtkomst till lagrings kontot.
Snabbare nätverk | Stöds | Accelererat nätverk måste vara aktiverat på den virtuella käll datorn. [Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo-nätverks installation | Stöds inte | Med tredje parts apparater finns det ofta begränsningar som providern har infört i den virtuella datorn. Azure Site Recovery behöver agent, tillägg och utgående anslutning för att vara tillgängliga. Men installationen tillåter inte att någon utgående aktivitet konfigureras i den virtuella datorn.
IPv6  | Stöds inte | Blandade konfigurationer som inkluderar både IPv4 och IPv6 stöds inte heller. Frigör under nätet för IPv6-intervallet innan Site Recovery åtgärd.
Åtkomst till Site Recovery-tjänst via privat länk | Stöds | [Läs mer](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Taggar  | Stöds | Användardefinierade taggar på nätverkskort replikeras var 24: e timme.



## <a name="next-steps"></a>Nästa steg

- Läs [nätverks vägledning](./azure-to-azure-about-networking.md)  för replikering av virtuella Azure-datorer.
- Distribuera haveri beredskap genom att [Replikera virtuella Azure-datorer](./azure-to-azure-quickstart.md).
