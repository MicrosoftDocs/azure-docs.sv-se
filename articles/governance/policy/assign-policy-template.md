---
title: 'Snabb start: ny princip tilldelning med mallar'
description: I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en princip tilldelning som identifierar icke-kompatibla resurser.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 93c8e907190a3e87c3c0ce3392ccfd7fd4d2e515
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520880"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med hjälp av en ARM-mall

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Den här snabb starten vägleder dig genom processen med att använda en Azure Resource Manager-mall (ARM-mall) för att skapa en princip tilldelning för att identifiera virtuella datorer som inte använder hanterade diskar. Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera ARM-mallen för att tilldela en Azure Policy till Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

I den här snabb starten skapar du en princip tilldelning och tilldelar en inbyggd princip definition som kallas _Granska virtuella datorer som inte använder hanterade diskar_. En lista över tillgängliga inbyggda principer finns i [Azure policy exempel](./samples/index.md).

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-30" highlight="20-28":::

Den resurs som definierats i mallen är:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Distribuera mallen

> [!NOTE]
> Azure Policys tjänsten är kostnads fri. Mer information finns i [Översikt över Azure policy](./overview.md).

1. Logga in på Azure-portalen och öppna mallen genom att välja följande bild:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera ARM-mallen för att tilldela en Azure Policy till Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. Välj eller ange följande värden:

   | Name | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Välj **Skapa ny**, ange ett namn och välj sedan **OK**. I skärm bilden är resurs gruppens namn _mypolicyquickstart \<Date in MMDD\> RG_. |
   | Plats | Välj en region. Välj till exempel **USA, centrala**. |
   | Princip tilldelnings namn | Ange ett princip tilldelnings namn. Du kan använda princip definitions visningen om du vill. Granska till exempel _virtuella datorer som inte använder hanterade diskar_. |
   | RG namn | Ange ett resurs grupps namn som du vill tilldela principen till. I den här snabb starten använder du standardvärdet **[resourceGroup (). name]**. **[resourceGroup ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** är en mall-funktion som hämtar resurs gruppen. |
   | ID för princip definition | Ange **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-B460-a2d36003525a**. |
   | Jag samtycker till de villkor som anges ovan | Select |

1. Välj **Köp**.

Vissa ytterligare resurser:

- Du hittar fler exempel på mallar i [Azure snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mal linne Reference går du till [referens för Azure-mallar](/azure/templates/microsoft.authorization/allversions).
- Information om hur du utvecklar ARM-mallar finns i [Azure Resource Manager-dokumentation](../../azure-resource-manager/management/overview.md).
- Information om distribution på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen _Granska virtuella datorer som inte använder hanterade diskar_ som du skapade.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Översikts sida för policy efterlevnad" border="false":::

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

Mer information finns i [så här fungerar efterlevnad](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen _Granska virtuella datorer som inte använder hanterade diskar_ som du skapade.

1. Högerklicka på tjänsten _Granska virtuella datorer som inte använder princip tilldelning för hanterade diskar_ och välj **ta bort tilldelning**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Ta bort en tilldelning från sidan Översikt över efterlevnad" border="false":::

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du tilldelat en inbyggd princip definition till ett definitions område och utvärderat dess Kompatibilitetsrapport. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)