---
title: Flytta ditt Azure Automation-konto till en annan prenumeration
description: Den här artikeln beskriver hur du flyttar ditt Automation-konto till en annan prenumeration.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: a86d876a723c89eb8dcdf18c8318f2a9c740a229
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051032"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Flytta ditt Azure Automation-konto till en annan prenumeration

Med Azure Automation kan du flytta vissa resurser till en ny resurs grupp eller prenumeration. Du kan flytta resurser via Azure Portal, PowerShell, Azure CLI eller REST API. Mer information om processen finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Automation-kontot är en av de resurser som du kan flytta. I den här artikeln lär du dig att flytta Automation-konton till en annan resurs eller prenumeration. De övergripande stegen för att flytta ditt Automation-konto är:

1. Inaktivera dina funktioner.
2. Ta bort länken till arbets ytan.
3. Flytta Automation-kontot.
4. Ta bort och återskapa kör som-konton.
5. Återaktivera dina funktioner.

## <a name="remove-features"></a>Ta bort funktioner

Om du vill ta bort länken till arbets ytan från ditt Automation-konto måste du ta bort funktions resurserna på arbets ytan:

- Ändringsspårning och inventering
- Uppdateringshantering
- Starta/stoppa virtuella datorer utanför arbetstid

1. Leta reda på resursgruppen på Azure-portalen.
2. Hitta varje funktion och välj **ta bort** på sidan **ta bort resurser** .

    ![Skärm bild som visar hur du tar bort funktions resurser från Azure Portal](../media/move-account/delete-solutions.png)

Om du vill kan du ta bort resurserna med hjälp av cmdleten [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource) :

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Ta bort aviserings regler för Starta/stoppa virtuella datorer när de inte används

För Starta/stoppa virtuella datorer när de inte används måste du också ta bort aviserings reglerna som skapats av funktionen.

1. I Azure Portal går du till din resurs grupp och väljer **övervaknings**  >  **aviseringar**  >  **Hantera aviserings regler**.

   ![Skärm bild av sidan aviseringar med val av hantera aviserings regler](../media/move-account/alert-rules.png)

