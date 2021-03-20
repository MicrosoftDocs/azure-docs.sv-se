---
title: 'Mönster: påverkan av en princip definition'
description: Detta Azure Policy mönster ger exempel på hur du kan använda de olika effekterna av en princip definition.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: f1da9bd153707db35c07ed3c176542797a694d7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92073044"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy mönster: effekter

Azure Policy har ett antal [effekter](../concepts/effects.md) som avgör hur tjänsten reagerar på icke-kompatibla resurser. Vissa effekter är enkla och kräver inga ytterligare egenskaper i princip definitionen medan andra kräver flera egenskaper.

## <a name="sample-1-simple-effect"></a>Exempel 1: enkel påverkan

Den här princip definitionen kontrollerar om taggen som definierats i parametern **TagName** finns på den utvärderade resursen. Om taggen ännu inte finns utlöses [ändrings](../concepts/effects.md#modify) resultatet för att lägga till taggen med värdet i parametern **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Exempel 1: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

En **ändrings** funktion kräver **policyRule. then. information** -block som definierar **roleDefinitionIds** och **åtgärder**. Dessa parametrar informerar Azure Policy vilka roller som behövs för att lägga till taggen och åtgärda resursen och vilken **ändrings** åtgärd som ska användas. I det här exemplet används **åtgärden** _Lägg till_ och parametrarna för att ange taggen och dess värde.

## <a name="sample-2-complex-effect"></a>Exempel 2: komplex påverkan

Den här princip definitionen granskar varje virtuell dator för när ett tillägg, definierat i parametrarna **utgivare** och **typ**, inte finns. Den använder [auditIfNotExists](../concepts/effects.md#auditifnotexists) för att kontrol lera en resurs som är relaterad till den virtuella datorn för att se om det finns en instans som matchar de definierade parametrarna. I det här exemplet kontrol leras typen av **tillägg** .

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Exempel 2: förklaring

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

En **auditIfNotExists** -påverkan kräver **policyRule. then. information** block för att definiera både en **typ** och den **existenceCondition** som ska sökas efter. **ExistenceCondition** använder princip språk element, till exempel [logiska operatorer](../concepts/definition-structure.md#logical-operators), för att avgöra om det finns en matchande relaterad resurs. I det här exemplet definieras värdena som kontrol leras mot varje [alias](../concepts/definition-structure.md#aliases) i parametrar.

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).