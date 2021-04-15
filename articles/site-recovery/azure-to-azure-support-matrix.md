---
title: Supportmatris för haveriberedskap för virtuella Azure-datorer med Azure Site Recovery
description: Sammanfattar stödet för haveriberedskap för virtuella Azure-datorer till en sekundär region med Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.openlocfilehash: 02268471d58cbd473493b6001aa9f1df271077bb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376162"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Supportmatris för haveriberedskap för virtuella Azure-datorer mellan Azure-regioner

Den här artikeln sammanfattar support och krav för [haveriberedskap](site-recovery-overview.md) för virtuella Azure-datorer från en Azure-region till en annan med hjälp Azure Site Recovery tjänsten.


## <a name="deployment-method-support"></a>Stöd för distributionsmetod

**Distribution** |  **Support**
--- | ---
**Azure-portalen** | Stöds.
**PowerShell** | Stöds. [Läs mer](azure-to-azure-powershell.md)
**REST-API** | Stöds.
**CLI** | Stöds för närvarande inte


## <a name="resource-support"></a>Resursstöd

**Resursåtgärd** | **Information**
--- | ---
**Flytta valv mellan resursgrupper** | Stöds inte
**Flytta beräknings-/lagrings-/nätverksresurser mellan resursgrupper** | Stöds inte.<br/><br/> Om du flyttar en virtuell dator eller associerade komponenter, till exempel lagring/nätverk efter att den virtuella datorn har replikerats, måste du inaktivera och sedan återaktivera replikeringen för den virtuella datorn.
**Replikera virtuella Azure-datorer från en prenumeration till en annan för haveriberedskap** | Stöds inom samma Azure Active Directory klientorganisation.
**Migrera virtuella datorer mellan regioner inom geografiska kluster som stöds (inom och mellan prenumerationer)** | Stöds inom samma Azure Active Directory klientorganisation.
**Migrera virtuella datorer inom samma region** | Stöds inte.
**Dedikerade Azure-värdar** | Stöds inte.

## <a name="region-support"></a>Stöd för regioner

Du kan replikera och återställa virtuella datorer mellan två regioner inom samma geografiska kluster. Geografiska kluster definieras med datafördröjning och landssuveränitet i åtanke.


**Geografiskt kluster** | **Azure-regioner**
-- | --
Amerika | Kanada, östra, Kanada, centrala, USA, södra centrala, USA, västra centrala, USA, östra, USA, östra 2, USA, västra, USA, västra 2, USA, centrala, USA, norra centrala
Europa | Storbritannien, västra, Storbritannien, södra, Europa, norra, Europa, västra, Sydafrika, västra, Sydafrika, norra, Tyskland, östra, Frankrike, centrala, Schweiz, norra, Tyskland, västra centrala
Asien | Indien, södra, Indien, centrala, Indien, västra, Sydostasien, Asien, östra, Japan, östra, Japan, västra, Sydkorea, centrala, Sydkorea, södra
Australien    | Australien, östra, Australien, sydöstra, Australien, centrala, Australien, centrala 2
Azure Government    | US GOV, Virginia, USA GOV, Iowa, USA GOV, Arizona, USA GOV, Texas, USA DOD, östra, USA DOD, centrala
Tyskland    | Tyskland, centrala, Tyskland, nordöstra
Kina | Kina, östra, Kina, norra, Kina, norra 2, Kina, östra 2
Begränsade regioner som är reserverade för haveriberedskap i landet |Schweiz, västra reserverat för Schweiz, norra; Frankrike, södra reserverade för Frankrike, centrala; Förenade Arabemiraten, centrala är begränsat för kunder i Förenade Arabemiraten, norra; Tyskland, västra för kunder i Östra Tyskland

>[!NOTE]
>
> - För **Brasilien, södra** kan du replikera och redundans för dessa regioner: USA, södra centrala, USA, västra centrala, USA, östra, USA, östra 2, USA, västra, USA, västra 2 och USA, norra centrala.
> - Brasilien, södra kan bara användas som en källregion som virtuella datorer kan replikeras från med Site Recovery. Det kan inte fungera som en målregion. Detta beror på problem med svarstider på grund av geografiska avstånd. Observera att om du redundans redundansar från Brasilien, södra som en källregion till ett mål, stöds återställning till Brasilien, södra från målregionen.
> - Du kan arbeta i regioner som du har rätt åtkomst till.
> - Om den region där du vill skapa ett valv inte visas kontrollerar du att din prenumeration har åtkomst till att skapa resurser i den regionen.
> - Om du inte kan se en region i ett geografiskt kluster när du aktiverar replikering kontrollerar du att din prenumeration har behörighet att skapa virtuella datorer i den regionen.



## <a name="cache-storage"></a>Cachelagring

I den här tabellen sammanfattas stödet för cachelagringskontot som används Site Recovery under replikeringen.

