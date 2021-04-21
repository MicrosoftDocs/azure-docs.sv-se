---
title: Migrera från Orchestrator till Azure Automation (beta)
description: Den här artikeln beskriver hur du migrerar runbooks och integreringspaket från Orchestrator till Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c929781772b510639e1e5e47eed19927f408d16a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830510"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrera från Orchestrator till Azure Automation (beta)

Runbooks [i System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) baseras på aktiviteter från integrationspaket som är skrivna specifikt för Orchestrator, medan runbooks i Azure Automation baseras på Windows PowerShell. [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) i Azure Automation liknar Orchestrator-runbooks, där deras aktiviteter representerar PowerShell-cmdlets, underordnade runbooks och tillgångar. Förutom att konvertera själva runbooks måste du konvertera integreringspaketen med de aktiviteter som runbooks använder för att integrera moduler med Windows PowerShell cmdlets. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) lagrar och kör runbooks i ditt lokala datacenter som Orchestrator och använder samma integreringsmoduler som Azure Automation. Runbook Converter konverterar Orchestrator-runbooks till grafiska runbooks, som inte stöds i SMA. Du kan fortfarande installera standardaktivitetsmodulen och System Center Orchestrator-integreringsmoduler i SMA, men du måste skriva om [dina runbooks manuellt.](/system-center/sma/authoring-automation-runbooks)

## <a name="download-the-orchestrator-migration-toolkit"></a>Ladda ned Orchestrator Migration Toolkit

Det första steget i migreringen är att ladda [ned System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323). Den här verktygslådan innehåller verktyg som hjälper dig att konvertera runbooks från Orchestrator till Azure Automation.  

## <a name="import-the-standard-activities-module"></a>Importera modulen Standardaktiviteter

Importera modulen [Standardaktiviteter till](/system-center/orchestrator/standard-activities) Azure Automation. Detta inkluderar konverterade versioner av Orchestrator-standardaktiviteter som konverterade grafiska runbooks kan använda.

## <a name="import-orchestrator-integration-modules"></a>Importera Orchestrator-integreringsmoduler

