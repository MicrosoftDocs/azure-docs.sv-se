---
title: Lär dig PowerShell-arbetsflöde för Azure Automation
description: I den här artikeln lär du dig skillnaderna mellan PowerShell Workflow och PowerShell och begrepp som gäller för Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b3beb8b3eda4dfabf9240aa328a24d5f855689b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833516"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Lär dig PowerShell-arbetsflöde för Azure Automation

Runbooks i Azure Automation implementeras som Windows PowerShell arbetsflöden, Windows PowerShell skript som använder Windows Workflow Foundation. Ett arbetsflöde är en sekvens med programmerade, nätverksanslutna steg som utför tidskrävande uppgifter eller kräver samordning av flera steg i flera enheter eller hanterade noder. 

Medan ett arbetsflöde skrivs med Windows PowerShell syntax och startas av Windows PowerShell bearbetas det av Windows Workflow Foundation. Fördelarna med ett arbetsflöde framför ett normalt skript är samtidig prestanda för en åtgärd mot flera enheter och automatisk återställning från fel. 

> [!NOTE]
> Ett PowerShell Workflow-skript liknar ett Windows PowerShell skript, men har några viktiga skillnader som kan vara förvirrande för en ny användare. Därför rekommenderar vi att du endast skriver dina runbooks med PowerShell Workflow om du behöver [använda kontrollpunkter](#use-checkpoints-in-a-workflow). 

Fullständig information om ämnena i den här artikeln finns i [Komma igång med Windows PowerShell Workflow](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Använd nyckelordet Arbetsflöde

Det första steget för att konvertera ett PowerShell-skript till ett PowerShell-arbetsflöde är att omsluta det med `Workflow` nyckelordet . Ett arbetsflöde börjar med `Workflow` nyckelordet följt av skriptets brödtext inom kparenteser. Namnet på arbetsflödet följer `Workflow` nyckelordet enligt följande syntax:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Namnet på arbetsflödet måste matcha namnet på Automation-runbooken. Om runbooken importeras måste filnamnet matcha arbetsflödets namn och sluta med **.ps1**.

Om du vill lägga till parametrar i arbetsflödet använder `Param` du nyckelordet precis som i ett skript.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Lär dig skillnaderna mellan PowerShell Workflow-kod och PowerShell-skriptkod

PowerShell Workflow-koden ser nästan identisk ut med PowerShell-skriptkoden förutom några få betydande ändringar. I följande avsnitt beskrivs ändringar som du behöver göra i ett PowerShell-skript för att det ska kunna köras i ett arbetsflöde.

### <a name="activities"></a>Aktiviteter

En aktivitet är en specifik uppgift i ett arbetsflöde som utförs i en sekvens. När ett arbetsflöde körs i Windows PowerShell konverteras många Windows PowerShell-cmdletar automatiskt till aktiviteter. När du anger en av dessa cmdlets i din runbook körs motsvarande aktivitet av Windows Workflow Foundation. 

Om en cmdlet inte har någon motsvarande aktivitet Windows PowerShell Workflow automatiskt cmdleten i en [InlineScript-aktivitet.](#use-inlinescript) Vissa cmdlets undantas och kan inte användas i ett arbetsflöde om du inte uttryckligen inkluderar dem i ett InlineScript-block. Mer information finns i Använda [aktiviteter i skriptarbetsflöden.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11))

Arbetsflödesaktiviteter delar en uppsättning gemensamma parametrar för konfiguration av deras funktion. Se [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Positionsparametrar

Du kan inte använda positionsparametrar med aktiviteter och cmdlets i ett arbetsflöde. Därför måste du använda parameternamn. Tänk på följande kod som hämtar alla tjänster som körs:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Om du försöker köra den här koden i ett arbetsflöde får du ett meddelande som To correct for this issue (To correct for this issue, ange `Parameter set cannot be resolved using the specified named parameters.` parameternamnet) som i följande exempel:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserialiserade objekt

Objekt i arbetsflöden deserialiseras, vilket innebär att deras egenskaper fortfarande är tillgängliga, men inte deras metoder.  Tänk dig till exempel följande PowerShell-kod, som stoppar en tjänst med `Stop` hjälp av -metoden för `Service` -objektet.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Om du försöker köra detta i ett arbetsflöde får du ett felmeddelande `Method invocation is not supported in a Windows PowerShell Workflow.`

Ett alternativ är att omsluta de här två kodraderna i [ett InlineScript-block.](#use-inlinescript) I det här fallet `Service` representerar ett tjänstobjekt i blocket.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Ett annat alternativ är att använda en annan cmdlet som har samma funktioner som metoden, om en sådan är tillgänglig. I vårt exempel innehåller `Stop-Service` cmdleten samma funktioner som metoden `Stop` och du kan använda följande kod för ett arbetsflöde.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Använda InlineScript

Aktiviteten `InlineScript` är användbar när du behöver köra ett eller flera kommandon som ett traditionellt PowerShell-skript i stället för ett PowerShell-arbetsflöde.  Medan kommandon i ett arbetsflöde skickas till Windows Workflow Foundation för bearbetning, bearbetas kommandona i ett InlineScript-block av Windows PowerShell.

InlineScript använder följande syntax som visas nedan.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Du kan returnera utdata från ett InlineScript genom att tilldela utdata till en variabel. I följande exempel stoppas en tjänst och tjänstens namn matas sedan ut.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Du kan skicka värden till ett InlineScript-block, men du måste **$Using** omfångsmodifierare.  Följande exempel är identiskt med föregående exempel, förutom att tjänstnamnet anges av en variabel.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

InlineScript-aktiviteter kan vara kritiska i vissa arbetsflöden, men de stöder inte arbetsflödeskonstruktioner. Du bör endast använda dem när det behövs av följande skäl:

* Du kan inte använda [kontrollpunkter i ett](#use-checkpoints-in-a-workflow) InlineScript-block. Om ett fel inträffar i blocket måste det återupptas från början av blocket.
* Du kan inte använda parallell [körning i](#use-parallel-processing) ett InlineScript-block.
* InlineScript påverkar arbetsflödets skalbarhet eftersom det innehåller Windows PowerShell sessionen för hela Längden på InlineScript-blocket.

Mer information om hur du använder InlineScript finns i [Köra Windows PowerShell kommandon i ett arbetsflöde](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) och [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Använda parallell bearbetning

En av fördelarna med Windows PowerShell-arbetsflöden är möjligheten att utföra en uppsättning kommandon parallellt i stället för sekventiellt som i ett vanligt skript.

Du kan använda `Parallel` nyckelordet för att skapa ett skriptblock med flera kommandon som körs samtidigt. Här används följande syntax som visas nedan. I det här fallet startar Activity1 och Activity2 samtidigt. Activity3 startar först efter att både Activity1 och Activity2 har slutförts.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Överväg till exempel följande PowerShell-kommandon som kopierar flera filer till ett nätverksmål. Dessa kommandon körs sekventiellt så att en fil måste slutföra kopieringen innan nästa startas.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Följande arbetsflöde kör samma kommandon parallellt så att alla börjar kopiera samtidigt.  Det är först efter att alla har kopierats som slutförandemeddelandet visas.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Du kan använda `ForEach -Parallel` -konstruktionen för att bearbeta kommandon för varje objekt i en samling samtidigt. Objekten i samlingen bearbetas parallellt medan kommandona i skriptblocket körs sekventiellt. I den här processen används följande syntax som visas nedan. I det här fallet startar Activity1 samtidigt för alla objekt i samlingen. För varje objekt startar Activity2 när Activity1 har slutförts. Activity3 startar först efter att både Activity1 och Activity2 har slutförts för alla objekt. Vi använder `ThrottleLimit` parametern för att begränsa parallellitet. För hög av en `ThrottleLimit` kan orsaka problem. Det perfekta värdet för `ThrottleLimit` parametern beror på många faktorer i din miljö. Börja med ett lågt värde och prova olika ökande värden tills du hittar ett värde som fungerar för dina specifika omständigheter.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

I följande exempel liknar det föregående exemplet kopiering av filer parallellt.  I det här fallet visas ett meddelande för varje fil när den har kopierat.  Det är först efter att alla har kopierats som det slutgiltiga slutförandemeddelandet visas.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Vi rekommenderar inte att du kör underordnade runbooks parallellt eftersom detta har visat sig ge otillförlitliga resultat. Utdata från den underordnade runbooken visas ibland inte, och inställningarna i en underordnad runbook kan påverka de andra parallella underordnade runbooks. Variabler som `VerbosePreference` , `WarningPreference` och andra kanske inte sprids till underordnade runbooks. Och om den underordnade runbooken ändrar dessa värden kanske de inte återställs korrekt efter anropet.

## <a name="use-checkpoints-in-a-workflow"></a>Använda kontrollpunkter i ett arbetsflöde

En kontrollpunkt är en ögonblicksbild av arbetsflödets aktuella tillstånd som innehåller de aktuella värdena för variabler och alla utdata som genererats till den punkten. Om ett arbetsflöde slutar med ett fel eller pausas startar det från den sista kontrollpunkten nästa gång det körs, i stället för att starta i början. 

Du kan ange en kontrollpunkt i ett arbetsflöde med `Checkpoint-Workflow` aktiviteten . Azure Automation har en funktion som heter fair [share](automation-runbook-execution.md#fair-share), där alla runbooks som körs i tre timmar tas bort så att andra runbooks kan köras. Till slut läse runbooken in igen. När den är det återupptar den körningen från den senaste kontrollpunkten som tagits i runbooken.

För att garantera att runbooken slutförs så småningom måste du lägga till kontrollpunkter med intervall som körs i mindre än tre timmar. Om en ny kontrollpunkt läggs till under varje körning och runbooken avlägsnas efter tre timmar på grund av ett fel, återupptas runbooken på obestämd tid.

I följande exempel inträffar ett undantag efter Activity2, vilket gör att arbetsflödet avslutas. När arbetsflödet körs igen startar det genom att köra Activity2, eftersom den här aktiviteten var precis efter den senaste kontrollpunktsuppsättningen.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Ange kontrollpunkter i ett arbetsflöde efter aktiviteter som kan vara känsliga för undantag och som inte ska upprepas om arbetsflödet återupptas. Arbetsflödet kan till exempel skapa en virtuell dator. Du kan ange en kontrollpunkt både före och efter kommandona för att skapa den virtuella datorn. Om skapandet misslyckas upprepas kommandona om arbetsflödet startas igen. Om arbetsflödet misslyckas efter att det har skapats skapas inte den virtuella datorn igen när arbetsflödet återupptas.

I följande exempel kopieras flera filer till en nätverksplats och en kontrollpunkt anges efter varje fil.  Om nätverksplats förloras slutar arbetsflödet med ett fel.  När den startas igen återupptas den vid den senaste kontrollpunkten. Endast de filer som redan har kopierats hoppas över.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Eftersom autentiseringsuppgifterna för användarnamnet inte bevaras efter att du har anropat aktiviteten [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) eller efter den senaste kontrollpunkten, måste du ange autentiseringsuppgifterna till null och sedan hämta dem igen från tillgångslagret när eller kontrollpunkten `Suspend-Workflow` anropas.  Annars kan du få följande felmeddelande: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Följande kod visar hur du hanterar den här situationen i dina PowerShell Workflow-runbooks.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> För icke-grafiska PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Du kan använda dessa cmdlets eller uppdatera [dina moduler i ditt](automation-update-azure-modules.md) Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto. Du behöver inte använda dessa cmdlets om du autentiserar med ett Kör som-konto som konfigurerats med ett huvudnamn för tjänsten.

Mer information om kontrollpunkter finns i Lägga till [kontrollpunkter i ett skriptarbetsflöde.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11))

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell Workflow-runbooks finns i [Självstudie: Skapa en PowerShell Workflow-runbook.](learn/automation-tutorial-runbook-textual.md)
