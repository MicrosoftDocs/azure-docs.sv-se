---
title: Azure Automation runbooktyper
description: I den här artikeln beskrivs de typer av runbooks som du kan använda i Azure Automation och överväganden för att avgöra vilken typ som ska användas.
services: automation
ms.subservice: process-automation
ms.date: 02/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3e4f90372c2da22e8df3430ce340477352e5033b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830456"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation runbooktyper

Funktionen Azure Automation Process Automation stöder flera typer av runbooks, enligt definitionen i följande tabell. Mer information om processautomatiseringsmiljön finns i [Runbook-körning i Azure Automation](automation-runbook-execution.md).

| Typ | Description |
|:--- |:--- |
| [Grafisk](#graphical-runbooks)|Grafisk runbook baserad på Windows PowerShell skapas och redigeras helt i den grafiska redigeraren i Azure Portal. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks)|Grafisk runbook baserad på Windows PowerShell Workflow och skapas och redigeras helt i den grafiska redigeraren i Azure Portal. |
| [PowerShell](#powershell-runbooks) |Textbaserad runbook baserat på Windows PowerShell skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks)|Textbaserad runbook baserat på Windows PowerShell av arbetsflödesskript. |
| [Python](#python-runbooks) |Textbaserad runbook baserat på Python-skript. |

Ta hänsyn till följande överväganden när du avgör vilken typ som ska användas för en viss runbook.

* Du kan inte konvertera runbooks från grafisk till texttyp eller tvärtom.
* Det finns begränsningar när du använder runbooks av olika typer som underordnade runbooks. Mer information finns i [Underordnade runbooks i Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafiska runbooks

Du kan skapa och redigera grafiska och grafiska PowerShell Workflow-runbooks med hjälp av den grafiska redigeraren i Azure Portal. Du kan dock inte skapa eller redigera den här typen av runbook med ett annat verktyg. Huvudfunktioner i grafiska runbooks:

* Exporterades till filer i ditt Automation-konto och importerades sedan till ett annat Automation-konto.
* Generera PowerShell-kod.
* Konverteras till eller från grafiska PowerShell Workflow-runbooks under importen.

### <a name="advantages"></a>Fördelar

* Använd visuell insert-link-configure-redigeringsmodell.
* Fokusera på hur data flödar genom processen.
* Representerar hanteringsprocesser visuellt.
* Inkludera andra runbooks som underordnade runbooks för att skapa arbetsflöden på hög nivå.
* Uppmuntra modulär programmering.

### <a name="limitations"></a>Begränsningar

* Det går inte att skapa eller redigera utanför Azure Portal.
* Kan kräva en kodaktivitet som innehåller PowerShell-kod för att köra komplex logik.
* Det går inte att konvertera till något [av textformaten](automation-runbook-types.md), och du kan inte heller konvertera en text-runbook till grafiskt format. 
* Det går inte att visa eller redigera PowerShell-kod direkt som det grafiska arbetsflödet skapar. Du kan visa koden som du skapar i alla kodaktiviteter.
* Det går inte att köra runbooks på en Linux-Hybrid Runbook Worker. Se [Automatisera resurser i ditt datacenter eller moln med hjälp av Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runbooks

PowerShell-runbooks baseras på Windows PowerShell. Du redigerar koden för runbooken direkt med hjälp av textredigeraren i Azure Portal.  Du kan också använda valfri offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-kod utan de andra komplexiteterna i PowerShell-arbetsflödet.
* Starta snabbare än PowerShell Workflow-runbooks eftersom de inte behöver kompileras innan de körs.
* Kör i Azure och på Hybrid Runbook Workers för både Windows och Linux.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med PowerShell-skript.
* Runbooks kan inte använda [parallell bearbetning](automation-powershell-workflow.md#use-parallel-processing) för att köra flera åtgärder parallellt.
* Runbooks kan inte använda [kontrollpunkter för](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) att återuppta runbook om det uppstår ett fel.
* Du kan bara inkludera PowerShell Workflow-runbooks och grafiska runbooks som underordnade runbooks med hjälp av cmdleten [Start-AzAutomationRunbook,](/powershell/module/az.automation/start-azautomationrunbook) som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem

Följande är kända problem med PowerShell-runbooks:

* PowerShell-runbooks kan inte hämta en okrypterad [variabeltillgång](./shared-resources/variables.md) med ett null-värde.
* PowerShell-runbooks kan inte hämta en variabeltillgång `*~*` med i namnet.
* En [Get-Process-åtgärd](/powershell/module/microsoft.powershell.management/get-process) i en loop i en PowerShell-runbook kan krascha efter cirka 80 iterationer.
* En PowerShell-runbook kan misslyckas om den försöker skriva en stor mängd data till utdataströmmen på samma gång. Du kan vanligtvis komma runt det här problemet genom att ha runbook-utdata bara den information som behövs för att arbeta med stora objekt. I stället för att använda utan begränsningar kan du till exempel ha `Get-Process` cmdlet-utdata bara de obligatoriska parametrarna som i `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>Runbooks för PowerShell-arbetsflöden

PowerShell Workflow-runbooks är text-runbooks som baseras [Windows PowerShell Workflow](automation-powershell-workflow.md). Du redigerar koden för runbooken direkt med hjälp av textredigeraren i Azure Portal. Du kan också använda valfri offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-arbetsflödeskod.
* Använd [kontrollpunkter för](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) att återuppta åtgärden om det uppstår ett fel.
* Använd [parallell bearbetning](automation-powershell-workflow.md#use-parallel-processing) för att utföra flera åtgärder parallellt.
* Kan innehålla andra grafiska runbooks och PowerShell Workflow-runbooks som underordnade runbooks för att skapa arbetsflöden på hög nivå.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med PowerShell Workflow.
* Runbooks måste hantera den ytterligare komplexiteten i PowerShell-arbetsflödet, till exempel [deserialiserade objekt](automation-powershell-workflow.md#deserialized-objects).
* Runbooks tar längre tid att starta än PowerShell-runbooks eftersom de måste kompileras innan de körs.
* Du kan bara inkludera PowerShell-runbooks som underordnade runbooks med hjälp av `Start-AzAutomationRunbook` cmdleten .
* Runbooks kan inte köras på en Linux-Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python-runbooks

Python-runbooks kompileras under Python 2 och Python 3. Python 3-runbooks är för närvarande i förhandsversion. Du kan redigera koden för runbooken direkt med hjälp av textredigeraren i Azure Portal. Du kan också använda en offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

Python 3-runbooks stöds i följande globala Azure-infrastrukturer:

* Azure global
* Azure Government

### <a name="advantages"></a>Fördelar

* Använd de robusta Python-biblioteken.
* Kan köras i Azure eller på Hybrid Runbook Workers.
* För Python 2 stöds Windows Hybrid Runbook Workers med [python 2.7](https://www.python.org/downloads/release/latest/python2) installerat.
* Python 3.8-versionen stöds för Python 3-molnjobb. Skript och paket från valfri 3.x-version kan fungera om koden är kompatibel i olika versioner.  
* För Python 3-hybridjobb på Windows-datorer kan du välja att installera valfri version av 3.x som du kanske vill använda.  
* För Python 3-hybridjobb på Linux-datorer är vi beroende av den Python 3-version som är installerad på datorn för att köra DSC OMSConfig och Linux-Hybrid Worker. Vi rekommenderar att du installerar 3.6 på Linux-datorer. Olika versioner bör dock också fungera om det inte finns några större ändringar i metodsignaturer eller kontrakt mellan versioner av Python 3.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med Python-skript.
* Om du vill använda bibliotek från tredje part måste [du importera paketen](python-packages.md) till Automation-kontot.
* Att använda cmdleten **Start-AutomationRunbook** i PowerShell/PowerShell Workflow för att starta en   Python 3-runbook (förhandsversion) fungerar inte. Du kan använda cmdleten **Start-AzAutomationRunbook** från Az.Automation-modulen eller cmdleten **Start-AzureRmAutomationRunbook** från AzureRm.Automation-modulen för att komma runt den här begränsningen.  
* Python 3-runbooks (förhandsversion) och paket fungerar inte med PowerShell.
* Azure Automation stöder inte **sys.stderr**.

### <a name="known-issues"></a>Kända problem

Python 3-jobb misslyckas ibland med ett undantagsmeddelande om ogiltig *körbar tolksökväg*. Du kan se det här undantaget om ett jobb har fördröjts, startar mer än 10 minuter eller använder **Start-AutomationRunbook** för att starta Python 3-runbooks. Om jobbet är fördröjt räcker det att starta om runbooken.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell-runbooks finns i [Självstudie: Skapa en PowerShell-runbook.](learn/automation-tutorial-runbook-textual-powershell.md)
* Mer information om PowerShell Workflow-runbooks finns i [Självstudie: Skapa en PowerShell Workflow-runbook.](learn/automation-tutorial-runbook-textual.md)
* Mer information om grafiska runbooks finns i [Självstudie: Skapa en grafisk runbook.](learn/automation-tutorial-runbook-graphical.md)
* Mer information om Python-runbooks finns i [Självstudie: Skapa en Python-runbook.](learn/automation-tutorial-runbook-textual-python2.md)
