---
title: 'Snabb start: ny princip tilldelning med PowerShell'
description: I den här snabb starten använder du Azure PowerShell för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: e941b74101308af703f243197fb4043f8f32d233
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548420"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med hjälp av Azure PowerShell

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar kommer du att identifiera virtuella datorer som _inte är kompatibla_.

Azure PowerShell-modulen används för att hantera Azure-resurser från kommando raden eller i skript.
Den här guiden förklarar hur du använder AZ-modulen för att skapa en princip tilldelning.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Innan du börjar bör du kontrollera att den senaste versionen av Azure PowerShell har installerats. Detaljerad information finns i [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

- Registrera resurs leverantören Azure Policy Insights med Azure PowerShell. När du registrerar resursprovidern säkerställer du att din prenumeration fungerar med den. Om du vill registrera en resursprovider måste du ha behörighet att utföra åtgärden att registrera resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Mer information om att registrera och Visa resurs leverantörer finns i [resurs leverantörer och typer](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabb starten skapar du en princip tilldelning för definitionen _Granska virtuella datorer utan Managed disks_ . Den här princip definitionen identifierar virtuella datorer som inte använder hanterade diskar.

Skapa en ny principtilldelning genom att köra följande kommandon:

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Föregående kommandon använder följande information:

- **Namn** – det faktiska namnet på tilldelningen. I det här exemplet användes _audit-vm-manageddisks_.
- **Visningsnamn** – Visningsnamn för principtilldelningen. I det här fallet använder du _granskning av virtuella datorer utan Managed disks tilldelning_.
- **Definition** – Principdefinitionen som du använder som bas för att skapa tilldelningen. I det här fallet är det ID: t för granskning av princip definition för _virtuella datorer som inte använder hanterade diskar_.
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetstillstånd.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Använd följande information för att identifiera resurser som inte är kompatibla med principtilldelningen som du skapade. Kör följande kommandon:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Mer information om hur du hämtar princip status finns i [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Ditt resultat liknar följande exempel:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Resultaten matchar det du ser på fliken **Resource Compliance (resurs krav** ) i en princip tilldelning i vyn Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den skapade tilldelningen använder du följande kommando:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)