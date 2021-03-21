---
title: Migrera från Orchestrator till Azure Automation (beta)
description: Den här artikeln beskriver hur du migrerar Runbooks och integrerings paket från Orchestrator till Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6ee4a09df0f95cb809db0e5c0e63d195ee5cfdff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896943"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrera från Orchestrator till Azure Automation (beta)

Runbooks i [System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) baseras på aktiviteter från integrations paket som är skrivna specifikt för Orchestrator, medan runbooks i Azure Automation baseras på Windows PowerShell. [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) i Azure Automation har ett liknande utseende för Orchestrator-Runbooks, med deras aktiviteter som representerar PowerShell-cmdletar, underordnade Runbooks och till gångar. Förutom att konvertera Runbooks själva måste du konvertera integrerings paketen till de aktiviteter som Runbooks använder för att integrera moduler med Windows PowerShell-cmdlets. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) lagrar och Kör Runbooks i ditt lokala data Center som Orchestrator, och använder samma integrerings moduler som Azure Automation. Runbook-konverteraren konverterar Orchestrator-Runbooks till grafiska runbooks, som inte stöds i SMA. Du kan fortfarande installera modulen standard aktiviteter och System Center Orchestrator-integrering i SMA, men du måste skriva om [dina runbooks](/system-center/sma/authoring-automation-runbooks)manuellt.

## <a name="download-the-orchestrator-migration-toolkit"></a>Hämta Orchestrator Migration Toolkit

Det första steget i migreringen är att ladda ned [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323). Det här verktyget innehåller verktyg som hjälper dig att konvertera Runbooks från Orchestrator till Azure Automation.  

## <a name="import-the-standard-activities-module"></a>Importera modulen standard aktiviteter

Importera [modulen standard aktiviteter](/system-center/orchestrator/standard-activities) till Azure Automation. Detta inkluderar konverterade versioner av vanliga Orchestrator-aktiviteter som konverterade grafiska runbooks kan använda.

## <a name="import-orchestrator-integration-modules"></a>Importera Orchestrator-integrerings moduler

