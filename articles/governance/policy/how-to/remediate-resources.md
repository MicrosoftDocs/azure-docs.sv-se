---
title: Åtgärda icke-kompatibla resurser
description: Den här guiden vägleder dig genom reparationen av resurser som inte är kompatibla med principer i Azure Policy.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: e567bedf48393a36215c1ac3f3d11f467ae7badd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742236"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Åtgärda icke-kompatibla resurser med Azure Policy

Resurser som inte är kompatibla med en **deployIfNotExists** eller en **ändrings** princip kan försättas i ett kompatibelt tillstånd genom **reparation**. Reparationen utförs genom att instruera Azure Policy att köra **deployIfNotExists** -eller **ändra** -åtgärden för den tilldelade principen på dina befintliga resurser och prenumerationer, oavsett om tilldelningen är till en hanterings grupp, en prenumeration, en resurs grupp eller en enskild resurs. Den här artikeln visar de steg som krävs för att förstå och utföra reparation med Azure Policy.

## <a name="how-remediation-security-works"></a>Så här fungerar reparations säkerhet

När Azure Policy kör mallen i **deployIfNotExists** -princip definitionen används en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy skapar en hanterad identitet för varje tilldelning, men du måste ha information om vilka roller som ska bevilja den hanterade identiteten. Om den hanterade identiteten saknar roller visas ett fel när principen eller ett initiativ tilldelas. När du använder portalen beviljas Azure Policy automatiskt den hanterade identitet som listade roller när tilldelningen startar. När du använder SDK måste rollerna manuellt beviljas till den hanterade identiteten. _Platsen_ för den hanterade identiteten påverkar inte åtgärden med Azure policy.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="Skärm bild av en deployIfNotExists-princip som saknar definierad behörighet för den hanterade identiteten." border="false":::

