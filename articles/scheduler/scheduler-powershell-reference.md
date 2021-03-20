---
title: Referens för PowerShell-cmdletar
description: Lär dig mer om PowerShell-cmdletar för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: ccc9f709348d961e49bced00946658a6997837c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368119"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>PowerShell-cmdlets-referens för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt. 
>
> Scheduler är inte längre tillgänglig i Azure Portal, men [PowerShell-cmdletarna](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) och Azure Scheduler är tillgängliga just nu så att du kan hantera jobb och jobb samlingar.

Om du vill redigera skript för att skapa och hantera jobb i Schemaläggaren och jobb samlingar kan du använda PowerShell-cmdletar. Den här artikeln innehåller de viktigaste PowerShell-cmdletarna för Azure Scheduler med länkar till sina referens artiklar. Information om hur du installerar Azure PowerShell för din Azure-prenumeration finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/). Mer information om [Azure Resource Manager-cmdlets](/powershell/azure/)finns i [använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | Beskrivning |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Inaktiverar en jobb samling. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Aktiverar en jobb samling. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Hämtar Scheduler-jobb. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Hämtar jobb samlingar. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Hämtar jobb historik. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Skapar ett HTTP-jobb. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Skapar en jobb samling. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Skapar ett Service Bus Queue-jobb. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Skapar ett Service Bus ämne-jobb. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Skapar ett jobb i en lagringspool. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Tar bort ett Scheduler-jobb. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Tar bort en jobb samling. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Ändrar ett HTTP-jobb i Schemaläggaren. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Ändrar en jobb samling. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Ändrar ett Service Bus Queue-jobb. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Ändrar ett Service Bus ämnes jobb. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Ändrar ett jobb i en lagringspool. |
||| 

Om du vill ha mer information kan du köra någon av dessa cmdlet: ar: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)