---
title: Ta bort resursgrupp och resurser
description: Beskriver hur du tar bort resursgrupper och resurser. Den beskriver hur Azure Resource Manager beställer borttagningen av resurser när en resursgrupp tas bort. Den beskriver svarskoderna och hur Resource Manager hanterar dem för att avgöra om borttagningen lyckades.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 3c062c2f775e145347129f24b201748ee517daf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768677"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager resursgrupp och resursborttagning

Den här artikeln visar hur du tar bort resursgrupper och resurser. Den beskriver hur Azure Resource Manager beställer borttagningen av resurser när du tar bort en resursgrupp.

## <a name="how-order-of-deletion-is-determined"></a>Hur borttagningsordningen bestäms

När du tar bort en resursgrupp Resource Manager bestämmer ordningen för att ta bort resurser. Den använder följande ordning:

1. Alla underordnade (kapslade) resurser tas bort.

2. Resurser som hanterar andra resurser tas bort härnäst. En resurs kan ha egenskapen `managedBy` inställd för att ange att en annan resurs hanterar den. När den här egenskapen har angetts tas den resurs som hanterar den andra resursen bort före de andra resurserna.

3. Återstående resurser tas bort efter de föregående två kategorierna.

När ordningen har fastställts utfärdar Resource Manager DELETE-åtgärd för varje resurs. Den väntar på att eventuella beroenden ska slutföras innan du fortsätter.

För synkrona åtgärder är de förväntade svarskoderna:

* 200
* 204
* 404

För asynkrona åtgärder är det förväntade svaret 202. Resource Manager spårar platsrubriken eller åtgärdshuvudet azure-async för att fastställa status för den asynkrona borttagningsåtgärden.
  
### <a name="deletion-errors"></a>Fel vid borttagning

När en borttagningsåtgärd returnerar ett fel Resource Manager försöker igen med DELETE-anropet. Återförsök sker för statuskoderna 5xx, 429 och 408. Som standard är tidsperioden för återförsök 15 minuter.

## <a name="after-deletion"></a>Efter borttagningen

Resource Manager ett GET-anrop för varje resurs som den försökte ta bort. Svaret för det här GET-anropet förväntas vara 404. När Resource Manager får ett 404-fel anses borttagningen ha slutförts. Resource Manager tar bort resursen från dess cacheminne.

Men om GET-anropet för resursen returnerar ett 200- eller 201-Resource Manager återskapar resursen.

Om GET-åtgärden returnerar ett Resource Manager försöker hämta igen för följande felkod:

* Mindre än 100
* 408
* 429
* Större än 500

För andra felkoder Resource Manager inte borttagningen av resursen.

> [!IMPORTANT]
> Det går inte att ångra borttagningen av resursgruppen.

## <a name="delete-resource-group"></a>Ta bort resursgrupp

Använd någon av följande metoder för att ta bort resursgruppen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I portalen [väljer](https://portal.azure.com)du den resursgrupp som du vill ta bort.

1. Välj **Ta bort resursgrupp**.

   ![Ta bort resursgrupp](./media/delete-resource-group/delete-group.png)

1. Bekräfta borttagningen genom att skriva namnet på resursgruppen

---

## <a name="delete-resource"></a>Ta bort resurs

Använd någon av följande metoder för att ta bort en resurs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I portalen [väljer](https://portal.azure.com)du den resurs som du vill ta bort.

1. Välj **Ta bort**. Följande skärmbild visar hanteringsalternativen för en virtuell dator.

   ![Ta bort resurs](./media/delete-resource-group/delete-resource.png)

1. Bekräfta borttagningen när du uppmanas att göra det.

---

## <a name="required-access"></a>Nödvändig åtkomst

Om du vill ta bort en resursgrupp behöver du åtkomst till **borttagningsåtgärden för resursen Microsoft.Resources/subscriptions/resourceGroups.** Du behöver också ta bort för alla resurser i resursgruppen.

En lista över åtgärder finns i Åtgärder [för Azure-resursprovider.](../../role-based-access-control/resource-provider-operations.md) En lista över inbyggda roller finns i [Inbyggda roller i Azure.](../../role-based-access-control/built-in-roles.md)

Om du har nödvändig åtkomst, men borttagningsbegäran misslyckas, kan det beror på att det finns [ett lås](lock-resources.md) för resursgruppen.

## <a name="next-steps"></a>Nästa steg

* Information om Resource Manager finns i [Azure Resource Manager översikt](overview.md).
* För borttagningskommandon, [se PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup) [Azure CLI](/cli/azure/group#az_group_delete)och [REST API](/rest/api/resources/resourcegroups/delete).
