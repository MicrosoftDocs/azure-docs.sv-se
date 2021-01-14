---
title: Distribuera resurser till klient organisationen
description: Beskriver hur du distribuerar resurser i klient omfånget i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b3ddc63e49b272c93349ada91e9a1599ea4be4f
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186217"
---
# <a name="tenant-deployments-with-arm-templates"></a>Klient distributioner med ARM-mallar

När din organisation är vuxen kan du behöva definiera och tilldela [principer](../../governance/policy/overview.md) eller [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) i Azure AD-klienten. Med mallar för klient organisations nivå kan du tillämpa principer och tilldela roller på global nivå.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurs typer kan inte distribueras till klient nivån. I det här avsnittet listas vilka resurs typer som stöds.

Använd följande för Azure-principer:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

För rollbaserad åtkomst kontroll i Azure (Azure RBAC) använder du:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

För kapslade mallar som distribuerar till hanterings grupper, prenumerationer eller resurs grupper, använder du:

* [distributioner](/azure/templates/microsoft.resources/deployments)

Använd följande för att skapa hanterings grupper:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Använd följande för att skapa prenumerationer:

* [alias](/azure/templates/microsoft.subscription/aliases)

För att hantera kostnader använder du:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [problemlösning](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Använd följande för att konfigurera portalen:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Schema

Schemat som används för klient distributioner skiljer sig från schemat för resurs grupps distributioner.

För mallar använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

Schemat för en parameter fil är detsamma för alla distributions omfång. För parameter-filer använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Nödvändig åtkomst

Huvud kontot som distribuerar mallen måste ha behörighet att skapa resurser i klient omfånget. Huvud kontot måste ha behörighet att köra distributions åtgärder ( `Microsoft.Resources/deployments/*` ) och för att skapa de resurser som definierats i mallen. Om du till exempel vill skapa en hanterings grupp måste huvudobjektet ha deltagar behörighet för klient omfånget. Huvud kontot måste ha ägar behörighet för att skapa roll tilldelningar.

Den globala administratören för Azure Active Directory har inte automatiskt behörighet att tilldela roller. Om du vill aktivera mall distributioner i klient omfånget måste den globala administratören utföra följande steg:

1. Öka konto åtkomsten så att den globala administratören kan tilldela roller. Mer information finns i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../../role-based-access-control/elevate-access-global-admin.md).

1. Tilldela ägare eller deltagare till det huvud konto som måste distribuera mallarna.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Huvud kontot har nu de behörigheter som krävs för att distribuera mallen.

## <a name="deployment-commands"></a>Distributions kommandon

Kommandona för klient distributioner skiljer sig från kommandona för resurs grupp distributioner.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För Azure CLI använder du [AZ Deployment Tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För Azure PowerShell använder du [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Mer detaljerad information om distributions kommandon och alternativ för att distribuera ARM-mallar finns i:

* [Distribuera resurser med ARM-mallar och Azure Portal](deploy-portal.md)
* [Distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md)
* [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md)
* [Distribuera resurser med ARM-mallar och Azure Resource Manager REST API](deploy-rest.md)
* [Använd en distributions knapp för att distribuera mallar från GitHub-lagringsplatsen](deploy-to-azure-button.md)
* [Distribuera ARM-mallar från Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på klient nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras. Distributioner av [prenumerationer](deploy-to-subscription.md) och [hanterings grupper](deploy-to-management-group.md) kräver också en plats. För [resurs grupps](deploy-to-resource-group.md) distributioner används resurs gruppens plats för att lagra distributions data.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall som heter **azuredeploy.jspå** skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du till exempel skapar en klient distribution med namnet **deployment1** i **centralen** kan du inte senare skapa en annan distribution med namnet **deployment1** men en plats med **väst**. Om du får fel koden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="deployment-scopes"></a>Distributions omfång

När du distribuerar till en klient kan du distribuera resurser för att:

* klienten
* hanterings grupper inom klienten
* prenumerationer
* resursgrupper

En [tilläggs resurs](scope-extension-resources.md) kan begränsas till ett mål som skiljer sig från distributions målet.

Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

I det här avsnittet visas hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-tenant"></a>Scope till klient organisation

De resurser som definieras i avsnittet resurser i mallen tillämpas på klienten.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Omfång till hanterings grupp

Om du vill rikta en hanterings grupp inom klienten lägger du till en kapslad distribution och anger `scope` egenskapen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Omfång till prenumeration

Du kan också rikta prenumerationer inom klienten. Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta en prenumeration inom klienten använder du en kapslad distribution och `subscriptionId` egenskapen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Omfång till resurs grupp

Du kan också rikta resurs grupper inom klienten. Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta en resurs grupp inom klienten använder du en kapslad distribution. Ange `subscriptionId` egenskaperna och `resourceGroup` . Ange ingen plats för den kapslade distributionen eftersom den har distribuerats i resurs gruppens plats.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Skapa en hanteringsgrupp

Följande mall skapar en hanterings grupp.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Om ditt konto inte har behörighet att distribuera till klienten kan du fortfarande skapa hanterings grupper genom att distribuera till ett annat omfång. Mer information finns i [hanterings grupp](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Tilldela rollen

Följande mall tilldelar en roll i klient omfånget.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Nästa steg

* Information om hur du tilldelar roller finns i [lägga till Azure-roll tilldelningar med hjälp av Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Du kan också distribuera mallar på [prenumerations nivå](deploy-to-subscription.md) eller på [hanterings grupps nivå](deploy-to-management-group.md).
