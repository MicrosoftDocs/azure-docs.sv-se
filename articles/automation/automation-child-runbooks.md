---
title: Skapa modulära Runbooks i Azure Automation
description: Den här artikeln beskriver hur du skapar en Runbook som anropas av en annan Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: f0dd5cf5209924972080af6d22429252338754de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491256"
---
# <a name="create-modular-runbooks"></a>Skapa modulära runbooks

Det är en rekommenderad metod i Azure Automation att skriva återanvändbara, modulära Runbooks med en diskret funktion som anropas av andra Runbooks. En överordnad Runbook anropar ofta en eller flera underordnade Runbooks för att utföra de funktioner som krävs. 

Det finns två sätt att anropa en underordnad Runbook och det finns distinkta skillnader som du bör känna till för att kunna avgöra vilket som passar bäst för dina scenarier. I följande tabell sammanfattas skillnaderna mellan de två sätten att anropa en Runbook från en annan.

|  | Infogad | Cmdlet |
|:--- |:--- |:--- |
| **Jobb** |Underordnade runbooks körs i samma jobb som överordnade. |Ett separat jobb skapas för den underordnade runbooken. |
| **Projektering** |Överordnad runbook väntar på att underordnad runbook ska slutföras innan du fortsätter. |Överordnad Runbook fortsätter omedelbart efter att underordnad Runbook har startats *eller* överordnad Runbook väntar tills det underordnade jobbet har slutförts. |
| **Resultat** |Överordnad runbook kan hämta utdata direkt från underordnad runbook. |Överordnad Runbook måste hämta utdata från underordnat Runbook-jobb *eller* överordnad Runbook kan hämta utdata direkt från underordnad Runbook. |
| **Parametrar** |Värden för parametrar i underordnad runbook anges separat och kan använda alla datatyper. |Värden för underordnade Runbook-parametrar måste kombineras till en enskild hash. Den här hash-koden kan bara innehålla enkla, matris-och objekt data typer som använder JSON-serialisering. |
| **Automation-konto** |Överordnad Runbook kan bara använda underordnad Runbook i samma Automation-konto. |Överordnad Runbooks kan använda en underordnad Runbook från alla Automation-konton, från samma Azure-prenumeration och till och med från en annan prenumeration som du har en anslutning till. |
| **Publicera** |Underordnad runbook måste publiceras innan överordnad runbook publiceras. |Underordnad Runbook publiceras när som helst innan överordnad Runbook startas. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Anropa en underordnad Runbook med intern körning

Om du vill anropa en Runbook infogad från en annan Runbook använder du namnet på runbooken och anger värden för parametrarna, precis som du skulle använda en aktivitet eller en cmdlet. Alla Runbooks i samma Automation-konto är tillgängliga för alla andra som ska användas på det här sättet. Överordnad Runbook väntar på att den underordnade runbooken ska slutföras innan den flyttas till nästa rad, och alla utdata återgår direkt till den överordnade.

När du aktiverar en infogad runbook körs den i samma jobb som den överordnade runbooken. Det finns ingen indikation i den underordnade Runbook-jobbets historik. Eventuella undantag och eventuella strömmade utdata från den underordnade runbooken är kopplade till den överordnade. Det här beteendet resulterar i färre jobb och gör dem enklare att spåra och felsöka.

När en Runbook publiceras måste alla underordnade Runbooks som den anropar redan publiceras. Anledningen är att Azure Automation skapar en Association med underordnade runbooks när den kompilerar en Runbook. Om de underordnade Runbooks inte redan har publicerats, verkar den överordnade runbooken publicera korrekt men genererar ett undantag när den startas. Om detta inträffar kan du publicera om den överordnade runbooken så att den refererar till underordnade Runbooks korrekt. Du behöver inte publicera om den överordnade runbooken om en underordnad Runbook ändras eftersom associationen redan har skapats.

