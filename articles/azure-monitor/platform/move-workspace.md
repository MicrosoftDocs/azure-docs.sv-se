---
title: Flytta en Log Analytics arbets yta i Azure Monitor | Microsoft Docs
description: Lär dig hur du flyttar din Log Analytics-arbetsyta till en annan prenumeration eller resurs grupp.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.openlocfilehash: 4915fd69940450f22548ae326879b5729b687248
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942084"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Flytta en Log Analytics arbets yta till en annan prenumeration eller resurs grupp

I den här artikeln lär du dig hur du flyttar Log Analytics arbets ytan till en annan resurs grupp eller prenumeration i samma region. Du kan lära dig mer om att flytta Azure-resurser via Azure Portal, PowerShell, Azure CLI eller REST API. [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Du kan inte flytta en arbets yta till en annan region.

## <a name="verify-active-directory-tenant"></a>Verifiera Active Directory klient
Käll-och mål prenumerationer måste finnas i samma Azure Active Directory-klient. Använd Azure PowerShell för att kontrol lera att båda prenumerationerna har samma klient-ID.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Överväganden vid arbets ytans flyttning
- Hanterade lösningar som är installerade på arbets ytan flyttas med flytt åtgärden Log Analytics-arbetsyta. 
- Nycklar för arbets ytor (både primära och sekundära) återskapas med flytt av arbets ytan. Om du behåller en kopia av dina arbets ytans nycklar i Key Vault uppdaterar du dem med de nya nycklarna som skapas när arbets ytan flyttas. 
- Anslutna agenter förblir anslutna och fortsätter att skicka data till arbets ytan efter flytten. 
- Eftersom flytt åtgärden kräver att det inte finns några länkade tjänster från arbets ytan, måste lösningar som förlitar sig på den länken tas bort så att arbets ytan flyttas. Lösningar som måste tas bort innan du kan ta bort länken till ditt Automation-konto:
  - Uppdateringshantering
  - Spårning av ändringar
  - Starta/stoppa virtuella datorer utanför arbetstid
  - Azure Security Center

>[!IMPORTANT]
> **Azure Sentinel-kunder**
> - För närvarande stöds inte att flytta arbets ytan till en annan resurs grupp eller prenumeration efter att Azure Sentinel har distribuerats på en arbets yta. 
> - Om du redan har flyttat arbets ytan inaktiverar du alla aktiva regler under **analyser** och aktiverar dem igen efter fem minuter. Detta bör vara en effektiv lösning i de flesta fall, men för att kunna upprepas, stöds den inte och görs på egen risk.
> 
> **Återskapa aviseringar**
> - Alla aviseringar måste återskapas efter en flyttning eftersom behörigheterna baseras på arbets ytans Azure-resurs-ID, som ändras när en arbets yta flyttas.
>
> **Uppdatera resurs Sök vägar**
> - När en arbets yta flyttas måste alla Azure-eller externa resurser som pekar på arbets ytan granskas och uppdateras så att de pekar på den nya resurs mål Sök vägen.
> 
>   *Exempel:*
>   - [Azure Monitor varnings regler](alerts-resource-move.md)
>   - Program från tredje part
>   - Anpassat skript
>

### <a name="delete-solutions-in-azure-portal"></a>Ta bort lösningar i Azure Portal
Använd följande procedur för att ta bort lösningarna med hjälp av Azure Portal:

1. Öppna menyn för resurs gruppen som alla lösningar är installerade i.
2. Välj de lösningar som ska tas bort.
3. Klicka på **ta bort resurser** och bekräfta sedan de resurser som ska tas bort genom att klicka på **ta bort**.

![Ta bort lösningar](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Ta bort med PowerShell

Om du vill ta bort lösningarna med PowerShell använder du cmdleten [Remove-AzResource](/powershell/module/az.resources/remove-azresource) som visas i följande exempel:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Ta bort aviserings regler för lösningen starta/stoppa virtuella datorer
Om du vill ta bort lösningen för att **Starta/stoppa virtuella datorer** måste du också ta bort de aviserings regler som skapats av lösningen. Använd följande procedur i Azure Portal för att ta bort dessa regler.

1. Öppna menyn **övervakare** och välj sedan **aviseringar**.
2. Klicka på **Hantera aviserings regler**.
3. Välj följande tre varnings regler och klicka sedan på **ta bort**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Ta bort regler](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Ta bort länk för Automation-konto
Använd följande procedur för att ta bort länken till Automation-kontot från arbets ytan med hjälp av Azure Portal:

1. Öppna menyn **Automation-konton** och välj sedan det konto som ska tas bort.
2. I avsnittet **relaterade resurser** på menyn väljer du **länkad arbets yta**. 
3. Klicka på länken för att **ta bort länkar** till arbets ytan från ditt Automation-konto.

    ![Ta bort arbetsytans länk](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Flytta din arbets yta

### <a name="azure-portal"></a>Azure-portalen
Använd följande procedur för att flytta din arbets yta med Azure Portal:

1. Öppna menyn **Log Analytics arbets ytor** och välj sedan din arbets yta.
2. På sidan **Översikt** klickar du på **ändra** bredvid antingen **resurs grupp** eller **prenumeration**.
3. En ny sida öppnas med en lista över resurser som är relaterade till arbets ytan. Välj de resurser som du vill flytta till samma mål prenumeration och resurs grupp som arbets ytan. 
4. Välj en mål **prenumeration** och **resurs grupp**. Om du flyttar arbets ytan till en annan resurs grupp i samma prenumeration visas inte **prenumerations** alternativet.
5. Klicka på **OK** för att flytta arbets ytan och valda resurser.

    ![Skärm bild som visar översikts fönstret i arbets ytan Log Analytics med alternativ för att ändra resurs grupp och prenumerations namn.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Om du vill flytta din arbets yta med PowerShell använder du [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) som i följande exempel:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> Efter flyttningen bör borttagna lösningar och Automation-konto-länken konfigureras om så att arbets ytan återställs till sitt tidigare tillstånd.


## <a name="next-steps"></a>Nästa steg
- En lista över vilka resurser som stöder flytta finns i [Flytta åtgärds stöd för resurser](../../azure-resource-manager/management/move-support-resources.md).
