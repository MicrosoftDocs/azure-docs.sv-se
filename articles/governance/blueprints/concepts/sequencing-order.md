---
title: Förstå ordningen för distributions ordningen
description: Lär dig mer om standard ordningen som skiss artefakter distribueras i under en skiss tilldelning och hur du anpassar distributions ordningen.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: ada7e7771e44e6f43c8546e8307fd5d2b218c009
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918236"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Förstå distributions ordningen i Azure-ritningar

Azure-ritningar använder **sekvenseringen** för att avgöra i vilken ordning som en resurs skapas när tilldelningen av en skiss definition bearbetas. I den här artikeln beskrivs följande begrepp:

- Standard ordningen för ordningsföljd som används
- Så här anpassar du ordningen
- Hur den anpassade ordern bearbetas

Det finns variabler i JSON-exemplen som du måste ersätta med dina egna värden:

- `{YourMG}` – Ersätt med namnet på din hanteringsgrupp

## <a name="default-sequencing-order"></a>Ordning för ordningsföljd

Om skiss definitionen inte innehåller något direktiv för att kunna distribuera artefakter eller om direktivet är null, används följande ordning:

- Artefakter för **roll tilldelning** på prenumerations nivå sorteras efter artefakt namn
- Artefakter för **princip tilldelning** på prenumerations nivå sorteras efter artefakt namn
- **Azure Resource Manager mall** för prenumerations nivå (arm-mallar) artefakter sorterade efter artefakt namn
- **Resurs grupp** artefakter (inklusive underordnade artefakter) sorterade efter plats hållarens namn

I varje **resurs grupps** artefakt används följande ordning för artefakter som skapas i den resurs gruppen:

- Resurs gruppens underordnade **Roll tilldelnings** artefakter sorterade efter artefakt namn
- Resurs gruppens underordnade **princip tilldelnings** artefakter sorterade efter artefakt namn
- Resurs gruppens underordnade **Azure Resource Manager mall** (arm-mallar) artefakter sorterade efter artefakt namn

> [!NOTE]
> Användning av [artefakter ()](../reference/blueprint-functions.md#artifacts) skapar ett implicit beroende på den artefakt som refereras till.

## <a name="customizing-the-sequencing-order"></a>Anpassa ordningsföljden ordning

När du skapar stora skiss definitioner kan det vara nödvändigt att resurser skapas i en speciell ordning. Det vanligaste användnings mönstret i det här scenariot är när en skiss definition innehåller flera ARM-mallar. Azure-ritningar hanterar det här mönstret genom att tillåta ordningsföljds ordning att definieras.

Ordningen utförs genom att definiera en `dependsOn` egenskap i JSON. Skiss definitionen, för resurs grupper och artefakt objekt stöder den här egenskapen. `dependsOn` är en sträng mat ris med artefakt namn som den specifika artefakten måste skapas innan den skapas.

> [!NOTE]
> När du skapar skiss objekt hämtar varje artefakt resurs sitt namn från fil namnet, om du använder [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact), eller URL-slutpunkten om du använder [REST API](/rest/api/blueprints/artifacts/createorupdate). _resourceGroup_ -referenser i artefakter måste matcha de som definierats i skiss definitionen.

### <a name="example---ordered-resource-group"></a>Exempel på ordnad resurs grupp

Den här exempel ritnings definitionen har en resurs grupp som har definierat en anpassad ordningsföljd ordning genom att deklarera ett värde för `dependsOn` , tillsammans med en standard resurs grupp. I det här fallet bearbetas artefakten med namnet **assignPolicyTags** före den **beställda-RG** resurs gruppen.
**standard – RG** kommer att bearbetas enligt standard ordningen för ordningsföljd.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Exempel – artefakt med anpassad ordning

Det här exemplet är en princip artefakt som är beroende av en ARM-mall. Som standard skapas en princip artefakt före ARM-mallen. Den här ordningen gör att princip artefakten kan vänta på att ARM-mallen ska skapas.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exempel på en mall för prenumerations nivå artefakt beroende på en resurs grupp

Det här exemplet är för en ARM-mall som distribueras på prenumerations nivån till beroende av en resurs grupp. I standard ordning skulle prenumerations nivå artefakter skapas före eventuella resurs grupper och underordnade artefakter i dessa resurs grupper. Resurs gruppen definieras i skiss definitionen så här:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Mallen för prenumerations nivåns artefakt beroende på resurs gruppen **väntar på mig** definieras så här:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Bearbetar den anpassade sekvensen

Under skapandet används en Top ologiska sortering för att skapa ett beroende diagram över ritningens artefakter. Kontrollen säkerställer att varje nivå av beroendet mellan resurs grupper och artefakter stöds.

Om ett artefakt beroende deklareras som inte ändrar standard ordningen görs ingen ändring.
Ett exempel är en resurs grupp som är beroende av en princip för en prenumerations nivå. Ett annat exempel är en resurs grupps "standard-RG"-princip tilldelning som är beroende av den underordnade roll tilldelningen standard-RG för resurs gruppen. I båda fallen `dependsOn` har inte den förändrade standard ordningen och inga ändringar görs.

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](./lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](./parameters.md).
- Lär dig hur du använder [resurslåsning för en skiss](./resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).