Parametrarna för en underordnad Runbook som kallas infogade kan vara av valfri datatyp, inklusive komplexa objekt. Det finns ingen [JSON-serialisering](start-runbooks.md#work-with-runbook-parameters)eftersom du startar runbooken med hjälp av Azure Portal eller med cmdleten [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) .

### <a name="runbook-types"></a>Runbook-typer

Vilka Runbook-typer kan anropa varandra?

* En [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) och en [grafisk Runbook](automation-runbook-types.md#graphical-runbooks) kan anropa varandra på samma sätt som båda är PowerShell-baserade.
* En [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) och en grafisk PowerShell Workflow-Runbook kan anropa varandra, eftersom båda är PowerShell-arbetsflöde-baserade.
* PowerShell-typerna och PowerShell-arbetsflödet kan inte anropa varandra på samma rad och måste använda `Start-AzAutomationRunbook` .

När sker publicerings ordningen?

Publicerings ordningen för Runbooks är bara av betydelse för PowerShell-arbetsflöde och grafiska PowerShell-arbetsflöden.

När en Runbook anropar en underordnad eller PowerShell-underordnad Runbook med hjälp av infogad körning används namnet på runbooken. Namnet måste börja med `.\\` för att ange att skriptet finns i den lokala katalogen.

### <a name="example"></a>Exempel

I följande exempel startar en underordnad test-Runbook som accepterar ett komplext objekt, ett heltals värde och ett booleskt värde. Resultatet av den underordnade runbooken tilldelas till en variabel. I det här fallet är underordnad Runbook en PowerShell-Runbook för arbets flöden.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Här är samma exempel som om du använder en PowerShell-Runbook som underordnad.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Starta en underordnad Runbook med en cmdlet

> [!IMPORTANT]
> Om din Runbook anropar en underordnad Runbook med `Start-AzAutomationRunbook` cmdlet: en med `Wait` parametern och den underordnade Runbook skapar ett objekt resultat kan åtgärden stöta på ett fel. Undvik felet genom att se [underordnade Runbooks med objekt utdata](troubleshoot/runbooks.md#child-runbook-object) för att lära dig hur du implementerar logiken för att söka efter resultat med hjälp av cmdleten [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Du kan använda `Start-AzAutomationRunbook` för att starta en Runbook enligt beskrivningen i [för att starta en Runbook med Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Det finns två användnings lägen för denna cmdlet. I ett läge returnerar cmdleten jobb-ID när jobbet skapas för den underordnade runbooken. I det andra läget, som skriptet tillåter genom att ange parametern *wait* , väntar cmdleten tills det underordnade jobbet är klart och returnerar utdata från den underordnade runbooken.

Jobbet från en underordnad Runbook som startades med en cmdlet körs separat från det överordnade Runbook-jobbet. Detta leder till fler jobb än att starta Runbook infogat och gör jobbet svårare att spåra. Överordnad kan starta fler än en underordnad Runbook asynkront utan att vänta på att de ska slutföras. För att den här parallella körningen ska anropa de underordnade Runbooks som infogas, måste den överordnade runbooken använda det [parallella nyckelordet](automation-powershell-workflow.md#use-parallel-processing).

Underordnade Runbook-utdata återgår inte till överordnad Runbook tillförlitlig på grund av tids inställning. Dessutom kanske variabler som `$VerbosePreference` , `$WarningPreference` och andra inte kan spridas till underordnade Runbooks. För att undvika dessa problem kan du starta de underordnade Runbooks som separata Automation-jobb med hjälp av `Start-AzAutomationRunbook` `Wait` parametern. Den här tekniken blockerar den överordnade runbooken tills den underordnade runbooken har slutförts.

Om du inte vill att den överordnade runbooken ska blockeras vid väntan kan du starta den underordnade runbooken med hjälp av `Start-AzAutomationRunbook` `Wait` parametern utan parametern. I det här fallet måste din Runbook använda [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) för att vänta på att jobbet ska slutföras. Det måste också använda [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) och [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) för att hämta resultaten.

Parametrar för en underordnad Runbook som startas med en cmdlet anges som en hash-modul enligt beskrivningen i [Runbook-parametrar](start-runbooks.md#work-with-runbook-parameters). Endast enkla data typer kan användas. Om runbooken har en parameter med en komplex datatyp, måste den anropas infogad.

Prenumerations kontexten kan gå förlorad när du startar underordnade Runbooks som separata jobb. För att underordnad Runbook ska kunna köra cmdletar för AZ-moduler mot en speciell Azure-prenumeration, måste den underordnade autentisera den här prenumerationen oberoende av den överordnade runbooken.

Om jobb inom samma Automation-konto fungerar med fler än en prenumeration kan du ändra det aktuella prenumerations sammanhanget för andra jobb genom att välja en prenumeration i ett jobb. Undvik den här situationen genom att använda `Disable-AzContextAutosave –Scope Process` i början av varje Runbook. Den här åtgärden sparar bara kontexten till den Runbook-körningen.

### <a name="example"></a>Exempel

I följande exempel startar en underordnad Runbook med parametrar och väntar sedan på att den ska slutföras med hjälp av `Start-AzAutomationRunbook` cmdleten med `Wait` parametern. När det är klart samlar exemplet in cmdlet-utdata från den underordnade runbooken. För att du ska kunna använda `Start-AzAutomationRunbook` måste skriptet autentisera till din Azure-prenumeration.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du kör en Runbook finns [i starta en Runbook i Azure Automation](start-runbooks.md).
* För övervakning av Runbook-åtgärd, se [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md).
