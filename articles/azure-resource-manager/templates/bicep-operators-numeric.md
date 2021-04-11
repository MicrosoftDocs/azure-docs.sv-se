---
title: Bicep-numeriska operatorer
description: Beskriver bicep numeriska operatorer som beräknar värden.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211319"
---
# <a name="bicep-numeric-operators"></a>Bicep-numeriska operatorer

De numeriska operatorerna använder heltal för att utföra beräkningar och returnera heltals värden. Om du vill köra exemplen använder du Azure CLI eller Azure PowerShell för att [distribuera en bicep-fil](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Name |
| ---- | ---- |
| `*` | [Multiplicera](#multiply-) |
| `/` | [Dela](#divide-) |
| `%` | [Modulo](#modulo-) |
| `+` | [Lägg till](#add-) |
| `-` | [Subtrahera](#subtract--) |
| `-` | [Minus](#minus--) |

> [!NOTE]
> Subtrahera och minus Använd samma operator. Funktionen skiljer sig åt eftersom subtrahera använder två operander och minus använder en operand.

## <a name="multiply-"></a>Återskapa

`operand1 * operand2`

Multiplicerar två heltal.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1`  | heltal | Tal att multiplicera. |
| `operand2`  | heltal  | Multiplikatorn för talet. |

### <a name="return-value"></a>Returvärde

Multiplikationen returnerar produkten som ett heltal.

### <a name="example"></a>Exempel

Två heltal multipliceras och returnerar produkten.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `product` | heltal | 10 |

## <a name="divide-"></a>Dela

`operand1 / operand2`

Dividerar ett heltal med ett heltal.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | heltal | Heltal som delas. |
| `operand2` | heltal | Heltal som används för division. Får inte vara noll. |

### <a name="return-value"></a>Returvärde

Divisionen returnerar kvoten som ett heltal.

### <a name="example"></a>Exempel

Två heltal delas och returnerar kvoten.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `quotient` | heltal | 5 |

## <a name="modulo-"></a>Modulo

`operand1 % operand2`

Dividerar ett heltal med ett heltal och returnerar resten.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1`  | heltal  | Det heltal som delas. |
| `operand2`  | heltal  | Det heltal som används för division. Kan inte vara 0. |

### <a name="return-value"></a>Returvärde

Resten returneras som ett heltal. Om divisionen inte genererar en rest returneras 0.

### <a name="example"></a>Exempel

Två par heltal delas och returnerar resten.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `remainder` | heltal | 1 |
| `zeroRemainder` | heltal | 0 |

## <a name="add-"></a>Lägg till +

`operand1 + operand2`

Lägger till två heltal.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | heltal | Nummer att lägga till. |
| `operand2` | heltal | Tal som har lagts till i ett tal. |

### <a name="return-value"></a>Returvärde

Additionen Returnerar summan som ett heltal.

### <a name="example"></a>Exempel

Två heltal läggs till och Returnerar summan.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `sum` | heltal | 12 |

## <a name="subtract--"></a>Förminska

`operand1 - operand2`

Subtraherar ett heltal från ett heltal.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | heltal | Större tal som subtraheras från. |
| `operand2` | heltal | Tal som subtraheras från det större talet. |

### <a name="return-value"></a>Returvärde

Subtraktionen Returnerar skillnaden som ett heltal.

### <a name="example"></a>Exempel

Ett heltal subtraheras och returnerar skillnaden.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `difference` | heltal | 6 |

## <a name="minus--"></a>Minus

`-integerValue`

Multiplicerar ett heltal med `-1` .

### <a name="operand"></a>Operand

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `integerValue` | heltal | Heltal multiplicerat med `-1` . |

### <a name="return-value"></a>Returvärde

Ett heltal multipliceras med `-1` . Ett positivt heltal returnerar ett negativt heltal och ett negativt heltal returnerar ett positivt heltal. Värdena kan omges av parenteser.

### <a name="example"></a>Exempel

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `startedPositive` | heltal | −10 |
| `startedNegative` | heltal | 20 |

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en bicep-fil finns i [Självstudier: skapa och distribuera den första Azure Resource Manager bicep-filen](bicep-tutorial-create-first-bicep.md).
- Information om hur du löser bicep typ fel finns i [valfri funktion för bicep](template-functions-any.md).
- Om du vill jämföra syntaxen för bicep och JSON, se [jämföra JSON och bicep for templates](compare-template-syntax.md).
- Exempel på funktioner för bicep och ARM-mallar finns i [funktioner i arm-mallar](template-functions.md).
