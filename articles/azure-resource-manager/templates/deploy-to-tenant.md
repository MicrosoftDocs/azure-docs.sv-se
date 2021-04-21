---
title: Distribuera resurser till klientorganisation
description: Beskriver hur du distribuerar resurser i klientorganisationsomfånget i en Azure Resource Manager mall.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b17b8741d1701720de86d8039be3b6cd28ace5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781909"
---
# <a name="tenant-deployments-with-arm-templates"></a>Klientdistributioner med ARM-mallar

När din organisation mognar kan du [](../../governance/policy/overview.md) behöva definiera och tilldela principer eller rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../../role-based-access-control/overview.md) i din Azure AD-klientorganisation. Med mallar på klientorganisationsnivå kan du deklarativt tillämpa principer och tilldela roller på global nivå.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurstyper kan inte distribueras på klientorganisationsnivå. I det här avsnittet visas vilka resurstyper som stöds.

För Azure-principer använder du:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

För rollbaserad åtkomstkontroll i Azure (Azure RBAC) använder du:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

För kapslade mallar som distribueras till hanteringsgrupper, prenumerationer eller resursgrupper använder du:

* [Distributioner](/azure/templates/microsoft.resources/deployments)

Om du vill skapa hanteringsgrupper använder du:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Använd följande för att skapa prenumerationer:

* [Alias](/azure/templates/microsoft.subscription/aliases)

Använd följande för att hantera kostnader:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [Instruktioner](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

För att konfigurera portalen använder du:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Schema

Schemat som du använder för klientdistributioner skiljer sig från schemat för resursgruppsdistributioner.

För mallar använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

Schemat för en parameterfil är detsamma för alla distributionsomfång. För parameterfiler använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Nödvändig åtkomst

Det huvudnamn som distribuerar mallen måste ha behörighet att skapa resurser i klientorganisationsomfånget. Huvudnamn måste ha behörighet att köra distributionsåtgärderna ( `Microsoft.Resources/deployments/*` ) och för att skapa de resurser som definierats i mallen. Om du till exempel vill skapa en hanteringsgrupp måste huvudnamn ha behörigheten Deltagare i klientorganisationsomfånget. Om du vill skapa rolltilldelningar måste huvudägaren ha behörigheten Ägare.

Den globala administratören för Azure Active Directory har automatiskt behörighet att tilldela roller. Om du vill aktivera malldistributioner i klientorganisationsomfånget måste den globala administratören göra följande:

1. Utöka kontoåtkomsten så att den globala administratören kan tilldela roller. Mer information finns i Utöka [åtkomst för att hantera alla Azure-prenumerationer och hanteringsgrupper.](../../role-based-access-control/elevate-access-global-admin.md)

1. Tilldela ägare eller deltagare till det huvudnamn som behöver distribuera mallarna.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Huvudnamn har nu de behörigheter som krävs för att distribuera mallen.

## <a name="deployment-commands"></a>Distributionskommandon

Kommandona för klientdistributioner skiljer sig från kommandona för resursgruppsdistributioner.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För Azure CLI använder du [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd Azure PowerShell [New-AzTenantDeployment för att skapa en](/powershell/module/az.resources/new-aztenantdeployment)ny.

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Mer detaljerad information om distributionskommandon och alternativ för att distribuera ARM-mallar finns i:

* [Distribuera resurser med ARM-mallar och Azure Portal](deploy-portal.md)
* [Distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md)
* [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md)
* [Distribuera resurser med ARM-mallar och Azure Resource Manager REST API](deploy-rest.md)
* [Använd en distributionsknapp för att distribuera mallar från GitHub-lagringsplatsen](deploy-to-azure-button.md)
* [Distribuera ARM-mallar från Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Distributionsplats och namn

För distributioner på klientorganisationsnivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributionsplatsen anger var distributionsdata ska lagras. [Distributioner](deploy-to-subscription.md) [av prenumerationer och](deploy-to-management-group.md) hanteringsgrupp kräver också en plats. För [distributioner](deploy-to-resource-group.md) av resursgrupper används platsen för resursgruppen för att lagra distributionsdata.

Du kan ange ett namn för distributionen eller använda standarddistributionsnamnet. Standardnamnet är namnet på mallfilen. Om du till exempel distribuerar en _mallazuredeploy.jspå_ skapas ett standarddistributionsnamn för **azuredeploy**.

För varje distributionsnamn är platsen oföränderlig. Du kan inte skapa en distribution på en plats när det finns en befintlig distribution med samma namn på en annan plats. Om du till exempel skapar en klientdistribution med namnet **deployment1** i **centralus** kan du inte senare skapa en annan distribution med namnet **deployment1** utan platsen **westus**. Om du får felkoden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="deployment-scopes"></a>Distributionsomfång

När du distribuerar till en klientorganisation kan du distribuera resurser till:

* klienten
* hanteringsgrupper i klientorganisationen
* Prenumerationer
* resursgrupper

En [tilläggsresurs](scope-extension-resources.md) kan vara begränsad till ett mål som skiljer sig från distributionsmålet.

Den användare som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Det här avsnittet visar hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-tenant"></a>Omfång till klientorganisation

Resurser som definierats i resursavsnittet i mallen tillämpas på klientorganisationen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Omfång till hanteringsgrupp

Om du vill rikta in dig på en hanteringsgrupp i klientorganisationen lägger du till en kapslad distribution och anger `scope` egenskapen .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Omfång till prenumeration

Du kan också rikta in dig på prenumerationer i klientorganisationen. Den användare som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta in dig på en prenumeration i klientorganisationen använder du en kapslad distribution och `subscriptionId` egenskapen .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Omfång till resursgrupp

Du kan också rikta in dig på resursgrupper i klientorganisationen. Den användare som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta in dig på en resursgrupp i klientorganisationen använder du en kapslad distribution. Ange egenskaperna `subscriptionId` `resourceGroup` och . Ange inte en plats för den kapslade distributionen eftersom den distribueras på platsen för resursgruppen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Skapa en hanteringsgrupp

Följande mall skapar en hanteringsgrupp.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Om ditt konto inte har behörighet att distribuera till klientorganisationen kan du fortfarande skapa hanteringsgrupper genom att distribuera till ett annat omfång. Mer information finns i [Hanteringsgrupp.](deploy-to-management-group.md#management-group)

## <a name="assign-role"></a>Tilldela rollen

Följande mall tilldelar en roll i klientorganisationsomfånget.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar roller finns i [Lägga till Azure-rolltilldelningar med hjälp Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Du kan också distribuera mallar på [prenumerationsnivå eller](deploy-to-subscription.md) [hanteringsgruppsnivå.](deploy-to-management-group.md)
