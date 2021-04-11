---
title: Bicep-operatörer
description: Beskriver de bicep-operatörer som är tillgängliga för Azure Resource Manager-distributioner.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211335"
---
# <a name="bicep-operators"></a>Bicep-operatörer

I den här artikeln beskrivs de bicep-operatörer som är tillgängliga när du skapar en bicep-mall och använder Azure Resource Manager för att distribuera resurser. Operatorer används för att beräkna värden, jämföra värden eller utvärdera villkor. Det finns tre typer av bicep-operatorer: [jämförelse](#comparison), [logiska](#logical)och [numeriska](#numeric).

## <a name="comparison"></a>Jämförelse

Jämförelse operatorer jämför värden och returnerar antingen `true` eller `false` .

| Operator | Name | Beskrivning |
| ---- | ---- | ---- |
| `>=` | [Större än eller lika med](bicep-operators-comparison.md#greater-than-or-equal-) | Utvärderar om det första värdet är större än eller lika med det andra värdet. |
| `>`  | [Större än](bicep-operators-comparison.md#greater-than-) | Utvärderar om det första värdet är större än det andra värdet. |
| `<=` | [Mindre än eller lika med](bicep-operators-comparison.md#less-than-or-equal-) | Utvärderar om det första värdet är mindre än eller lika med det andra värdet. |
| `<`  | [Mindre än](bicep-operators-comparison.md#less-than-) | Utvärderar om det första värdet är mindre än det andra värdet. |
| `==` | [Lika med](bicep-operators-comparison.md#equals-) | Utvärderar om två värden är lika. |
| `!=` | [Inte lika med](bicep-operators-comparison.md#not-equal-) | Utvärderar om två värden **inte** är lika. |
| `=~` | [Lika med Skift läges okänslig](bicep-operators-comparison.md#equal-case-insensitive-) | Ignorerar Skift läge för att avgöra om två värden är lika. |
| `!~` | [Inte lika med Skift läge](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignorerar Skift läge för att avgöra om två värden **inte** är lika. |

## <a name="logical"></a>Logiskt

Logiska operatorer utvärderar booleska värden, returnerar värden som inte är null eller utvärderar ett villkors uttryck.

| Operator | Name | Beskrivning |
| ---- | ---- | ---- |
| `&&` | [Särskilt](bicep-operators-logical.md#and-) | Returnerar `true` om alla värden är true. |
| `||`| [Eller](bicep-operators-logical.md#or-) | Returnerar `true` om något av värdena är true. |
| `!` | [Ogiltigt](bicep-operators-logical.md#not-) | Negerar ett booleskt värde. |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | Returnerar det första värdet som inte är null. |
| `?` `:` | [Villkors uttryck](bicep-operators-logical.md#conditional-expression--) | Utvärderar ett villkor för True eller false och returnerar ett värde. |

## <a name="numeric"></a>Numerisk

De numeriska operatorerna använder heltal för att utföra beräkningar och returnera heltals värden.

| Operator | Name | Beskrivning |
| ---- | ---- | ---- |
| `*` | [Multiplicera](bicep-operators-numeric.md#multiply-) | Multiplicerar två heltal. |
| `/` | [Dela](bicep-operators-numeric.md#divide-) | Dividerar ett heltal med ett heltal. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Dividerar ett heltal med ett heltal och returnerar resten. |
| `+` | [Lägg till](bicep-operators-numeric.md#add-) | Lägger till två heltal. |
| `-` | [Subtrahera](bicep-operators-numeric.md#subtract--) | Subtraherar ett heltal från ett heltal. |
| `-` | [Minus](bicep-operators-numeric.md#minus--) | Multiplicerar ett heltal med `-1` . |

> [!NOTE]
> Subtrahera och minus Använd samma operator. Funktionen skiljer sig åt eftersom subtrahera använder två operander och minus använder en operand.

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en bicep-fil finns i [Självstudier: skapa och distribuera den första Azure Resource Manager bicep-filen](bicep-tutorial-create-first-bicep.md).
- Information om hur du löser bicep typ fel finns i [valfri funktion för bicep](template-functions-any.md).
- Om du vill jämföra syntaxen för bicep och JSON, se [jämföra JSON och bicep for templates](compare-template-syntax.md).
- Exempel på funktioner för bicep och ARM-mallar finns i [funktioner i arm-mallar](template-functions.md).
