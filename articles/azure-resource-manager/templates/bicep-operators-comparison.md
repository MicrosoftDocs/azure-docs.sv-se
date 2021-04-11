---
title: Jämförelse operatorer för bicep
description: Beskriver bicep-jämförelse operatorer som jämför värden.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211320"
---
# <a name="bicep-comparison-operators"></a>Jämförelse operatorer för bicep

Jämförelse operatorer jämför värden och returnerar antingen `true` eller `false` . Om du vill köra exemplen använder du Azure CLI eller Azure PowerShell för att [distribuera en bicep-fil](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Name |
| ---- | ---- |
| `>=` | [Större än eller lika med](#greater-than-or-equal-) |
| `>`  | [Större än](#greater-than-) |
| `<=` | [Mindre än eller lika med](#less-than-or-equal-) |
| `<`  | [Mindre än](#less-than-) |
| `==` | [Lika med](#equals-) |
| `!=` | [Inte lika med](#not-equal-) |
| `=~` | [Lika med Skift läges okänslig](#equal-case-insensitive-) |
| `!~` | [Inte lika med Skift läge](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Större än eller lika med >=

`operand1 >= operand2`

Utvärderar om det första värdet är större än eller lika med det andra värdet.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | heltal, sträng | Första värdet i jämförelsen. |
| `operand2` | heltal, sträng | Andra värdet i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om det första värdet är större än eller lika med det andra värdet `true` returneras. Annars `false` returneras.

### <a name="example"></a>Exempel

Ett par av heltal och sträng par jämförs.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Större än >

`operand1 > operand2`

Utvärderar om det första värdet är större än det andra värdet.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | heltal, sträng | Första värdet i jämförelsen. |
| `operand2` | heltal, sträng | Andra värdet i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om det första värdet är större än det andra värdet `true` returneras. Annars `false` returneras.

### <a name="example"></a>Exempel

Ett par av heltal och sträng par jämförs.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Utdata från exemplet:

**E** i **böj** är den första strängen större.

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Mindre än eller lika med <=

`operand1 <= operand2`

Utvärderar om det första värdet är mindre än eller lika med det andra värdet.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | heltal, sträng | Första värdet i jämförelsen. |
| `operand2` | heltal, sträng | Andra värdet i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om det första värdet är mindre än eller lika med det andra värdet `true` returneras. Annars `false` returneras.

### <a name="example"></a>Exempel

Ett par av heltal och sträng par jämförs.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Mindre än <

`operand1 < operand2`

Utvärderar om det första värdet är mindre än det andra värdet.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | heltal, sträng | Första värdet i jämförelsen. |
| `operand2` | heltal, sträng | Andra värdet i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om det första värdet är mindre än det andra värdet `true` returneras. Annars `false` returneras.

### <a name="example"></a>Exempel

Ett par av heltal och sträng par jämförs.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Utdata från exemplet:

Strängen beror på `true` att gemener är mindre än versaler.

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Är lika med = =

`operand1 == operand2`

Utvärderar om värdena är lika.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | sträng, heltal, boolesk, matris, objekt | Första värdet i jämförelsen. |
| `operand2` | sträng, heltal, boolesk, matris, objekt | Andra värdet i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om operanderna är lika `true` returneras. Om operanderna skiljer sig `false` returneras.

### <a name="example"></a>Exempel

Par av heltal, strängar och booleska värden jämförs.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

## <a name="not-equal-"></a>Inte lika med! =

`operand1 != operand2`

Utvärderar om två värden **inte** är lika.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | sträng, heltal, boolesk, matris, objekt | Första värdet i jämförelsen. |
| `operand2` | sträng, heltal, boolesk, matris, objekt | Andra värdet i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om operanderna **inte** är lika `true` returneras. Om operanderna är lika `false` returneras.

### <a name="example"></a>Exempel

Par av heltal, strängar och booleska värden jämförs.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

## <a name="equal-case-insensitive-"></a>Lika med Skift läges okänslig = ~

`operand1 =~ operand2`

Ignorerar Skift läge för att avgöra om de två värdena är lika.

### <a name="operands"></a>Operander

| Operand | Typ | Description |
| ---- | ---- | ---- |
| `operand1`  | sträng | Första strängen i jämförelsen. |
| `operand2`  | sträng | Andra strängen i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om strängarna är lika `true` returneras. Annars `false` returneras.

### <a name="example"></a>Exempel

Jämför strängar som använder blandade versaler.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | falskt |

## <a name="not-equal-case-insensitive-"></a>Inte lika med Skift läge – ~

`operand1 !~ operand2`

Ignorerar Skift läge för att avgöra om de två värdena **inte** är lika.

### <a name="operands"></a>Operander

| Operand | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | sträng | Första strängen i jämförelsen. |
| `operand2` | sträng | Andra strängen i jämförelsen. |

### <a name="return-value"></a>Returvärde

Om strängarna **inte** är lika `true` returneras. Annars `false` returneras.

### <a name="example"></a>Exempel

Jämför strängar som använder blandade versaler.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | falskt |

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en bicep-fil finns i [Självstudier: skapa och distribuera den första Azure Resource Manager bicep-filen](bicep-tutorial-create-first-bicep.md).
- Information om hur du löser bicep typ fel finns i [valfri funktion för bicep](template-functions-any.md).
- Om du vill jämföra syntaxen för bicep och JSON, se [jämföra JSON och bicep for templates](compare-template-syntax.md).
- Exempel på funktioner för bicep och ARM-mallar finns i [funktioner i arm-mallar](template-functions.md).
