---
title: 'Mönster: använda taggar i en princip definition'
description: Detta Azure Policy mönster innehåller exempel på hur du lägger till parametriserade taggar eller ärver taggar från en resurs grupp i en princip definition.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3016fc7889f68fd13e993c67ca645a4af055c651
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545564"
---
# <a name="azure-policy-pattern-tags"></a>Azure Policy mönster: Taggar

[Taggar](../../..//azure-resource-manager/management/tag-resources.md) är en viktig del av hantering, organisering och styrning av dina Azure-resurser. Azure Policy gör det möjligt att konfigurera taggar på nya och befintliga resurser i stor skala med hjälp av [åtgärderna](../how-to/remediate-resources.md) [ändra](../concepts/effects.md#modify) effekter och åtgärder.

## <a name="sample-1-parameterize-tags"></a>Exempel 1: Parameterisera-Taggar

Den här princip definitionen använder två parametrar, **TagName** och **tagValue** för att ange vad princip tilldelningen söker efter i resurs grupper. Med det här formatet kan du använda princip definitionen för valfritt antal kombinationer av taggnamn och kombinationer av taggar, men endast underhålla en enda princip definition.

> [!NOTE]
> Även om det här princip definitions mönstret liknar det som finns i [mönstret: Parameters-Sample #1](./pattern-parameters.md#sample-1-string-parameters), använder det här exemplet **läget** _alla_ och mål resurs grupper.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

I det här exemplet är **läget** inställt på _alla_ eftersom det riktar sig till en resurs grupp. I de flesta fall ska **läget** vara inställt på _Indexerat_ när du arbetar med taggar. Mer information finns i [lägen](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

I den här delen av princip definitionen `concat` kombinerar parametern **TagName** -parameter och det `tags['name']` format som ska visas i **fältet** för att utvärdera taggen för parametern **tagValue**.
När **notEquals** används utlöses **ändrings** resultatet om **taggens \[ \] TagName** inte är lika med **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Här används samma format för att använda parameter värden i **addOrReplace** -åtgärden för att skapa eller uppdatera taggen till önskat värde i den utvärderade resurs gruppen.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Exempel 2: Ärv taggvärde från resurs gruppen

Den här princip definitionen använder parametern **TagName** för att fastställa vilken taggs värde som ska ärvas från den överordnade resurs gruppen.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

I det här exemplet är **läget** inställt på _Indexerat_ eftersom det inte har en resurs grupp eller prenumeration som mål, trots att värdet hämtas från en resurs grupp. Mer information finns i [lägen](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**PolicyRule. om** använder `concat` som [exempel #1](#sample-1-parameterize-tags) för att utvärdera **TagName**-värdet, men använder `resourceGroup()` funktionen för att jämföra det med värdet för samma tagg i den överordnade resurs gruppen. Den andra satsen här kontrollerar att taggen i resurs gruppen har ett värde och inte är null.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Här använder det värde som tilldelas till **TagName** -taggen på resursen även `resourceGroup()` funktionen för att hämta värdet från den överordnade resurs gruppen. På så sätt kan du ärva taggar från överordnade resurs grupper. Om du redan har skapat resursen men inte lagt till taggen, kan den här samma princip definition och en [reparations uppgift](../how-to/remediate-resources.md) uppdatera befintliga resurser.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).