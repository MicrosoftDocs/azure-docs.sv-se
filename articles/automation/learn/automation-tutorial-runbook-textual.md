---
title: Skapa en PowerShell Workflow-runbook i Azure Automation
description: I den här artikeln lär du dig att skapa, testa och publicera en enkel PowerShell Workflow-runbook.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 45c7bb19e3f48fedf93fbb147a3014e09914a475
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831284"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Självstudie: Skapa en PowerShell Workflow-runbook

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell Workflow-runbook](../automation-runbook-types.md#powershell-workflow-runbooks) i Azure Automation. PowerShell Workflow-runbooks är text-runbooks som baseras Windows PowerShell Arbetsflöde. Du kan skapa och redigera koden för runbooken med hjälp av textredigeraren i Azure Portal. 

> [!div class="checklist"]
> * Skapa en enkel PowerShell Workflow-runbook
> * Testa och publicera runbooken
> * Köra och spåra status för runbook-jobbet
> * Uppdatera runbooken för att starta en virtuell Azure-dator med runbook-parametrar

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](../index.yml) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör det inte vara en virtuell produktionsdator.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook

Börja med att skapa en enkel runbook som matar ut texten `Hello World` .

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du bör också ha tillgång för autentiseringsuppgifter som är associerad med din prenumeration.
 
2. Välj **Runbooks** under **Processautomatisering** för att öppna listan över runbooks.

3. Skapa en ny runbook genom att välja **Skapa en runbook**.

4. Ge runbooken namnet **MyFirstRunbook-Workflow**.

