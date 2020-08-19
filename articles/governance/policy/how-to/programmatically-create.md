---
title: Skapa principer programmässigt
description: Den här artikeln vägleder dig genom program mässigt skapa och hantera principer för Azure Policy med Azure CLI, Azure PowerShell och REST API.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7a72986d8ffe64953e68ff166de9a02a15fb9c86
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548319"
---
# <a name="programmatically-create-policies"></a>Skapa principer programmässigt

Den här artikeln vägleder dig genom program mässigt skapa och hantera principer. Azure Policy definitioner tillämpar olika regler och effekter över dina resurser. Tvång ser till att resurserna är kompatibla med företagets standarder och service nivå avtal.

Information om kompatibilitet finns i [Hämta efterlevnadsprinciper](get-compliance-data.md).

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att följande krav är uppfyllda innan du börjar:

1. Installera [ARMClient](https://github.com/projectkudu/ARMClient), om du inte redan gjort det. Det är ett verktyg som skickar HTTP-begäranden till Azure Resource Manager-baserade API:er.

1. Uppdatera Azure PowerShell-modulen till den senaste versionen. Detaljerad information finns i [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Mer information om den senaste versionen finns i [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registrera resurs leverantören Azure Policy Insights med Azure PowerShell för att kontrol lera att din prenumeration fungerar med resurs leverantören. Om du vill registrera en resurs leverantör måste du ha behörighet att köra register åtgärds åtgärden för resurs leverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Mer information om att registrera och Visa resurs leverantörer finns i [resurs leverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Installera Azure CLI om du inte redan gjort det. Du kan hämta den senaste versionen när du [installerar Azure CLI i Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Skapa och tilldela en principdefinition

Det första steget mot bättre insyn i resurserna är att skapa och tilldela principer över dina resurser. Nästa steg är att lära dig att program mässigt skapa och tilldela en princip. Exempel principen granskar lagrings konton som är öppna för alla offentliga nätverk med PowerShell, Azure CLI och HTTP-begäranden.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Skapa och tilldela en princip definition med PowerShell

1. Använd följande JSON-kodfragment för att skapa en JSON-fil med namnet AuditStorageAccounts.jspå.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Mer information om hur du skapar en princip definition finns i [Azure policy definitions struktur](../concepts/definition-structure.md).

1. Kör följande kommando för att skapa en princip definition med hjälp av AuditStorageAccounts.jsi filen.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Kommandot skapar en princip definition med namnet _Granska lagrings konton som är öppna i offentliga nätverk_.
   Mer information om andra parametrar som du kan använda finns i [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   När anropas utan plats parametrar, `New-AzPolicyDefinition` sparar standard princip definitionen i den valda prenumerationen av sessionens kontext. Om du vill spara definitionen på en annan plats använder du följande parametrar:

   - **SubscriptionId** – Spara i en annan prenumeration. Kräver ett _GUID_ -värde.
   - **ManagementGroupName** – Spara till en hanterings grupp. Kräver ett _sträng_ värde.

1. När du har skapat en princip definition kan du skapa en princip tilldelning genom att köra följande kommandon:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Ersätt _conto sorg_ med namnet på den avsedda resurs gruppen.

   **Omfattnings** parametern i `New-AzPolicyAssignment` fungerar med hanterings grupp, prenumeration, resurs grupp eller en enskild resurs. Parametern använder en fullständig resurs Sök väg som egenskapen **ResourceID** i `Get-AzResourceGroup` returnerar. Mönstret för **omfånget** för varje behållare är följande. Ersätt `{rName}` , `{rgName}` , `{subId}` , och `{mgName}` med resurs namnet, resurs gruppens namn, prenumerations-ID och namn på hanterings gruppen.
   `{rType}` ersätts med resurs **typen** för resursen, till exempel `Microsoft.Compute/virtualMachines` för en virtuell dator.

   - Klusterresursen `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resurs grupp – `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Prenumerera `/subscriptions/{subId}/`
   - Hanterings grupp – `/providers/Microsoft.Management/managementGroups/{mgName}`

Mer information om hur du hanterar resurs principer med hjälp av Resource Manager PowerShell-modulen finns i [AZ. Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Skapa och tilldela en princip definition med ARMClient

Använd följande procedur för att skapa en princip definition.

1. Kopiera följande JSON-kodfragment för att skapa en JSON-fil. Du anropar filen i nästa steg.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Skapa princip definitionen med något av följande anrop:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Ersätt föregående {subscriptionId} med ID: t för din prenumeration eller {managementGroupId} med ID: t för din [hanterings grupp](../../management-groups/overview.md).

   Mer information om frågans struktur finns i [Azure policy definitioner – skapa eller uppdatera](/rest/api/resources/policydefinitions/createorupdate) och [princip definitioner – skapa eller uppdatera i hanterings gruppen](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Använd följande procedur för att skapa en princip tilldelning och tilldela princip definitionen på resurs grupps nivå.

1. Kopiera följande JSON-kodfragment för att skapa en fil med en JSON-princip tilldelning. Ersätt exempel information i &lt; &gt; symboler med dina egna värden.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Skapa princip tilldelningen med följande anrop:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Ersätt exempel information i &lt; &gt; symboler med dina egna värden.

   Mer information om hur du gör HTTP-anrop till REST API finns i [Azure REST API-resurser](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Skapa och tilldela en princip definition med Azure CLI

Använd följande procedur för att skapa en princip definition:

1. Kopiera följande JSON-kodfragment för att skapa en fil med en JSON-princip tilldelning.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Mer information om hur du skapar en princip definition finns i [Azure policy definitions struktur](../concepts/definition-structure.md).

1. Kör följande kommando för att skapa en princip definition:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Kommandot skapar en princip definition med namnet _Granska lagrings konton som är öppna i offentliga nätverk_.
   Mer information om andra parametrar som du kan använda finns i [AZ policy definition Create](/cli/azure/policy/definition#az-policy-definition-create).

   När anropas utan plats parametrar, `az policy definition creation` sparar standard princip definitionen i den valda prenumerationen av sessionens kontext. Om du vill spara definitionen på en annan plats använder du följande parametrar:

   - **prenumeration** – Spara till en annan prenumeration. Kräver ett _GUID_ -värde för prenumerations-ID eller ett _sträng_ värde för prenumerations namnet.
   - **hantering-grupp** – Spara i en hanterings grupp. Kräver ett _sträng_ värde.

1. Använd följande kommando för att skapa en princip tilldelning. Ersätt exempel information i &lt; &gt; symboler med dina egna värden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   **Omfattnings** parametern i `az policy assignment create` fungerar med hanterings grupp, prenumeration, resurs grupp eller en enskild resurs. Parametern använder en fullständig resurs Sök väg. Mönstret för **omfånget** för varje behållare är följande. Ersätt `{rName}` , `{rgName}` , `{subId}` , och `{mgName}` med resurs namnet, resurs gruppens namn, prenumerations-ID och namn på hanterings gruppen. `{rType}` ersätts med resurs **typen** för resursen, till exempel `Microsoft.Compute/virtualMachines` för en virtuell dator.

   - Klusterresursen `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resurs grupp – `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Prenumerera `/subscriptions/{subID}`
   - Hanterings grupp – `/providers/Microsoft.Management/managementGroups/{mgName}`

Du kan hämta Azure Policy Definitions-ID med hjälp av PowerShell med följande kommando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Princip Definitions-ID: t för den princip definition som du skapade bör likna följande exempel:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Mer information om hur du kan hantera resurs principer med Azure CLI finns i [resurs principer för Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill ha mer information om kommandon och frågor i den här artikeln.

- [Azure REST API-resurser](/rest/api/resources/)
- [Azure PowerShell moduler](/powershell/module/az.resources/#policies)
- [Azure CLI-princip kommandon](/cli/azure/policy?view=azure-cli-latest)
- [Azure Policy insikter Resource Provider REST API referens](/rest/api/policy-insights)
- [Organisera dina resurser i Azure-hanteringsgrupper](../../management-groups/overview.md).