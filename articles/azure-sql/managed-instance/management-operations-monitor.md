---
title: Övervaka hanterings åtgärder
titleSuffix: Azure SQL Managed Instance
description: Lär dig mer om olika sätt att övervaka hanterings åtgärder för Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 6cdd3137798e221974dadda78dd55b6ae944bc78
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589351"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Övervaka hanterings åtgärder för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans tillhandahåller övervakning av [hanterings åtgärder](management-operations-overview.md) som du använder för att distribuera nya hanterade instanser, uppdatera instans egenskaper eller ta bort instanser när de inte längre behövs. 

## <a name="overview"></a>Översikt

Alla hanteringsåtgärder kan kategoriseras på följande sätt:

- Instans distribution (ny instans skapas).
- Instans uppdatering (ändring av instans egenskaper, till exempel virtuella kärnor eller reserverad lagring).
- Borttagning av instans.

De flesta hanterings åtgärder är [långvariga åtgärder](management-operations-overview.md#duration). Därför måste du övervaka statusen eller följa åtgärds stegens förlopp. 

Det finns flera sätt att övervaka hanterings åtgärder för hanterade instanser:

- [Distributioner av resurs grupper](../../azure-resource-manager/templates/deployment-history.md)
- [Aktivitetslogg](../../azure-monitor/essentials/activity-log.md)
- [API för hanterade instans åtgärder](#managed-instance-operations-api)


I följande tabell jämförs övervaknings alternativ för hanterings åtgärder: 

| Alternativ | Kvarhållning | Har stöd för Avbryt | Skapa | Uppdatera | Ta bort | Avbryt | Steg |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Distributioner av resurs grupper | Oändlig<sup>1</sup> | Nr<sup>2</sup> | Synliga | Synliga | Inte synlig | Synliga | Inte synlig |
| Aktivitetslogg | 90 dagar | No | Synliga | Synliga | Synliga | Synliga |  Inte synlig |
| API för hanterade instans åtgärder | 24 timmar | [Ja](management-operations-cancel.md) | Synliga | Synliga | Synliga | Synliga | Synliga |
|  |  |  |  |  |  |  | |

<sup>1</sup> distributions historiken för en resurs grupp är begränsad till 800 distributioner.

<sup>2</sup> distribution av resurs grupper stöder Avbryt åtgärd. Men på grund av en avbrotts logik avbryts bara en åtgärd som har schemalagts för distribution när åtgärden Avbryt har utförts. Pågående distribution avbryts inte när resurs grupps distributionen avbryts. Eftersom distributionen av hanterade instanser består av ett tids krävande steg (från Azure Resource Manager-perspektiv) avbryts distributionen av resurs gruppen inte genom att avbryta distributionen av en hanterad instans och åtgärden slutförs. 

## <a name="managed-instance-operations-api"></a>API för hanterade instans åtgärder

API: er för hanterings åtgärder är särskilt utformade för att övervaka åtgärder. Övervakning av hanterade instans åtgärder kan ge insikter om åtgärds parametrar och åtgärds steg, samt [avbryta vissa åtgärder](management-operations-cancel.md). Förutom åtgärds information och kommandot Avbryt kan du använda API: et i Automation-skript med distributioner för flera resurser, baserat på steg för steg, så kan du starta vissa beroende resurs distributioner.

Detta är API: erna: 

| Kommando | Beskrivning |
| --- | --- |
|[Hanterade instans åtgärder – Hämta](/rest/api/sql/managedinstanceoperations/get)|Hämtar en hanterings åtgärd på en hanterad instans.|
|[Hanterade instans åtgärder – Avbryt](/rest/api/sql/managedinstanceoperations/cancel)|Avbryter den asynkrona åtgärden på den hanterade instansen.|
|[Hanterade instans åtgärder – lista efter hanterad instans](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Hämtar en lista över åtgärder som utförts på den hanterade instansen.|

> [!NOTE]
> Använd API version 2020-02-02 för att se åtgärden Skapa i hanterade instanser i listan över åtgärder. Det här är standard versionen som används i Azure Portal och de senaste PowerShell-och Azure CLI-paketen.

## <a name="monitor-operations"></a>Övervaka åtgärder

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal använder du **översikts** sidan hanterade instanser för att övervaka hanterade instans åtgärder. 

Till exempel visas **åtgärden Skapa** i början av skapande processen på sidan **Översikt** : 

![Förlopp för att skapa hanterad instans](./media/management-operations-monitor/monitoring-create-operation.png)

Välj **pågående** åtgärd för att öppna sidan **pågående åtgärd** och visa **skapa** eller **Uppdatera** åtgärder. Du kan också [avbryta](management-operations-cancel.md) åtgärder från den här sidan även.  

![Information om hanterad instans åtgärd](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Skapa åtgärder som skickas via Azure Portal, PowerShell, Azure CLI eller andra verktyg som använder REST API version 2020-02-02 [kan avbrytas](management-operations-cancel.md). REST API versioner som är äldre än 2020-02-02 som används för att skicka en skapa-åtgärd startar instansen, men distributionen visas inte i API: et för åtgärder och kan inte avbrytas.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Get-AzSqlInstanceOperation-cmdlet: en hämtar information om åtgärder på en hanterad instans. Du kan visa alla åtgärder på en hanterad instans eller Visa en speciell åtgärd genom att ange namnet på åtgärden.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Detaljerade kommando förklaringar finns i [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

I AZ SQL mi op-listan hämtas en lista över åtgärder som utförs på den hanterade instansen. Om du inte redan har installerat Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Detaljerade kommando förklaringar finns i [AZ SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar din första hanterade instans finns i [snabb starts guide](instance-create-quickstart.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner](../database/features-comparison.md).
- Mer information om VNet-konfiguration finns i [konfiguration av SQL Managed instance VNet](connectivity-architecture-overview.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- En själv studie kurs om hur du använder Azure Database Migration Service för migrering finns i [migrering av SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).