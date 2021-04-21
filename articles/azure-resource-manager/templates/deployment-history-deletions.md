---
title: Borttagning av distributionshistorik
description: Beskriver hur Azure Resource Manager automatiskt tar bort distributioner från distributionshistoriken. Distributioner tas bort när historiken är nära att överskrida gränsen på 800.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: b55c022c35c43be6818bb3c551d5db85b1927ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781855"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatiska borttagningar från distributionshistorik

Varje gång du distribuerar en mall skrivs information om distributionen till distributionshistoriken. Varje resursgrupp är begränsad till 800 distributioner i distributionshistoriken.

Azure Resource Manager automatiskt bort distributioner från historiken när du närmar dig gränsen. Automatisk borttagning är en ändring från tidigare beteende. Tidigare var du tvungen att manuellt ta bort distributioner från distributionshistoriken för att undvika att få ett fel. Den här ändringen implementerades den 6 augusti 2020.

**Automatiska borttagningar stöds för distribution av resursgrupper. Distributioner i historiken [](deploy-to-subscription.md)för prenumerations-, [hanteringsgrupps-](deploy-to-management-group.md)och [klientdistributioner](deploy-to-tenant.md) tas för närvarande inte bort automatiskt.**

> [!NOTE]
> Om du tar bort en distribution från historiken påverkas inte några av de resurser som har distribuerats.

## <a name="when-deployments-are-deleted"></a>När distributioner tas bort

Distributioner tas bort från historiken när du överskrider 775 distributioner. Azure Resource Manager tar bort distributioner tills historiken är nere på 750. De äldsta distributionerna tas alltid bort först.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Borttagningar från distributionshistorik":::

> [!NOTE]
> Startnumret (775) och det sista talet (750) kan ändras.
>
> Om din resursgrupp redan är på gränsen 800 misslyckas nästa distribution med ett fel. Den automatiska borttagningsprocessen startar omedelbart. Du kan prova distributionen igen efter en kort stund.

Förutom distributioner utlöser du även borttagningar när du kör [vad om-åtgärden eller](template-deploy-what-if.md) validerar en distribution.

När du ger en distribution samma namn som en i historiken återställer du dess plats i historiken. Distributionen flyttas till den senaste platsen i historiken. Du återställer också platsen för en distribution när du [återställer till distributionen efter](rollback-on-error.md) ett fel.

## <a name="remove-locks-that-block-deletions"></a>Ta bort lås som blockerar borttagningar

Om du har [ett CanNotDelete-lås](../management/lock-resources.md) på en resursgrupp kan distributionerna för den resursgruppen inte tas bort. Du måste ta bort låset för att dra nytta av automatiska borttagningar i distributionshistoriken.

Om du vill använda PowerShell för att ta bort ett lås kör du följande kommandon:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Om du vill använda Azure CLI för att ta bort ett lås kör du följande kommandon:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Behörigheter som krävs

Borttagningarna begärs under identiteten för den användare som distribuerade mallen. Om du vill ta bort distributioner måste användaren ha åtkomst till **åtgärden Microsoft.Resources/deployments/delete.** Om användaren inte har de behörigheter som krävs tas inte distributioner bort från historiken.

Om den aktuella användaren inte har de behörigheter som krävs görs ett nytt försök att ta bort automatiskt under nästa distribution.

## <a name="opt-out-of-automatic-deletions"></a>Avanmäla dig från automatiska borttagningar

Du kan välja bort automatiska borttagningar från historiken. **Använd bara det här alternativet om du vill hantera distributionshistoriken själv.** Gränsen på 800 distributioner i historiken tillämpas fortfarande. Om du överskrider 800 distributioner får du ett felmeddelande och distributionen misslyckas.

Om du vill inaktivera automatiska borttagningar registrerar du `Microsoft.Resources/DisableDeploymentGrooming` funktionsflaggan. När du registrerar funktionsflaggan avanmäler du dig från automatiska borttagningar för hela Azure-prenumerationen. Du kan inte välja bort endast en viss resursgrupp. Om du vill återerablering av automatiska borttagningar avregistrerar du funktionsflaggan.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell använder du [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Om du vill se den aktuella statusen för din prenumeration använder du:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Om du vill återererbara automatiska borttagningar använder du Azure REST API eller Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För Azure CLI använder du [az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Om du vill se den aktuella statusen för din prenumeration använder du:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Om du vill återererbara automatiska borttagningar använder [du az feature unregister](/cli/azure/feature#az_feature_unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

För REST API du funktioner [– registrera](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Om du vill se den aktuella statusen för din prenumeration använder du:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Om du vill återererbara automatiska borttagningar använder [du Funktioner – Avregistrera](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du visar distributionshistoriken finns i [Visa distributionshistorik med Azure Resource Manager](deployment-history.md).