**Inställning** | **Support** | **Information**
--- | --- | ---
V2-lagringskonton för generell användning (nivån Hot och Cool) | Stöds | Användning av GPv2 rekommenderas inte eftersom transaktionskostnaderna för V2 är betydligt högre än V1-lagringskonton.
Premium Storage | Stöds inte | Standardlagringskonton används för cachelagring för att optimera kostnaderna.
Azure Storage brandväggar för virtuella nätverk  | Stöds | Om du använder ett brandväggsaktiverad cachelagringskonto eller mållagringskonto ska du se till att ["Tillåt betrodda Microsoft-tjänster"](../storage/common/storage-network-security.md#exceptions).<br></br>Kontrollera också att du tillåter åtkomst till minst ett undernät för det virtuella källnätverket.

Tabellen nedan visar gränserna för antalet diskar som kan replikeras till ett enda lagringskonto.

**Typ av lagringskonto**    |    **Omsättning = 4 M/s per disk**    |    **Omsättning = 8 M/s per disk**
---    |    ---    |    ---
V1-lagringskonto    |    300 diskar    |    150 diskar
V2-lagringskonto    |    750 diskar    |    375 diskar

När den genomsnittliga dataomsättningen på diskarna ökar, minskar antalet diskar som ett lagringskonto har stöd för. Tabellen ovan kan användas som vägledning för att fatta beslut om antalet lagringskonton som behöver etableras.

Observera att ovanstående gränser är specifika för Azure för Azure och scenarier för zon-till-zon-DR. 

## <a name="replicated-machine-operating-systems"></a>Replikerade operativsystem för datorer

Site Recovery stöd för replikering av virtuella Azure-datorer som kör operativsystemen som anges i det här avsnittet. Observera att om en dator som redan replikeras senare uppgraderas (eller nedgraderas) till en annan större kernel, måste du inaktivera replikering och återaktivera replikering efter uppgraderingen.

### <a name="windows"></a>Windows


**Operativsystem** | **Information**
--- | ---
Windows Server 2019 | Stöds för Server Core, Server med skrivbordsupplevelse.
Windows Server 2016  | Server Core, server med skrivbordsupplevelse som stöds.
Windows Server 2012 R2 | Stöds.
Windows Server 2012 | Stöds.
Windows Server 2008 R2 med SP1/SP2 | Stöds.<br/><br/> Från version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av tjänsten Mobility-tillägget för virtuella Azure-datorer måste du installera en Windows [Servicing Stack Update (SSU)](https://support.microsoft.com/help/4490628) och [SHA-2-uppdatering](https://support.microsoft.com/help/4474419) på datorer som kör Windows Server 2008 R2 SP1/SP2.  SHA-1 stöds inte från september 2019 och om SHA-2-kodsignering inte är aktiverat kommer agenttillägget inte att installeras/uppgraderas som förväntat. Läs mer om [SHA-2-uppgradering och krav.](https://aka.ms/SHA-2KB)
Windows 10 (x64) | Stöds.
Windows 8.1 (x64) | Stöds.
Windows 8 (x64) | Stöds.
Windows 7 (x64) med SP1 och framåt | Från version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av tjänsten Mobility-tillägget för virtuella Azure-datorer måste du installera en Windows [Servicing Stack Update (SSU)](https://support.microsoft.com/help/4490628) och [SHA-2-uppdatering](https://support.microsoft.com/help/4474419) på datorer som kör Windows 7 med SP1.  SHA-1 stöds inte från september 2019 och om SHA-2-kodsignering inte är aktiverat kommer agenttillägget inte att installeras/uppgraderas som förväntat.. Läs mer om [SHA-2-uppgradering och krav.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**Operativsystem** | **Information**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [7.8,](https://support.microsoft.com/help/4564347/) [7.9,](https://support.microsoft.com/help/4578241/) [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609/), [8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10 </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, [7.8](https://support.microsoft.com/help/4564347/), [7.9 före GA-versionen](https://support.microsoft.com/help/4578241/), version 7.9 GA stöds från 9.37 snabbkorrigeringskorrigering** </br> 8.0, 8.1, [8.2,](https://support.microsoft.com/en-us/help/4570609) [8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | Innehåller stöd för alla 14.04. *x* versioner; [Kernelversioner som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Ubuntu 16.04 LTS Server | Innehåller stöd för alla 16.04. *x* versioner; [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servrar som använder lösenordsbaserad autentisering och inloggning och cloud-init-paketet för att konfigurera virtuella datorer i molnet kan ha lösenordsbaserad inloggning inaktiverad vid redundans (beroende på cloudinit-konfigurationen). Lösenordsbaserad inloggning kan återaktiveras på den virtuella datorn genom att återställa lösenordet från menyn Support > Troubleshooting > Settings (Felsökning av >-inställningar) (för den virtuella datorn som misslyckades i Azure Portal.
Ubuntu 18.04 LTS Server | Innehåller stöd för alla 18.04. *x* versioner; [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servrar som använder lösenordsbaserad autentisering och inloggning och cloud-init-paketet för att konfigurera virtuella datorer i molnet kan ha lösenordsbaserad inloggning inaktiverad vid redundans (beroende på cloudinit-konfigurationen). Lösenordsbaserad inloggning kan återaktiveras på den virtuella datorn genom att återställa lösenordet från menyn Support > Troubleshooting > Settings (Felsökning av >-inställningar) (för den felande virtuella datorn i Azure Portal.
Ubuntu 20.04 LTS-server | Innehåller stöd för alla 20.04. *x* versioner; [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Innehåller stöd för alla 7. *x* versioner som [stöds av kernelversioner](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Innehåller stöd för alla 8. *x* versioner som [stöds av kernelversioner](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Innehåller stöd för 9.1 till 9.13. Debian 9.0 stöds inte. [Kernelversioner som stöds](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Kernelversioner som stöds](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(kernelversioner som stöds)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(kernelversioner som stöds)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Uppgradering av replikering av datorer från SP3 till SP4 stöds inte. Om en replikerad dator har uppgraderats måste du inaktivera replikering och återaktivera replikering efter uppgraderingen.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4573888/), [7.9](https://support.microsoft.com/help/4597409), [8.0](https://support.microsoft.com/help/4573888/), [8.1](https://support.microsoft.com/help/4573888/) (kör Red Hat-kompatibel kernel eller Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK4, UEK5)<br/><br/>8.1 (körs på alla UEK-kärnor och RedHat kernel <= 3.10.0-1062.* stöds i [9.35](https://support.microsoft.com/help/4573888/). Stöd för resten av RedHat-kernels finns i [9.36](https://support.microsoft.com/help/4578241/))

> [!NOTE]
> För Linux-versioner Azure Site Recovery inte anpassade OS-avbildningar. Endast de lagerkärnor som ingår i den lägre distributionsversionen stöds.

**Obs! För att stödja de senaste Linux-kernels inom 15 dagar efter lanseringen Azure Site Recovery en korrigeringsfil för snabbkorrigering ovanpå den senaste versionen av mobilitetsagenten. Den här korrigeringen distribueras mellan två huvudversionsversionerna. Om du vill uppdatera till den senaste versionen av mobilitetsagenten (inklusive korrigering av snabbkorrigering) följer du stegen i den [här artikeln.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Den här korrigeringen distribueras för närvarande för mobilitetsagenter som används i DR-scenariot för Azure till Azure.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ubuntu-kernelversioner som stöds för virtuella Azure-datorer

**Frisläpp** | **tjänsten Mobility version** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24-generic till 3.13.0-170-generic,<br/>3.16.0-25-generic till 3.16.0-77-generic,<br/>3.19.0-18-generic till 3.19.0-80-generic,<br/>4.2.0-18-generic till 4.2.0-42-generic,<br/>4.4.0-21-generic till 4.4.0-148-generic,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
|||
16.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic till 4.4.0-201-generic,<br/>4.8.0-34-generic till 4.8.0-58-generic,<br/>4.10.0-14-generic till 4.10.0-42-generic,<br/>4.11.0-13-generic till 4.11.0-14-generic,<br/>4.13.0-16-generic till 4.13.0-45-generic,<br/>4.15.0-13-generic till 4.15.0-133-generic<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1106-azure <br/> 4.4.0-203-generic, 4.4.0-204-generic, 4.4.0-206-generic, 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 4.15.0-1108-azure, 4.15.0-1109-azure, 4.15.0-1110-azure, 4.15.0-1111-azure till 9.41 hot fix patch**|
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic till 4.4.0-197-generic,<br/>4.8.0-34-generic till 4.8.0-58-generic,<br/>4.10.0-14-generic till 4.10.0-42-generic,<br/>4.11.0-13-generic till 4.11.0-14-generic,<br/>4.13.0-16-generic till 4.13.0-45-generic,<br/>4.15.0-13-generic till 4.15.0-128-generic<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1102-azure </br> 4.15.0-132-generic, 4.4.0-200-generic, 4.15.0-1106-azure, 4.15.0-133-generic, 4.4.0-201-generic through 9.40 hot fix patch**|
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic till 4.4.0-194-generic,<br/>4.8.0-34-generic till 4.8.0-58-generic,<br/>4.10.0-14-generic till 4.10.0-42-generic,<br/>4.11.0-13-generic till 4.11.0-14-generic,<br/>4.13.0-16-generic till 4.13.0-45-generic,<br/>4.15.0-13-generic till 4.15.0-123-generic<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1098-azure </br> 4.4.0-197-generic, 4.15.0-126-generic, 4.15.0-128-generic, 4.15.0-1100-azure, 4.15.0-1102-azure through 9.39 hot fix**|
16.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic till 4.4.0-190-generic,<br/>4.8.0-34-generic till 4.8.0-58-generic,<br/>4.10.0-14-generic till 4.10.0-42-generic,<br/>4.11.0-13-generic till 4.11.0-14-generic,<br/>4.13.0-16-generic till 4.13.0-45-generic,<br/>4.15.0-13-generic till 4.15.0-118-generic<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1096-azure </br> 4.4.0-193-generic, 4.15.0-120-generic, 4.15.0-122-generic, 4.15.0-1098-azure through 9.38 hot fix patch**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic till 4.4.0-189-generic,<br/>4.8.0-34-generic till 4.8.0-58-generic,<br/>4.10.0-14-generic till 4.10.0-42-generic,<br/>4.11.0-13-generic till 4.11.0-14-generic,<br/>4.13.0-16-generic till 4.13.0-45-generic,<br/>4.15.0-13-generic till 4.15.0-115-generic<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1093-azure </br> 4.4.0-190-generic, 4.15.0-117-generic, 4.15.0-118-generic, 4.15.0-1095-azure, 4.15.0-1096-azure through 9.37 hot fix**|
|||
18.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic till 4.15.0-135-generic </br> 4.18.0-13-generic till 4.18.0-25-generic </br> 5.0.0-15-generic till 5.0.0-65-generic </br> 5.3.0-19-generic till 5.3.0-70-generic </br> 5.4.0-37-generic till 5.4.0-59-generic</br> 5.4.0-60-generic till 5.4.0-65-generic </br> 4.15.0-1009-azure till 4.15.0-1106-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1036-azure </br> 5.3.0-1007-azure till 5.3.0-1035-azure </br> 5.4.0-1020-azure till 5.4.0-1039-azure </br> 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 5.3.0-72-generic, 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 4.15.0-1108-azure, 4.15.0-1111-azure, 5.4.0-1040-azure, 5.4.0-1041 -azure, 5.4.0-1043-azure, 4.15.0-1109-azure, 4.15.0-1110-azure through 9.41 hot fix patch**|
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic till 4.15.0-129-generic </br> 4.18.0-13-generic till 4.18.0-25-generic </br> 5.0.0-15-generic till 5.0.0-63-generic </br> 5.3.0-19-generic till 5.3.0-69-generic </br> 5.4.0-37-generic till 5.4.0-59-generic</br> 4.15.0-1009-azure till 4.15.0-1103-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1036-azure </br> 5.3.0-1007-azure till 5.3.0-1035-azure </br> 5.4.0-1020-azure till 5.4.0-1035-azure </br> 4.15.0-1104-azure, 4.15.0-130-generic, 4.15.0-132-generic, 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 4.15.0-1106-azure, 4.15.0-134-generic, 4.15.0-135-generic, 5.. 4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic till och med 9.40 snabbkorrigering**|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic till 4.15.0-123-generic </br> 4.18.0-13-generic till 4.18.0-25-generic </br> 5.0.0-15-generic till 5.0.0-63-generic </br> 5.3.0-19-generic till 5.3.0-69-generic </br> 5.4.0-37-generic till 5.4.0-53-generic</br> 4.15.0-1009-azure till 4.15.0-1099-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1036-azure </br> 5.3.0-1007-azure till 5.3.0-1035-azure </br> 5.4.0-1020-azure till 5.4.0-1031-azure </br> 4.15.0-124-generic, 5.4.0-54-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 4.15.0-1100-azure, 4.15.0-126-generic, 4.15.0-128-generic, 5.4.0-58-generic, 4.15.0-1102-azure, 5.4.0-1034-azure through 9.39 hot fix**|
18.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic till 4.15.0-118-generic </br> 4.18.0-13-generic till 4.18.0-25-generic </br> 5.0.0-15-generic till 5.0.0-61-generic </br> 5.3.0-19-generic till 5.3.0-67-generic </br> 5.4.0-37-generic till 5.4.0-48-generic</br> 4.15.0-1009-azure till 4.15.0-1096-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1036-azure </br> 5.3.0-1007-azure till 5.3.0-1035-azure </br> 5.4.0-1020-azure till 5.4.0-1026-azure </br> 4.15.0-121-generic, 4.15.0-122-generic, 5.0.0-62-generic, 5.3.0-68-generic, 5.4.0-51-generic, 5.4.0-52-generic, 4.15.0-1099-azure, 5.4.0-1031-azure till 9.38 hot fix patch**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic till 4.15.0-115-generic </br> 4.18.0-13-generic till 4.18.0-25-generic </br> 5.0.0-15-generic till 5.0.0-60-generic </br> 5.3.0-19-generic till 5.3.0-66-generic </br> 5.4.0-37-generic till 5.4.0-45-generic</br> 4.15.0-1009-azure till 4.15.0-1093-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1036-azure </br> 5.3.0-1007-azure till 5.3.0-1035-azure </br> 5.4.0-1020-azure till 5.4.0-1023-azure</br> 4.15.0-117-generic, 4.15.0-118-generic, 5.0.0-61-generic, 5.3.0-67-generic, 5.4.0-47-generic, 5.4.0-48-generic, 4.15.0-1095-azure, 4.15.0-1096-azure, 5.4.0-1025-azure, 5.4.0-1026-azure through 9.37 hot fix**|
|||
20.04 LTS |[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic till 5.4.0-65 </br> -generic 5.4.0-1010-azure till 5.4.0-1039-azure </br> 5.8.0-29-generic till 5.8.0-43-generic </br> 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 5.8.0-44-generic, 5.8.0-45-generic, 5.8.0-48-generic, 5.4.0-1040-azure, 5.4.0-1041-azure, 5.4.0-1043-azure through 9.41 hot fix**|
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic till 5.4.0-59 </br> -generic 5.4.0-1010-azure till 5.4.0-1035-azure </br> 5.8.0-29-generic till 5.8.0-34-generic </br> 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 5.8.0-36-generic, 5.8.0-38-generic, 5.4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic, 5.8.0-40-generic, 5.8.0-41-generic through 9.40 hot fix patch**|
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic till 5.4.0-53 </br> -generic 5.4.0-1010-azure to 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 hot fix patch**
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic till 5.4.0-53 </br> -generic 5.4.0-1010-azure to 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 hot fix patch**
20.04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic till 5.4.0-48 </br> -generic 5.4.0-1010-azure to 5.4.0-1026-azure </br> 5.4.0-51-generic, 5.4.0-52-generic, 5.8.0-23-generic, 5.8.0-25-generic, 5.4.0-1031-azure through 9.38 hot fix patch**
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic till 5.4.0-45 </br> -generic 5.4.0-1010-azure to 5.4.0-1023-azure </br> 5.4.0-47-generic, 5.4.0-48-generic, 5.4.0-1025-azure, 5.4.0-1026-azure till 9.37 hot fix patch**

**Obs! För att stödja de senaste Linux-kernels inom 15 dagar efter lanseringen Azure Site Recovery en korrigeringsfil för snabbkorrigering ovanpå den senaste versionen av mobilitetsagenten. Den här korrigeringen distribueras mellan två huvudversionsversionerna. Följ stegen i den här artikeln för att uppdatera till den senaste versionen av mobilitetsagenten (inklusive korrigering av [snabbkorrigering).](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Den här korrigeringen distribueras för närvarande för mobilitetsagenter som används i dr-scenariot i Azure till Azure.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debian-kernelversioner som stöds för virtuella Azure-datorer

**Frisläpp** | **tjänsten Mobility version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) , [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-amd64 till 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 till 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 till 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 till 4.19.0-0.bpo.14-cloud-amd64 </br> 4.9.0-15-amd64, 4.19.0-0.bpo.16-amd64, 4.19.0-0.bpo.16-cloud-amd64 till och med 9.41 snabbkorrigering**
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 till 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 till 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 till 4.19.0-0.bpo.13-cloud-amd64 
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 till 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 till 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 till 4.19.0-0.bpo.12-cloud-amd64 </br> 4.19.0-0.bpo.13-amd64, 4.19.0-0.bpo.13-cloud-amd64 till och med 9.39 snabbkorrigering**</br> 
Debian 9.1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 till 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 till 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 till 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0-0.bpo.12-amd64, 4.19.0-0.bpo.12-cloud-amd64 till 9.38 hot fix patch**
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 till 4.9.0-13-amd64, 4.19.0-0.bpo.6-amd64 till 4.19.0-0.bpo.10-amd64, 4.19.0-0.bpo.6-cloud-amd64 till 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64 till 4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64 till 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64 </br> 4.19.0-10-cloud-amd64, 4.19.0-16-amd64, 4.19.0-16-cloud-amd64 till och med 9.41 snabbkorrigering**
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 till 4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64 till 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

**Obs! För att stödja de senaste Linux-kernels inom 15 dagar efter lanseringen Azure Site Recovery en korrigeringskorrigering för snabbkorrigering ovanpå den senaste versionen av mobilitetsagenten. Den här korrigeringen distribueras mellan två huvudversionsversionerna. Följ stegen i den här artikeln för att uppdatera till den senaste versionen av mobilitetsagenten (inklusive korrigering av [snabbkorrigering).](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Den här korrigeringen distribueras för närvarande för mobilitetsagenter som används i dr-scenariot i Azure till Azure.

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Stöd SUSE Linux Enterprise Server 12 kernelversioner för virtuella Azure-datorer

**Frisläpp** | **tjänsten Mobility version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Alla [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.43-azure </br> 4.12.14-16.7-azure till 4.12.14-16.44-azure </br> 4.12.14-16.47-azure till och med 9.41 snabbkorrigering**|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Alla [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.43-azure </br> 4.12.14-16.7-azure till 4.12.14-16.38-azure </br> 4.12.14-16.41-azure till och med 9.40 snabbkorrigering**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.39](https://support.microsoft.com/help/4597409/) | Alla [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.43-azure </br> 4.12.14-16.7-azure till 4.12.14-16.34-azure </br> 4.12.14-16.38-azure till och med 9.39 snabbkorrigering**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Alla [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.43-azure </br> 4.12.14-16.7-azure till 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.37](https://support.microsoft.com/help/4582666/) | Alla [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kärnor](https://www.suse.com/support/kb/doc/?id=000019587) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.43-azure </br> 4.12.14-16.7-azure till 4.12.14-16.22-azure </br> 4.12.14-16.25-azure, 4.12.14-16.28-azure till och med 9.37 snabbkorrigering**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Stöd SUSE Linux Enterprise Server 15 kernelversioner för virtuella Azure-datorer

**Frisläpp** | **tjänsten Mobility version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Som standard stöds alla [SUSE 15-, SP1- och SP2-kärnor.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.12.14-5.5-azure till 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure till 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure till 5.3.18-18.35-azure </br> 5.3.18-18.38-azure till och med 9.41 snabbkorrigering**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Som standard stöds alla [SUSE 15-, SP1- och SP2-kärnor.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.12.14-5.5-azure till 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure till 4.12.14-8.58-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure till 5.3.18-18.29-azure </br> 5.3.18-18.32-azure, 4.12.14-8.58-azure till och med 9.40 snabbkorrigering**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | Som standard stöds alla [SUSE 15-, SP1- och SP2-kärnor.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.12.14-5.5-azure till 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure till 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure till 5.3.18-18.21-azure </br> 4.12.14-8.52-azure, 5.3.18-18.24-azure, 4.12.14-8.55-azure, 5.3.18-18.29-azure till och med 9.39 hot fix patch**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Som standard stöds alla [SUSE 15-, SP1- och SP2-kärnor.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.12.14-5.5-azure till 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure till 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure till 5.3.18-18.18-azure </br> 4.12.14-8.47-azure, 5.3.18-18.21-azure till och med 9.38 snabbkorrigering**
SUSE Linux Enterprise Server 15 och 15 SP1 | [9.37](https://support.microsoft.com/help/4582666/)  | Som standard stöds alla [SUSE 15-, SP1- och SP2-kärnor.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.12.14-5.5-azure till 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure till 4.12.14-8.38-azure </br> 4.12.14-8.41-azure, 4.12.14-8.44-azure till och med 9.37 snabbkorrigering**

**Obs! Om du vill ha stöd för de senaste Linux-kernels inom 15 dagar efter lanseringen Azure Site Recovery en korrigeringskorrigering för snabbkorrigering ovanpå den senaste versionen av mobilitetsagenten. Den här korrigeringen distribueras mellan två huvudversionsversionerna. Följ stegen i den här artikeln för att uppdatera till den senaste versionen av mobilitetsagenten (inklusive korrigering av [snabbkorrigering).](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Den här korrigeringen distribueras för närvarande för mobilitetsagenter som används i dr-scenariot i Azure till Azure.

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikerade datorer – Linux-filsystem/gästlagring

* Filsystem: ext3, ext4, XFS, BTRFS
* Volymhanterare: LVM2

> [!NOTE]
> Multipath-programvara stöds inte.


## <a name="replicated-machines---compute-settings"></a>Replikerade datorer – beräkningsinställningar

**Inställning** | **Support** | **Information**
--- | --- | ---
Storlek | Valfri storlek på virtuella Azure-datorer med minst 2 CPU-kärnor och 1 GB RAM-minne | Kontrollera [storleken på den virtuella Azure-datorn.](../virtual-machines/sizes.md)
Tillgänglighetsuppsättningar | Stöds | Om du aktiverar replikering för en virtuell Azure-dator med standardalternativen skapas en tillgänglighetsuppsättning automatiskt baserat på inställningarna för källregionen. Du kan ändra de här inställningarna.
Tillgänglighetszoner | Stöds |
Hybrid Use Benefit (HUB) | Stöds | Om den virtuella källdatorn har en aktiverad HUB-licens använder ett redundanstest eller redundans redundans även den virtuella datorn HUB-licensen.
Skalningsuppsättningar för virtuella datorer | Stöds inte |
Azure-galleriavbildningar – Microsoft publicerade | Stöds | Stöds om den virtuella datorn körs på ett operativsystem som stöds.
Azure-galleriavbildningar – publicering från tredje part | Stöds | Stöds om den virtuella datorn körs på ett operativsystem som stöds.
Anpassade avbildningar – publicering från tredje part | Stöds | Stöds om den virtuella datorn körs på ett operativsystem som stöds.
Virtuella datorer som migrerats med Site Recovery | Stöds | Om en virtuell VMware-dator eller en fysisk dator migrerades till Azure med hjälp av Site Recovery måste du avinstallera den äldre versionen av tjänsten Mobility som körs på datorn och starta om datorn innan du replikerar den till en annan Azure-region.
Azure RBAC-principer | Stöds inte | Azures principer för rollbaserad åtkomstkontroll (Azure RBAC) på virtuella datorer replikeras inte till den virtuella datorn för redundans i målregionen.
Tillägg | Stöds inte | Tillägg replikeras inte till den virtuella redundansdatorn i målregionen. Den måste installeras manuellt efter redundans.
Närhetsplaceringsgrupper | Stöds | Virtuella datorer som finns i en närhetsplaceringsgrupp kan skyddas med hjälp av Site Recovery.
Taggar  | Stöds | Användargenererade taggar som tillämpas på virtuella källdatorer förs över till virtuella måldatorer efter redundanstest eller redundans. Taggar på de virtuella datorer replikeras en gång var 24:e timme så länge de virtuella datorer finns i målregionen.


## <a name="replicated-machines---disk-actions"></a>Replikerade datorer – diskåtgärder

**Åtgärd** | **Information**
-- | ---
Ändra storlek på disk på replikerad virtuell dator | Stöds på den virtuella källdatorn före redundans. Du behöver inte inaktivera/återaktivera replikering.<br/><br/> Om du ändrar den virtuella källdatorn efter redundansväxlingen avbildas inte ändringarna.<br/><br/> Om du ändrar diskstorleken på den virtuella Azure-datorn efter redundansväxlingen avbildas inte ändringar av Site Recovery och återställning efter fel kommer att ske till den ursprungliga VM-storleken.
Lägga till en disk till en replikerad virtuell dator | Stöds
Offlineändringar av skyddade diskar | Frånkoppling av diskar och offlineändringar kräver att en fullständig omsynkronisering utlöses.

## <a name="replicated-machines---storage"></a>Replikerade datorer – lagring

I den här tabellen sammanfattas stödet för OS-disken för virtuella Azure-datorer, datadisken och den tillfälliga disken.

- Det är viktigt att observera vm-diskbegränsningarna och målen för [hanterade diskar för](../virtual-machines/disks-scalability-targets.md) att undvika prestandaproblem.
- Om du distribuerar med standardinställningarna Site Recovery automatiskt diskar och lagringskonton baserat på källinställningarna.
- Om du anpassar följer du riktlinjerna.

**Komponent** | **Support** | **Information**
--- | --- | ---
Maximal storlek för OS-disk | 2 048 GB | [Läs mer](../virtual-machines/managed-disks-overview.md) om virtuella datordiskar.
Tillfällig disk | Stöds inte | Den tillfälliga disken undantas alltid från replikering.<br/><br/> Lagra inte beständiga data på den tillfälliga disken. [Läs mer](../virtual-machines/managed-disks-overview.md).
Maximal storlek för datadisk | 32 TB för hanterade diskar<br></br>4 TB för ohanterade diskar|
Minsta storlek för datadisk | Ingen begränsning för ohanterade diskar. 2 GB för hanterade diskar |
Maximalt antal datadiskar | Upp till 64, i enlighet med stöd för en specifik storlek för virtuella Azure-datorer | [Läs mer](../virtual-machines/sizes.md) om VM-storlekar.
Ändringsfrekvens för datadisk | Högst 20 MBps per disk för Premium Storage. Högst 2 MBps per disk för Standard Storage. | Om den genomsnittliga dataändringstakten på disken kontinuerligt är högre än maxvärdet kommer replikeringen inte ikapp.<br/><br/>  Men om det högsta värdet överskrids sporadiskt kan replikeringen komma ikapp, men du kan se något fördröjda återställningspunkter.
Datadisk – standardlagringskonto | Stöds |
Datadisk – Premium Storage-konto | Stöds | Om en virtuell dator har diskar utspridda över premium- och standardlagringskonton kan du välja ett annat mållagringskonto för varje disk för att säkerställa att du har samma lagringskonfiguration i målregionen.
Hanterad disk – standard | Stöds i Azure-regioner där Azure Site Recovery stöds. |
Hanterad disk – Premium | Stöds i Azure-regioner där Azure Site Recovery stöds. |
Standard SSD | Stöds |
Redundans | LRS och GRS stöds.<br/><br/> ZRS stöds inte.
Kall och het lagring | Stöds inte | Virtuella datordiskar stöds inte på kall och het lagring
Lagringsutrymmen | Stöds |
NVMe-lagringsgränssnitt | Stöds inte
Kryptering i vila (SSE) | Stöds | SSE är standardinställningen för lagringskonton.
Kryptering i vila (CMK) | Stöds | Både Programvara och HSM-nycklar stöds för hanterade diskar
Dubbel kryptering i vila | Stöds | Läs mer om regioner som stöds för [Windows](../virtual-machines/disk-encryption.md) och [Linux](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) för Windows OS | Stöds för virtuella datorer med hanterade diskar. | Virtuella datorer som använder ohanterade diskar stöds inte. <br/><br/> HSM-skyddade nycklar stöds inte. <br/><br/> Kryptering av enskilda volymer på en enskild disk stöds inte. |
Azure Disk Encryption (ADE) för Linux OS | Stöds för virtuella datorer med hanterade diskar. | Virtuella datorer som använder ohanterade diskar stöds inte. <br/><br/> HSM-skyddade nycklar stöds inte. <br/><br/> Kryptering av enskilda volymer på en enskild disk stöds inte. <br><br> Känt problem med att aktivera replikering. [Läs mer.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS-nyckelrotation | Stöds inte | Om SAS-nyckeln för lagringskonton roteras måste kunden inaktivera och återaktivera replikeringen. |
Cachelagring av värd | Stöds
Hot add (Lägg till hot)    | Stöds | Aktivering av replikering för en datadisk som du lägger till i en replikerad virtuell Azure-dator stöds för virtuella datorer som använder hanterade diskar. <br/><br/> Endast en disk kan läggas till i en virtuell Azure-dator i taget. Parallell addition av flera diskar stöds inte. |
Ta bort disk vid snabb borttagning    | Stöds inte | Om du tar bort datadisken på den virtuella datorn måste du inaktivera replikering och aktivera replikering igen för den virtuella datorn.
Uteslut disk | Stöd. Du måste använda [PowerShell för](azure-to-azure-exclude-disks.md) att konfigurera. |    Temporära diskar undantas som standard.
Lagringsutrymmen direkt  | Stöds för krasch konsekventa återställningspunkter. Program konsekventa återställningspunkter stöds inte. |
Skalningsfilserver  | Stöds för krasch konsekventa återställningspunkter. Program konsekventa återställningspunkter stöds inte. |
Drbd | Diskar som ingår i en DRBD-konfiguration stöds inte. |
LRS | Stöds |
GRS | Stöds |
RA-GRS | Stöds |
ZRS | Stöds inte |
Cool och Hot Storage | Stöds inte | Virtuella datordiskar stöds inte på kall och het lagring
Azure Storage brandväggar för virtuella nätverk  | Stöds | Om du vill begränsa åtkomsten till virtuella nätverk till lagringskonton [aktiverar du Tillåt betrodda Microsoft-tjänster](../storage/common/storage-network-security.md#exceptions).
V2-lagringskonton för generell användning (både på den heta och den kalla nivån) | Stöds | Transaktionskostnaderna ökar avsevärt jämfört med V1-lagringskonton för generell användning
Generation 2 (UEFI-start) | Stöds
NVMe-diskar | Stöds inte
Delade diskar i Azure | Stöds inte
Alternativet Säker överföring | Stöds
Skrivningsacceleratoraktiverade diskar | Stöds inte
Taggar  | Stöds | Användargenererade taggar replikeras var 24:e timme.

>[!IMPORTANT]
> Undvik prestandaproblem genom att se till att du följer VM-diskens skalbarhets- och prestandamål [för hanterade diskar.](../virtual-machines/disks-scalability-targets.md) Om du använder standardinställningar Site Recovery de diskar och lagringskonton som krävs, baserat på källkonfigurationen. Om du anpassar och väljer egna inställningar följer du diskskalbarhets- och prestandamålen för dina virtuella käll datorer.

## <a name="limits-and-data-change-rates"></a>Gränser och dataändringshastigheter

I följande tabell sammanfattas Site Recovery gränser.

- Dessa gränser baseras på våra tester, men omfattar naturligtvis inte alla möjliga kombinationer av program-I/O.
- De faktiska resultaten kan variera beroende på din app-I/O-blandning.
- Det finns två begränsningar att tänka på, dataomsättning per disk och dataomsättning per virtuell dator.
- Den aktuella gränsen för dataomsättning per virtuell dator är 54 MB/s, oavsett storlek.

**Lagringsmål** | **Genomsnittlig källdisk-I/O** |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total dataomsättning för källdisk per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |    336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1 684 GB per disk

## <a name="replicated-machines---networking"></a>Replikerade datorer – nätverk
**Inställning** | **Support** | **Information**
--- | --- | ---
NIC | Maximalt antal som stöds för en specifik storlek för en virtuell Azure-dator | Nätverkskort skapas när den virtuella datorn skapas under redundans.<br/><br/> Antalet nätverkskort på den virtuella datorn för växling vid fel beror på antalet nätverkskort på den virtuella källdatorn när replikeringen aktiverades. Om du lägger till eller tar bort ett nätverkskort efter aktivering av replikering påverkar det inte antalet nätverkskort på den replikerade virtuella datorn efter redundans. <br/><br/> Ordningen på nätverkskort efter redundans är inte garanterad att vara samma som den ursprungliga ordningen. <br/><br/> Du kan byta namn på nätverkskort i målregionen baserat på organisationens namngivningskonventioner. NIC-namnbyte stöds med Hjälp av PowerShell.
Internet-lastbalanserare | Stöds inte | Du kan konfigurera offentliga/Internet-lastbalanserare i den primära regionen. Offentliga/Internet-lastbalanserare stöds dock inte av Azure Site Recovery i DR-regionen.
Intern lastbalanserare | Stöds | Associera den förkonfigurerade lastbalanseraren med ett Azure Automation skript i en återställningsplan.
Offentlig IP-adress | Stöds | Associera en befintlig offentlig IP-adress med nätverkskortet. Du kan också skapa en offentlig IP-adress och associera den med nätverkskortet med hjälp Azure Automation skript i en återställningsplan.
NSG på nätverkskort | Stöds | Associera NSG med nätverkskortet med hjälp av ett Azure Automation skript i en återställningsplan.
NSG på undernät | Stöds | Associera NSG med undernätet med hjälp av ett Azure Automation skript i en återställningsplan.
Reserverad (statisk) IP-adress | Stöds | Om nätverkskortet på den virtuella källdatorn har en statisk IP-adress och målundernätet har samma tillgängliga IP-adress, tilldelas det till den virtuella datorn som inte har det.<br/><br/> Om målundernätet inte har samma tillgängliga IP-adress är en av de tillgängliga IP-adresserna i undernätet reserverad för den virtuella datorn.<br/><br/> Du kan också ange en fast IP-adress och ett undernät i **Replikerade**  >  **objekt Inställningar** För  >  **beräknings- och**  >  **nätverksnätverksgränssnitt.**
Dynamisk IP-adress | Stöds | Om nätverkskortet på källan har dynamisk IP-adressering är nätverkskortet på den virtuella datorn som är överkänd också dynamiskt som standard.<br/><br/> Du kan ändra detta till en fast IP-adress om det behövs.
Flera IP-adresser | Stöds inte | När du redundanser en virtuell dator som har ett nätverkskort med flera IP-adresser behålls bara den primära IP-adressen för nätverkskortet i källregionen. Om du vill tilldela flera IP-adresser [](recovery-plan-overview.md) kan du lägga till virtuella datorer i en återställningsplan och bifoga ett skript för att tilldela ytterligare IP-adresser till planen, eller så kan du göra ändringen manuellt eller med ett skript efter en redundansväxling.
Traffic Manager     | Stöds | Du kan förkonfigurera Traffic Manager så att trafiken dirigeras till slutpunkten i källregionen regelbundet och till slutpunkten i målregionen vid redundans.
Azure DNS | Stöds |
Anpassad DNS    | Stöds |
Oauthenticerad proxy | Stöds | [Läs mer](./azure-to-azure-about-networking.md)
Autentiserad proxy | Stöds inte | Om den virtuella datorn använder en autentiserad proxy för utgående anslutning kan den inte replikeras med hjälp av Azure Site Recovery.
PLATS-till-plats-anslutning via VPN till lokal plats<br/><br/>(med eller utan ExpressRoute)| Stöds | Se till att UDR:erna och NSG:erna är konfigurerade på ett sådant sätt Site Recovery att trafiken inte dirigeras till den lokala platsen. [Läs mer](./azure-to-azure-about-networking.md)
VNET-till-VNET-anslutning    | Stöds | [Läs mer](./azure-to-azure-about-networking.md)
Tjänstslutpunkter för virtuellt nätverk | Stöds | Om du begränsar åtkomsten till lagringskonton för det virtuella nätverket ska du se till Microsoft-tjänster betrodda nätverksåtkomsten tillåts åtkomst till lagringskontot.
Snabbare nätverk | Stöds | Accelererat nätverk måste vara aktiverat på den virtuella källdatorn. [Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto Network Appliance | Stöds inte | Med apparater från tredje part finns det ofta begränsningar av leverantören i den virtuella datorn. Azure Site Recovery agent, tillägg och utgående anslutning måste vara tillgängliga. Men installationen tillåter inte att någon utgående aktivitet konfigureras i den virtuella datorn.
IPv6  | Stöds inte | Blandade konfigurationer som omfattar både IPv4 och IPv6 stöds inte heller. Frigör undernätet för IPv6-intervallet innan du Site Recovery åtgärden.
Privat länkåtkomst till Site Recovery tjänsten | Stöds | [Läs mer](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Taggar  | Stöds | Användargenererade taggar på nätverkskort replikeras var 24:e timme.



## <a name="next-steps"></a>Nästa steg

- Läs [nätverksvägledning](./azure-to-azure-about-networking.md)  för replikering av virtuella Azure-datorer.
- Distribuera haveriberedskap genom [att replikera virtuella Azure-datorer.](./azure-to-azure-quickstart.md)
