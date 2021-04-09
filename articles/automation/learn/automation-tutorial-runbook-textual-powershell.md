---
title: Skapa en PowerShell-Runbook i Azure Automation
description: I den här artikeln lär du dig att skapa, testa och publicera en enkel PowerShell-Runbook.
keywords: azure powershell, självstudier i powershell-skript, powershell-automation
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 896b4db433164471f41aa09791ede5d677028bfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896620"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Självstudie: skapa en PowerShell-Runbook

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](../automation-runbook-types.md#powershell-runbooks) i Azure Automation. PowerShell-Runbooks baseras på Windows PowerShell. Du kan redigera koden för runbooken direkt med hjälp av text redigeraren i Azure Portal.

> [!div class="checklist"]
> * Skapa en enkel PowerShell-Runbook
> * Testa och publicera runbooken
> * Kör och spåra statusen för Runbook-jobbet
> * Uppdatera runbooken för att starta en virtuell Azure-dator med Runbook-parametrar

## <a name="prerequisites"></a>Förutsättningar

För att göra den här självstudien behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](../automation-quickstart-create-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör den inte vara en virtuell produktions dator.
* Om det behövs [importerar du Azure-moduler](../shared-resources/modules.md) eller [uppdaterar moduler](../automation-update-azure-modules.md) baserat på de cmdletar som du använder.

## <a name="differences-from-powershell-workflow-runbooks"></a>Skillnader jämfört med PowerShell Workflow-Runbooks

PowerShell-Runbooks har samma livs cykel, funktioner och hantering som PowerShell Workflow-Runbooks. Det finns dock vissa skillnader och begränsningar.

| Egenskap  | PowerShell-Runbooks | PowerShell Workflow-Runbooks |
| ------ | ----- | ----- |
| Hastighet | Kör snabbt eftersom de inte använder ett kompilerings steg. | Kör långsammare. |
| Kontrollpunkter | Stöder inte kontroll punkter. En PowerShell-Runbook kan bara återuppta åtgärden från början. | Använd kontroll punkter, vilket gör att en arbets bok kan fortsätta att fungera från vilken punkt som helst. |
| Kommando körning | Endast stöd för seriell körning. | Stöd för både seriell och parallell körning.|
| Körnings utrymme | En enskild körnings utrymme kör allt i ett skript. | En separat körnings utrymme kan användas för en aktivitet, ett kommando eller ett skript block. |

Utöver dessa skillnader har PowerShell-Runbooks vissa [syntaktiska skillnader](/previous-versions/technet-magazine/dn151046(v=msdn.10)) jämfört med PowerShell Workflow-Runbooks.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook

Börja med att skapa en enkel Runbook som matar ut texten `Hello World` .

1. Öppna ditt Automation-konto på Azure Portal.

2. Öppna listan över runbooks genom att välja **Runbooks** under **process automatisering** .

3. Skapa en ny Runbook genom att välja **skapa en Runbook**.

4. Ge runbooken namnet **MyFirstRunbook-PowerShell**.

5. I det här fallet ska du skapa en PowerShell- [Runbook](../automation-runbook-types.md#powershell-runbooks). Välj **PowerShell** som **Runbook-typ**.

6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här självstudien kommer du att skriva kod direkt i runbooken.

1. Din Runbook är tom för närvarande. Skriv `Write-Output "Hello World"` in skriptets brödtext.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> Steg 3 – testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktion bör du testa den och kontrol lera att den fungerar som den ska. Om du testar en runbook körs dess utkast version och du kan visa dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.

2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.

3. Observera att ett [Runbook-jobb](../automation-runbook-execution.md) skapas och att dess status visas i fönstret.

   Jobbets status börjar i kö, vilket anger att jobbet väntar på att en Runbook Worker i molnet ska bli tillgänglig. Statusen ändras till att börja när en arbets uppgift anlitar jobbet. Slutligen blir statusen igång när runbooken faktiskt börjar köras.

4. När Runbook-jobbet har slutförts visas utdata i test fönstret. I det här fallet visas `Hello World` .

   ![Utdata i testfönstret](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Den Runbook som du har skapat är fortfarande i utkast läge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.

2. Rulla åt vänster för att Visa runbooken på sidan Runbooks och Observera att status värdet för **redigering** är inställt på **publicerat**.

3. Rulla tillbaka till höger för att visa sidan för **MyFirstRunbook – PowerShell**.
   
   Med alternativen över överst kan du starta runbooken nu, schemalägga en framtida start tid eller skapa en [webhook](../automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

4. Välj **Start** och sedan **Ja** när du uppmanas att starta runbooken. 

5. Ett jobb fönster öppnas för det Runbook-jobb som har skapats. Även om du kan stänga det här fönstret, lämna det öppet just nu så att du kan se jobbets förlopp. Jobbets status visas i **jobb Sammanfattning** och möjliga statusar beskrivs för att testa runbooken.

   ![Jobbsammanfattning](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. När Runbook-statusen har slutförts klickar du på **utdata** för att öppna sidan utdata där du kan se den `Hello World` .

   ![Jobbutdata](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Stäng sidan utdata.

8. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se `Hello World` i utdataströmmen.

    Observera att fönstret strömmar kan visa andra strömmar för ett Runbook-jobb, till exempel utförliga data strömmar och fel strömmar, om Runbook skriver till dem.

   ![Alla loggar](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Stäng fönstret strömmar och fönstret jobb för att återgå till MyFirstRunbook-PowerShell sidan.

10. Klicka på **jobb** under **information** för att öppna sidan jobb för denna Runbook. Den här sidan visar alla jobb som har skapats av din Runbook. Du bör bara se ett jobb i listan eftersom du bara kör jobbet en gång.

   ![Jobblista](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Klicka på jobb namnet för att öppna samma jobb fönster som du visade när du startade runbooken. Använd det här fönstret om du vill visa information om alla jobb som har skapats för runbooken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. För att göra detta måste runbooken kunna autentisera med kör som-kontot som skapades automatiskt när du skapade ditt Automation-konto.

Som du ser i exemplet nedan görs kör som-anslutningen med cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Om du hanterar resurser över flera prenumerationer måste du använda `AzContext` parametern med [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> För PowerShell-Runbooks `Add-AzAccount` och `Add-AzureRMAccount` är alias för `Connect-AzAccount` . Du kan använda dessa cmdletar, eller så kan du [Uppdatera dina moduler](../automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

1. Öppna text redigeraren genom att klicka på **Redigera** på sidan MyFirstRunbook-PowerShell.

2. Du behöver inte `Write-Output` längre linjen. Du behöver bara gå vidare och ta bort den.

3. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt kör som-konto för Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

4. Klicka på **test fönster** så att du kan testa runbooken.

5. Starta testet genom att klicka på **Starta**. När den är klar bör du se utdata som liknar följande och visa grundläggande information från ditt konto. Det här resultatet bekräftar att kör som-kontot är giltigt.

   ![Autentisera](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din Runbook autentiseras till din Azure-prenumeration kan du hantera resurser. Nu ska vi lägga till ett kommando för att starta en virtuell dator. Du kan välja en virtuell dator i din Azure-prenumeration och bara hårdkoda det namnet i runbooken för tillfället.

1. I Runbook-skriptet lägger du till cmdleten [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) för att starta den virtuella datorn. Som det visas nedan startar cmdleten en virtuell dator med namnet `VMName` och med en resurs grupp med namnet `ResourceGroupName` .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

2. Spara runbooken och klicka sedan på **test fönster** så att du kan testa den.

3. Starta testet genom att klicka på **Starta** . När den är klar kontrollerar du att den virtuella datorn har startats.

## <a name="step-7---add-an-input-parameter"></a>Steg 7 – lägga till en indataparameter

Din Runbook startar för närvarande den virtuella datorn som du hårdkodade i runbooken. Runbooken är mer användbar om du anger den virtuella datorn när runbooken startas. Nu ska vi lägga till indataparametrar till Runbook för att tillhandahålla den funktionen.

1. Ändra cmdleten i text redigeraren för `Start-AzVM` att använda variabler för parametrarna `VMName` och `ResourceGroupName` . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

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

7. Skriv värdena för **VMNAME** och **RESOURCEGROUPNAME** för den virtuella dator som du ska starta och klicka sedan på **OK**.

    ![Skicka parameter](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. När Runbook-flödet har slutförts kontrollerar du att den virtuella datorn har startats.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
* Information om hur du kommer igång med grafiska runbooks finns i [skapa en grafisk Runbook](automation-tutorial-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell Workflow-Runbooks finns i [skapa en PowerShell Workflow-Runbook](automation-tutorial-runbook-textual.md).
* Om du vill veta mer om Runbook-typer och deras fördelar och begränsningar, se [Azure Automation Runbook-typer](../automation-runbook-types.md).
* Mer information om stöd funktionen för PowerShell-skript finns [i stöd för inbyggda PowerShell-skript i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
