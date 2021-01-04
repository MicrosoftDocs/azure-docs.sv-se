---
title: Azure Automation Runbook-typer
description: I den här artikeln beskrivs de typer av Runbooks som du kan använda i Azure Automation och överväganden för att bestämma vilken typ som ska användas.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1a0c12297f19d30bf13ffbe594e0433c83914a8e
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733968"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook-typer

Funktionen Azure Automation process automatisering stöder flera typer av Runbooks som definieras i följande tabell. Mer information om process automatiserings miljön finns [i Runbook-körning i Azure Automation](automation-runbook-execution.md).

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks)|Grafisk Runbook baserad på Windows PowerShell och skapas och redige ras helt i den grafiska redigeraren i Azure Portal. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks)|Grafisk Runbook baserad på Windows PowerShell-arbetsflöde och skapas och redige ras helt i den grafiska redigeraren i Azure Portal. |
| [PowerShell](#powershell-runbooks) |Text-Runbook baserad på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks)|Text-Runbook baserad på skript för Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Text-Runbook baserat på Python-skript. |

Ta hänsyn till följande överväganden när du bestämmer vilken typ som ska användas för en viss Runbook.

* Du kan inte konvertera Runbooks från grafiskt till text typ, eller tvärtom.
* Det finns begränsningar när du använder Runbooks av olika typer som underordnade Runbooks. Mer information finns i [underordnade Runbooks i Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafiska runbooks

Du kan skapa och redigera grafiska och grafiska PowerShell Workflow-Runbooks med hjälp av den grafiska redigeraren i Azure Portal. Du kan dock inte skapa eller redigera den här typen av Runbook med ett annat verktyg. Huvud funktioner i grafiska runbooks:

* Exporterat till filerna i ditt Automation-konto och sedan importerat till ett annat Automation-konto.
* Generera PowerShell-kod.
* Konverteras till eller från grafiska PowerShell Workflow-Runbooks under import.

### <a name="advantages"></a>Fördelar

* Använd visuell infogning-länka-konfigurera redigerings modell.
* Fokusera på hur data flödar genom processen.
* Visar hanterings processer visuellt.
* Ta med andra Runbooks som underordnade Runbooks för att skapa arbets flöden på hög nivå.
* Uppmuntra modulär programmering.

### <a name="limitations"></a>Begränsningar

* Det går inte att skapa eller redigera utanför Azure Portal.
* Kan kräva en kod aktivitet som innehåller PowerShell-kod för att köra komplex logik.
* Det går inte att konvertera till ett [text format](automation-runbook-types.md), eller så kan du inte konvertera en text-Runbook till grafiskt format. 
* Det går inte att visa eller direkt redigera PowerShell-kod som skapas i det grafiska arbets flödet. Du kan visa koden som du skapar i alla kod aktiviteter.
* Det går inte att köra Runbooks på en Linux-Hybrid Runbook Worker. Se [automatisera resurser i ditt data Center eller i molnet med hjälp av hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-Runbooks

PowerShell-Runbooks baseras på Windows PowerShell. Du redigerar koden för runbooken direkt med hjälp av text redigeraren i Azure Portal.  Du kan också använda valfri text redigerare och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-kod utan andra komplexa funktioner i PowerShell-arbetsflöde.
* Starta snabbare än PowerShell Workflow-Runbooks eftersom de inte behöver kompileras innan de körs.
* Kör i Azure och i hybrid Runbook Worker för både Windows och Linux.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med PowerShell-skript.
* Runbooks kan inte använda [parallell bearbetning](automation-powershell-workflow.md#use-parallel-processing) för att köra flera åtgärder parallellt.
* Runbooks kan inte använda [kontroll punkter](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) för att återuppta Runbook om det uppstår ett fel.
* Du kan inkludera endast PowerShell Workflow-Runbooks och grafiska runbooks som underordnade Runbooks med hjälp av cmdleten [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) , som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem

Följande är aktuella kända problem med PowerShell-Runbooks:

* PowerShell-Runbooks kan inte hämta en okrypterad [variabel till gång](./shared-resources/variables.md) med ett null-värde.
* PowerShell-Runbooks kan inte hämta en variabel till gång med `*~*` i namnet.
* En [Get-process](/powershell/module/microsoft.powershell.management/get-process) -åtgärd i en slinga i en PowerShell-Runbook kan krascha efter cirka 80 iterationer.
* En PowerShell-Runbook kan inte köras om den försöker skriva en stor mängd data till utdataströmmen samtidigt. Du kan vanligt vis undvika det här problemet genom att låta Runbook-jobbet bara mata ut den information som behövs för att arbeta med stora objekt. I stället för att använda `Get-Process` utan begränsningar kan du till exempel låta cmdleten bara mata in de nödvändiga parametrarna som i `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>PowerShell Workflow-Runbooks

PowerShell Workflow-Runbooks är text-Runbooks baserade på [Windows PowerShell-arbetsflöde](automation-powershell-workflow.md). Du redigerar koden för runbooken direkt med hjälp av text redigeraren i Azure Portal. Du kan också använda valfri text redigerare och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell Workflow Code.
* Använd [kontroll punkter](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) för att återuppta åtgärden om det uppstår ett fel.
* Använd [parallell bearbetning](automation-powershell-workflow.md#use-parallel-processing) för att utföra flera åtgärder parallellt.
* Kan inkludera andra grafiska runbooks och PowerShell Workflow-Runbooks som underordnade Runbooks för att skapa arbets flöden på hög nivå.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med PowerShell-arbetsflöde.
* Runbooks måste hantera den ytterligare komplexiteten i PowerShell-arbetsflöde, till exempel [avserialiserade objekt](automation-powershell-workflow.md#deserialized-objects).
* Runbooks tar längre tid att starta än PowerShell-Runbooks eftersom de måste kompileras innan de körs.
* Du kan bara ta med PowerShell-Runbooks som underordnade Runbooks med hjälp av `Start-AzAutomationRunbook` cmdleten.
* Runbooks kan inte köras på en Linux-Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks kompileras under python 2 och python 3. Python 3-Runbooks är för närvarande en för hands version. Du kan redigera koden för runbooken direkt med hjälp av text redigeraren i Azure Portal. Du kan också använda en text redigerare offline och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Använd robusta python-bibliotek.
* Kan köras i Azure eller i hybrid Runbook Worker.
* För python 2 stöds Windows hybrid Runbook Worker med [Python 2,7](https://www.python.org/downloads/release/latest/python2) installerat.
* För python 3-moln jobb stöds python 3,8-versionen. Skript och paket från vilken 3. x-version som helst kan fungera om koden är kompatibel mellan olika versioner.  
* För python 3 hybrid jobb på Windows-datorer kan du välja att installera valfri 3. x-version som du kanske vill använda.  
* För python 3 hybrid jobb på Linux-datorer är det beroende av python 3-versionen som är installerad på datorn för att köra DSC-OMSConfig och Linux-Hybrid Worker. Vi rekommenderar att du installerar 3,6 på Linux-datorer. Olika versioner bör dock också fungera om det inte finns några större ändringar i metoden signaturer eller kontrakt mellan versioner av python 3.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med python-skript.
* Om du vill använda bibliotek från tredje part måste du [Importera paketen](python-packages.md) till Automation-kontot.
* Användning av cmdleten **Start-AutomationRunbook**   i PowerShell/PowerShell-arbetsflöde för att starta en python 3-Runbook (för hands version) fungerar inte. Du kan använda cmdleten **Start-AzAutomationRunbook** från AZ. Automation-modulen eller **Start-AzureRmAutomationRunbook-** cmdleten från AzureRm. Automation-modulen för att undvika den här begränsningen.  
* Python 3-Runbooks (för hands version) och paket fungerar inte med PowerShell.
* Det finns inte stöd för att använda en webhook för att starta en python-Runbook.
* Azure Automation stöder inte **sys. stderr**.

### <a name="known-issues"></a>Kända problem

Python 3-jobb kan ibland Miss lyckas med ett undantags meddelande *Ogiltig sökväg till en tolknings körbar fil*. Du kan se detta undantag om ett jobb fördröjs, med start i mer än 10 minuter eller med **Start-AutomationRunbook** för att starta python 3-Runbooks. Om jobbet är försenat bör det räcka att starta om runbooken.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Mer information om PowerShell Workflow-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook för arbets flöden](learn/automation-tutorial-runbook-textual.md).
* Mer information om grafiska runbooks finns i [Självstudier: skapa en grafisk Runbook](learn/automation-tutorial-runbook-graphical.md).
* Mer information om python-Runbooks finns i [Självstudier: skapa en python-Runbook](learn/automation-tutorial-runbook-textual-python2.md).