5. I det här fallet ska du skapa en [PowerShell Workflow-runbook](../automation-runbook-types.md#powershell-workflow-runbooks). Välj **PowerShell Workflow** som **Runbook-typ.**

6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och tillgångar från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här självstudien skriver du kod direkt i runbooken.

1. Din runbook är för närvarande tom med endast det obligatoriska nyckelordet, namnet på runbooken och de `Workflow` kparenteser som omsluter hela arbetsflödet.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Skriv `Write-Output "Hello World"` mellan kparenteserna.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktion bör du testa den för att se till att den fungerar korrekt. När du testar en runbook körs utkastversionen och du kan visa dess utdata interaktivt.

1. Välj **Testfönster** för att öppna testfönstret.

2. Klicka **på** Starta för att starta testet och testa det enda aktiverade alternativet.

3. Observera att ett [runbook-jobb](../automation-runbook-execution.md) skapas och dess status visas i fönstret.

   Jobbstatusen startar som I kö, vilket indikerar att jobbet väntar på att en Runbook Worker i molnet ska bli tillgänglig. Statusen ändras till Startar när en arbetsroll gör anspråk på jobbet. Slutligen blir statusen Körs när runbooken faktiskt börjar köras.

4. När runbook-jobbet har slutförts visar testfönstret dess utdata. I det här fallet visas `Hello World` .

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Den runbook som du har skapat är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.

2. Rulla åt vänster för att visa runbooken **på sidan Runbooks** och observera att fältet **Redigeringsstatus** är inställt på **Publicerad.**

3. Rulla tillbaka till höger för att visa sidan för **MyFirstRunbook-Workflow.**

   Med alternativen längst upp kan du starta runbooken nu, schemalägga en framtida starttid eller skapa en [webhook](../automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

4. Välj **Start** och sedan **Ja när** du uppmanas att starta runbooken.

   ![Starta runbooken](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Ett jobbfönster öppnas för runbook-jobbet som har skapats. I det här fallet lämnar du fönstret öppet så att du kan se jobbets förlopp.

6. Observera att jobbstatusen visas i **Jobbsammanfattning.** Den här statusen matchar de statusar som du såg när du testade runbooken.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Skärmbild av runbook-jobbfönstret som visar avsnittet Jobbsammanfattning markerat.":::

7. När runbook-statusen visar Slutförd klickar du på **Utdata.** Sidan Utdata öppnas, där du kan se `Hello World` meddelandet.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Skärmbild av fönstret Runbook Job (Runbook-jobb) med knappen Output (Utdata) markerad.":::

8. Stäng sidan Utdata.

9. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se `Hello World` i utdataströmmen. Observera att fönstret Strömmar kan visa andra strömmar för ett runbook-jobb, till exempel utförliga dataströmmar och felströmmar, om runbooken skriver till dem.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Skärmbild av fönstret Runbook Job (Runbook-jobb) med knappen Alla loggar markerad.":::

10. Stäng fönstret Strömmar och fönstret Jobb för att återgå till sidan MyFirstRunbook.

11. Klicka **på Jobb** under **Resurser** för att öppna sidan Jobb för den här runbooken. På den här sidan visas alla jobb som skapats av din runbook. Du bör bara se ett jobb i listan eftersom du bara har kört jobbet en gång.

   ![Jobb](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Klicka på jobbnamnet för att öppna samma jobbfönster som du visade när du startade runbooken. Använd det här fönstret för att visa information om alla jobb som skapats för runbooken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det kan inte göra det om det inte autentiseras med autentiseringsuppgifterna för prenumerationen. Autentiseringen använder [cmdleten Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

>[!NOTE]
>För PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` `Connect-AzAccount` . Du kan använda dessa cmdlets eller uppdatera [dina moduler i ditt](../automation-update-azure-modules.md) Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

1. Gå till sidan MyFirstRunbook-Workflow och öppna textredigeraren genom att klicka på **Redigera.**

2. Ta bort `Write-Output` raden.

3. Placera markören på en tom rad mellan klammerparenteserna.

4. Skriv eller kopiera och klistra in följande kod, som hanterar autentiseringen med ditt Kör som-konto för Automation.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Klicka **på Testfönster** så att du kan testa runbooken.

6. Starta testet genom att klicka på **Starta**. När det är klart bör du se utdata som liknar följande, som visar grundläggande information från ditt konto. Den här åtgärden bekräftar att autentiseringssuppgifter är giltiga.

   ![Autentisera](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din runbook autentiseras för Azure-prenumerationen kan du hantera resurser. Nu ska vi lägga till ett kommando för att starta en virtuell dator. Du kan välja valfri virtuell dator i din Azure-prenumeration och för tillfället hårdkodar du namnet i runbooken. Om du hanterar resurser över flera prenumerationer måste du använda `AzContext` parametern med cmdleten [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Ange namnet och resursgruppens namn på den virtuella datorn som du vill starta genom att ange ett anrop till cmdleten [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) enligt nedan. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Spara runbooken och klicka sedan **på Testfönster** så att du kan testa den.

3. Starta testet genom att klicka på **Starta**. När den är klar kontrollerar du att den virtuella datorn har startats.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken

Din runbook startar för närvarande den virtuella dator som du har hårdkodat i runbooken. Det är mer användbart om du kan ange den virtuella datorn när runbooken startas. Nu ska vi lägga till indataparametrar i runbooken för att tillhandahålla den funktionen.

1. Lägg till variabler för parametrarna och i `VMName` `ResourceGroupName` runbooken och använd variablerna med `Start-AzVM` cmdleten enligt nedan.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som ingår i testet.

3. Stäng Testfönster.

4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.

5. Stoppa den virtuella dator som du har startat.

6. Starta runbooken genom att klicka på **Starta**. 

7. Ange värdena för **VMNAME och** **RESOURCEGROUPNAME** för den virtuella dator som du ska starta.

   ![Starta runbooken](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. När runbooken är klar kontrollerar du att den virtuella datorn har startat.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språkreferenser och inlärningsmoduler, finns i [PowerShell Docs](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
* Information om hur du kommer igång med grafiska runbooks finns [i Skapa en grafisk runbook.](automation-tutorial-runbook-graphical.md)
* Information om hur du kommer igång med PowerShell-runbooks finns [i Skapa en PowerShell-runbook.](automation-tutorial-runbook-textual-powershell.md)
* Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation olika runbook-typer.](../automation-runbook-types.md)
* Mer information om stödfunktioner för PowerShell-skript finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
