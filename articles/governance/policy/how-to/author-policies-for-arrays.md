---
title: Redigera principer för mat ris egenskaper för resurser
description: Lär dig att arbeta med mat ris parametrar och matris språk uttryck, utvärdera [*]-aliaset och lägga till element med Azure Policy definitions regler.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: f3ba5179ca7acc60042d23e88d42a504d4d05f08
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544595"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Redigera principer för mat ris egenskaper på Azure-resurser

Azure Resource Manager egenskaper definieras vanligt vis som strängar och booleska värden. När en en-till-många-relation finns, definieras komplexa egenskaper i stället som matriser. I Azure Policy används matriser på flera olika sätt:

- Typ av [definitions parameter](../concepts/definition-structure.md#parameters)för att tillhandahålla flera alternativ
- En del av en [princip regel](../concepts/definition-structure.md#policy-rule) med hjälp av villkoren **i** eller **notIn**
- En del av en princip regel som utvärderar det [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) som ska utvärderas:
  - Scenarier som **ingen**, **alla**eller **alla**
  - Komplexa scenarier med **antal**
- I Lägg till- [effekter](../concepts/effects.md#append) för att ersätta eller lägga till i en befintlig matris

Den här artikeln beskriver varje användning av Azure Policy och innehåller flera exempel definitioner.

## <a name="parameter-arrays"></a>Parameter mat ris

### <a name="define-a-parameter-array"></a>Definiera en parameter mat ris

Genom att definiera en parameter som en matris kan principen vara flexibel när fler än ett värde krävs.
Med den här princip definitionen kan parametern **allowedLocations** och standard platsen vara _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Som **typen** var _sträng_, kan endast ett värde anges när principen tilldelas. Om den här principen är tilldelad tillåts endast resurser i omfattning inom en enda Azure-region. De flesta princip definitioner måste tillåta en lista över godkända alternativ, till exempel att tillåta _eastus2_, _öster_och _westus2_.

Använd _mat ris_ **typen**för att skapa princip definitionen för att tillåta flera alternativ. Samma princip kan skrivas om på följande sätt:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> När en princip definition har sparats går det inte att ändra **typ** egenskapen för en parameter.

Den här nya parameter definitionen tar fler än ett värde under princip tilldelningen. När du har definierat mat ris egenskapen **allowedValues** är de värden som är tillgängliga under tilldelningen ytterligare begränsad till den fördefinierade listan med alternativ. Användning av **allowedValues** är valfritt.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Skicka värden till en parameter mat ris under tilldelningen

När du tilldelar principen via Azure Portal visas en parameter av **typen** _matris_ som en enda text ruta. Tipset står "use; separera värden. (t. ex. London; New York) ". Använd följande sträng för att överföra de tillåtna positions värden för _eastus2_, _öster_och _westus2_ till parametern:

`eastus2;eastus;westus2`

Parameter värdets format skiljer sig från Azure CLI, Azure PowerShell eller REST API. Värdena skickas via en JSON-sträng som även innehåller namnet på parametern.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Använd följande kommandon om du vill använda den här strängen med varje SDK:

- Azure CLI: kommando [AZ princip tilldelning skapa](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) med parameter **parametrar**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) med parametern **PolicyParameter**
- REST API: i åtgärden _Lägg_ till [skapa](/rest/api/resources/policyassignments/create) som en del av begär ande texten som värde för egenskapen **Properties. Parameters**

## <a name="policy-rules-and-arrays"></a>Princip regler och matriser

### <a name="array-conditions"></a>Mat ris villkor

Princip regel [villkoren](../concepts/definition-structure.md#conditions) som en _mat ris_ 
 **typ** parameter kan användas med är begränsad till `in` och `notIn` . Vidta följande princip definition med villkor `equals` som exempel:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Försöket att skapa princip definitionen via Azure Portal leder till ett fel meddelande som detta fel meddelande:

- "Det gick inte att parameterstyrda principen {GUID} på grund av verifierings fel. Kontrol lera att princip parametrarna är korrekt definierade. Det inre undantaget för utvärderings resultatet av språk uttrycket [parameters (' allowedLocations ')] är type ' matris ', förväntad typ är sträng '. "

Den förväntade **typen** av villkor `equals` är _sträng_. Eftersom **allowedLocations** har definierats som **typ** _mat ris_, utvärderar principmodulen språk uttryck och genererar felet. Med `in` `notIn` villkoret och förväntar sig princip motorn **typ** _mat ris_ i språk uttrycket. Lös det här fel meddelandet genom att ändra `equals` till antingen `in` eller `notIn` .

### <a name="evaluating-the--alias"></a>Utvärdera [*]-aliaset

Alias som är **\[\*\]** kopplade till deras namn anger att **typen** är en _matris_. I stället för att utvärdera värdet för hela matrisen **\[\*\]** gör det möjligt att utvärdera varje element i matrisen individuellt, med logiska och mellan dem. Det finns tre standard scenarier för utvärdering av objekt per objekt är användbart i: _ingen_, _alla_eller _alla_ element matchar. För komplexa scenarier använder du [Count](../concepts/definition-structure.md#count).

Princip motorn utlöser **effekterna** i **sedan** bara när **IF** -regeln utvärderas som sant.
Det här faktum är viktigt att förstå i samband med hur man **\[\*\]** utvärderar varje enskilt element i matrisen.

Exempel princip regeln för scenario tabellen nedan:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

**IpRules** -matrisen ser ut så här i scenario tabellen nedan:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Ersätt med för varje villkors exempel `<field>` nedan `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` .

Följande resultat är resultatet av kombinationen av villkoret och exempel princip regeln och matrisen med befintliga värden ovan:

|Villkor |Resultat | Scenario |Förklaring |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Ingenting |Ingen matchning |Ett mat ris element utvärderas som falskt (127.0.0.1! = 127.0.0.1) och ett som sant (127.0.0.1! = 192.168.1.1), så **notEquals** -villkoret är _falskt_ och effekterna utlöses inte. |
|`{<field>,"notEquals":"10.0.4.1"}` |Princip påverkan |Ingen matchning |Båda mat ris elementen utvärderas som sant (10.0.4.1! = 127.0.0.1 och 10.0.4.1! = 192.168.1.1), så **notEquals** -villkoret är _Sant_ och resultatet utlöses. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Princip påverkan |En eller flera matchningar |Ett mat ris element utvärderas som falskt (127.0.0.1! = 127.0.0.1) och ett som sant (127.0.0.1! = 192.168.1.1), så **notEquals** -villkoret är _falskt_. Den logiska operatorn utvärderas som sant (**inte** _falskt_), så att resultatet utlöses. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Ingenting |En eller flera matchningar |Båda mat ris elementen utvärderas som sant (10.0.4.1! = 127.0.0.1 och 10.0.4.1! = 192.168.1.1), så **notEquals** -villkoret är _Sant_. Den logiska operatorn utvärderar sig som falskt (**inte** _Sant_), så det utlöses inte. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Princip påverkan |Ingen matchning |Ett mat ris element utvärderas som sant (127.0.0.1 = = 127.0.0.1) och ett som falskt (127.0.0.1 = = 192.168.1.1), så **likhets** villkoret är _falskt_. Den logiska operatorn utvärderas som sant (**inte** _falskt_), så att resultatet utlöses. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Princip påverkan |Ingen matchning |Båda mat ris elementen utvärderas som falskt (10.0.4.1 = = 127.0.0.1 och 10.0.4.1 = = 192.168.1.1), vilket innebär **att villkoret är** _false_. Den logiska operatorn utvärderas som sant (**inte** _falskt_), så att resultatet utlöses. |
|`{<field>,"Equals":"127.0.0.1"}` |Ingenting |Alla matchningar |Ett mat ris element utvärderas som sant (127.0.0.1 = = 127.0.0.1) och ett som falskt (127.0.0.1 = = 192.168.1.1), så **likhets** villkoret är _falskt_ och effekterna utlöses inte. |
|`{<field>,"Equals":"10.0.4.1"}` |Ingenting |Alla matchningar |Båda mat ris elementen utvärderas som falskt (10.0.4.1 = = 127.0.0.1 och 10.0.4.1 = = 192.168.1.1), så **likhets** villkoret är _falskt_ och effekterna utlöses inte. |

## <a name="the-append-effect-and-arrays"></a>Lägg till effekter och matriser

[Lägg till-resultatet](../concepts/effects.md#append) fungerar på olika sätt beroende på om **information. fältet** är ett **\[\*\]** alias eller inte.

- Om du inte **\[\*\]** använder ett alias ersätter append hela matrisen med egenskapen **Value**
- När ett **\[\*\]** alias läggs till lägger till egenskapen **Value** till den befintliga matrisen eller skapar den nya matrisen

Mer information finns i [Lägg till exempel](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
