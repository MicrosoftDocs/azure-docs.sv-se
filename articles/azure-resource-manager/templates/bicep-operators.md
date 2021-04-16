---
title: Bicep-operatorer
description: Beskriver de Bicep-operatorer som är Azure Resource Manager distributioner.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537824"
---
# <a name="bicep-operators"></a>Bicep-operatorer

I den här artikeln beskrivs de Bicep-operatorer som är tillgängliga när du skapar en Bicep-mall och använder Azure Resource Manager för att distribuera resurser. Operatorer används för att beräkna värden, jämföra värden eller utvärdera villkor. Det finns tre typer av Bicep-operatorer:

- [Jämförelse](#comparison)
- [Logiska](#logical)
- [Numeriska](#numeric)

Genom att omsluta ett uttryck mellan `(` och `)` kan du åsidosätta standardvärdet för Bicep-operatorn. Uttrycket x + y/z utvärderar till exempel divisionen först och sedan additionen. Uttrycket (x + y) /z utvärderar dock additionen först och division andra.

## <a name="comparison"></a>Jämförelse

Jämförelseoperatorer jämför värden och returnerar antingen `true` eller `false` .

| Operator | Name | Beskrivning |
| ---- | ---- | ---- |
| `>=` | [Större än eller lika med](bicep-operators-comparison.md#greater-than-or-equal-) | Utvärderar om det första värdet är större än eller lika med det andra värdet. |
| `>`  | [Större än](bicep-operators-comparison.md#greater-than-) | Utvärderar om det första värdet är större än det andra värdet. |
| `<=` | [Mindre än eller lika med](bicep-operators-comparison.md#less-than-or-equal-) | Utvärderar om det första värdet är mindre än eller lika med det andra värdet. |
| `<`  | [Mindre än](bicep-operators-comparison.md#less-than-) | Utvärderar om det första värdet är mindre än det andra värdet. |
| `==` | [Lika med](bicep-operators-comparison.md#equals-) | Utvärderar om två värden är lika. |
| `!=` | [Inte lika med](bicep-operators-comparison.md#not-equal-) | Utvärderar om två värden inte **är lika.** |
| `=~` | [Lika med icke-okänsligt](bicep-operators-comparison.md#equal-case-insensitive-) | Ignorerar ärendet för att avgöra om två värden är lika. |
| `!~` | [Inte lika med icke-okänsligt](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignorerar fall för att avgöra om två värden inte **är** lika. |

## <a name="logical"></a>Logiskt

De logiska operatorerna utvärderar booleska värden, returnerar värden som inte är null eller utvärderar ett villkorsuttryck.

| Operator | Name | Beskrivning |
| ---- | ---- | ---- |
| `&&` | [Och](bicep-operators-logical.md#and-) | Returnerar `true` om alla värden är sanna. |
| `||`| [Eller](bicep-operators-logical.md#or-) | Returnerar `true` om något av dessa värden är sant. |
| `!` | [Inte](bicep-operators-logical.md#not-) | Negerar ett booleskt värde. |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | Returnerar det första värdet som inte är null. |
| `?` `:` | [Villkorsuttryck](bicep-operators-logical.md#conditional-expression--) | Utvärderar ett villkor för sant eller falskt och returnerar ett värde. |

## <a name="numeric"></a>Numerisk

De numeriska operatorerna använder heltal för att göra beräkningar och returnera heltalsvärden.

| Operator | Name | Beskrivning |
| ---- | ---- | ---- |
| `*` | [Multiplicera](bicep-operators-numeric.md#multiply-) | Multiplicerar två heltal. |
| `/` | [Dela](bicep-operators-numeric.md#divide-) | Dividerar ett heltal med ett heltal. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Dividerar ett heltal med ett heltal och returnerar resten. |
| `+` | [Lägg till](bicep-operators-numeric.md#add-) | Lägger till två heltal. |
| `-` | [Subtrahera](bicep-operators-numeric.md#subtract--) | Subtraherar ett heltal från ett heltal. |
| `-` | [Minus](bicep-operators-numeric.md#minus--) | Multiplicerar ett heltal med `-1` . |

> [!NOTE]
> Subtrahera och minus använd samma operator. Funktionen skiljer sig eftersom subtrahering använder två operander och minus använder en operand.

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en Bicep-fil [finns i Självstudie: Skapa och distribuera först Azure Resource Manager Bicep-filen](bicep-tutorial-create-first-bicep.md).
- Information om hur du löser fel av Bicep-typ finns i [Valfri funktion för Bicep](template-functions-any.md).
- Om du vill jämföra syntax för Bicep och JSON, se [Jämföra JSON och Bicep för mallar](compare-template-syntax.md).
- Exempel på bicep- och ARM-mallfunktioner finns i [ARM-mallfunktioner.](template-functions.md)
