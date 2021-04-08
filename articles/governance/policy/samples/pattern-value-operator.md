---
title: 'Mönster: värde operatorn i en princip definition'
description: Detta Azure Policy mönster innehåller ett exempel på hur du använder värde operatorn i en princip definition.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 8392c69ff3d63ff4ecad2a26d5d914b4766147b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072891"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy mönster: värde operatorn

[Värde](../concepts/definition-structure.md#value) operatorn utvärderar [parametrar](../concepts/definition-structure.md#parameters), mall- [funktioner som stöds](../concepts/definition-structure.md#policy-functions)eller litteraler till ett angivet värde för ett givet [villkor](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Om resultatet av en _mall_ är ett fel, Miss lyckas princip utvärderingen. En misslyckad utvärdering är en implicit **nekande**. Mer information finns i [undvika mall-haverier](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Exempel på princip definition

Den här princip definitionen lägger till eller ersätter taggen som anges i parametern **TagName** (_sträng_) på resurser och ärver värdet för **TagName** från resurs gruppen som resursen finns i. Den här utvärderingen sker när resursen har skapats eller uppdaterats. Som en [ändrings](../concepts/effects.md#modify) funktion kan reparationen köras på befintliga resurser via en [reparations uppgift](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

**Värde** operatorn används i **policyRule. if** -block i **Egenskaper**. I det här exemplet används den [logiska operatorn](../concepts/definition-structure.md#logical-operators) **allOf** för att ange att båda villkors satserna måste vara sanna för att **ändringen** ska äga rum.

**värdet** utvärderar resultatet av [resourceGroup ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) till villkoret **notEquals** för ett tomt värde. Om det taggnamn som anges i **TagName** för den överordnade resurs gruppen finns, utvärderas villkoret till sant.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).