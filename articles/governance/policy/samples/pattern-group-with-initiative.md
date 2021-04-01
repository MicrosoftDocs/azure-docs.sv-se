---
title: 'Mönster: grup princip definitioner med initiativ'
description: Detta Azure Policy mönster ger ett exempel på hur du kan gruppera princip definitioner i ett initiativ.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: aa09cafe636a4665dba6a2e746c13b95ff304895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072925"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy mönster: grup princip definitioner

Ett initiativ är en grupp princip definitioner. Genom att gruppera relaterade princip definitioner till ett enda objekt kan du skapa en enskild tilldelning som har varit flera tilldelningar.

## <a name="sample-initiative-definition"></a>Exempel på initiativ definition

Det här initiativet distribuerar två princip definitioner, som var och en tar parametrarna **TagName** och **tagValue** . Själva initiativet har två parametrar: **costCenterValue** och **productNameValue**.
Dessa initiativ parametrar är de som anges för var och en av de grupperade princip definitionerna. Den här designen maximerar åter användning av befintliga princip definitioner och begränsar antalet tilldelningar som har skapats för att implementera dem efter behov.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Förklaring

#### <a name="initiative-parameters"></a>Initiativparametrar

Ett initiativ kan definiera egna parametrar som sedan skickas till de grupperade princip definitionerna.
I det här exemplet definieras både **costCenterValue** och **productNameValue** som initiativ parametrar. Värdena anges när initiativet tilldelas.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Innehåller princip definitioner

Varje princip definition som ingår måste tillhandahålla **policyDefinitionId** och en **parameter** mat ris om princip definitionen accepterar parametrar. I kodfragmentet nedan tar den inkluderade princip definitionen två parametrar: **TagName** och **tagValue**. **TagName** definieras med en literal, men **TagValue** använder parametern **costCenterValue** som definieras av initiativet. Den här genom strömningen av värden förbättrar åter användningen.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Nästa steg

- Granska andra [mönster och inbyggda definitioner](./index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).