> [!IMPORTANT]
> I följande scenarion måste tilldelningens hanterade identitet [manuellt beviljas åtkomst](#manually-configure-the-managed-identity) eller så Miss fungerar reparations distributionen:
>
> - Om tilldelningen skapas via SDK
> - Om en resurs som har ändrats av **deployIfNotExists** eller **ändra** ligger utanför omfånget för princip tilldelningen
> - Om mallen har åtkomst till egenskaper för resurser utanför omfånget för princip tilldelningen

## <a name="configure-policy-definition"></a>Konfigurera princip definition

Det första steget är att definiera de roller som **deployIfNotExists** och **ändra** behöver i princip definitionen för att kunna distribuera innehållet i den inkluderade mallen. Lägg till en **roleDefinitionIds** -egenskap under egenskapen **information** . Den här egenskapen är en sträng mat ris som matchar roller i din miljö. Ett fullständigt exempel finns i DeployIfNotExists- [exemplet](../concepts/effects.md#deployifnotexists-example) eller i [ändra exempel](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Egenskapen **roleDefinitionIds** använder det fullständiga resurs-ID: n och tar inte med den korta **roleName** för rollen. Använd följande kod för att hämta ID för rollen "deltagare" i din miljö:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Konfigurera den hanterade identiteten manuellt

När du skapar en tilldelning med hjälp av portalen genererar Azure Policy båda den hanterade identiteten och ger den roller som definierats i **roleDefinitionIds**. I följande fall måste steg för att skapa den hanterade identiteten och tilldela IT-behörigheter göras manuellt:

- När du använder SDK (till exempel Azure PowerShell)
- När en resurs utanför tilldelnings omfånget ändras av mallen
- När en resurs utanför tilldelnings omfånget läses av mallen

### <a name="create-managed-identity-with-powershell"></a>Skapa hanterad identitet med PowerShell

Om du vill skapa en hanterad identitet under tilldelningen av principen måste **plats** definieras och **AssignIdentity** användas. I följande exempel hämtas definitionen av den inbyggda principen **distribuera transparent data kryptering i SQL DB**, ställer in mål resurs gruppen och skapar sedan tilldelningen.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment`Variabeln innehåller nu det primära ID: t för den hanterade identiteten tillsammans med de standardvärden som returneras när du skapar en princip tilldelning. Den kan nås via `$assignment.Identity.PrincipalId` .

### <a name="grant-defined-roles-with-powershell"></a>Bevilja definierade roller med PowerShell

Den nya hanterade identiteten måste slutföra replikeringen via Azure Active Directory innan den kan beviljas nödvändiga roller. När replikeringen är klar itererar följande exempel princip definitionen i `$policyDef` för **roleDefinitionIds** och använder [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) för att ge den nya hanterade identiteten roller.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Bevilja definierade roller via portalen

Det finns två sätt att ge en tilldelnings hanterade identitet de definierade rollerna med hjälp av portalen, med hjälp av **åtkomst kontroll (IAM)** eller genom att redigera principen eller initiativ tilldelningen och välja **Spara**.

Följ dessa steg om du vill lägga till en roll i tilldelningens hanterade identitet:

1. Starta tjänsten Azure Policy i Azure Portal genom att välja **alla tjänster** och sedan söka efter och välja **princip**.

1. Välj **Tilldelningar** till vänster på sidan Azure Policy.

1. Leta upp tilldelningen med en hanterad identitet och välj namnet.

1. Hitta egenskapen **tilldelnings-ID** på sidan Redigera. Tilldelnings-ID: t ser ut ungefär så här:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Namnet på den hanterade identiteten är den sista delen av tilldelningens resurs-ID, som är `2802056bfc094dfb95d4d7a5` i det här exemplet. Kopiera den här delen av tilldelningens resurs-ID.

1. Gå till resursen eller den överordnade resurs behållaren (resurs grupp, prenumeration, hanterings grupp) som behöver roll definitionen manuellt tillagda.

1. Välj länken **åtkomst kontroll (IAM)** på sidan resurser och välj sedan **+ Lägg till roll tilldelning** överst på sidan åtkomst kontroll.

1. Välj lämplig roll som matchar en **roleDefinitionIds** från princip definitionen.
   Lämna **behörigheten tilldela** till standardvärdet för Azure AD-användare, grupp eller program. I rutan **Välj** klistrar du in eller skriver den del av tilldelningens resurs-ID som finns tidigare. När sökningen är klar markerar du objektet med samma namn för att välja ID och väljer **Spara**.

## <a name="create-a-remediation-task"></a>Skapa en reparations uppgift

### <a name="create-a-remediation-task-through-portal"></a>Skapa en reparations uppgift via portalen

Under utvärderingen bestämmer princip tilldelningen med **deployIfNotExists** eller **ändra** effekter om det finns icke-kompatibla resurser eller prenumerationer. När icke-kompatibla resurser eller prenumerationer hittas finns informationen på **reparations** sidan. Tillsammans med listan över principer som har icke-kompatibla resurser eller prenumerationer är alternativet att utlösa en **reparations uppgift**.
Det här alternativet är vad som skapar en distribution från **deployIfNotExists** -mallen eller **ändra** -åtgärder.

Följ dessa steg om du vill skapa en **reparations uppgift**:

1. Starta tjänsten Azure Policy i Azure Portal genom att välja **alla tjänster** och sedan söka efter och välja **princip**.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="Skärm bild av sökning efter princip i alla tjänster." border="false":::

1. Välj **reparation** till vänster på sidan Azure policy.

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Skärm bild av reparations noden på princip sidan." border="false":::

1. Alla **deployIfNotExists** och **ändra** princip tilldelningar med icke-kompatibla resurser ingår i **principerna för att åtgärda** fliken och data tabellen. Välj en princip med resurser som inte är kompatibla. Sidan **ny reparations aktivitet** öppnas.

   > [!NOTE]
   > Ett annat sätt att öppna sidan **reparations uppgift** är att söka efter och välja principen på sidan **efterlevnad** och sedan välja knappen **skapa reparations uppgift** .

1. På sidan **ny reparations aktivitet** filtrerar du de resurser som ska åtgärdas genom att använda **omfångs** punkter för att välja underordnade resurser från där principen tilldelas (inklusive enskilda resurs objekt). Använd dessutom List rutan **platser** för att ytterligare filtrera resurserna. Endast resurser som anges i tabellen kommer att åtgärdas.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="Skärm bild av den reparerande noden och det rutnät med resurser som ska åtgärdas." border="false":::

1. Påbörja reparations åtgärden när resurserna har filtrerats genom att välja **åtgärda**. Sidan policy efterlevnad öppnas på fliken **reparations aktiviteter** för att visa status för aktiviteternas förlopp. Distributioner som skapats av reparations aktiviteten börjar direkt.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="Skärm bild av fliken reparations aktiviteter och förlopp för befintliga reparations aktiviteter." border="false":::

1. Välj på **reparations uppgiften** från sidan efterlevnadsprincip för att få information om förloppet. Filtreringen som används för aktiviteten visas tillsammans med en lista över de resurser som åtgärdas.

1. På sidan **reparations aktivitet** högerklickar du på en resurs för att Visa reparations aktivitetens distribution eller resursen. I slutet av raden väljer du **relaterade händelser** för att se information, till exempel ett fel meddelande.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="Skärm bild av snabb menyn för en resurs på fliken åtgärda uppgift." border="false":::

Resurser som distribueras via en **reparations uppgift** läggs till på fliken **distribuerade resurser** på sidan efterlevnad av principer.

### <a name="create-a-remediation-task-through-azure-cli"></a>Skapa en reparations uppgift via Azure CLI

Använd kommandona om du vill skapa en **reparations uppgift** med Azure CLI `az policy remediation` . Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med ditt **deployIfNotExists** eller **ändra** princip tilldelnings-ID.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra reparations kommandon och exempel finns i kommandona för att [Reparera AZ-principer](/cli/azure/policy/remediation) .

### <a name="create-a-remediation-task-through-azure-powershell"></a>Skapa en reparations uppgift via Azure PowerShell

Om du vill skapa en **reparations uppgift** med Azure PowerShell använder du `Start-AzPolicyRemediation` kommandona. Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med ditt **deployIfNotExists** eller **ändra** princip tilldelnings-ID.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra reparations-cmdletar och exempel finns i modulen [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Skapa en reparations uppgift under princip tilldelningen i Azure Portal

Ett effektiviserat sätt att skapa en reparations uppgift är att göra det från Azure Portal under princip tilldelningen. Om princip definitionen att tilldela är en **deployIfNotExists** eller en **ändrings** inverkan, erbjuder guiden på fliken **reparation** ett alternativ för att _skapa en åtgärd_ . Om det här alternativet är markerat skapas en reparations uppgift samtidigt som princip tilldelningen.

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](get-compliance-data.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
