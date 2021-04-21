---
title: Skapa modulära runbooks i Azure Automation
description: Den här artikeln beskriver hur du skapar en runbook som anropas av en annan runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 101ff9affe43dcc97de6cf5a535c82559aafeced
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834920"
---
# <a name="create-modular-runbooks"></a>Skapa modulära runbooks

Det är en rekommenderad metod Azure Automation att skriva återanvändbara, modulära runbooks med en diskret funktion som anropas av andra runbooks. En överordnad runbook anropar ofta en eller flera underordnade runbooks för att utföra nödvändiga funktioner. 

Det finns två sätt att anropa en underordnad runbook, och det finns olika skillnader som du bör känna till för att kunna avgöra vilket som är bäst för dina scenarier. I följande tabell sammanfattas skillnaderna mellan de två sätten att anropa en runbook från en annan.

|  | Infogad | Cmdlet |
|:--- |:--- |:--- |
| **Jobb** |Underordnade runbooks körs i samma jobb som överordnade. |Ett separat jobb skapas för den underordnade runbooken. |
| **Körnings-** |Överordnad runbook väntar på att underordnad runbook ska slutföras innan du fortsätter. |Överordnad runbook fortsätter omedelbart efter att underordnad runbook har *startats eller* överordnad runbook väntar på att det underordnade jobbet ska slutföras. |
| **Resultat** |Överordnad runbook kan hämta utdata direkt från underordnad runbook. |Överordnad runbook måste hämta utdata från ett underordnat runbook-jobb *eller* så kan en överordnad runbook hämta utdata direkt från en underordnad runbook. |
| **Parametrar** |Värden för parametrar i underordnad runbook anges separat och kan använda alla datatyper. |Värden för underordnade runbook-parametrar måste kombineras till en enda hash-tabell. Den här hash-tabellen kan bara innehålla enkla, matris- och objektdatatyper som använder JSON-serialisering. |
| **Automation-konto** |Överordnad runbook kan bara använda underordnad runbook i samma Automation-konto. |Överordnade runbooks kan använda en underordnad runbook från alla Automation-konton, från samma Azure-prenumeration och till och med från en annan prenumeration som du har en anslutning till. |
| **Publicera** |Underordnad runbook måste publiceras innan överordnad runbook publiceras. |Underordnad runbook publiceras när som helst innan överordnad runbook startas. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Anropa en underordnad runbook med hjälp av infogade körningar

Om du vill anropa en infogade runbook från en annan runbook använder du namnet på runbooken och anger värden för dess parametrar, precis som du använder en aktivitet eller en cmdlet. Alla runbooks i samma Automation-konto är tillgängliga för alla andra som kan användas på det här sättet. Den överordnade runbooken väntar på att den underordnade runbooken ska slutföras innan den flyttas till nästa rad, och alla utdata returneras direkt till den överordnade.

När du aktiverar en infogad runbook körs den i samma jobb som den överordnade runbooken. Det finns ingen indikation i jobbhistoriken för den underordnade runbooken. Eventuella undantag och eventuella strömutdata från den underordnade runbooken är associerade med den överordnade runbooken. Det här beteendet resulterar i färre jobb och gör dem enklare att spåra och felsöka.

När en runbook publiceras måste alla underordnade runbooks som anropas redan ha publicerats. Anledningen är att Azure Automation skapar en association med eventuella underordnade runbooks när en runbook kompileras. Om underordnade runbooks inte redan har publicerats verkar den överordnade runbooken publicera korrekt, men genererar ett undantag när den startas. Om detta inträffar kan du publicera om den överordnade runbooken för att korrekt referera till underordnade runbooks. Du behöver inte publicera om den överordnade runbooken om någon underordnad runbook ändras eftersom associationen redan har skapats.

