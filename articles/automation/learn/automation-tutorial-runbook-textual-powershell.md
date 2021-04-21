---
title: Skapa en PowerShell-runbook i Azure Automation
description: I den här artikeln lär du dig att skapa, testa och publicera en enkel PowerShell-runbook.
keywords: azure powershell, självstudier i powershell-skript, powershell-automation
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 958cbf8b7b03e2dd9e4b0e03ba178ffe180bfb48
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831266"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Självstudie: Skapa en PowerShell-runbook

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](../automation-runbook-types.md#powershell-runbooks) i Azure Automation. PowerShell-runbooks baseras på Windows PowerShell. Du kan redigera koden för runbooken direkt med hjälp av textredigeraren i Azure Portal.

> [!div class="checklist"]
> * Skapa en enkel PowerShell-runbook
> * Testa och publicera runbooken
> * Köra och spåra status för runbook-jobbet
> * Uppdatera runbooken för att starta en virtuell Azure-dator med runbook-parametrar

## <a name="prerequisites"></a>Förutsättningar

För att göra den här självstudien behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](../automation-quickstart-create-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör det inte vara en virtuell produktionsdator.
* Om det behövs [importerar du Azure-moduler](../shared-resources/modules.md) [eller uppdateringsmoduler](../automation-update-azure-modules.md) baserat på de cmdlets som du använder.

## <a name="differences-from-powershell-workflow-runbooks"></a>Skillnader från PowerShell Workflow-runbooks

PowerShell-runbooks har samma livscykel, funktioner och hantering som PowerShell Workflow-runbooks. Det finns dock vissa skillnader och begränsningar.

| Egenskap  | PowerShell Runbooks | Runbook-flöden för PowerShell-arbetsflöden |
| ------ | ----- | ----- |
| Hastighet | Kör snabbt eftersom de inte använder ett kompileringssteg. | Körs långsammare. |
| Kontrollpunkter | Stöder inte kontrollpunkter. En PowerShell-runbook kan bara återuppta åtgärden från början. | Använd kontrollpunkter, vilket gör att en arbetsbok kan återuppta åtgärden från valfri punkt. |
| Kommandokörning | Stöder endast seriell körning. | Stöd för både seriell och parallell körning.|
| Runspace | Ett enda körningsutrymme kör allt i ett skript. | Ett separat körningsutrymme kan användas för en aktivitet, ett kommando eller ett skriptblock. |

Förutom dessa skillnader har PowerShell-runbooks vissa [syntaktiska skillnader](/previous-versions/technet-magazine/dn151046(v=msdn.10)) från PowerShell Workflow-runbooks.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook

Börja med att skapa en enkel runbook som matar ut texten `Hello World` .

1. Öppna ditt Automation-konto på Azure Portal.

2. Välj **Runbooks** under **Processautomatisering** för att öppna listan över runbooks.

3. Skapa en ny runbook genom att välja **Skapa en runbook**.

4. Ge runbooken namnet **MyFirstRunbook-PowerShell**.

5. I det här fallet ska du skapa en [PowerShell-runbook](../automation-runbook-types.md#powershell-runbooks). Välj **PowerShell** som **Runbook-typ.**

6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här självstudien skriver du kod direkt i runbooken.

1. Din runbook är tom för närvarande. Skriv `Write-Output "Hello World"` in brödtexten i skriptet.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktion bör du testa den för att se till att den fungerar korrekt. När du testar en runbook körs utkastversionen och du kan visa dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.

2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.

3. Observera att ett [runbook-jobb](../automation-runbook-execution.md) skapas och dess status visas i fönstret.

   Jobbstatusen startar som I kö, vilket anger att jobbet väntar på att en Runbook Worker i molnet ska bli tillgänglig. Statusen ändras till Startar när en arbetsroll gör anspråk på jobbet. Slutligen blir statusen Körs när runbooken faktiskt börjar köras.

4. När runbook-jobbet har slutförts visar fönstret Test dess utdata. I det här fallet visas `Hello World` .

   ![Utdata i testfönstret](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Runbooken som du har skapat är fortfarande i utkastläge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.

2. Rulla åt vänster för att visa runbooken på sidan Runbooks och observera att **värdet Redigeringsstatus** är inställt på **Publicerad.**

3. Rulla tillbaka till höger för att visa sidan för **MyFirstRunbook-PowerShell.**
   
   Med alternativen längst upp kan du starta runbooken nu, schemalägga en framtida starttid eller skapa en [webhook](../automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

4. Välj **Start** och sedan **Ja när** du uppmanas att starta runbooken. 

5. Ett jobbfönster öppnas för runbook-jobbet som har skapats. Även om du kan stänga det här fönstret lämnar du det öppet just nu så att du kan titta på jobbets förlopp. Jobbstatusen visas i **Jobbsammanfattning** och möjliga statusar beskrivs för att testa runbooken.

   ![Jobbsammanfattning](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. När runbook-statusen är Slutförd klickar du **på Utdata** för att öppna sidan Utdata, där du kan `Hello World` se den.

   ![Jobbutdata](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Stäng sidan Utdata.

8. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se `Hello World` i utdataströmmen.

    Observera att fönstret Strömmar kan visa andra strömmar för ett runbook-jobb, till exempel utförliga dataströmmar och felströmmar, om runbooken skriver till dem.

   ![Alla loggar](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Stäng fönstret Strömmar och fönstret Jobb för att återgå till MyFirstRunbook-PowerShell sidan.

10. Under **Information** klickar du **på Jobb** för att öppna sidan Jobb för den här runbooken. På den här sidan visas alla jobb som skapats av din runbook. Du bör bara se ett jobb i listan eftersom du bara har kört jobbet en gång.

   ![Jobblista](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Klicka på jobbnamnet för att öppna samma jobbfönster som du visade när du startade runbooken. Använd det här fönstret för att visa information om alla jobb som skapats för runbooken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Om du vill göra detta måste runbooken kunna autentisera med hjälp av det Kör som-konto som skapades automatiskt när du skapade ditt Automation-konto.

Som du ser i exemplet nedan görs Kör som-anslutningen med cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Om du hanterar resurser över flera prenumerationer måste du använda `AzContext` parametern med [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> För PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` `Connect-AzAccount` . Du kan använda dessa cmdlets eller uppdatera [dina moduler i ditt](../automation-update-azure-modules.md) Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. Öppna textredigeraren genom att klicka **på** Redigera MyFirstRunbook-PowerShell sidan.

2. Du behöver inte raden `Write-Output` längre. Det är bara att ta bort den.

3. Skriv eller kopiera och klistra in följande kod, som hanterar autentiseringen med ditt Kör som-konto för Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Klicka **på Testfönster** så att du kan testa runbooken.

5. Starta testet genom att klicka på **Starta**. När det är klart bör du se utdata som liknar följande och som visar grundläggande information från ditt konto. Dessa utdata bekräftar att Kör som-kontot är giltigt.

   ![Autentisera](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din runbook autentiseras för din Azure-prenumeration kan du hantera resurser. Nu ska vi lägga till ett kommando för att starta en virtuell dator. Du kan välja valfri virtuell dator i din Azure-prenumeration och bara hårdkoda det namnet i runbooken för tillfället.

1. I runbook-skriptet lägger du till [cmdleten Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) för att starta den virtuella datorn. Som du ser nedan startar cmdleten en virtuell dator med namnet `VMName` och med en resursgrupp med namnet `ResourceGroupName` .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Spara runbooken och klicka sedan **på Testfönster** så att du kan testa den.

3. Klicka **på Starta** för att starta testet. När den är klar kontrollerar du att den virtuella datorn har startats.

## <a name="step-7---add-an-input-parameter"></a>Steg 7 – Lägg till en indataparameter

Din runbook startar för närvarande den virtuella dator som du hårdkodade i runbooken. Runbooken är mer användbar om du anger den virtuella datorn när runbooken startas. Nu ska vi lägga till indataparametrar i runbooken för att tillhandahålla den funktionen.

1. I textredigeraren ändrar du `Start-AzVM` cmdleten så att den använder variabler för parametrarna `VMName` och `ResourceGroupName` . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som du använder i testet.

3. Stäng Testfönster.

4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.

5. Stoppa den virtuella datorn som du startade tidigare.

6. Starta runbooken genom att klicka på **Starta**. 

7. Ange värdena för **VMNAME och** **RESOURCEGROUPNAME** för den virtuella dator som du ska starta och klicka sedan på **OK.**

    ![Skicka parameter](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. När runbooken är klar ser du till att den virtuella datorn har startats.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språkreferenser och inlärningsmoduler, finns i [PowerShell Docs](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
* Information om hur du kommer igång med grafiska runbooks finns [i Skapa en grafisk runbook.](automation-tutorial-runbook-graphical.md)
* Information om hur du kommer igång med PowerShell Workflow-runbooks finns i [Skapa en PowerShell Workflow-runbook.](automation-tutorial-runbook-textual.md)
* Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation runbook-typer.](../automation-runbook-types.md)
* Mer information om stödfunktionen för PowerShell-skript finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
