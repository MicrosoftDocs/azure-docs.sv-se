---
title: Information om princip tilldelnings strukturen
description: Beskriver den princip tilldelnings definition som används av Azure Policy för att relatera princip definitioner och parametrar till resurser för utvärdering.
ms.date: 03/17/2021
ms.topic: conceptual
ms.openlocfilehash: 909c1c361e092c512a73854a40e22a67efe5f2f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604873"
---
# <a name="azure-policy-assignment-structure"></a>Tilldelningsstruktur i Azure Policy

Princip tilldelningar används av Azure Policy för att definiera vilka resurser som tilldelas vilka principer eller initiativ. Princip tilldelningen kan fastställa värdena för parametrar för den grupp resurser vid tilldelnings tid, vilket gör det möjligt att återanvända princip definitioner som hanterar samma resurs egenskaper med olika behov av efterlevnad.

Du använder JSON för att skapa en princip tilldelning. Princip tilldelningen innehåller element för:

- visningsnamn
- beskrivning
- metadata
- tvingande läge
- undantagna omfattningar
- princip definition
- meddelanden som inte uppfyller kraven
- parametrar

Följande JSON visar till exempel en princip tilldelning i _DoNotEnforce_ -läge med dynamiska parametrar:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Alla Azure Policys exempel finns på [Azure policy exempel](../samples/index.md).

## <a name="display-name-and-description"></a>Visnings namn och beskrivning

Du kan använda **DisplayName** och **Description** för att identifiera princip tilldelningen och tillhandahålla kontext för dess användning med en speciell uppsättning resurser. **DisplayName** får innehålla högst _128_ tecken och **beskrivningen** får bestå av högst _512_ tecken.

## <a name="enforcement-mode"></a>Tvingande läge

Egenskapen **enforcementMode** ger kunderna möjlighet att testa resultatet av en princip på befintliga resurser utan att initiera princip påverkan eller utlösa poster i [Azure aktivitets loggen](../../../azure-monitor/essentials/platform-logs-overview.md). Det här scenariot kallas vanligt vis "What If" och anpassas till säkra distributions metoder. **enforcementMode** skiljer sig från den [inaktiverade](./effects.md#disabled) inställningen, eftersom den här inställningen förhindrar att resurs utvärderingen sker alls.

Den här egenskapen har följande värden:

|Läge |JSON-värde |Typ |Åtgärda manuellt |Aktivitets logg post |Beskrivning |
|-|-|-|-|-|-|
|Enabled |Standardvärde |sträng |Ja |Ja |Princip påverkan tillämpas när en resurs skapas eller uppdateras. |
|Inaktiverad |DoNotEnforce |sträng |Ja |Inga | Princip påverkan tillämpas inte när en resurs skapas eller uppdateras. |

Om **enforcementMode** inte anges i en princip eller initiativ definition används värdet _default_ . [Reparations uppgifter](../how-to/remediate-resources.md) kan startas för [deployIfNotExists](./effects.md#deployifnotexists) -principer, även när **enforcementMode** har angetts till _DoNotEnforce_.

## <a name="excluded-scopes"></a>Undantagna omfattningar

Tilldelningens **omfattning** inkluderar alla underordnade resurs behållare och underordnade resurser. Om en underordnad resurs behållare eller underordnad resurs inte ska ha definitionen tillämpad, kan var och en _uteslutas_ från utvärderingen genom att ställa in **notScopes**. Den här egenskapen är en matris som aktiverar undantag för en eller flera resurs behållare eller resurser från utvärderingen. **notScopes** kan läggas till eller uppdateras när den inledande tilldelningen har skapats.

> [!NOTE]
> En _undantagen_ resurs skiljer sig från en _undantagen_ resurs. Mer information finns i [förstå omfattning i Azure policy](./scope.md).

## <a name="policy-definition-id"></a>ID för princip definition

Det här fältet måste vara det fullständiga Sök vägs namnet för antingen en princip definition eller en initiativ definition.
`policyDefinitionId` är en sträng och inte en matris. Vi rekommenderar att om flera principer ofta tilldelas tillsammans, så att du kan använda ett [initiativ](./initiative-definition-structure.md) i stället.

## <a name="non-compliance-messages"></a>Meddelanden som inte uppfyller kraven

Ange ett anpassat meddelande som beskriver varför en resurs inte är kompatibel med principen eller initiativ definitionen, som anges `nonComplianceMessages` i tilldelnings definitionen. Den här noden är en matris med `message` poster. Det anpassade meddelandet är förutom standard fel meddelandet för inkompatibilitet och är valfritt.

> [!IMPORTANT]
> Anpassade meddelanden för icke-efterlevnad stöds endast för definitioner eller initiativ med definitioner för [resurs hanterings lägen](./definition-structure.md#resource-manager-modes) .

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Om tilldelningen är för ett initiativ kan olika meddelanden konfigureras för varje princip definition i initiativet. Meddelandena använder det `policyDefinitionReferenceId` värde som kon figurer ATS i initiativ definitionen. Mer information finns i [Egenskaper för princip definitioner](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parametrar

Det här segmentet i princip tilldelningen innehåller värdena för de parametrar som definierats i [princip definitionen eller initiativ definitionen](./definition-structure.md#parameters). Den här designen gör det möjligt att återanvända en princip eller initiativ definition med olika resurser, men kontrol lera om det finns olika affärs värden eller resultat.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

I det här exemplet är parametrarna som tidigare definierats i princip definitionen `prefix` och `suffix` . Den här princip tilldelningen anger `prefix` till **Avda** och `suffix` till **-LC**. Samma princip definition kan återanvändas med en annan uppsättning parametrar för en annan avdelning, vilket minskar dupliceringen och komplexiteten i princip definitioner samtidigt som den ger flexibilitet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [princip definitions strukturen](./definition-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
