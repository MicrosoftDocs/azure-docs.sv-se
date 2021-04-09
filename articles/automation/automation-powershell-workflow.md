---
title: Lär dig mer om PowerShell-arbetsflöde för Azure Automation
description: I den här artikeln lär du dig skillnaderna mellan PowerShell-arbetsflöde och PowerShell och begrepp som är tillämpliga för Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 483d4c16f1b77bf7328857eb25e1571a741d144e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896926"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Lär dig mer om PowerShell-arbetsflöde för Azure Automation

Runbooks i Azure Automation implementeras som Windows PowerShell-arbetsflöden, Windows PowerShell-skript som använder Windows Workflow Foundation. Ett arbetsflöde är en sekvens med programmerade, nätverksanslutna steg som utför tidskrävande uppgifter eller kräver samordning av flera steg i flera enheter eller hanterade noder. 

När ett arbets flöde skrivs med Windows PowerShell-syntax och startas av Windows PowerShell bearbetas det av Windows Workflow Foundation. Fördelarna med ett arbets flöde över ett normalt skript inkluderar samtidig prestanda för en åtgärd mot flera enheter och automatisk återställning från haverier. 

> [!NOTE]
> Ett PowerShell-arbetsflöde skript liknar ett Windows PowerShell-skript men har några viktiga skillnader som kan vara förvirrande för en ny användare. Därför rekommenderar vi att du bara skriver dina Runbooks med hjälp av PowerShell-arbetsflöde om du behöver använda [kontroll punkter](#use-checkpoints-in-a-workflow). 

Fullständig information om ämnena i den här artikeln finns [komma igång med Windows PowerShell-arbetsflöde](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Använd arbets flödets nyckelord

Det första steget för att konvertera ett PowerShell-skript till ett PowerShell-arbetsflöde är att stänga det med `Workflow` nyckelordet. Ett arbets flöde börjar med `Workflow` nyckelordet följt av bröd texten i skriptet som omges av klammerparenteser. Namnet på arbets flödet följer `Workflow` nyckelordet som det visas i följande syntax:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Namnet på arbets flödet måste matcha namnet på Automation-runbooken. Om runbooken importeras måste fil namnet matcha arbets flödets namn och måste sluta med **. ps1**.

Om du vill lägga till parametrar i arbets flödet använder `Param` du nyckelordet precis som i ett skript.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Lär dig skillnader mellan PowerShell-arbetsflöde och kod för PowerShell-skript

PowerShell-arbetsflödet ser nästan likadant ut som skript kod med PowerShell förutom några få betydande ändringar. I följande avsnitt beskrivs ändringar som du måste göra i ett PowerShell-skript för att det ska kunna köras i ett arbets flöde.

### <a name="activities"></a>Aktiviteter

En aktivitet är en speciell uppgift i ett arbets flöde som utförs i en sekvens. När ett arbetsflöde körs i Windows PowerShell konverteras många Windows PowerShell-cmdletar automatiskt till aktiviteter. När du anger en av dessa cmdletar i din runbook körs motsvarande aktivitet med Windows Workflow Foundation. 

Om en cmdlet saknar motsvarande aktivitet, kör Windows PowerShell-arbetsflöde automatiskt cmdleten i en [InlineScript](#use-inlinescript) -aktivitet. Vissa cmdletar undantas och kan inte användas i ett arbets flöde om du inte uttryckligen inkluderar dem i ett InlineScript-block. Mer information finns i [använda aktiviteter i skript arbets flöden](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

Arbetsflödesaktiviteter delar en uppsättning gemensamma parametrar för konfiguration av deras funktion. Se [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Positions parametrar

Du kan inte använda positions parametrar med aktiviteter och cmdlets i ett arbets flöde. Därför måste du använda parameter namn. Överväg följande kod som hämtar alla tjänster som körs:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Om du försöker köra den här koden i ett arbets flöde får du ett meddelande som `Parameter set cannot be resolved using the specified named parameters.` du kan åtgärda för det här problemet genom att ange parameter namnet, som i följande exempel:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Avserialiserade objekt

Objekt i arbets flöden deserialiseras, vilket innebär att deras egenskaper fortfarande är tillgängliga, men inte deras metoder.  Överväg till exempel följande PowerShell-kod som stoppar en tjänst med hjälp av- `Stop` metoden för `Service` objektet.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Om du försöker köra detta i ett arbets flöde får du ett fel meddelande `Method invocation is not supported in a Windows PowerShell Workflow.`

Ett alternativ är att figursätta dessa två kodrader i ett [InlineScript](#use-inlinescript) -block. I det här fallet `Service` representerar ett tjänst objekt i blocket.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Ett annat alternativ är att använda en annan cmdlet som har samma funktion som metoden, om en sådan finns. I vårt exempel `Stop-Service` tillhandahåller cmdleten samma funktioner som `Stop` metoden, och du kan använda följande kod för ett arbets flöde.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Använd InlineScript

`InlineScript`Aktiviteten är användbar när du behöver köra ett eller flera kommandon som traditionellt PowerShell-skript i stället för PowerShell-arbetsflöde.  Medan kommandon i ett arbetsflöde skickas till Windows Workflow Foundation för bearbetning, bearbetas kommandona i ett InlineScript-block av Windows PowerShell.

InlineScript använder följande syntax som visas nedan.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Du kan returnera utdata från en InlineScript genom att tilldela utdata till en variabel. I följande exempel stoppas en tjänst och sedan genereras tjänst namnet.

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

Du kan skicka värden till ett InlineScript-block, men du måste använda **$using** omfångs modifierare.  Följande exempel är identiskt med föregående exempel, förutom att tjänst namnet tillhandahålls av en variabel.

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

Även om InlineScript-aktiviteter kan vara kritiska i vissa arbets flöden, stöder de inte arbets flödes konstruktioner. Du bör endast använda dem när det är nödvändigt av följande anledningar:

* Det går inte att använda [kontroll punkter](#use-checkpoints-in-a-workflow) i ett InlineScript-block. Om ett fel inträffar i blocket måste det fortsätta från början av blocket.
* Du kan inte använda [parallell körning](#use-parallel-processing) i ett InlineScript-block.
* InlineScript påverkar skalbarheten för arbets flödet eftersom den innehåller Windows PowerShell-sessionen för hela längden på InlineScript-blocket.

Mer information om hur du använder InlineScript finns [i köra Windows PowerShell-kommandon i ett arbets flöde](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) och [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Använd parallell bearbetning

En av fördelarna med Windows PowerShell-arbetsflöden är möjligheten att utföra en uppsättning kommandon parallellt i stället för sekventiellt som i ett vanligt skript.

Du kan använda `Parallel` nyckelordet för att skapa ett skript block med flera kommandon som körs samtidigt. Följande syntax visas nedan. I det här fallet startar Activity1 och Activity2 på samma tidpunkt. Activity3 startar bara efter att både Activity1 och Activity2 har slutförts.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Överväg till exempel följande PowerShell-kommandon som kopierar flera filer till ett nätverks mål. Dessa kommandon körs i tur och ordning så att en fil måste slutföras innan nästa startas.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Följande arbets flöde kör samma kommandon parallellt så att alla börjar kopieras samtidigt.  När alla har kopierats visas meddelandet slut för ande.

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

Du kan använda `ForEach -Parallel` konstruktion för att bearbeta kommandon för varje objekt i en samling samtidigt. Objekten i samlingen bearbetas parallellt medan kommandona i skriptblocket körs sekventiellt. I den här processen används följande syntax som visas nedan. I det här fallet startar Activity1 på samma tid för alla objekt i samlingen. Activity2 startar när Activity1 har slutförts för varje objekt. Activity3 startar bara efter att både Activity1 och Activity2 har slutförts för alla objekt. Vi använder `ThrottleLimit` parametern för att begränsa parallellitet. För hög av a `ThrottleLimit` kan orsaka problem. Det idealiska värdet för `ThrottleLimit` parametern beror på många faktorer i din miljö. Börja med ett lågt värde och prova olika ökande värden tills du hittar ett som fungerar för dina speciella omständigheter.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Följande exempel liknar det tidigare exemplet som kopierar filer parallellt.  I det här fallet visas ett meddelande för varje fil efter att den har kopierats.  När alla har kopierats är meddelandet slut för ande visas.

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
> Vi rekommenderar inte att du kör underordnade Runbooks parallellt eftersom detta har visats för att ge otillförlitliga resultat. Utdata från den underordnade runbooken visas ibland inte, och inställningarna i en underordnad Runbook kan påverka de andra parallella underordnade Runbooks. Variabler som `VerbosePreference` , `WarningPreference` och andra kanske inte kan spridas till underordnade Runbooks. Och om den underordnade runbooken ändrar dessa värden kanske de inte återställs korrekt efter anrop.

## <a name="use-checkpoints-in-a-workflow"></a>Använda kontroll punkter i ett arbets flöde

En kontroll punkt är en ögonblicks bild av det aktuella läget för det arbets flöde som innehåller de aktuella värdena för variabler och alla utdata som genererats till den punkten. Om ett arbets flöde slutar fungera eller har pausats startar det från den senaste kontroll punkten nästa gång den körs, i stället för att börja vid början. 

Du kan ange en kontroll punkt i ett arbets flöde med `Checkpoint-Workflow` aktiviteten. Azure Automation har en funktion som kallas [rättvis delning](automation-runbook-execution.md#fair-share), för vilken alla Runbook-flöden som körs i tre timmar är inaktiverade för att tillåta andra Runbooks att köras. Slutligen laddas den inaktiverade runbooken om. I så fall återupptas körningen från den senaste kontroll punkten som tagits i runbooken.

För att garantera att runbooken slutligen slutförs måste du lägga till kontroll punkter i intervall som körs i mindre än tre timmar. Om du lägger till en ny kontroll punkt under varje körning, och om runbooken avlägsnas efter tre timmar på grund av ett fel, återupptas runbooken under obestämd tid.

I följande exempel inträffar ett undantag efter Activity2, vilket gör att arbets flödet slutar. När arbets flödet körs igen startar det genom att köra Activity2, eftersom den här aktiviteten precis var efter den senaste kontroll punkts uppsättningen.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Ange kontroll punkter i ett arbets flöde efter aktiviteter som kan vara känsliga för undantag och som inte bör upprepas om arbets flödet återupptas. Arbets flödet kan till exempel skapa en virtuell dator. Du kan ange en kontroll punkt både före och efter kommandon för att skapa den virtuella datorn. Om det inte går att skapa, upprepas kommandona om arbets flödet startas igen. Om arbets flödet Miss lyckas när skapandet har slutförts skapas inte den virtuella datorn igen när arbets flödet återupptas.

I följande exempel kopieras flera filer till en nätverks plats och anger en kontroll punkt efter varje fil.  Om nätverks platsen tappas bort slutar arbets flödet med fel.  När den startas igen återupptas den vid den senaste kontroll punkten. Endast filer som redan har kopierats hoppas över.

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

Eftersom autentiseringsuppgifter för användar namn inte är sparade när du anropar aktiviteten [pausa – arbets flöde](/powershell/module/psworkflow/about/about_suspend-workflow) eller efter den senaste kontroll punkten, måste du ange autentiseringsuppgifterna som null och sedan hämta dem igen från till gångs lagret efter det att `Suspend-Workflow` kontroll punkten har anropats.  Annars kan du få följande fel meddelande: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Följande kod visar hur du hanterar den här situationen i dina PowerShell Workflow-Runbooks.

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
> För icke-grafiska PowerShell-Runbooks `Add-AzAccount` och `Add-AzureRMAccount` är alias för [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Du kan använda dessa cmdletar, eller så kan du [Uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto. Du behöver inte använda dessa cmdlets om du autentiserar med ett Kör som-konto som kon figurer ATS med ett huvud namn för tjänsten.

Mer information om kontroll punkter finns i [lägga till kontroll punkter i ett skript arbets flöde](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell Workflow-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook för arbets flöden](learn/automation-tutorial-runbook-textual.md).
