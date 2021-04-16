---
title: Felsöka långsam säkerhetskopiering av filer och mappar
description: Innehåller felsökningsvägledning som hjälper dig att diagnostisera orsaken Azure Backup prestandaproblem
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 791f0edf5f50d27147e402f09e7a3e4c2ea7ca43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518532"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup

Den här artikeln innehåller felsökningsvägledning som hjälper dig att diagnostisera orsaken till långsam säkerhetskopieringsprestanda för filer och mappar när du använder Azure Backup. När du använder Azure Backup för att säkerhetskopiera filer kan säkerhetskopieringen ta längre tid än förväntat. Den här fördröjningen kan orsakas av en eller flera av följande:

* [Det finns prestandaflaskhalsar på datorn som säkerhetskopieras.](#cause1)
* [En annan process eller ett antivirusprogram stör Azure Backup processen.](#cause2)
* [Backup-agenten körs på en virtuell Azure-dator (VM).](#cause3)  
* [Du backar upp ett stort antal (miljontals) filer.](#cause4)

Innan du börjar felsöka problem rekommenderar vi att du laddar ned och installerar den [senaste Azure Backup agenten](https://aka.ms/azurebackup_agent). Vi uppdaterar Backup-agenten regelbundet för att åtgärda olika problem, lägga till funktioner och förbättra prestanda.

Vi rekommenderar också starkt att du går [Azure Backup vanliga](backup-azure-backup-faq.yml) frågor och svar om tjänsten för att se till att du inte har några vanliga konfigurationsproblem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Orsak: Säkerhetskopieringsjobb som körs i icke-optimerat läge

* MARS-agenten kan köra säkerhetskopieringsjobbet i **optimerat** läge med hjälp av ÄNDRINGsjournalen för USN (uppdateringssekvensnummer) eller **icke-optimerat** läge genom att söka efter ändringar i kataloger eller filer genom att söka igenom hela volymen.
* Icke-optimerat läge är långsamt eftersom agenten måste genomsöka varje fil på volymen och jämföra med metadata för att fastställa de ändrade filerna.
* Kontrollera detta genom att öppna **Jobbinformation** från MARS-agentkonsolen och kontrollera statusen för att se om det står Överföring **av data (icke-optimerad,** kan ta längre tid) enligt nedan:

    ![Köra i icke-optimerat läge](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Följande villkor kan göra att säkerhetskopieringsjobbet körs i icke-optimerat läge:
  * Första säkerhetskopieringen (kallas även inledande replikering) körs alltid i icke-optimerat läge
  * Om det tidigare säkerhetskopieringsjobbet misslyckas körs nästa schemalagda säkerhetskopieringsjobb som icke-optimerat.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: Prestandaflaskhalsar på datorn

Flaskhalsar på datorn som säkerhetskopieras kan orsaka fördröjningar. Till exempel kan datorns möjlighet att läsa eller skriva till disk, eller tillgänglig bandbredd för att skicka data via nätverket, orsaka flaskhalsar.

Windows tillhandahåller ett inbyggt verktyg som heter [Prestandaövervakaren](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) för att identifiera dessa flaskhalsar.

Här är några prestandaräknare och intervall som kan vara till hjälp vid diagnostisering av flaskhalsar för optimala säkerhetskopieringar.

| Räknare | Status |
| --- | --- |
| Logisk disk (fysisk disk)--%inaktiv |<li> 100 % inaktivitet till 50 % inaktivitet = felfritt</br><li> 49 % inaktiv till 20 % inaktivitet = varning eller övervakare</br><li> 19 % inaktivitet till 0 % inaktivitet = kritisk eller utanför specifikation |
| Logisk disk (fysisk disk)--%genomsnittlig disk s läs- eller skrivskydd |<li> 0,001 ms till 0,015 ms = Felfritt</br><li> 0,015 ms till 0,025 ms = Varning eller Övervakare</br><li> 0,026 ms eller längre = Kritisk eller Utanför specifikation |
| Logisk disk (fysisk disk) – Aktuell diskkölängd (för alla instanser) |80 begäranden i mer än 6 minuter |
| Minnespool – icke växlingsbar byte |<li> Mindre än 60 % av poolen som förbrukas = Felfritt<br><li> 61 % till 80 % av poolen som förbrukas = Varning eller Övervakare</br><li> Större än 80 % förbrukad pool = kritisk eller utanför specifikation |
| Växlingsbyte för minnespool |<li> Mindre än 60 % förbrukad pool = Felfri</br><li> 61 % till 80 % av poolen förbrukade = Varning eller Övervakare</br><li> Större än 80 % förbrukad pool = kritisk eller utanför specifikation |
| Minne – tillgängliga megabyte |<li> 50 % ledigt minne tillgängligt eller mer = Felfritt</br><li> 25 % ledigt minne tillgängligt = Övervaka</br><li>10 % ledigt minne tillgängligt = Varning</br><li> Mindre än 100 MB eller 5 % ledigt minne tillgängligt = kritiskt eller utanför specifikation |
| Processor – \% Processortid (alla instanser) |<li> Mindre än 60 % förbrukat = Felfritt</br><li> 61 % till 90 % förbrukat = Övervaka eller Varning</br><li> 91 % till 100 % förbrukat = kritiskt |

> [!NOTE]
> Om du fastställer att infrastrukturen är den som ligger till fel, rekommenderar vi att du defragmentar diskarna regelbundet för att få bättre prestanda.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: En annan process eller ett antivirusprogram stör Azure Backup

Vi har sett flera instanser där andra processer i Windows-systemet har påverkat prestandan för Azure Backup agentprocessen negativt. Om du till exempel använder både Azure Backup-agenten och ett annat program för att säkerhetskopiera data, eller om antivirusprogram körs och har ett lås på filer som ska säkerhetskopieras, kan de flera låsen på filer orsaka spridning. I så fall kan säkerhetskopieringen misslyckas eller så kan jobbet ta längre tid än förväntat.

Den bästa rekommendationen i det här scenariot är att stänga av det andra säkerhetskopieringsprogrammet för att se om säkerhetskopieringstiden för Azure Backup agenten ändras. Vanligtvis räcker det att se till att flera säkerhetskopieringsjobb inte körs samtidigt för att förhindra att de påverkar varandra.

För antivirusprogram rekommenderar vi att du undantar följande filer och platser:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program Files\Microsoft Azure Recovery Services Agent\folders
* Scratch-plats (om du inte använder standardplatsen)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: Säkerhetskopieringsagent som körs på en virtuell Azure-dator

Om du kör Säkerhetskopieringsagenten på en virtuell dator går prestandan långsammare än när du kör den på en fysisk dator. Detta förväntas på grund av IOPS-begränsningar.  Du kan dock optimera prestandan genom att växla de dataenheter som säkerhetskopieras till Azure Premium Storage. Vi arbetar på att åtgärda det här problemet och korrigeringen blir tillgänglig i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: Äldsta till ett stort antal (miljoner) filer

Det tar längre tid att flytta en stor mängd data än att flytta en mindre datavolym. I vissa fall är säkerhetskopieringstiden relaterad till inte bara storleken på data, utan även antalet filer eller mappar. Detta gäller särskilt när miljontals små filer (några byte till några kilobyte) säkerhetskopieras.

Det här beteendet beror på att Azure katalogiserar dina filer samtidigt när du backar upp data och flyttar dem till Azure. I vissa sällsynta fall kan katalogåtgärden ta längre tid än förväntat.

Följande indikatorer kan hjälpa dig att förstå flaskhalsen och därmed arbeta med nästa steg:

* **Användargränssnittet visar förloppet för dataöverföringen**. Data överförs fortfarande. Nätverksbandbredden eller storleken på data kan orsaka fördröjningar.
* **Användargränssnittet visar inte förloppet för dataöverföringen**. Öppna loggarna som finns i C:\Program Files\Microsoft Azure Recovery Services Agent\Temp och sök efter posten FileProvider::EndData i loggarna. Den här posten innebär att dataöverföringen har slutförts och att katalogåtgärden utförs. Avbryt inte säkerhetskopieringsjobben. Vänta i stället lite längre tills katalogåtgärden har avslutats. Kontakta [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support)om problemet kvarstår.

Om du försöker säkerhetskopiera stora diskar rekommenderar vi att du använder Azure Data Box [för](./offline-backup-azure-data-box.md) den första säkerhetskopieringen (inledande replikering).  Om du inte kan använda Data Box-enhet kan eventuella tillfälliga nätverksproblem som inträffar i din miljö under långa dataöverföringar över nätverket orsaka säkerhetskopieringsfel.  För att skydda dig mot dessa fel kan du lägga till några mappar i din första säkerhetskopia och fortsätta att lägga till fler mappar stegvis tills alla mappar har säkerhetskopierats till Azure.  Efterföljande inkrementella säkerhetskopieringar går relativt snabbare.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor om att filer och mappar ska backas upp](backup-azure-file-folder-backup-faq.yml)