Microsoft tillhandahåller [integrations paket](/previous-versions/system-center/packs/hh295851(v=technet.10)) för att skapa Runbooks för att automatisera System Center-komponenter och andra produkter. Vissa av integrerings paketen är för närvarande baserade på OIT, men kan för närvarande inte konverteras till integrations moduler på grund av kända problem. Importera [System Center Orchestrator integration-moduler](https://www.microsoft.com/download/details.aspx?id=49555) till Azure Automation för de integrerings paket som används av dina runbooks som ansluter till System Center. Det här paketet innehåller konverterade versioner av integrerings paket som kan importeras till Azure Automation och Service Management Automation.  

## <a name="convert-integration-packs"></a>Konvertera integrerings paket

Använd [integrerings paket konverteraren](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) för att konvertera integrerings paket som skapats med hjälp av [Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) till PowerShell-baserade integrations moduler som kan importeras till Azure Automation eller Service Management Automation. När du kör integrerings paketets konverterare visas en guide som gör att du kan välja en integrations paket fil (. OIP). I guiden visas sedan de aktiviteter som ingår i integrerings paketet och du kan välja vilka aktiviteter som ska migreras. När du slutför guiden skapas en integrerings modul som innehåller en motsvarande cmdlet för var och en av aktiviteterna i det ursprungliga integrerings paketet.

> [!NOTE]
> Du kan inte använda integrations paket konverteraren för att konvertera integrerings paket som inte har skapats med OIT. Det finns även vissa integrerings paket från Microsoft som för närvarande inte kan konverteras med det här verktyget. Konverterade versioner av dessa integrerings paket tillhandahålls för nedladdning så att de kan installeras i Azure Automation eller Service Management Automation.

### <a name="parameters"></a>Parametrar

Alla egenskaper för en aktivitet i integrerings paketet konverteras till parametrar för motsvarande cmdlet i integrerings modulen.  Windows PowerShell-cmdletar har en uppsättning [gemensamma parametrar](/powershell/module/microsoft.powershell.core/about/about_commonparameters) som kan användas med alla cmdletar. Till exempel ger parametern-verbose en cmdlet för att mata ut detaljerad information om dess funktion.  Ingen cmdlet kan ha en parameter med samma namn som en gemensam parameter. Om en aktivitet har en egenskap med samma namn som en gemensam parameter uppmanas du att ange ett annat namn för parametern i guiden.

### <a name="monitor-activities"></a>Övervaka aktiviteter

Övervaka Runbooks i Orchestrator startar med en [Övervaka aktivitet](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) och körs kontinuerligt och väntar på att anropas av en viss händelse. Azure Automation stöder inte övervaknings-Runbooks, så alla övervaknings aktiviteter i integrerings paketet konverteras inte. I stället skapas en placeholder-cmdlet i integrations-modulen för aktiviteten övervaka.  Denna cmdlet har inga funktioner, men den tillåter alla konverterade Runbook-funktioner som använder den för att installeras. Det går inte att köra denna Runbook i Azure Automation, men den kan installeras så att du kan ändra den.

Orchestrator innehåller en uppsättning [standard aktiviteter](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) som inte ingår i ett integrations paket men som används av många Runbooks.  Modulen standard aktiviteter är en integrerings modul som innehåller en motsvarande cmdlet för var och en av dessa aktiviteter. Du måste installera den här integrerings modulen i Azure Automation innan du importerar några konverterade Runbooks som använder en standard aktivitet.

Förutom stöd för konverterade Runbooks kan cmdletarna i modulen standard aktiviteter användas av någon bekant med Orchestrator för att bygga nya Runbooks i Azure Automation. Även om funktionerna för alla standard aktiviteter kan utföras med cmdletar kan de fungera annorlunda. Cmdletarna i den konverterade modulen standard aktiviteter fungerar på samma sätt som motsvarande aktiviteter och använder samma parametrar. Detta kan hjälpa dig vid över gången till Azure Automation runbooks.

## <a name="convert-orchestrator-runbooks"></a>Konvertera Orchestrator-Runbooks

Orchestrator Runbook Converter konverterar Orchestrator-Runbooks till [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) som kan importeras till Azure Automation. Runbook-konverteraren implementeras som en PowerShell-modul med den cmdlet `ConvertFrom-SCORunbook` som gör konverteringen. När du installerar konverteraren skapas en genväg till en PowerShell-session som läser in cmdleten.   

Här följer de grundläggande stegen för att konvertera en Runbook och importera den till Azure Automation. Information om hur du använder cmdleten finns senare i det här avsnittet.

1. Exportera en eller flera Runbooks från Orchestrator.
2. Hämta integrerings moduler för alla aktiviteter i runbooken.
3. Konvertera Orchestrator-Runbooks i den exporterade filen.
4. Granska informationen i loggarna för att validera konverteringen och för att fastställa eventuella manuella uppgifter som krävs.
5. Importera konverterade Runbooks till Azure Automation.
6. Skapa alla nödvändiga till gångar i Azure Automation.
7. Redigera runbooken i Azure Automation för att ändra eventuella obligatoriska aktiviteter.

Syntaxen för `ConvertFrom-SCORunbook` är:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – sökvägen till export filen som innehåller de Runbooks som ska konverteras.
* Modul-kommaavgränsad lista över integrerings moduler som innehåller aktiviteter i Runbooks.
* OutputFolder – sökvägen till mappen för att skapa konverterade grafiska runbooks.

I följande exempel kommando konverterar Runbooks i en export fil med namnet **MyRunbooks.ois_export**.  Dessa Runbooks använder Active Directory och Data Protection Manager integrerings paket.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Använd loggfiler för Runbook-omvandlare

Runbook-konverteraren skapar följande loggfiler på samma plats som den konverterade runbooken.  Om filerna redan finns skrivs de över med information från den senaste konverteringen.

| Fil | Innehåll |
|:--- |:--- |
| Runbook-omvandlare-Progress. log |Detaljerade steg i konverteringen inklusive information för varje aktivitet som har konverterats och varning för varje aktivitet som inte konverteras. |
| Runbook-konverterare – Sammanfattning. logg |Sammanfattning av den senaste konverteringen inklusive eventuella varningar och Uppföljnings aktiviteter som du behöver utföra, till exempel skapa en variabel som krävs för den konverterade runbooken. |

### <a name="export-runbooks-from-orchestrator"></a>Exportera Runbooks från Orchestrator

Runbook-konverteraren fungerar med en export fil från Orchestrator som innehåller en eller flera Runbooks.  Den skapar en motsvarande Azure Automation Runbook för varje Orchestrator-Runbook i export filen.  

Om du vill exportera en Runbook från Orchestrator högerklickar du på namnet på runbooken i Runbook-designer och väljer **Exportera**.  Om du vill exportera alla Runbooks i en mapp högerklickar du på mappens namn och väljer **Exportera**.

### <a name="convert-runbook-activities"></a>Konvertera Runbook-aktiviteter

Runbook-konverteraren konverterar varje aktivitet i Orchestrator-runbooken till en motsvarande aktivitet i Azure Automation.  För de aktiviteter som inte kan konverteras skapas en plats hållare-aktivitet i Runbook med varnings text.  När du har importerat den konverterade runbooken till Azure Automation måste du ersätta alla dessa aktiviteter med giltiga aktiviteter som utför de nödvändiga funktionerna.

Alla Orchestrator-aktiviteter i modulen standard aktiviteter konverteras. Det finns vissa vanliga Orchestrator-aktiviteter som inte finns i den här modulen, men som inte konverteras. Har till exempel `Send Platform Event` ingen Azure Automation motsvarighet eftersom händelsen är speciell för Orchestrator.

[Övervaknings aktiviteter](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) konverteras inte eftersom det inte finns någon motsvarighet till dem i Azure Automation. Undantagen är övervaka aktiviteter i konverterade integrerings paket som konverteras till plats hållar aktiviteten.

Alla aktiviteter från ett konverterat integrerings paket konverteras om du anger sökvägen till integrerings modulen med- `modules` parametern. För System Center-integrerings paket kan du använda System Center Orchestrator-integreringens moduler.

### <a name="manage-orchestrator-resources"></a>Hantera Orchestrator-resurser

Runbook-konverteraren konverterar bara Runbooks, inte andra Orchestrator-resurser som räknare, variabler eller anslutningar.  Räknare stöds inte i Azure Automation.  Variabler och anslutningar stöds, men du måste skapa dem manuellt. Loggfilerna meddelar dig om runbooken kräver sådana resurser och anger motsvarande resurser som du måste skapa i Azure Automation för att den konverterade runbooken ska fungera korrekt.

En Runbook kan till exempel använda en variabel för att fylla i ett visst värde i en aktivitet.  Den konverterade Runbook-flödet konverterar aktiviteten och anger en variabel till gång i Azure Automation med samma namn som Orchestrator-variabeln. Den här åtgärden anges i **Runbook-konverteraren-Summary. log-** filen som skapas efter konverteringen. Du måste skapa den här variabeln till gången manuellt i Azure Automation innan du använder runbooken.

### <a name="work-with-orchestrator-input-parameters"></a>Arbeta med ingångs parametrar för Orchestrator

Runbooks i Orchestrator accepterar indataparametrar med `Initialize Data` aktiviteten.  Om den Runbook som konverteras innehåller den här aktiviteten, skapas en [indataparameter](automation-graphical-authoring-intro.md#handle-runbook-input) i Azure Automation Runbook för varje parameter i aktiviteten.  En [kontroll aktivitet för arbets flödes skript](automation-graphical-authoring-intro.md#use-activities) skapas i den konverterade Runbook som hämtar och returnerar varje parameter. Aktiviteter i en Runbook som använder en indataparameter refererar till utdata från den här aktiviteten.

Skälet till att den här strategin används är att bäst spegla funktionerna i Orchestrator-runbooken.  Aktiviteter i nya grafiska runbooks ska referera direkt till indataparametrarna med hjälp av en data källa för indata för Runbook.

### <a name="invoke-runbook-activity"></a>Anropa Runbook-aktivitet

Runbooks i Orchestrator startar andra Runbooks med `Invoke Runbook` aktiviteten. Om den Runbook som konverteras innehåller den här aktiviteten och `Wait for completion` alternativet är inställt, skapas en Runbook-aktivitet för den i den konverterade runbooken.  Om `Wait for completion` alternativet inte anges skapas en arbets flödes skript aktivitet som använder [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) för att starta runbooken. När du har importerat den konverterade runbooken till Azure Automation måste du ändra den här aktiviteten med den information som anges i aktiviteten.

## <a name="create-orchestrator-assets"></a>Skapa Orchestrator-tillgångar

Runbook-konverteraren konverterar inte Orchestrator-tillgångar. Du måste manuellt skapa eventuella nödvändiga Orchestrator-tillgångar i Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Konfigurera Hybrid Runbook Worker

Orchestrator lagrar Runbooks på en databas server och kör dem på Runbook-servrar, både i ditt lokala data Center. Runbooks i Azure Automation lagras i Azure-molnet och kan köras i ditt lokala data Center med hjälp av en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Konfigurera en arbetare att köra dina runbooks som har konverterats från Orchestrator, eftersom de är utformade för att köras på lokala servrar och komma åt lokala resurser.

## <a name="related-articles"></a>Relaterade artiklar

* Information om Orchestrator finns i [System Center 2012-Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* Läs mer om hur du automatiserar hanteringen av tjänster i [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Information om Orchestrator-aktiviteter finns i [Orchestrator standard-aktiviteter](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)).
* Information om hur du skaffar Orchestrator Migration Toolkit finns i [Hämta System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323).
* En översikt över Azure Automation Hybrid Runbook Worker finns i [hybrid Runbook Worker översikt](automation-hybrid-runbook-worker.md).
