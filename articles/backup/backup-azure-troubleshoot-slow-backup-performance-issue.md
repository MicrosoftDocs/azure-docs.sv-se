---
title: Felsöka långsam säkerhets kopiering av filer och mappar
description: Innehåller fel söknings vägledning som hjälper dig att diagnostisera orsaken till Azure Backup prestanda problem
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: b3f2ac343ef4a703f347ec8a57f242a636bb32d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88824023"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup

Den här artikeln innehåller fel söknings vägledning som hjälper dig att diagnostisera orsaken till långsamma säkerhets kopierings prestanda för filer och mappar när du använder Azure Backup. När du använder Azure Backup Agent för att säkerhetskopiera filer kan säkerhets kopieringen ta längre tid än förväntat. Den här fördröjningen kan ha orsakats av ett eller flera av följande:

* [Det finns prestanda Flask halsar på datorn som säkerhets kopie ras.](#cause1)
* [En annan process eller ett antivirus program stör Azure Backup processen.](#cause2)
* [Säkerhets kopierings agenten körs på en virtuell Azure-dator (VM).](#cause3)  
* [Du säkerhetskopierar ett stort antal filer.](#cause4)

Innan du börjar felsöka problem rekommenderar vi att du hämtar och installerar den [senaste Azure Backup agenten](https://aka.ms/azurebackup_agent). Vi gör frekventa uppdateringar av säkerhets kopierings agenten för att åtgärda olika problem, lägga till funktioner och förbättra prestandan.

Vi rekommenderar också starkt att du läser igenom [vanliga frågor om Azure Backup-tjänsten](backup-azure-backup-faq.md) för att se till att du inte har några vanliga konfigurations problem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Orsak: säkerhets kopierings jobb körs i ej optimerat läge

* MARS-agenten kan köra säkerhets kopierings jobbet i **optimerat läge** med hjälp av USN (Update Sequence Number) ändra journal eller ej **optimerat läge** genom att söka efter ändringar i kataloger eller filer genom att genomsöka hela volymen.
* Icke-optimerat läge är långsamt eftersom agenten måste genomsöka varje fil och varje fil på volymen och jämföra med metadata för att fastställa de ändrade filerna.
* Du kan kontrol lera detta genom att öppna **jobb information** från mars agent-konsolen och kontrol lera statusen för att se om det står att **överföring av data (ej optimerad, kan ta längre tid)** enligt nedan:

    ![Körs i ej optimerat läge](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Följande villkor kan orsaka att säkerhets kopierings jobbet körs i ej optimerat läge:
  * Den första säkerhets kopieringen (kallas även inledande replikering) körs alltid i ej optimerat läge
  * Om det tidigare säkerhets kopierings jobbet Miss lyckas körs nästa schemalagda säkerhets kopierings jobb som inte optimerat.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: prestanda Flask halsar på datorn

Flask halsar på datorn som säkerhets kopie ras kan orsaka fördröjningar. Till exempel kan datorns möjlighet att läsa eller skriva till disk, eller tillgänglig bandbredd för att skicka data via nätverket, orsaka Flask halsar.

Windows innehåller ett inbyggt verktyg som kallas [prestanda övervakaren](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (PerfMon) för att identifiera Flask halsar.

Här följer några prestanda räknare och intervall som kan vara till hjälp vid diagnostisering av flask halsar för optimala säkerhets kopieringar.

| Räknare | Status |
| --- | --- |
| Logisk disk (fysisk disk)--% inaktiv |<li> 100% inaktiv till 50% Idle = felfri</br><li> 49% inaktiv till 20% inaktiv = varning eller Övervakare</br><li> 19% inaktiv till 0% inaktiv = kritisk eller ur specifikation |
| Logisk disk (fysisk disk)--% medel s disk läsning eller skrivning |<li> 0,001 MS till 0,015 MS = felfri</br><li> 0,015 MS till 0,025 MS = varning eller Övervakare</br><li> 0,026 MS eller längre = kritisk eller från specifikation |
| Logisk disk (fysisk disk)--Aktuell diskkölängd (för alla instanser) |80 förfrågningar i mer än 6 minuter |
| Minne – icke växlings Bart system minne-byte |<li> Mindre än 60% av poolen förbrukade = felfri<br><li> 61% till 80% av poolen förbrukat = varning eller övervaka</br><li> Mer än 80% pool förbrukad = kritisk eller ur specifikation |
| Minne--växlings Bart system minne-byte |<li> Mindre än 60% av poolen förbrukade = felfri</br><li> 61% till 80% av poolen förbrukat = varning eller övervaka</br><li> Mer än 80% pool förbrukad = kritisk eller ur specifikation |
| Minne – tillgängliga megabyte |<li> 50% ledigt minne tillgängligt eller mer = felfri</br><li> 25% ledigt minne tillgängligt = övervaka</br><li>10% ledigt minne tillgängligt = varning</br><li> Mindre än 100 MB eller 5% ledigt minne tillgängligt = kritisk eller ur specifikationen |
| Processor-- \% processor tid (alla instanser) |<li> Mindre än 60% förbrukat = felfri</br><li> 61% till 90% förbrukat = övervaka eller varning</br><li> 91% till 100% förbrukat = kritisk |

> [!NOTE]
> Om du fastställer att infrastrukturen är orsaken rekommenderar vi att du defragmenterar diskarna regelbundet för bättre prestanda.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: en annan process eller ett antivirus program som stör Azure Backup

Vi har sett flera instanser där andra processer i Windows-systemet har negativt prestanda för Azure Backup Agent processen. Om du till exempel använder både Azure Backup-agenten och ett annat program för att säkerhetskopiera data, eller om antivirus programmet körs och har låst filer som ska säkerhets kopie ras, kan flera lås på filer orsaka konkurrens. I den här situationen kan säkerhets kopieringen Miss Miss kan, eller så kan det ta längre tid än förväntat.

Den bästa rekommendationen i det här scenariot är att stänga av det andra säkerhets kopierings programmet för att se om säkerhets kopierings tiden för Azure Backup agenten ändras. Att se till att flera säkerhets kopierings jobb inte körs samtidigt är tillräckligt för att förhindra att de påverkar varandra.

För antivirus program rekommenderar vi att du undantar följande filer och platser:

* C:\Program\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program\Microsoft Azure Recovery Services agent \ mappar
* Arbets plats (om du inte använder standard platsen)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: säkerhets kopierings agenten körs på en virtuell Azure-dator

Om du kör säkerhets kopierings agenten på en virtuell dator blir prestandan långsammare än när du kör den på en fysisk dator. Detta förväntas på grund av IOPS-begränsningar.  Du kan dock optimera prestanda genom att växla data enheter som säkerhets kopie ras till Azure Premium Storage. Vi arbetar med att åtgärda det här problemet och korrigeringen är tillgänglig i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: säkerhetskopiera ett stort antal filer

Det tar längre tid att flytta en stor mängd data än att flytta en mindre data volym. I vissa fall är säkerhets kopierings tiden relaterad till inte bara storleken på data, utan också antalet filer eller mappar. Detta gäller särskilt när miljon tals små filer (några byte till några kilobyte) säkerhets kopie ras.

Det här problemet beror på att när du säkerhetskopierar data och flyttar dem till Azure, katalogiserar Azure samtidigt dina filer. I vissa sällsynta fall kan katalog åtgärden ta längre tid än förväntat.

Följande indikatorer kan hjälpa dig att förstå Flask halsen och därefter arbeta på nästa steg:

* **UI visar förloppet för data överföringen**. Data överförs fortfarande. Nätverks bandbredden eller data storleken kan orsaka fördröjningar.
* **UI visar inte förloppet för data överföringen**. Öppna loggfilerna som finns i C:\Program\Microsoft Azure Recovery Services Agent\Temp och kontrol lera sedan posten FileProvider:: EndData i loggarna. Den här posten indikerar att data överföringen är slutförd och att katalog åtgärden sker. Avbryt inte säkerhets kopierings jobben. Vänta i stället lite längre tills katalog åtgärden har slutförts. Kontakta [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support)om problemet kvarstår.

Om du försöker säkerhetskopiera stora diskar rekommenderar vi att du använder [Azure Data Box](./offline-backup-azure-data-box.md) för den första säkerhets kopieringen (inledande replikering).  Om du inte kan använda Data Box-enhet kan eventuella tillfälliga nätverks problem i din miljö under långa data överföringar över nätverket orsaka säkerhets kopierings fel.  Om du vill skydda dig mot dessa fel kan du lägga till några mappar i den första säkerhets kopian och fortsätta att lägga till fler mappar stegvis tills alla mappar har säkerhetskopierats till Azure.  Efterföljande stegvisa säkerhets kopieringar är relativt snabbare.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor om säkerhets kopiering av filer och mappar](backup-azure-file-folder-backup-faq.md)
