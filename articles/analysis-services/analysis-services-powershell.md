---
title: Hantera Azure Analysis Services med PowerShell | Microsoft Docs
description: Beskriver Azure Analysis Services PowerShell-cmdletar för vanliga administrativa uppgifter som att skapa servrar, pausa åtgärder eller ändra service nivå.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28f414c5eaaea7b987f2c3694cb8fc73b70838e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018788"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Hantera Azure Analysis Services med PowerShell

I den här artikeln beskrivs PowerShell-cmdletar som används för att utföra Azure Analysis Services server-och databas hanterings uppgifter. 

Hanterings uppgifter för server resurser som att skapa eller ta bort en server, pausa eller återuppta Server åtgärder, eller ändra service nivån (nivån) använder Azure Analysis Services-cmdletar. Andra aktiviteter för att hantera databaser som att lägga till eller ta bort roll medlemmar, bearbetning eller partitionering använder cmdletar som ingår i samma SqlServer-modul som SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Behörigheter

De flesta PowerShell-aktiviteter kräver att du har administratörs behörighet på den Analysis Services server som du hanterar. Schemalagda PowerShell-aktiviteter är obevakade åtgärder. Kontot eller tjänstens huvud namn som kör Scheduler måste ha administratörs behörighet på Analysis Servicess servern. 

För Server åtgärder med hjälp av Azure PowerShell-cmdletar måste ditt konto eller kontot som kör Scheduler också tillhöra ägar rollen för resursen i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Resurs-och Server åtgärder 

Installera modul- [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentation – [referens för AZ. AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databasanvändning

Azure Analysis Services databas åtgärder använder samma SqlServer-modul som SQL Server Analysis Services. Men alla cmdletar stöds inte för Azure Analysis Services. 

SqlServer-modulen tillhandahåller verksamhetsspecifika databas hanterings-cmdletar samt den generella Invoke-ASCmd-cmdlet som accepterar en fråga eller ett skript för tabell modell skript språk (TMSL). Följande cmdletar i SqlServer-modulen stöds för Azure Analysis Services.

Installera modul – [SQLServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentation – [SQLServer-referens](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets som stöds

|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Lägg till en medlem i en databas roll.| 
|[Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase)|Säkerhetskopiera en Analysis Services databas.|  
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Ta bort en medlem från en databas roll.|   
|[Invoke-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Kör ett TMSL-skript.|
|[Invoke-ProcessASDatabase](/powershell/module/sqlserver/invoke-processasdatabase)|Bearbeta en databas.|  
|[Invoke-ProcessPartition](/powershell/module/sqlserver/invoke-processpartition)|Bearbeta en partition.| 
|[Invoke-ProcessTable](/powershell/module/sqlserver/invoke-processtable)|Bearbetar en tabell.|  
|[Sammanslagning-partition](/powershell/module/sqlserver/merge-partition)|Sammanfoga en partition.|  
|[Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase)|Återställa en Analysis Services databas.| 
  

## <a name="related-information"></a>Relaterad information

* [SQL Server PowerShell](/sql/powershell/sql-server-powershell)      
* [Ladda ned SQL Server PowerShell-modul](/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modul i PowerShell-galleriet](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabell modell programmering för kompatibilitetsnivå 1200 och högre](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)