Microsoft tillhandahåller [integrationspaket](/previous-versions/system-center/packs/hh295851(v=technet.10)) för att skapa runbooks för att automatisera System Center komponenter och andra produkter. Vissa av dessa integreringspaket baseras för närvarande på OIT, men kan för närvarande inte konverteras till integreringsmoduler på grund av kända problem. Importera [System Center Orchestrator-integreringsmoduler](https://www.microsoft.com/download/details.aspx?id=49555) till Azure Automation för de integreringspaket som används av dina runbooks som har åtkomst System Center. Det här paketet innehåller konverterade versioner av integreringspaketen som kan importeras till Azure Automation och Service Management Automation.  

## <a name="convert-integration-packs"></a>Konvertera integreringspaket

Använd [Integration Pack Converter för](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) att konvertera eventuella integreringspaket som skapats med [hjälp av Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) till PowerShell-baserade integreringsmoduler som kan importeras till Azure Automation eller Service Management Automation. När du kör Integration Pack Converter visas en guide som gör att du kan välja en fil för integrationspaket (.oip). Guiden visar sedan de aktiviteter som ingår i integrationspaketet och gör att du kan välja vilka aktiviteter som ska migreras. När du slutför guiden skapas en integreringsmodul som innehåller en motsvarande cmdlet för var och en av aktiviteterna i det ursprungliga integrationspaketet.

> [!NOTE]
> Du kan inte använda Integration Pack Converter för att konvertera integreringspaket som inte har skapats med OIT. Det finns även vissa integreringspaket från Microsoft som för närvarande inte kan konverteras med det här verktyget. Konverterade versioner av dessa integreringspaket tillhandahålls för nedladdning så att de kan installeras i Azure Automation eller Service Management Automation.

### <a name="parameters"></a>Parametrar

Alla egenskaper för en aktivitet i integrationspaketet konverteras till parametrar för motsvarande cmdlet i integreringsmodulen.  Windows PowerShell cmdlets har en uppsättning [gemensamma parametrar](/powershell/module/microsoft.powershell.core/about/about_commonparameters) som kan användas med alla cmdlets. Parametern -Verbose gör till exempel att en cmdlet matar ut detaljerad information om åtgärden.  Ingen cmdlet kan ha en parameter med samma namn som en gemensam parameter. Om en aktivitet har en egenskap med samma namn som en gemensam parameter uppmanas du att ange ett annat namn för parametern.

### <a name="monitor-activities"></a>Övervaka aktiviteter

Övervaka runbooks i Orchestrator börjar med en [övervakningsaktivitet](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) och kör kontinuerligt i väntan på att anropas av en viss händelse. Azure Automation har inte stöd för att övervaka runbooks, så alla övervakningsaktiviteter i integrationspaketet konverteras inte. I stället skapas en platshållar-cmdlet i integreringsmodulen för övervakningsaktiviteten.  Den här cmdleten har inga funktioner, men gör att alla konverterade runbooks som använder den kan installeras. Den här runbooken kan inte köras i Azure Automation, men den kan installeras så att du kan ändra den.

Orchestrator innehåller en uppsättning [standardaktiviteter](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) som inte ingår i ett integrationspaket men som används av många runbooks.  Modulen Standardaktiviteter är en integreringsmodul som innehåller en cmdlet-motsvarighet för var och en av dessa aktiviteter. Du måste installera den här integreringsmodulen Azure Automation innan du importerar konverterade runbooks som använder en standardaktivitet.

Förutom att stödja konverterade runbooks kan cmdletarna i modulen standardaktiviteter användas av någon som är bekant med Orchestrator för att skapa nya runbooks i Azure Automation. Även om funktionerna i alla standardaktiviteter kan utföras med cmdlets kan de fungera på olika sätt. Cmdletarna i den konverterade standardaktivitetsmodulen fungerar på samma sätt som motsvarande aktiviteter och använder samma parametrar. Detta kan hjälpa dig att övergå till Azure Automation runbooks.

## <a name="convert-orchestrator-runbooks"></a>Konvertera Orchestrator-runbooks

Orchestrator Runbook Converter konverterar Orchestrator-runbooks till [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) som kan importeras till Azure Automation. Runbook Converter implementeras som en PowerShell-modul med den cmdlet `ConvertFrom-SCORunbook` som gör konverteringen. När du installerar konverteraren skapas en genväg till en PowerShell-session som läser in cmdleten.   

Här är de grundläggande stegen för att konvertera en runbook och importera den till Azure Automation. Information om hur du använder cmdleten finns senare i det här avsnittet.

1. Exportera en eller flera runbooks från Orchestrator.
2. Hämta integreringsmoduler för alla aktiviteter i runbooken.
3. Konvertera Orchestrator-runbooks i den exporterade filen.
4. Granska informationen i loggarna för att verifiera konverteringen och för att fastställa eventuella manuella uppgifter som krävs.
5. Importera konverterade runbooks till Azure Automation.
6. Skapa alla nödvändiga tillgångar i Azure Automation.
7. Redigera runbooken i Azure Automation ändra alla nödvändiga aktiviteter.

Syntaxen för `ConvertFrom-SCORunbook` är:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – Sökväg till exportfilen som innehåller runbooks som ska konverteras.
* Modul – Kommaavgränsad lista över integreringsmoduler som innehåller aktiviteter i runbooks.
* OutputFolder – Sökväg till mappen för att skapa konverterade grafiska runbooks.

Följande exempelkommando konverterar runbooks i en exportfil med namnet **MyRunbooks.ois_export**.  Dessa runbooks använder Active Directory- Data Protection Manager integreringspaket.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Använda Runbook Converter-loggfiler

Runbook Converter skapar följande loggfiler på samma plats som den konverterade runbooken.  Om filerna redan finns skrivs de över med information från den senaste konverteringen.

| Fil | Innehåll |
|:--- |:--- |
| Runbook Converter – Progress.log |Detaljerade steg för konverteringen, inklusive information för varje aktivitet har konverterats och varning för varje aktivitet som inte konverterats. |
| Runbook Converter – Summary.log |Sammanfattning av den senaste konverteringen, inklusive eventuella varningar och uppföljningsuppgifter som du behöver utföra, till exempel att skapa en variabel som krävs för den konverterade runbooken. |

### <a name="export-runbooks-from-orchestrator"></a>Exportera runbooks från Orchestrator

Runbook Converter fungerar med en exportfil från Orchestrator som innehåller en eller flera runbooks.  Den skapar en Azure Automation runbook för varje Orchestrator-runbook i exportfilen.  

Om du vill exportera en runbook från Orchestrator högerklickar du på namnet på runbooken i Runbook-designer väljer **Exportera**.  Om du vill exportera alla runbooks i en mapp högerklickar du på namnet på mappen och väljer **Exportera**.

### <a name="convert-runbook-activities"></a>Konvertera Runbook-aktiviteter

Runbook Converter konverterar varje aktivitet i Orchestrator-runbooken till en motsvarande aktivitet i Azure Automation.  För de aktiviteter som inte kan konverteras skapas en platshållaraktivitet i runbooken med varningstext.  När du har importerat den konverterade runbooken till Azure Automation måste du ersätta någon av dessa aktiviteter med giltiga aktiviteter som utför de nödvändiga funktionerna.

Orkestreringsaktiviteter i modulen Standardaktiviteter konverteras. Det finns vissa orchestrator-standardaktiviteter som inte finns i den här modulen men som inte konverteras. har till exempel `Send Platform Event` ingen Azure Automation eftersom händelsen är specifik för Orchestrator.

[Övervakningsaktiviteter](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) konverteras inte eftersom det inte finns någon motsvarighet till dem i Azure Automation. Undantagen är övervakningsaktiviteter i konverterade integreringspaket som konverteras till platshållaraktiviteten.

Alla aktiviteter från ett konverterat integrationspaket konverteras om du anger sökvägen till integreringsmodulen med `modules` parametern . För System Center-integreringspaket kan du använda System Center Orchestrator Integration Modules.

### <a name="manage-orchestrator-resources"></a>Hantera Orchestrator-resurser

Runbook Converter konverterar endast runbooks, inte andra Orchestrator-resurser som räknare, variabler eller anslutningar.  Räknare stöds inte i Azure Automation.  Variabler och anslutningar stöds, men du måste skapa dem manuellt. Loggfilerna informerar dig om runbooken kräver sådana resurser och anger motsvarande resurser som du behöver skapa i Azure Automation för att den konverterade runbooken ska fungera korrekt.

En runbook kan till exempel använda en variabel för att fylla i ett visst värde i en aktivitet.  Den konverterade runbooken konverterar aktiviteten och anger en variabeltillgång i Azure Automation med samma namn som Orchestrator-variabeln. Den här åtgärden anges i **filen Runbook Converter - Summary.log** som skapas efter konverteringen. Du måste skapa den här variabeltillgången manuellt Azure Automation innan du använder runbooken.

### <a name="work-with-orchestrator-input-parameters"></a>Arbeta med Orchestrator-indataparametrar

Runbooks i Orchestrator accepterar indataparametrar med `Initialize Data` aktiviteten.  Om runbooken som konverteras innehåller [](automation-graphical-authoring-intro.md#handle-runbook-input) den här aktiviteten skapas en indataparameter i Azure Automation runbook för varje parameter i aktiviteten.  En [kontrollaktivitet för arbetsflödesskript](automation-graphical-authoring-intro.md#use-activities) skapas i den konverterade runbooken som hämtar och returnerar varje parameter. Alla aktiviteter i runbooken som använder en indataparameter refererar till utdata från den här aktiviteten.

Anledningen till att den här strategin används är att bäst spegla funktionerna i Orchestrator-runbooken.  Aktiviteter i nya grafiska runbooks bör referera direkt till indataparametrar med hjälp av en Indatakälla för Runbook.

### <a name="invoke-runbook-activity"></a>Anropa Runbook-aktivitet

Runbooks i Orchestrator startar andra runbooks med `Invoke Runbook` aktiviteten . Om runbooken som konverteras innehåller den här aktiviteten och alternativet anges skapas en Runbook-aktivitet för `Wait for completion` den i den konverterade runbooken.  Om alternativet inte anges skapas en aktivitet för arbetsflödesskript som använder `Wait for completion` [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) för att starta runbooken. När du har importerat den konverterade runbooken till Azure Automation måste du ändra den här aktiviteten med den information som anges i aktiviteten.

## <a name="create-orchestrator-assets"></a>Skapa Orchestrator-tillgångar

Runbook Converter konverterar inte Orchestrator-tillgångar. Du måste manuellt skapa nödvändiga Orchestrator-tillgångar i Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Konfigurera Hybrid Runbook Worker

Orchestrator lagrar runbooks på en databasserver och kör dem på Runbook-servrar, båda i ditt lokala datacenter. Runbooks i Azure Automation lagras i Azure-molnet och kan köras i ditt lokala datacenter med hjälp av en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Konfigurera en arbetsroll för att köra dina runbooks som konverterats från Orchestrator, eftersom de är utformade för att köras på lokala servrar och komma åt lokala resurser.

## <a name="related-articles"></a>Relaterade artiklar

* Mer information om Orchestrator finns [i System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* Läs mer om att automatisera hanteringen av tjänster i [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Information om Orchestrator-aktiviteter finns i [Orchestrator Standard Activities](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)).
* Information om hur du hämtar Orchestrator Migration Toolkit finns [i Ladda ned System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323).
* En översikt över Azure Automation Hybrid Runbook Worker finns i [Hybrid Runbook Worker översikt.](automation-hybrid-runbook-worker.md)
