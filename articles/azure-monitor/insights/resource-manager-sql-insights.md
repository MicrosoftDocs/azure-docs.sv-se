---
title: Resource Manager mallexempel för SQL-insikter
description: Exempel på Azure Resource Manager för att distribuera och konfigurera SQL-insikter.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730534"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Resource Manager mallexempel för SQL-insikter
Den här artikeln innehåller Azure Resource Manager [för att](../../azure-resource-manager/templates/template-syntax.md) aktivera SQL-insikter för övervakning av SQL som körs i Azure.  I dokumentationen [om SQL-insikter](sql-insights-overview.md) finns mer information om de sql-erbjudanden och SQL-versioner som vi stöder. Varje exempel innehåller en mallfil och en parameterfil med exempelvärden som ska anges för mallen.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>Skapa en övervakningsprofil för SQL-insikter
I följande exempel skapas en övervakningsprofil för SQL-insikter, som innehåller de SQL-övervakningsdata som ska samlas in, datainsamlingsfrekvensen och arbetsytan som data ska skickas till.


### <a name="template-file"></a>Mallfil

Visa [mallfilen på git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate).

### <a name="parameter-file"></a>Parameterfil

Visa [parameterfilen på git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json).


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Lägga till en virtuell övervakningsdatorn i en övervakningsprofil för SQL-insikter
När du har skapat en övervakningsprofil måste du allokera virtuella Azure-datorer som ska konfigureras för att fjärrsamla in data från de SQL-resurser som du anger i konfigurationen för den virtuella datorn.  Mer information finns i dokumentationen om att aktivera SQL-insikter.

I följande exempel konfigureras en virtuell övervakningsdatorn för att samla in data från de angivna SQL-resurserna.


### <a name="template-file"></a>Mallfil

Visa [mallfilen på git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate).

### <a name="parameter-file"></a>Parameterfil

Visa [parameterfilen på git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json).


## <a name="create-an-alert-rule-for-sql-insights"></a>Skapa en aviseringsregel för SQL-insikter
I följande exempel skapas en aviseringsregel som täcker SQL-resurserna inom omfånget för den angivna övervakningsprofilen.  Den här aviseringsregeln visas i ANVÄNDArgränssnittet för SQL Insights i kontextpanelen för aviseringsgränssnittet.  

Parameterfilen innehåller värden från en av de aviseringsmallar som vi tillhandahåller i SQL-insikter. Du kan ändra den så att den avisering om andra data som vi samlar in för SQL.  Mallen anger inte någon åtgärdsgrupp för aviseringsregeln.


#### <a name="template-file"></a>Mallfil

Visa [mallfilen på git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate).

### <a name="parameter-file"></a>Parameterfil

Visa [parameterfilen på git hub](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json).





## <a name="next-steps"></a>Nästa steg

* [Hämta andra exempelmallar för Azure Monitor](../resource-manager-samples.md).
* [Läs mer om SQL-insikter.](sql-insights-overview.md)
