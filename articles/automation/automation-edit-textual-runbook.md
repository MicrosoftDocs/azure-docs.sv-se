---
title: Redigera textbaserade runbooks i Azure Automation
description: Den här artikeln beskriver hur du använder Azure Automation textredigerare för att arbeta med PowerShell- och PowerShell Workflow-runbooks.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 296d45fae4d59553b54a1b68923c91be4168d3a5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829394"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Redigera textbaserade runbooks i Azure Automation

Du kan använda textredigeraren i Azure Automation för att redigera [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) och [PowerShell Workflow-runbooks.](automation-runbook-types.md#powershell-workflow-runbooks) Den här redigeraren har vanliga funktioner i andra kodredigerare, till exempel IntelliSense. Den använder också färgkodning med ytterligare specialfunktioner som hjälper dig att komma åt resurser som är gemensamma för runbooks. 

Textredigeraren innehåller en funktion för att infoga kod för cmdlets, tillgångar och underordnade runbooks i en runbook. I stället för att skriva in koden själv kan du välja från en lista över tillgängliga resurser så infogar redigeraren lämplig kod i runbooken.

Varje runbook i Azure Automation har två versioner, Draft och Published. Du redigerar utkastversionen av runbooken och publicerar den så att den kan köras. Den publicerade versionen kan inte redigeras. Mer information finns i [Publicera en runbook](manage-runbooks.md#publish-a-runbook).

Den här artikeln innehåller detaljerade anvisningar för att utföra olika funktioner med den här redigeraren. Dessa gäller inte för [grafiska runbooks](automation-runbook-types.md#graphical-runbooks). Information om hur du arbetar med dessa runbooks finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Redigera en runbook med Azure Portal

1. I Azure Portal väljer du ditt Automation-konto.
2. Under **PROCESSAUTOMATISERING** väljer **du Runbooks** för att öppna listan över runbooks.
3. Välj den runbook du vill redigera och klicka sedan på **Redigera.**
4. Redigera runbooken.
5. Klicka **på** Spara när redigeringarna är klara.
6. Klicka **på** Publicera om du vill publicera den senaste utkastversionen av runbooken.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Infoga en cmdlet i en runbook

1. Placera markören där du vill placera cmdleten på arbetsytan i textredigeraren.
2. Expandera **noden Cmdlets** i bibliotekskontrollen.
3. Expandera modulen som innehåller cmdleten som ska användas.
4. Högerklicka på cmdlet-namnet för att infoga och välj Lägg **till på arbetsytan.** Om cmdleten har fler än en parameteruppsättning lägger redigeraren till standarduppsättningen. Du kan också expandera cmdleten och välja en annan parameteruppsättning.
5. Observera att koden för cmdleten infogas med hela listan med parametrar.
6. Ange ett lämpligt värde i stället för värdet som omges av vinkelparenteser (<>) för alla obligatoriska parametrar. Ta bort alla parametrar som du inte behöver.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Infoga kod för en underordnad runbook i en runbook

1. Placera markören på arbetsytan i textredigeraren där du vill placera koden för den underordnade [runbooken](automation-child-runbooks.md).
2. Expandera **noden Runbooks** i bibliotekskontrollen.
3. Högerklicka på runbooken för att infoga och välj Lägg **till på arbetsytan.**
4. Koden för den underordnade runbooken infogas med alla platshållare för alla runbook-parametrar.
5. Ersätt platshållarna med lämpliga värden för varje parameter.

### <a name="insert-an-asset-into-a-runbook"></a>Infoga en tillgång i en runbook

1. I kontrollen Canvas i textredigeraren placerar du markören där du vill placera koden för den underordnade runbooken.
2. Expandera **noden** Tillgångar i bibliotekskontrollen.
3. Expandera noden för önskad tillgångstyp.
4. Högerklicka på tillgångsnamnet för att infoga och välj Lägg **till på arbetsytan.** För [variabeltillgångar](./shared-resources/variables.md)väljer du **antingen Lägg till "Hämta variabel"** på arbetsytan eller Lägg till **"Ange variabel"** på arbetsytan, beroende på om du vill hämta eller ange variabeln.
5. Observera att koden för tillgången infogas i runbooken.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Redigera en Azure Automation runbook med Windows PowerShell

Om du vill redigera en runbook med Windows PowerShell använder du valfri redigerare och sparar runbooken i en **.ps1-fil.** Du kan använda [cmdleten Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) för att hämta innehållet i runbooken. Du kan använda cmdleten  [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) för att ersätta den befintliga utkasts-runbooken med den ändrade.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Hämta innehållet i en runbook med hjälp av Windows PowerShell

Följande exempelkommandon visar hur du hämtar skriptet för en runbook och sparar den till en skriptfil. I det här exemplet hämtas utkastversionen. Det är också möjligt att hämta den publicerade versionen av runbooken, även om den här versionen inte kan ändras.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Ändra innehållet i en runbook med hjälp av Windows PowerShell

Följande exempelkommandon visar hur du ersätter det befintliga innehållet i en runbook med innehållet i en skriptfil. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

* [Hantera runbooks i Azure Automation](manage-runbooks.md).
* [Lär dig PowerShell-arbetsflödet](automation-powershell-workflow.md).
* [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
* [Certifikat](./shared-resources/certificates.md).
* [Anslutningar](automation-connections.md).
* [Inloggningsuppgifter](./shared-resources/credentials.md).
* [Schemalägger](./shared-resources/schedules.md).
* [Variabler](./shared-resources/variables.md).
* [Referens för PowerShell-cmdleten](/powershell/module/az.automation).
