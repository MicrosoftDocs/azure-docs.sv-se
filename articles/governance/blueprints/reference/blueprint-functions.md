---
title: Azure-ritningar fungerar
description: Beskriver de funktioner som är tillgängliga för användning med skiss artefakter i definitioner och tilldelningar av Azure-modeller.
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919282"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funktioner för användning med Azure-ritningar

Azure-ritningar tillhandahåller funktioner som gör en skiss definition mer dynamisk. Dessa funktioner används med skiss definitioner och skiss artefakter. En mall för Azure Resource Manager mall (ARM-mall) stöder fullständig användning av Resource Manager-funktioner, förutom att hämta ett dynamiskt värde via en skiss parameter.

Följande funktioner stöds:

- [artefakter](#artifacts)
- [concat](#concat)
- [komponentparametrar](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [Prenumerera](#subscription)

## <a name="artifacts"></a>artefakter

`artifacts(artifactName)`

Returnerar ett objekt med egenskaper som har fyllts med den skiss artefaktens utdata.

> [!NOTE]
> `artifacts()`Funktionen kan inte användas i en arm-mall. Funktionen kan bara användas i ritnings definitions-JSON eller i artefakt-JSON när du hanterar skissen med Azure PowerShell eller REST API som en del av [skisser som kod](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Description |
|:--- |:--- |:--- |:--- |
| artifactName |Ja |sträng |Namnet på skiss artefakten. |

### <a name="return-value"></a>Returvärde

Ett objekt av utdata-egenskaper. Egenskaperna för **utdata** är beroende av den typ av skiss artefakt som refereras till. Alla typer följer formatet:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Princip tilldelnings artefakt

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>ARM-mallens artefakt

**Utmatnings** egenskaperna för det returnerade objektet definieras i arm-mallen och returneras av distributionen.

#### <a name="role-assignment-artifact"></a>Roll tilldelnings artefakt

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exempel

En ARM-mall artefakt med ID- _myTemplateArtifact_ som innehåller följande exempel på utdata-egenskap:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Några exempel på hämtning av data från _myTemplateArtifact_ -exemplet är:

| Uttryck | Typ | Värde |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Matris | \["First", "sekund"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Sträng | förstagångskörningen |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Sträng | "mitt sträng värde" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objekt | {"egenskap": "mitt värde", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Sträng | "mitt värde" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | Sant |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Kombinerar flera sträng värden och returnerar den sammanfogade strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Description |
|:--- |:--- |:--- |:--- |
| sträng1 |Ja |sträng |Det första värdet för sammanfogning. |
| ytterligare argument |Inga |sträng |Ytterligare värden i sekventiell ordning för sammanfogning |

### <a name="return-value"></a>Returvärde

En sträng med sammanfogade värden.

### <a name="remarks"></a>Kommentarer

Funktionen Azure Blueprint skiljer sig från funktionen ARM-mall i så att den bara fungerar med strängar.

### <a name="example"></a>Exempel

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parametrar

`parameters(parameterName)`

Returnerar ett skiss parameter värde. Det angivna parameter namnet måste definieras i skiss definitionen eller i skiss artefakter.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |sträng |Namnet på den parameter som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna ritnings-eller skiss artefakt parametern.

### <a name="remarks"></a>Kommentarer

Funktionen Azure Blueprint skiljer sig från funktionen ARM-mall i så att den bara fungerar med skiss parametrar.

### <a name="example"></a>Exempel

Definiera parametern _principalIds_ i skiss definitionen:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Använd sedan _principalIds_ som argument för `parameters()` i en skiss artefakt:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Returnerar ett objekt som representerar den aktuella resurs gruppen.

### <a name="return-value"></a>Returvärde

Det returnerade objektet har följande format:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Kommentarer

Funktionen Azure Blueprint skiljer sig från funktionen ARM-mall. Det `resourceGroup()` går inte att använda funktionen i en artefakt på prenumerations nivå eller skiss definitionen. Den kan bara användas i skiss artefakter som ingår i en resurs grupps artefakt.

En vanlig användning av `resourceGroup()` funktionen är att skapa resurser på samma plats som resurs gruppens artefakt.

### <a name="example"></a>Exempel

Om du vill använda resurs gruppens plats, anger du antingen skiss definitionen eller under tilldelningen, som plats för en annan artefakt, deklarerar du ett objekt för resurs grupps plats hållare i skiss definitionen. I det här exemplet är _NetworkingPlaceholder_ namnet på resurs gruppens plats hållare.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Använd sedan `resourceGroup()` funktionen i kontexten för en skiss artefakt som är riktad mot ett resurs grupps plats hållare objekt. I det här exemplet distribueras mallens artefakt till resurs gruppen _NetworkingPlaceholder_ och ger parametern _resourceLocation_ dynamiskt ifylld med _NetworkingPlaceholder_ resurs gruppens plats till mallen. Platsen för _NetworkingPlaceholder_ -resurs gruppen kan ha definierats statiskt i skiss definitionen eller dynamiskt definieras under tilldelningen. I båda fallen anges mallens artefakt som information som en parameter och använder den för att distribuera resurserna till rätt plats.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Returnerar ett objekt som representerar den angivna resurs grupps artefakten. Till skillnad från `resourceGroup()` , som kräver kontexten för artefakten, används den här funktionen för att hämta egenskaperna för en speciell resurs grupps plats hållare när den inte ingår i kontexten för den resurs gruppen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Description |
|:--- |:--- |:--- |:--- |
| placeholderName |Ja |sträng |Plats hållarens namn för den resurs grupps artefakt som ska returneras. |

### <a name="return-value"></a>Returvärde

Det returnerade objektet har följande format:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exempel

Om du vill använda resurs gruppens plats, anger du antingen skiss definitionen eller under tilldelningen, som plats för en annan artefakt, deklarerar du ett objekt för resurs grupps plats hållare i skiss definitionen. I det här exemplet är _NetworkingPlaceholder_ namnet på resurs gruppens plats hållare.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Använd sedan `resourceGroups()` funktionen från kontexten för alla skiss artefakter för att få en referens till resurs gruppens placeholder-objekt. I det här exemplet distribueras mallens artefakt utanför resurs gruppen _NetworkingPlaceholder_ och ger parametern _artifactLocation_ dynamiskt ifylld med _NetworkingPlaceholder_ resurs gruppens plats till mallen. Platsen för _NetworkingPlaceholder_ -resurs gruppen kan ha definierats statiskt i skiss definitionen eller dynamiskt definieras under tilldelningen. I båda fallen anges mallens artefakt som information som en parameter och använder den för att distribuera resurserna till rätt plats.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>prenumeration

`subscription()`

Returnerar information om prenumerationen för den aktuella skiss tilldelningen.

### <a name="return-value"></a>Returvärde

Det returnerade objektet har följande format:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exempel

Använd prenumerationens visnings namn och `concat()` funktionen för att skapa en namngivnings konvention som skickas som parameter- _resourceName_ till mallens artefakt.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).