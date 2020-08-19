---
title: 'Självstudie: hantera märkes styrning'
description: I den här självstudien använder du ändrings resultatet för Azure Policy för att skapa och tillämpa en etikett styrnings modell på nya och befintliga resurser.
ms.date: 08/17/2020
ms.topic: tutorial
ms.openlocfilehash: f49eedb00c98d3c362140fdca9b195a086903f10
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545513"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Självstudie: hantera märkes styrning med Azure Policy

[Taggar](../../../azure-resource-manager/management/tag-resources.md) är en viktig del av att organisera dina Azure-resurser i en taxonomi. När du följer [metod tipsen för etikett hantering](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)kan du använda taggar för att tillämpa affärs principer med Azure policy eller [spåra kostnader med Cost Management](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources).
Oavsett hur eller varför du använder taggar är det viktigt att du snabbt kan lägga till, ändra och ta bort taggarna på dina Azure-resurser. Om du vill se om din Azure-resurs stöder taggning, se [tagg stöd](../../../azure-resource-manager/management/tag-support.md).

Azure Policyens [ändrings](../concepts/effects.md#modify) funktion har utformats för att hjälpa till med styrning av Taggar oavsett vilket stadium av resurs styrning som du är i. **Ändra** hjälper när:

- Du är inte nybörjare i molnet och har ingen märknings styrning
- Har redan tusentals resurser utan någon märknings styrning
- Redan har en befintlig taxonomi som du behöver ändra

I den här självstudien utför du följande aktiviteter:

> [!div class="checklist"]
> - Identifiera dina affärskrav
> - Mappa varje krav till en princip definition
> - Gruppera tag-principerna i ett initiativ

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="identify-requirements"></a>Identifiera krav

Precis som all bra implementering av styrnings kontroller bör kraven komma från dina affärs behov och förstås bra innan du skapar tekniska kontroller. I den här scenario kursen är följande saker som är våra affärs krav:

- Två obligatoriska taggar för alla resurser: _CostCenter_ och _Kuvert_
- _CostCenter_ måste finnas på alla behållare och enskilda resurser
  - Resurser ärver från den behållare som de befinner sig i, men de kan åsidosättas individuellt
- _Kuvert_ måste finnas på alla behållare och enskilda resurser
  - Resurser fastställer miljö för namngivnings schema för behållare och kan inte åsidosättas
  - Alla resurser i en behållare ingår i samma miljö

## <a name="configure-the-costcenter-tag"></a>Konfigurera CostCenter-taggen

Med avseende på en Azure-miljö som hanteras av Azure Policy, kräver _CostCenter_ tag-krav för följande resultat:

- Neka resurs grupper som saknar taggen _CostCenter_
- Ändra resurser för att lägga till _CostCenter_ -taggen från den överordnade resurs gruppen när den saknas

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Neka resurs grupper som saknar taggen CostCenter

Eftersom _CostCenter_ för en resurs grupp inte kan bestämmas av namnet på resurs gruppen, måste den ha taggen som definierats i begäran för att skapa resurs gruppen. Följande princip regel med [neka](../concepts/effects.md#deny) -funktionen förhindrar skapande eller uppdatering av resurs grupper som inte har _CostCenter_ -taggen:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Eftersom den här princip regeln är riktad mot en resurs grupp måste _läget_ i princip definitionen vara all i stället för indexerad.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Ändra resurser för att ärva CostCenter-taggen när den saknas

Det andra _CostCenter_ -behovet är för alla resurser som ska ärva taggen från den överordnade resurs gruppen när den saknas. Om taggen redan har definierats på resursen, även om den skiljer sig från den överordnade resurs gruppen, måste den lämnas ensam. Följande princip regel använder [ändring](../concepts/effects.md#modify):

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Den här princip regeln använder åtgärden **Lägg till** i stället för **addOrReplace** eftersom vi inte vill ändra taggnamnet om det är tillgängligt när du [reparerar](../how-to/remediate-resources.md) befintliga resurser. Den använder också `[resourcegroup()]` funktionen mall för att hämta taggvärde från den överordnade resurs gruppen.

> [!NOTE]
> Eftersom den här princip regeln riktar resurser som stöder taggar, måste _läget_ i princip definitionen vara ' Indexed '. Den här konfigurationen ser också till att den här principen hoppar över resurs grupper.

## <a name="configure-the-env-tag"></a>Konfigurera kuvert-taggen

I termer som är särskilt för en Azure-miljö som hanteras av Azure Policy, är kraven på _Kuvert_ märkningen för följande resultat:

- Ändra _Kuvert_ -taggen i resurs gruppen baserat på resurs gruppens namngivnings schema
- Ändra _Kuvert_ -taggen för alla resurser i resurs gruppen till samma som den överordnade resurs gruppen

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Ändra kuvert kod för resurs grupper baserat på namn

En [ändrings](../concepts/effects.md#modify) princip krävs för varje miljö som finns i din Azure-miljö. Ändra principen för var och en ser ut ungefär så här princip definitionen:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Eftersom den här princip regeln är riktad mot en resurs grupp måste _läget_ i princip definitionen vara all i stället för indexerad.

Den här principen matchar endast resurs grupper med det exempel på namngivnings schema som används för produktions resurser för `prd-` . Mer komplexa namngivnings scheman kan uppnås med flera **matchnings** villkor i stället för samma **som** i det här exemplet.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Ändra resurser för att ärva kuvert-taggen

Företags kraven kräver att alla resurser har en _Kuvert_ -tagg som deras överordnade resurs grupp gör. Det går inte att åsidosätta den här taggen, så vi använder **addOrReplace** -åtgärden med [ändra](../concepts/effects.md#modify) -resultatet. Exempel ändrings principen ser ut som följande regel:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Eftersom den här princip regeln riktar resurser som stöder taggar, måste _läget_ i princip definitionen vara ' Indexed '. Den här konfigurationen ser också till att den här principen hoppar över resurs grupper.

Den här princip regeln söker efter en resurs som inte har det överordnade resurs grupp svärdet för _Kuvert_ -taggen eller som saknar taggen _Kuvert_ . De matchande resurserna har sina _Kuvert_ etiketter inställt på värdet för överordnade resurs grupper, även om taggen redan fanns på resursen men med ett annat värde.

## <a name="assign-the-initiative-and-remediate-resources"></a>Tilldela initiativet initiativ och åtgärda resurser

När du har skapat tag-principerna ovan kan du ansluta dem till ett enda initiativ för märknings styrning och tilldela dem till en hanterings grupp eller prenumeration. Initiativet initiativ och inkluderade principer utvärderar sedan efterlevnad av befintliga resurser och ändrar begär Anden för nya eller uppdaterade resurser som matchar **IF** -egenskapen i princip regeln. Principen uppdaterar dock inte automatiskt befintliga icke-kompatibla resurser med den definierade tag gen ändringen.

Precis som [deployIfNotExists](../concepts/effects.md#deployifnotexists) -principer använder **ändrings** principen åtgärds åtgärder för att ändra befintliga icke-kompatibla resurser. Följ anvisningarna för [hur du kan åtgärda resurserna](../how-to/remediate-resources.md) för att identifiera dina icke-kompatibla **ändrings** resurser och korrigera taggarna till din definierade taxonomi.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med att arbeta med resurser i den här självstudien kan du använda följande steg för att ta bort tilldelningar eller definitioner som skapades ovan:

1. Välj **Definitioner** (eller **Tilldelningar** om du ska ta bort en tilldelning) under **Redigering** till vänster på sidan Azure Policy.

1. Sök efter den nya initiativ- eller principdefinition (eller tilldelning) som du vill ta bort.

1. Högerklicka på raden eller välj ellipserna i slutet av definitionen (eller tilldelningen) och välj **Ta bort definition** (eller **Ta bort tilldelning**).

## <a name="review"></a>Genomgång

I den här självstudien har du lärt dig om följande uppgifter:

> [!div class="checklist"]
> - Identifierade dina affärskrav
> - Mappat varje krav till en princip definition
> - Grupperade tag policys i ett initiativ

## <a name="next-steps"></a>Nästa steg

Mer information om principdefinitionernas strukturer finns i den här artikeln:

> [!div class="nextstepaction"]
> [Azure Policy-definitionsstruktur](../concepts/definition-structure.md)