2. På sidan regler bör du se en lista över de aviseringar som kon figurer ATS i resurs gruppen. Funktionen skapar följande regler:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Välj reglerna en i taget och välj **ta bort** för att ta bort dem.

    ![Skärm bild av sidan regler, begär bekräftelse av borttagning för de valda reglerna](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Om du inte ser några varnings regler på sidan regler ändrar du fältet **status** till **inaktiverat** för att visa inaktiverade aviseringar. 

4. När du tar bort aviserings reglerna måste du ta bort åtgärds gruppen som skapats för Starta/stoppa virtuella datorer när de inte används meddelanden. I Azure Portal väljer du **övervaka**  >  **aviseringar**  >  **Hantera åtgärds grupper**.

5. Välj **StartStop_VM_Notification**. 

6. På sidan åtgärds grupp väljer du **ta bort**.

    ![Skärm bild av sidan åtgärds grupp](../media/move-account/delete-action-group.png)

Om du vill kan du ta bort din åtgärds grupp med hjälp av cmdleten [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup) :

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Ta bort länk till arbets ytan

Nu kan du ta bort länken till arbets ytan:

1. I Azure Portal väljer du **Automation-konto**  >  **relaterade resurser**  >  **länkad arbets yta**. 

2. Välj **länken för att ta bort länkar** för arbets ytan från ditt Automation-konto.

    ![Skärm bild av avlänkning av en arbets yta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta ditt Automation-konto

Nu kan du flytta ditt Automation-konto och dess Runbooks. 

1. I Azure Portal bläddrar du till resurs gruppen för ditt Automation-konto. Välj **Flytta**  >  **Flytta till en annan prenumeration**.

    ![Skärm bild av sidan resurs grupp, flytta till en annan prenumeration](../media/move-account/move-resources.png)

2. Välj de resurser i resurs gruppen som du vill flytta. Se till att du inkluderar ditt Automation-konto, Runbooks och Log Analytics arbets ytans resurser.

## <a name="re-create-run-as-accounts"></a>Återskapa kör som-konton

[Kör som-konton](../automation-security-overview.md#run-as-accounts) skapa ett huvud namn för tjänsten i Azure Active Directory för att autentisera med Azure-resurser. När du ändrar prenumerationer använder Automation-kontot inte längre det befintliga kör som-kontot. Skapa Kör som-konton på nytt:

1. Gå till ditt Automation-konto i den nya prenumerationen och välj **Kör som-konton** under **konto inställningar**. Du ser att kör som-kontona visas som ofullständiga nu.

    ![Skärm bild av kör som-konton som visar ofullständiga](../media/move-account/run-as-accounts.png)

2. Ta bort kör som-kontona, ett i taget, genom att välja **ta bort** på sidan **Egenskaper** . 

    > [!NOTE]
    > Om du inte har behörighet att skapa eller Visa kör som-konton visas följande meddelande: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Mer information finns i [behörigheter som krävs för att konfigurera kör som-konton](../automation-security-overview.md#permissions).

3. När du har tagit bort kör som-kontona väljer du **skapa** under **Kör som-konto i Azure**. 

4. På sidan Lägg till Azure kör som-konto väljer du **skapa** för att skapa kör som-kontot och tjänstens huvud namn. 

5. Upprepa stegen ovan med det klassiska kör som-kontot i Azure.

## <a name="enable-features"></a>Aktivera funktioner

När du har återskapat kör som-kontona måste du återaktivera de funktioner som du har tagit bort innan du flyttar:

1. Aktivera Ändringsspårning och inventering genom att välja **ändringsspårning och inventering** i ditt Automation-konto. Välj Log Analytics arbets ytan som du flyttade över och välj **Aktivera**.

2. Upprepa steg 1 för Uppdateringshantering.

    ![Skärm bild som visar hur du återaktiverar funktioner i det flyttade Automation-kontot](../media/move-account/reenable-solutions.png)

3. Datorer som är aktiverade med dina funktioner visas när du har anslutit den befintliga Log Analytics-arbetsytan. Om du vill aktivera funktionen Starta/stoppa virtuella datorer när de inte används måste du aktivera den igen. Under **relaterade resurser** väljer du **Starta/stoppa virtuella datorer**  >  **Läs mer om och aktiverar lösningen**  >  **skapa** för att starta distributionen.

4. På sidan Lägg till lösning väljer du Log Analytics arbets yta och Automation-konto.

    ![Skärm bild av menyn Lägg till lösning](../media/move-account/add-solution-vm.png)

5. Konfigurera funktionen enligt beskrivningen i [Starta/stoppa virtuella datorer när de inte används översikt](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verifiera flytten

När flyttningen är klar kontrollerar du att funktionerna i listan nedan är aktiverade. 

|Funktion|Tester|Felsökning|
|---|---|---|
|Runbooks|En Runbook kan köra och ansluta till Azure-resurser.|[Felsöka runbook-flöden](../troubleshoot/runbooks.md)
|Källkontroll|Du kan köra en manuell synkronisering på käll kontrollens lagrings plats.|[Källkontrollsintegrering](../source-control-integration.md)|
|Ändringsspårning och inventering|Kontrol lera att du ser aktuella inventerings data från datorerna.|[Felsöka ändrings spårning](../troubleshoot/change-tracking.md)|
|Hantering av uppdateringar|Kontrol lera att du ser dina datorer och att de är felfria.</br>Kör en test program uppdaterings distribution.|[Felsöka uppdaterings hantering](../troubleshoot/update-management.md)|
|Delade resurser|Kontrol lera att du ser alla dina delade resurser, t. ex. [autentiseringsuppgifter](../shared-resources/credentials.md) och [variabler](../shared-resources/variables.md).|

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du flyttar resurser i Azure i [Flytta resurser i Azure](../../azure-resource-manager/management/move-support-resources.md).
