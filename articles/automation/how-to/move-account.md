---
title: Flytta ditt Azure Automation till en annan prenumeration
description: Den här artikeln beskriver hur du flyttar ditt Automation-konto till en annan prenumeration.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fd9350baf1805c0e6278b6210ad9818fa18c8d8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831500"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Flytta ditt Azure Automation till en annan prenumeration

Azure Automation kan du flytta vissa resurser till en ny resursgrupp eller prenumeration. Du kan flytta resurser via Azure Portal, PowerShell, Azure CLI eller REST API. Mer information om processen finns i Flytta [resurser till en ny resursgrupp eller prenumeration.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

Automation-kontot är en av de resurser som du kan flytta. I den här artikeln får du lära dig att flytta Automation-konton till en annan resurs eller prenumeration. De avancerade stegen för att flytta ditt Automation-konto är:

1. Inaktivera dina funktioner.
2. Ta bort länken till din arbetsyta.
3. Flytta Automation-kontot.
4. Ta bort och skapa Kör som-kontona på nytt.
5. Återaktivera dina funktioner.

## <a name="remove-features"></a>Ta bort funktioner

Om du vill ta bort länken till arbetsytan från ditt Automation-konto måste du ta bort funktionsresurserna på arbetsytan:

- Ändringsspårning och inventering
- Uppdateringshantering
- Starta/stoppa virtuella datorer utanför arbetstid

1. Leta reda på resursgruppen på Azure-portalen.
2. Leta upp varje funktion och välj **Ta bort** på sidan **Ta bort** resurser.

    ![Skärmbild av borttagning av funktionsresurser från Azure Portal](../media/move-account/delete-solutions.png)

Om du vill kan du ta bort resurserna med hjälp av [cmdleten Remove-AzResource:](/powershell/module/Az.Resources/Remove-AzResource)

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Ta bort aviseringsregler för Starta/stoppa virtuella datorer när de inte används

För Starta/stoppa virtuella datorer när de inte används måste du också ta bort aviseringsreglerna som skapats av funktionen.

1. I Azure Portal du till resursgruppen och väljer Övervakningsaviseringar  >    >  **Hantera aviseringsregler.**

   ![Skärmbild av sidan Aviseringar som visar valet av Hantera aviseringsregler](../media/move-account/alert-rules.png)

2. På sidan Regler bör du se en lista över de aviseringar som konfigurerats i den resursgruppen. Funktionen skapar följande regler:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Välj reglerna en i taget och välj Ta bort för **att** ta bort dem.

    ![Skärmbild av sidan Regler som begär bekräftelse på borttagning för valda regler](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Om du inte ser några aviseringsregler på sidan Regler ändrar du fältet **Status till Inaktiverad** för att visa inaktiverade aviseringar.  

4. När du tar bort aviseringsreglerna måste du ta bort den åtgärdsgrupp som skapats för Starta/stoppa virtuella datorer när de inte används meddelanden. I Azure Portal du Övervaka **aviseringar**  >  **Hantera**  >  **åtgärdsgrupper.**

5. Välj **StartStop_VM_Notification**. 

6. Välj Ta bort på **åtgärdsgruppssidan.**

    ![Skärmbild av sidan Åtgärdsgrupp](../media/move-account/delete-action-group.png)

Om du vill kan du ta bort åtgärdsgruppen med hjälp av [cmdleten Remove-AzActionGroup:](/powershell/module/az.monitor/remove-azactiongroup)

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Ta bort länk till din arbetsyta

Nu kan du ta bort länken till arbetsytan:

1. I den Azure Portal väljer du **Automation-konto**  >  **Relaterade resurser**  >  **Länkad arbetsyta**. 

2. Välj **Ta bort länk till arbetsyta** för att ta bort länken till arbetsytan från ditt Automation-konto.

    ![Skärmbild av avlänkning av en arbetsyta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta ditt Automation-konto

Nu kan du flytta ditt Automation-konto och dess runbooks. 

1. I Azure Portal bläddrar du till resursgruppen för ditt Automation-konto. Välj **Flytta flytta** till en annan  >  **prenumeration.**

    ![Skärmbild av sidan Resursgrupp, flytta till en annan prenumeration](../media/move-account/move-resources.png)

2. Välj de resurser i resursgruppen som du vill flytta. Se till att du inkluderar dina Automation-konton, runbooks och Log Analytics-arbetsyteresurser.

## <a name="re-create-run-as-accounts"></a>Skapa Kör som-konton på nytt

[Kör som-konton](../automation-security-overview.md#run-as-accounts) skapar ett huvudnamn för tjänsten i Azure Active Directory autentisera med Azure-resurser. När du ändrar prenumerationer använder Automation-kontot inte längre det befintliga Kör som-kontot. Så här skapar du Kör som-kontona på nytt:

1. Gå till ditt Automation-konto i den nya prenumerationen och välj **Kör som-konton** under **Kontoinställningar.** Du ser nu att Kör som-kontona visas som ofullständiga.

    ![Skärmbild av Kör som-konton som visar ofullständiga](../media/move-account/run-as-accounts.png)

2. Ta bort Kör som-konton, ett i taget, genom att **välja Ta** bort på **sidan** Egenskaper. 

    > [!NOTE]
    > Om du inte har behörighet att skapa eller visa Kör som-konton visas följande meddelande: Mer information finns i Behörigheter som krävs för att konfigurera `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` [Kör som-konton.](../automation-security-overview.md#permissions)

3. När du har tagit bort Kör som-kontona väljer du **Skapa** under **Kör som-konto i Azure.** 

4. På sidan Lägg till Kör som-konto i Azure väljer du **Skapa för** att skapa Kör som-kontot och tjänstens huvudnamn. 

5. Upprepa stegen ovan med det klassiska Kör som-kontot i Azure.

## <a name="enable-features"></a>Aktivera funktioner

När du har skapat Kör som-kontona på nytt måste du återaktivera de funktioner som du har tagit bort innan du flyttar:

1. Om du vill aktivera Ändringsspårning och inventering väljer du **Ändringsspårning och inventering på** ditt Automation-konto. Välj den Log Analytics-arbetsyta som du flyttade över och välj **Aktivera**.

2. Upprepa steg 1 för Uppdateringshantering.

    ![Skärmbild av Återaktivera funktioner i det flyttade Automation-kontot](../media/move-account/reenable-solutions.png)

3. Datorer som aktiveras med dina funktioner visas när du har anslutit den befintliga Log Analytics-arbetsytan. Om du vill Starta/stoppa virtuella datorer när de inte används-funktionen måste du återaktivera den. Under **Relaterade resurser** väljer du **Starta/stoppa virtuella datorer Läs** mer om och aktiverar lösningen  >  **Skapa**  >  **för** att starta distributionen.

4. På sidan Lägg till lösning väljer du Log Analytics-arbetsytan och Automation-kontot.

    ![Skärmbild av menyn Lägg till lösning](../media/move-account/add-solution-vm.png)

5. Konfigurera funktionen enligt beskrivningen i [Starta/stoppa virtuella datorer när de inte används översikt](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verifiera flytten

När flytten är klar kontrollerar du att funktionerna i listan nedan är aktiverade. 

|Funktion|Tester|Felsökning|
|---|---|---|
|Runbooks|En runbook kan köra och ansluta till Azure-resurser.|[Felsöka runbook-flöden](../troubleshoot/runbooks.md)
|Källkontroll|Du kan köra en manuell synkronisering på lagringsplatsen för källkontroll.|[Källkontrollsintegrering](../source-control-integration.md)|
|Ändringsspårning och inventering|Kontrollera att du ser aktuella inventeringsdata från dina datorer.|[Felsöka ändringsspårning](../troubleshoot/change-tracking.md)|
|Hantering av uppdateringar|Kontrollera att du ser dina datorer och att de är felfria.</br>Kör en testdistribution av programuppdatering.|[Felsöka uppdateringshantering](../troubleshoot/update-management.md)|
|Delade resurser|Kontrollera att du ser alla dina delade resurser, till exempel [autentiseringsuppgifter](../shared-resources/credentials.md) [och variabler](../shared-resources/variables.md).|

## <a name="next-steps"></a>Nästa steg

Mer information om hur du flyttar resurser i Azure finns [i Flytta resurser i Azure.](../../azure-resource-manager/management/move-support-resources.md)
