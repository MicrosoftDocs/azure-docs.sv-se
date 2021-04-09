---
title: Översikt över Azure Automation Ändringsspårning och inventering
description: I den här artikeln beskrivs funktionen Ändringsspårning och inventering, som hjälper dig att identifiera program-och Microsoft-tjänsteändringar i din miljö.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: e2371f3de8ed73250bca6639e6c749811c5559ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572618"
---
# <a name="change-tracking-and-inventory-overview"></a>Översikt över Ändringsspårning och inventering

I den här artikeln beskrivs hur du Ändringsspårning och inventering i Azure Automation. Den här funktionen spårar ändringar i virtuella datorer som finns i Azure, lokalt och i andra moln miljöer för att hjälpa dig att hitta drift-och miljö problem med program som hanteras av distributions paket hanteraren. Objekt som spåras av Ändringsspårning och lager inkluderar:

- Windows-programvara
- Linux-programvara (paket)
- Windows-och Linux-filer
- Windows-registernycklar
- Microsoft-tjänster
- Linux-daemon

> [!NOTE]
> Information om hur du spårar Azure Resource Manager egenskaps ändringar finns i [ändrings historiken](../../governance/resource-graph/how-to/get-resource-changes.md)för Azure Resource Graph.

Ändringsspårning och inventering använder [Azure Security Center fil integritets övervakning (FIM)](../../security-center/security-center-file-integrity-monitoring.md) för att undersöka operativ system och programfiler och Windows-registret. Även om FIM övervakar dessa entiteter, kan Ändringsspårning och inventera internt spår:

- Program varu ändringar
- Microsoft-tjänster
- Linux-daemon

Att aktivera alla funktioner som ingår i Ändringsspårning och inventering kan orsaka ytterligare kostnader. Läs igenom [priserna för automatisering](https://azure.microsoft.com/pricing/details/automation/) och [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/)innan du fortsätter.

Ändringsspårning och lagret vidarebefordrar data till Azure Monitor loggar och dessa insamlade data lagras i en Log Analytics arbets yta. FIM-funktionen (File Integrity Monitoring) är bara tillgänglig när **Azure Defender för-servrar** har Aktiver ATS. Mer information finns i Azure Security Center [prissättning](../../security-center/security-center-pricing.md) . FIM överför data till samma Log Analytics-arbetsyta som den som skapades för att lagra data från Ändringsspårning och inventering. Vi rekommenderar att du övervakar den länkade Log Analytics arbets ytan för att hålla reda på din exakta användning. Mer information om hur du analyserar Azure Monitor loggar data användning finns i [Hantera användning och kostnad](../../azure-monitor/logs/manage-cost-storage.md).

Datorer som är anslutna till Log Analytics arbets ytan använder [Log Analytics agent](../../azure-monitor/agents/log-analytics-agent.md) för att samla in data om ändringar av installerad program vara, Microsoft-tjänster, Windows-register och filer och Linux-daemon på övervakade servrar. När data är tillgängliga skickar agenten den till Azure Monitor loggar för bearbetning. Azure Monitor loggar använder logik till mottagna data, registrerar dem och gör dem tillgängliga för analys.

> [!NOTE]
> Ändringsspårning och inventeringen kräver att en Log Analytics arbets yta länkas till ditt Automation-konto. En slutgiltig lista över regioner som stöds finns i [mappningar för Azure-arbetsytor](../how-to/region-mappings.md). Region mappningarna påverkar inte möjligheten att hantera virtuella datorer i en separat region från ditt Automation-konto.

## <a name="current-limitations"></a>Aktuella begränsningar

Ändringsspårning och lagret stöder inte eller har följande begränsningar:

- Rekursion för spårning av Windows-register
- Nätverks fil system
- Olika installations metoder
- ***. exe** -filer som lagras i Windows
- Kolumnen **maximal fil storlek** och värden används inte i den aktuella implementeringen.
- Om du försöker samla in fler än 2500 filer på en 30-minuters samlings cykel kan Ändringsspårning och inventerings prestanda försämras.
- Om nätverks trafiken är hög kan ändrings poster ta upp till sex timmar innan de visas.
- Om du ändrar en konfiguration när en dator eller Server stängs av kan det Publicera ändringar som hör till den tidigare konfigurationen.
- Samla in snabb korrigerings uppdateringar på Windows Server 2016 Core RS3-datorer.
- Linux-demon kan visa ett ändrat tillstånd även om ingen ändring har skett. Det här problemet uppstår på grund av hur `SvcRunLevels` data i Azure Monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) -tabellen skrivs.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Ändringsspårning och inventering stöds på alla operativ system som uppfyller Log Analytics agent krav. Se [operativ system som stöds](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) för en lista över Windows-och Linux-operativsystem som för närvarande stöds av den Log Analytics agenten.

