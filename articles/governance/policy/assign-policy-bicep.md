---
title: 'Snabb start: ny princip tilldelning med bicep-fil (förhands granskning)'
description: I den här snabb starten använder du en bicep-fil (förhands granskning) för att skapa en princip tilldelning som identifierar icke-kompatibla resurser.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223990"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med hjälp av en bicep-fil

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
I den här snabb starten beskrivs hur du använder en [bicep-fil (för hands version)](https://github.com/Azure/bicep) som kompilerats till en Azure Resource Manager-mall (arm-mall) för att skapa en princip tilldelning för att identifiera virtuella datorer som inte använder hanterade diskar. Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas i Azure Portal.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Knapp för att distribuera ARM-mallen för att tilldela en Azure Policy till Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- Bicep-version `0.3` eller senare installerad. Om du ännu inte har bicep CLI eller behöver uppdatera kan du läsa [Installera bicep (för hands version)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Granska bicep-filen

I den här snabb starten skapar du en princip tilldelning och tilldelar en inbyggd princip definition som kallas _Granska virtuella datorer som inte använder hanterade diskar_ ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). En lista över tillgängliga inbyggda principer finns i [Azure policy exempel](./samples/index.md).

Skapa följande bicep-fil som `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

Resursen som definierats i filen är:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Distribuera mallen

> [!NOTE]
> Azure Policys tjänsten är kostnads fri. Mer information finns i [Översikt över Azure policy](./overview.md).

När bicep CLI har installerats och filen har skapats kan du distribuera bicep-filen med:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Vissa ytterligare resurser:

- Du hittar fler exempel på mallar i [Azure snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mal linne Reference går du till [referens för Azure-mallar](/azure/templates/microsoft.authorization/allversions).
- Information om hur du utvecklar ARM-mallar finns i [Azure Resource Manager-dokumentation](../../azure-resource-manager/management/overview.md).
- Information om distribution på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen _Granska virtuella datorer som inte använder hanterade diskar_ som du skapade.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Skärm bild av kompatibilitetsinformation om sidan policy efterlevnad." border="false":::

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

Mer information finns i [så här fungerar efterlevnad](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen _Granska virtuella datorer som inte använder hanterade diskar_ som du skapade.

1. Högerklicka på tjänsten _Granska virtuella datorer som inte använder princip tilldelning för hanterade diskar_ och välj **ta bort tilldelning**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Skärm bild som visar hur du använder snabb menyn för att ta bort en tilldelning från sidan efterlevnad." border="false":::

1. Ta bort `assignment.bicep` filen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du tilldelat en inbyggd princip definition till ett definitions område och utvärderat dess Kompatibilitetsrapport. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)