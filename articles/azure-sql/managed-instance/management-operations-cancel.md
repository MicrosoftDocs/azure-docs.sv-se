---
title: Avbryt hanterings åtgärder
titleSuffix: Azure SQL Managed Instance
description: Lär dig hur du avbryter hanterings åtgärder för Azure SQL-hanterad instans.
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
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861552"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Avbryta hanterings åtgärder för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans ger möjlighet att avbryta vissa [hanterings åtgärder](management-operations-overview.md), till exempel när du distribuerar en ny hanterad instans eller uppdatering av instans egenskaper. 

## <a name="overview"></a>Översikt

 Alla hanteringsåtgärder kan kategoriseras på följande sätt:

- Instans distribution (ny instans skapas).
- Instans uppdatering (ändring av instans egenskaper, till exempel virtuella kärnor eller reserverad lagring).
- Borttagning av instans.

Du kan [övervaka förloppet och statusen för hanterings åtgärder](management-operations-monitor.md) och avbryta vissa av dem om det behövs. 

I följande tabell sammanfattas hanterings åtgärder, oavsett om du kan säga upp dem och deras normala övergripande varaktighet:

Kategori  |Åtgärd  |Avbrytbar  |Beräknad tids längd för avbrott  |
|---------|---------|---------|---------|
|Distribution |Skapa instans |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Uppdatera |Skalning av instans lagring upp/ned (Generell användning) |Inga |  |
|Uppdatera |Skalning av instans lagring upp/ned (Affärskritisk) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Generell användning) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Affärskritisk) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Uppdatera |Instans tjänst nivå ändring (Generell användning till Affärskritisk och vice versa) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Ta bort |Borttagning av instans |Inga |  |
|Ta bort |Borttagning av virtuellt kluster (som användarinitierad åtgärd) |Inga |  |

## <a name="cancel-management-operation"></a>Avbryt hanterings åtgärden

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill avbryta hanterings åtgärder med hjälp av Azure Portal:

1. Gå till [Azure Portal](https://portal.azure.com)
1. Gå till bladet **Översikt** för din SQL-hanterade instans. 
1. Markera rutan **meddelande** bredvid den pågående åtgärden för att öppna sidan för **pågående åtgärder** . 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Välj rutan pågående åtgärd för att öppna sidan pågående åtgärd.":::

1. Välj **Avbryt åtgärden** längst ned på sidan. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Välj Avbryt om du vill avbryta åtgärden.":::

1. Bekräfta att du vill avbryta åtgärden. 


Om Cancel-begäran lyckas avbryts hanterings åtgärden och det uppstår ett haveri. Du får ett meddelande om att annulleringen lyckas eller Miss lyckas.

![Avbryter åtgärds resultat](./media/management-operations-cancel/canceling-operation-result.png)


Om Cancel-begäran Miss lyckas eller om Avbryt-knappen inte är aktiv, innebär det att hanterings åtgärden har angett ett tillstånd som inte kan avbrytas och som kommer att slutföras inom kort.  Hanterings åtgärden fortsätter att köras tills den har slutförts.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du inte redan har Azure PowerShell installerat, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Om du vill avbryta en hanterings åtgärd måste du ange namnet på hanterings åtgärden. Därför ska du först använda kommandot Get för att hämta åtgärds listan och sedan avbryta åtgärden.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Detaljerade kommando förklaringar finns i [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) och [Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte redan har installerat Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill avbryta hanterings åtgärden måste du ange namnet på hanterings åtgärden. Använd därför först kommandot Get för att hämta åtgärds listan och sedan avbryta den aktuella åtgärden.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Detaljerade kommando förklaringar finns i [AZ SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Begäran om avbruten distribution

Med API version 2020-02-02, så snart som begäran om att skapa instans godkänns, börjar instansen finnas som en resurs, oavsett förloppet för distributions processen (status för hanterade instanser är **etablering**). Om du avbryter instans distributions förfrågan (ny instans skapas), kommer den hanterade instansen att gå från **etablerings** statusen till **FailedToCreate**.

Instanser som inte kunde skapas finns fortfarande som en resurs och: 

- Debiteras inte
- Räkna inte mot resurs gränser (undernät eller vCore-kvot)


> [!NOTE]
> Om du vill minimera bruset i listan över resurser eller hanterade instanser, tar du bort instanser som inte har kunnat distribueras eller instanser med avbrutna distributioner. 


## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar din första hanterade instans finns i [snabb starts guide](instance-create-quickstart.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner](../database/features-comparison.md).
- Mer information om VNet-konfiguration finns i [konfiguration av SQL Managed instance VNet](connectivity-architecture-overview.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- En själv studie kurs om hur du använder Azure Database Migration Service för migrering finns i [migrering av SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
