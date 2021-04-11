---
title: Logiska bicep-operatörer
description: Beskriver logiska bicep-operatorer som utvärderar villkor.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211336"
---
# <a name="bicep-logical-operators"></a>Logiska bicep-operatörer

Logiska operatorer utvärderar booleska värden, returnerar värden som inte är null eller utvärderar ett villkors uttryck. Om du vill köra exemplen använder du Azure CLI eller Azure PowerShell för att [distribuera en bicep-fil](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Name |
| ---- | ---- |
| `&&` | [Särskilt](#and-) |
| `||` | [Eller](#or-) |
| `!` | [Ogiltigt](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [Villkors uttryck](#conditional-expression--) |

## <a name="and-"></a>Och &&

`operand1 && operand2`

Anger om båda värdena är sanna.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | boolean | Det första värdet som ska kontrol leras om värdet är sant. |
| `operand2` | boolean | Det andra värdet för att kontrol lera om värdet är sant. |
| Fler operander | boolean | Fler operander kan inkluderas. |

### <a name="return-value"></a>Returvärde

`True` När båda värdena är true returneras annars `false` .

### <a name="example"></a>Exempel

Utvärderar en uppsättning parameter värden och en uppsättning uttryck.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Eller | |

`operand1 || operand2`

Anger om något av värdena är true.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | boolean | Det första värdet som ska kontrol leras om värdet är sant. |
| `operand2` | boolean | Det andra värdet för att kontrol lera om värdet är sant. |
| Fler operander | boolean | Fler operander kan inkluderas. |

### <a name="return-value"></a>Returvärde

`True` om något av värdena är true `false` returneras annars.

### <a name="example"></a>Exempel

Utvärderar en uppsättning parameter värden och en uppsättning uttryck.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Ogiltigt!

`!boolValue`

Negerar ett booleskt värde.

### <a name="operand"></a>Operand

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `boolValue` | boolean | Booleskt värde som är negationt. |

### <a name="return-value"></a>Returvärde

Negerar det inledande värdet och returnerar ett booleskt värde. Om det ursprungliga värdet är `true` `false` returneras.

### <a name="example"></a>Exempel

`not`Operatorn negerar ett värde. Värdena kan omges av parenteser.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `startedTrue` | boolean | falskt |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Sammanslagning??

`operand1 ?? operand2`

Returnerar det första värdet som inte är null från operander.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `operand1` | sträng, heltal, boolesk, objekt, matris | Värde att testa för `null` . |
| `operand2` | sträng, heltal, boolesk, objekt, matris | Värde att testa för `null` . |
| Fler operander | sträng, heltal, boolesk, objekt, matris | Värde att testa för `null` . |

### <a name="return-value"></a>Returvärde

Returnerar det första värdet som inte är null. Tomma strängar, tomma matriser och tomma objekt är inte `null` och ett \<empty> värde returneras.

### <a name="example"></a>Exempel

Output-satserna returnerar värden som inte är null. Utdatatypen måste matcha typen i jämförelsen, annars genereras ett fel.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `nonNullStr` | sträng | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | sträng | \<empty> |

## <a name="conditional-expression--"></a>Villkors uttryck? :

`condition ? true-value : false-value`

Utvärderar ett villkor och returnerar ett värde om villkoret är sant eller falskt.

### <a name="operands"></a>Operander

| Operand | Typ | Beskrivning |
| ---- | ---- | ---- |
| `condition` | boolean | Villkor för att utvärdera som sant eller falskt. |
| `true-value` | sträng, heltal, boolesk, objekt, matris | Värde när villkoret är sant. |
| `false-value` | sträng, heltal, boolesk, objekt, matris | Värde när villkoret är falskt. |

### <a name="example"></a>Exempel

Det här exemplet utvärderar en Parameters initial och returnerar ett värde om villkoret är sant eller falskt.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Utdata från exemplet:

| Namn | Typ | Värde |
| ---- | ---- | ---- |
| `outValue` | sträng | sant värde |

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en bicep-fil finns i [Självstudier: skapa och distribuera den första Azure Resource Manager bicep-filen](bicep-tutorial-create-first-bicep.md).
- Information om hur du löser bicep typ fel finns i [valfri funktion för bicep](template-functions-any.md).
- Om du vill jämföra syntaxen för bicep och JSON, se [jämföra JSON och bicep for templates](compare-template-syntax.md).
- Exempel på funktioner för bicep och ARM-mallar finns i [funktioner i arm-mallar](template-functions.md).
