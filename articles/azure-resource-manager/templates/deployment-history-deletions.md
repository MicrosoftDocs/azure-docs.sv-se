---
title: Borttagning av distributionshistorik
description: Beskriver hur Azure Resource Manager automatiskt tar bort distributioner från distributions historiken. Distributioner tas bort när historiken är nära att överskrida gränsen på 800.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: fc4f7f33cdd7ccce3158aa95bd002f12c8c44c00
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951971"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatisk borttagning från distributions historik

Varje gång du distribuerar en mall skrivs information om distributionen till distributions historiken. Varje resurs grupp är begränsad till 800 distributioner i distributions historiken.

Azure Resource Manager tar automatiskt bort distributioner från historiken när du närmar dig gränsen. Automatisk borttagning är en förändring från tidigare beteende. Tidigare var du tvungen att manuellt ta bort distributioner från distributions historiken för att undvika ett fel. Den här ändringen implementerades den 6 augusti 2020.

**Automatisk borttagning stöds för resurs grupps distributioner. För närvarande tas distributioner i historiken för [prenumeration](deploy-to-subscription.md), [hanterings grupp](deploy-to-management-group.md)och [klient](deploy-to-tenant.md) distributioner inte bort automatiskt.**

> [!NOTE]
> Att ta bort en distribution från historiken påverkar inte några av de distribuerade resurserna.

## <a name="when-deployments-are-deleted"></a>När distributioner tas bort

Distributioner tas bort från din historik när du överskrider 775-distributioner. Azure Resource Manager tar bort distributioner tills historiken är nere till 750. De äldsta distributionerna tas alltid bort först.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Borttagningar från distributions historik":::

> [!NOTE]
> Start numret (775) och slut talet (750) kan ändras.
>
> Om din resurs grupp redan har nått gränsen 800 Miss lyckas nästa distribution med ett fel. Processen för automatisk borttagning startar omedelbart. Du kan prova distributionen igen efter en kort stund.

Förutom distributioner utlöser du även borttagningar när du kör [åtgärden vad händer om](template-deploy-what-if.md) eller verifierar en distribution.

När du ger en distribution samma namn som en i historiken återställer du dess plats i historiken. Distributionen flyttas till den senaste platsen i historiken. Du kan också återställa en distributions plats när du återställer [till distributionen](rollback-on-error.md) efter ett fel.

## <a name="remove-locks-that-block-deletions"></a>Ta bort lås som blockerar borttagningar

Om du har ett [CanNotDelete-lås](../management/lock-resources.md) på en resurs grupp går det inte att ta bort distributionerna för resurs gruppen. Du måste ta bort låset för att kunna dra nytta av automatiska borttagningar i distributions historiken.

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

Borttagningarna begärs under identiteten för den användare som distribuerade mallen. Användaren måste ha åtkomst till åtgärden **Microsoft. Resources/distribution/Delete** för att kunna ta bort distributioner. Om användaren inte har de behörigheter som krävs raderas inte distributioner från historiken.

Om den aktuella användaren inte har de behörigheter som krävs görs ett försök att utföra automatisk borttagning igen under nästa distribution.

## <a name="opt-out-of-automatic-deletions"></a>Inaktivera automatiska borttagningar

Du kan välja bort automatiska borttagningar från historiken. **Använd bara det här alternativet när du vill hantera distributions historiken själv.** Gränsen på 800-distributioner i historiken tillämpas fortfarande. Om du överskrider 800-distributioner får du ett fel meddelande och distributionen kommer att Miss förfaller.

Registrera funktions flaggan om du vill inaktivera automatiska borttagningar `Microsoft.Resources/DisableDeploymentGrooming` . När du registrerar funktions flaggan kan du välja bort automatiska borttagningar för hela Azure-prenumerationen. Du kan inte välja bara för en viss resurs grupp. Om du vill återaktivera automatisk borttagning avregistrerar du funktions flaggan.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell använder du [register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Om du vill se aktuell status för din prenumeration använder du:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Om du vill återaktivera automatiska borttagningar använder du Azure REST API eller Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [AZ-funktions register](/cli/azure/feature#az-feature-register)för Azure CLI.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Om du vill se aktuell status för din prenumeration använder du:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Om du vill återaktivera automatiska borttagningar använder du [AZ-funktionen avregistrera](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Använd [funktioner – registrera](/rest/api/resources/features/register)för REST API.

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Om du vill se aktuell status för din prenumeration använder du:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Aktivera automatisk borttagning igen genom att använda [funktioner-avregistrera](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Nästa steg

* Information om hur du visar distributions historiken finns i [Visa distributions historik med Azure Resource Manager](deployment-history.md).