Parametrarna för en underordnad runbook med namnet inline kan vara av valfri datatyp, inklusive komplexa objekt. Det finns ingen [JSON-serialisering,](start-runbooks.md#work-with-runbook-parameters)eftersom det är när du startar runbooken med hjälp av Azure Portal eller med cmdleten [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Runbook-typer

Vilka runbook-typer kan anropa varandra?

* En [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) och [en grafisk runbook](automation-runbook-types.md#graphical-runbooks) kan anropa varandra infogade, eftersom båda är PowerShell-baserade.
* En [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) och en grafisk PowerShell Workflow-runbook kan anropa varandra infogade, eftersom båda är PowerShell Workflow-baserade.
* PowerShell-typerna och PowerShell-arbetsflödestyperna kan inte anropa varandra infogade och måste använda `Start-AzAutomationRunbook` .

När spelar publiceringsordningen någon roll?

Publiceringsordningen för runbooks är bara viktig för PowerShell Workflow och grafiska PowerShell Workflow-runbooks.

När din runbook anropar en grafisk eller underordnad PowerShell Workflow-runbook med hjälp av infogade körningar används namnet på runbooken. Namnet måste börja med `.\\` för att ange att skriptet finns i den lokala katalogen.

### <a name="example"></a>Exempel

I följande exempel startas en underordnad test-runbook som accepterar ett komplext objekt, ett heltalsvärde och ett booleskt värde. Resultatet av den underordnade runbooken tilldelas till en variabel. I det här fallet är den underordnade runbooken en PowerShell Workflow-runbook.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

Här är samma exempel med en PowerShell-runbook som underordnad.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Starta en underordnad runbook med hjälp av en cmdlet

> [!IMPORTANT]
> Om din runbook anropar en underordnad runbook med cmdleten med parametern och den underordnade runbooken genererar ett objektresultat kan åtgärden `Start-AzAutomationRunbook` stöta på ett `Wait` fel. Information om hur du kan komma runt felet finns i Underordnade [runbooks](troubleshoot/runbooks.md#child-runbook-object) med objektutdata för att lära dig hur du implementerar logiken för att avssöka resultaten med hjälp av cmdleten [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Du kan använda `Start-AzAutomationRunbook` för att starta en runbook enligt beskrivningen i Starta en [runbook med Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Det finns två användningslägen för den här cmdleten. I ett läge returnerar cmdleten jobb-ID:t när jobbet skapas för den underordnade runbooken. I det andra läget, som skriptet  aktiverar genom att ange wait-parametern, väntar cmdleten tills det underordnade jobbet har avslutats och returnerar utdata från den underordnade runbooken.

Jobbet från en underordnad runbook som startades med en cmdlet körs separat från det överordnade runbook-jobbet. Det här beteendet resulterar i fler jobb än att starta den infogade runbooken och gör jobben svårare att spåra. Den överordnade runbooken kan starta mer än en underordnad runbook asynkront utan att vänta på att varje runbook ska slutföras. För den här parallella körningen som anropar underordnade runbooks måste den överordnade runbooken använda det [parallella nyckelordet](automation-powershell-workflow.md#use-parallel-processing).

Underordnade runbook-utdata återgår inte till den överordnade runbooken på ett tillförlitligt sätt på grund av tidsinställningen. Dessutom kan variabler som , och andra inte spridas till `$VerbosePreference` underordnade `$WarningPreference` runbooks. För att undvika dessa problem kan du starta underordnade runbooks som separata Automation-jobb med `Start-AzAutomationRunbook` hjälp av `Wait` parametern . Den här tekniken blockerar den överordnade runbooken tills den underordnade runbooken har slutförts.

Om du inte vill att den överordnade runbooken ska blockeras i väntan kan du starta den underordnade runbooken med hjälp `Start-AzAutomationRunbook` av utan `Wait` parametern . I det här fallet måste runbooken använda [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) för att vänta tills jobbet har slutförts. Den måste också använda [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) och [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) för att hämta resultaten.

Parametrar för en underordnad runbook som startas med en cmdlet tillhandahålls som en hash-tabell, enligt beskrivningen i [Runbook-parametrar .](start-runbooks.md#work-with-runbook-parameters) Endast enkla datatyper kan användas. Om runbooken har en parameter med en komplex datatyp, måste den anropas infogad.

Prenumerationskontexten kan gå förlorad när underordnade runbooks startas som separata jobb. För att den underordnade runbooken ska kunna köra Az-modul-cmdlets mot en specifik Azure-prenumeration måste den underordnade autentisera till den här prenumerationen oberoende av den överordnade runbooken.

Om jobb inom samma Automation-konto fungerar med fler än en prenumeration kan du ändra den valda prenumerationskontexten för andra jobb om du väljer en prenumeration i ett jobb. Undvik den här situationen genom att `Disable-AzContextAutosave -Scope Process` använda i början av varje runbook. Den här åtgärden sparar bara kontexten till den runbook-körningen.

### <a name="example"></a>Exempel

I följande exempel startas en underordnad runbook med parametrar och väntar sedan på att den ska slutföras med `Start-AzAutomationRunbook` hjälp av cmdleten med `Wait` parametern . När det är klart samlar exemplet in cmdlet-utdata från den underordnade runbooken. Om du `Start-AzAutomationRunbook` vill använda måste skriptet autentisera till din Azure-prenumeration.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

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
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    -Parameters $params -Wait
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du kör runbooken finns [i Starta en runbook i Azure Automation](start-runbooks.md).
* Information om övervakning av runbook-åtgärder finns [i Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md).