Information om klient kraven för TLS 1,2 finns i [TLS 1,2 Enforcement för Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

### <a name="python-requirement"></a>Python-krav

Ändringsspårning och inventering stöder endast Python2. Om din dator använder en distribution som inte innehåller python 2 som standard måste du installera den. Följande exempel kommandon kommer att installera python 2 på olika distributioner.

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE `zypper install -y python2`

Den körbara filen python2 måste ha ett alias till *python*.

## <a name="network-requirements"></a>Nätverkskrav

Kontrol lera [Azure Automation nätverks konfiguration](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory) för detaljerad information om portarna, URL: erna och andra nätverks uppgifter som krävs för ändringsspårning och inventering.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Du kan aktivera Ändringsspårning och inventering på följande sätt:

- Från ditt [Automation-konto](enable-from-automation-account.md) för en eller flera Azure-datorer och datorer som inte är Azure-datorer.

- Manuellt för datorer som inte är Azure-datorer, inklusive datorer eller servrar som är registrerade med [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md). För Hybrid datorer rekommenderar vi att du installerar Log Analytics agent för Windows genom att först ansluta datorn till [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md)och sedan använda Azure policy för att tilldela den inbyggda principen [distribuera Log Analytics agent till *Linux* eller *Windows* Azure Arc Machines](../../governance/policy/samples/built-in-policies.md#monitoring) . Om du även planerar att övervaka datorerna med Azure Monitor for VMs använder du i stället [aktivera Azure Monitor for VMS](../../governance/policy/samples/built-in-initiatives.md#monitoring) initiativ.

- För en enskild virtuell Azure-dator från [sidan virtuell dator](enable-from-vm.md) i Azure Portal. Det här scenariot är tillgängligt för virtuella Linux-och Windows-datorer.

- För [flera virtuella Azure-datorer](enable-from-portal.md) genom att välja dem från sidan virtuella datorer i Azure Portal.

## <a name="tracking-file-changes"></a>Spåra fil ändringar

För att spåra ändringar i filer på både Windows och Linux använder Ändringsspårning och lagret MD5-hashar av filerna. Funktionen använder hashar för att identifiera om ändringar har gjorts sedan den senaste inventeringen.

## <a name="tracking-file-content-changes"></a>Spåra ändringar i fil innehåll

Med Ändringsspårning och inventering kan du visa innehållet i en Windows-eller Linux-fil. För varje ändring i en fil lagrar Ändringsspårning och lager innehållet i filen i ett [Azure Storage konto](../../storage/common/storage-account-create.md). När du spårar en fil kan du visa dess innehåll före eller efter en ändring. Fil innehållet kan visas antingen i rad eller sida vid sida.

![Visa ändringar i en fil](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Spårning av register nycklar

Ändringsspårning och inventering möjliggör övervakning av ändringar i Windows-registernycklar. Med övervakning kan du hitta utöknings punkter där kod från tredje part och skadlig kod kan aktive ras. I följande tabell visas förkonfigurerade (men inte aktiverade) register nycklar. Om du vill spåra nycklarna måste du aktivera var och en.

> [!div class="mx-tdBreakAll"]
> |Registernyckel | Syfte |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Övervakar skript som körs vid start.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Övervakar skript som körs vid avstängning.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Övervakar nycklar som läses in innan användaren loggar in på Windows-kontot. Nyckeln används för 32-bitars program som körs på 64-bitars datorer.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Övervakar ändringar i program inställningarna.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Övervakar snabb meny hanterare som kopplas direkt till Utforskaren i Windows och som vanligt vis körs i processen med **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Övervakar kopior av Hook-hanterare som kopplas direkt till Utforskaren i Windows och som vanligt vis körs i processen med **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Övervakare för registrering av ikon överläggs hanterare.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Övervakare för ikon överläggs hanterare registrering för 32-bitars program som körs på 64-bitars datorer.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Övervakar nya plugin-program för webb läsar tillägg för Internet Explorer. Används för att få åtkomst till Document Object Model (DOM) för den aktuella sidan och för att kontrol lera navigeringen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Övervakar nya plugin-program för webb läsar tillägg för Internet Explorer. Används för att få åtkomst till Document Object Model (DOM) för den aktuella sidan och för att kontrol lera navigering för 32-bitars program som körs på 64-bitars datorer.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Övervakar nya Internet Explorer-tillägg, till exempel anpassade verktygs menyer och anpassade verktygsfälts knappar.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Övervakar nya Internet Explorer-tillägg, till exempel anpassade verktygs menyer och anpassade verktygsfälts knappar för 32-bitars program som körs på 64-bitars datorer.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Övervakar 32-bitars driv rutiner som är kopplade till wavemapper, wave1 och Wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 och vidc. Liknar avsnittet [drivers] i **system.ini** -filen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Övervakar 32-bitars driv rutiner som är kopplade till wavemapper, wave1 och Wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 och vidc för 32-bitars program som körs på 64-bitars datorer. Liknar avsnittet [drivers] i **system.ini** -filen.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Övervakar listan över kända eller ofta använda system-DLL-filer. Övervakning förhindrar att personer utnyttjar svaga program katalog behörigheter genom att släppa i trojanska hästar versioner av system-DLL: er.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Övervakar listan över paket som kan ta emot händelse meddelanden från **winlogon.exe**, den interaktiva stöd modellen för inloggning för Windows.

## <a name="recursion-support"></a>Rekursion-support

Ändringsspårning och inventering stöder rekursion, vilket gör att du kan ange jokertecken för att förenkla spårningen mellan kataloger. Rekursion tillhandahåller även miljövariabler som gör att du kan spåra filer i miljöer med flera eller dynamiska enhets namn. Följande lista innehåller allmän information som du bör känna till när du konfigurerar rekursion:

- Jokertecken krävs för att spåra flera filer.

- Du kan bara använda jokertecken i det sista segmentet i en fil Sök väg, till exempel **c:\folder- \\ filen** _ eller _ */etc/*. conf * *.

- Om en miljö variabel har en ogiltig sökväg, lyckas verifieringen men sökvägen Miss lyckas under körningen.

- Du bör undvika allmänna Sök vägs namn när du anger sökvägen, eftersom den här typen av inställning kan orsaka att för många mappar passerar.

## <a name="change-tracking-and-inventory-data-collection"></a>Ändringsspårning-och inventerings data insamling

I nästa tabell visas data insamlings frekvensen för de typer av ändringar som stöds av Ändringsspårning och inventering. För varje typ uppdateras även data ögonblicks bilden av det aktuella läget minst var 24: e timme.

| **Ändra typ** | **Frekvens** |
| --- | --- |
| Windows-register | 50 minuter |
| Windows-fil | 30 minuter |
| Linux-fil | 15 minuter |
| Microsoft-tjänster | 10 sekunder till 30 minuter</br> Standard: 30 minuter |
| Linux-daemon | 5 minuter |
| Windows-programvara | 30 minuter |
| Linux-programvara | 5 minuter |

I följande tabell visas gränserna för spårade objekt per dator för Ändringsspårning och inventering.

| **Resurs** | **Gränserna** |
|---|---|---|
|Fil|500|
|Register|250|
|Windows-programvara (inkluderar inte snabb korrigeringar) |250|
|Linux-paket|1250|
|Tjänster|250|
|Daemons|250|

Genomsnitts Log Analytics data användningen för en dator som använder Ändringsspårning och inventering är cirka 40 MB per månad, beroende på din miljö. Med funktionen användning och uppskattade kostnader i arbets ytan Log Analytics kan du visa data som matas in av Ändringsspårning och inventering i ett användnings diagram. Använd den här datavyn för att utvärdera din data användning och ta reda på hur den påverkar din faktura. Se [förstå användningen och beräkna kostnaderna](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Microsoft-tjänstedata

Standard frekvensen för insamling av Microsoft-tjänster är 30 minuter. Du kan konfigurera frekvensen med hjälp av ett skjutreglage på fliken **Microsoft-tjänster** under **Redigera inställningar**.

![Skjutreglage för Microsoft-tjänster](./media/overview/windowservices.png)

För att optimera prestanda spårar Log Analytics-agenten bara ändringar. Om du anger ett högt tröskelvärde kan det hända att ändringar Miss förfaller om tjänsten återgår till sitt ursprungliga tillstånd. Genom att ange frekvensen till ett mindre värde kan du fånga ändringar som annars kan missas.

> [!NOTE]
> Även om agenten kan spåra ändringar nedåt till ett intervall på 10 sekunder tar det fortfarande några minuter innan data visas i Azure Portal. Ändringar som sker under tiden som visas i portalen spåras och loggas fortfarande.

## <a name="support-for-alerts-on-configuration-state"></a>Stöd för aviseringar i konfigurations tillstånd

En viktig funktion i Ändringsspårning och inventering är aviseringar om ändringar i konfigurations läget för din hybrid miljö. Många användbara åtgärder är tillgängliga för utlösare som svar på aviseringar. Till exempel åtgärder på Azure Functions, Automation-runbooks, Webhooks och liknande. Aviseringar om ändringar i **c:\Windows\System32\drivers\etc\hosts** -filen för en dator är en lämplig applikation av aviseringar för ändringsspårning-och inventerings data. Det finns många fler scenarier för aviseringar, inklusive de fråge scenarier som definierats i nästa tabell.

|Fråga  |Beskrivning  |
|---------|---------|
|ConfigurationChange <br>&#124; där ConfigChangeType = = "Files" och FileSystemPath innehåller "c: \\ Windows \\ system32- \\ drivrutiner \\ "|Användbart för att spåra ändringar i systemkritiska filer.|
|ConfigurationChange <br>&#124; där FieldsChanged innehåller "FileContentChecksum" och FileSystemPath = = "c: \\ Windows \\ system32- \\ drivrutiner \\ osv \\ "|Användbart för att spåra ändringar i nyckel konfigurationsfiler.|
|ConfigurationChange <br>&#124; där ConfigChangeType = = "WindowsServices" och SvcName innehåller "W3SVC" och SvcState = = "stoppad"|Användbart för att spåra ändringar i system kritiska tjänster.|
|ConfigurationChange <br>&#124; där ConfigChangeType = = "daemons" och SvcName innehåller "SSH" och SvcState! = "kör"|Användbart för att spåra ändringar i system kritiska tjänster.|
|ConfigurationChange <br>&#124; där ConfigChangeType = = "Software" och ChangeCategory = = "Added"|Användbart för miljöer som behöver låsta program varu konfigurationer.|
|ConfigurationData <br>&#124; där SoftwareName innehåller "övervaknings agent" och CurrentVersion! = "8.0.11081.0"|Användbart för att se vilka datorer som har inaktuella eller inkompatibla program varu versioner installerade. Den här frågan rapporterar det senaste rapporterade konfigurations läget, men rapporterar inte ändringar.|
|ConfigurationChange <br>&#124; där RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ Software \\ Microsoft \\ Windows \\ CurrentVersion \\ QualityCompat"| Användbart för att spåra ändringar i viktiga Antivirus nycklar.|
|ConfigurationChange <br>&#124; där RegistryKey innehåller @ "HKEY_LOCAL_MACHINE \\ system \\ CurrentControlSet \\ Services \\ SharedAccess- \\ parametrar \\ FirewallPolicy"| Användbart för att spåra ändringar i brand Väggs inställningar.|

## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar från ett Automation-konto finns i [aktivera ändringsspårning och inventering från ett Automation-konto](enable-from-automation-account.md).

- Information om hur du aktiverar från Azure Portal finns i [aktivera ändringsspårning och inventering från Azure Portal](enable-from-portal.md).

- Information om hur du aktiverar från en Runbook finns i [aktivera ändringsspårning och inventering från en Runbook](enable-from-runbook.md).

- Information om hur du aktiverar från en virtuell Azure-dator finns i [aktivera ändringsspårning och inventering från en virtuell Azure-dator](enable-from-vm.md).
