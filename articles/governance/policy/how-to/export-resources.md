---
title: Exportera Azure Policy-resurser
description: Lär dig att exportera Azure Policy resurser till GitHub, till exempel princip definitioner och princip tilldelningar.
ms.date: 03/31/2021
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fd2a24d2c01b4214eda88054b45a409cd865a32
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096554"
---
# <a name="export-azure-policy-resources"></a>Exportera Azure Policy-resurser

Den här artikeln innehåller information om hur du exporterar befintliga Azure Policy-resurser. Att exportera dina resurser är användbart och rekommenderas för säkerhets kopiering, men är också ett viktigt steg i resan med moln styrning och behandling av din [princip som kod](../concepts/policy-as-code.md). Azure Policy resurser kan exporteras via [Azure Portal](#export-with-azure-portal), [Azure CLI](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)och var och en av de SDK: er som stöds.

## <a name="export-with-azure-portal"></a>Exportera med Azure Portal

Följ dessa steg om du vill exportera en princip definition från Azure Portal:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. Välj **definitioner** till vänster på sidan Azure policy.

1. Använd knappen **Exportera definitioner** eller Välj ellipsen på raden i en princip definition och välj sedan **Exportera definition**.

1. Välj knappen **Logga in med GitHub** . Om du ännu inte har autentiserat med GitHub för att auktorisera Azure Policy att exportera resursen, granskar du åtkomsten till [GitHub-åtgärden](https://github.com/features/actions) i det nya fönstret som öppnas och väljer **Godkänn AzureGitHubActions** för att fortsätta med export processen. När det är klart stängs det nya fönstret.

1. På fliken **grundläggande** inställningar anger du följande alternativ och väljer sedan fliken **principer** eller **Nästa: principer** -knappen längst ned på sidan.

   - **Lagrings filter**: Ställ in på _mina databaser_ om du bara vill se de databaser som du äger eller _alla_ lagrings platser för att se alla du har beviljat åtgärden GitHub.
   - **Databas**: Ange till den lagrings plats som du vill exportera Azure policy resurserna till.
   - **Gren**: Ange grenen i lagrings platsen. Att använda en annan gren än standardinställningen är ett bra sätt att verifiera dina uppdateringar innan du sammanfogar dem till käll koden.
   - **Katalog**: _rotmappen_ som Azure policy resurserna ska exporteras till. Undermappar under den här katalogen skapas baserat på vilka resurser som exporteras.

1. På fliken **principer** anger du Sök omfång genom att välja ellipsen och välja en kombination av hanterings grupper, prenumerationer eller resurs grupper.
   
1. Använd knappen **Lägg till princip definition (n)** för att söka efter de objekt som ska exporteras. I fönstret som öppnas väljer du varje objekt som ska exporteras. Filtrera markeringen efter sökrutan eller typ. När du har valt alla objekt som ska exporteras använder du knappen **Lägg till** längst ned på sidan.

1. För varje valt objekt väljer du önskade export alternativ, till exempel _definition_ eller _definition och tilldelning (er)_ för en princip definition. Välj sedan fliken **Granska + exportera** eller **Nästa: granska + exportera** -knappen längst ned på sidan.

   > [!NOTE]
   > Om du väljer alternativ _definition och tilldelning (er)_ , exporteras endast princip tilldelningar inom det område som anges av filtret när princip definitionen läggs till.

1. På fliken **Granska och exportera** kontrollerar du informationen och använder sedan knappen **Exportera** längst ned på sidan.

1. Kontrol lera mappen GitHub lagrings platsen, Branch och _root Level_ för att se att de valda resurserna nu har exporter ATS till din käll kontroll.

Azure Policy resurserna exporteras till följande struktur i den valda GitHub-lagringsplatsen och i _mappen rot nivå_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exportera med Azure CLI

Azure Policy definitioner, initiativ och tilldelningar kan båda exporteras som JSON med [Azure CLI](/cli/azure/install-azure-cli). Vart och ett av dessa kommandon använder en **namn** parameter för att ange vilket objekt som JSON ska hämtas för. Egenskapen **namn** är ofta ett _GUID_ och inte objektets **DisplayName** .

- Definition – [AZ princip definition show](/cli/azure/policy/definition#az_policy_definition_show)
- Initiativ – [AZ princip set-definition show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Tilldelning – [AZ princip tilldelning show](/cli/azure/policy/assignment#az_policy_assignment_show)

Här är ett exempel på att hämta JSON för en princip definition med **namnet** _VirtualMachineStorage_:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exportera med Azure PowerShell

Azure Policy definitioner, initiativ och tilldelningar kan exporteras som JSON med [Azure PowerShell](/powershell/azure/). Var och en av dessa cmdletar använder en **namn** parameter för att ange vilket objekt som JSON ska hämtas för. Egenskapen **namn** är ofta ett _GUID_ och inte objektets **DisplayName** .

- Definition- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Initiativ- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Tilldelning- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Här är ett exempel på att hämta JSON för en princip definition med **namnet** _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
