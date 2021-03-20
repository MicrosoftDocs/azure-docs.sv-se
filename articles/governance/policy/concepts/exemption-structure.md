---
title: Information om princip undantags strukturen
description: Beskriver den princip undantags definition som används av Azure Policy för att undanta resurser från utvärdering av initiativ eller definitioner.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: e6ced56c1dc65ca68998c5c58d3e985b63873e0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950184"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy undantags struktur

Funktionen Azure Policy undantag (för hands version) används för att _undanta_ en resurspool eller en enskild resurs från utvärdering av initiativ eller definitioner. Resurser som är _undantagna_ till övergripande efterlevnad, men inte kan utvärderas eller ha ett tillfälligt undantag. Mer information finns i [förstå omfattning i Azure policy](./scope.md). Azure Policy undantag fungerar bara med [Resource Manager-lägen](./definition-structure.md#resource-manager-modes) och fungerar inte med [resurs leverantörs lägen](./definition-structure.md#resource-provider-modes).

> [!IMPORTANT]
> Den här funktionen är kostnads fri under för **hands versionen**. Pris information finns i [Azure policy prissättning](https://azure.microsoft.com/pricing/details/azure-policy/). Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Du använder JSON för att skapa ett princip undantag. Princip undantaget innehåller element för:

- visningsnamn
- beskrivning
- metadata
- princip tilldelning
- princip definitioner inom ett initiativ
- undantags kategori
- dag

> [!NOTE]
> Ett princip undantag skapas som ett underordnat objekt i resurspoolen eller en enskild resurs som beviljats undantaget, så målet ingår inte i undantags definitionen.

Följande JSON visar till exempel ett princip undantag i kategorin **avsägelse** för en resurs till en initiativ tilldelning med namnet `resourceShouldBeCompliantInit` . Resursen är _undantagen_ från endast två av princip definitionerna i initiativet, `customOrgPolicy` anpassad princip definition (referens `requiredTags` ) och den inbyggda princip definitionen "tillåtna platser" (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , referens `allowedLocations` ):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Kodfragment för det relaterade initiativet med matchningen `policyDefinitionReferenceIds` som används av princip undantaget:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Visnings namn och beskrivning

Du kan använda **DisplayName** och **Description** för att identifiera princip undantaget och tillhandahålla kontext för användning med den angivna resursen. **DisplayName** får innehålla högst _128_ tecken och **beskrivningen** får bestå av högst _512_ tecken.

## <a name="metadata"></a>Metadata

Med egenskapen **metadata** kan du skapa en underordnad egenskap som krävs för att lagra relevant information. I exemplet ovan innehåller egenskaperna **RequestedBy**, **approvedBy**, **approvedOn** och **ticketRef** kund värden för att tillhandahålla information om vem som begärde undantaget, vem som godkänt det och när och en intern spårnings biljett för begäran. Dessa **metadata** -egenskaper är exempel, men de är inte obligatoriska och **metadata** är inte begränsade till dessa underordnade egenskaper.

## <a name="policy-assignment-id"></a>ID för princip tilldelning

Det här fältet måste vara det fullständiga Sök vägs namnet för antingen en princip tilldelning eller en initiativ tilldelning.
`policyAssignmentId` är en sträng och inte en matris. Den här egenskapen anger vilken tilldelning som den överordnade resursens hierarki eller enskild resurs är _undantagen_ från.

## <a name="policy-definition-ids"></a>ID för princip definition

Om `policyAssignmentId` är för en initiativ tilldelning `policyDefinitionReferenceIds` kan egenskapen användas för att ange vilka princip definitioner i initiativet som ämnes resursen har undantaget. Eftersom resursen kan undantas från en eller flera princip definitioner som ingår är den här egenskapen en _matris_. Värdena måste matcha värdena i initiativ definitionen i `policyDefinitions.policyDefinitionReferenceId` fälten.

## <a name="exemption-category"></a>Undantags kategori

Det finns två undantags kategorier och används för att gruppera undantag:

- **Dämpat**: undantaget beviljas eftersom princip avsikten är uppfyllt genom en annan metod.
- **Befrielse: undantaget** beviljas eftersom resursens icke-hälsotillstånd är tillfälligt accepterat. En annan orsak till att använda den här kategorin är för en resurs-eller resurs-hierarki som ska uteslutas från en eller flera definitioner i ett initiativ, men bör inte uteslutas från hela initiativet.

## <a name="expiration"></a>Förfallodatum

Om du vill ange när en mappstruktur eller en enskild resurs inte längre är _undantagen_ till en tilldelning anger du `expiresOn` egenskapen. Denna valfria egenskap måste vara i Universal ISO 8601 DateTime-format `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> Princip undantagen tas inte bort när `expiresOn` datumet har nåtts. Objektet bevaras för Arkiv hållning, men undantaget hanteras inte längre.

## <a name="required-permissions"></a>Behörigheter som krävs

De Azure RBAC-behörigheter som krävs för att hantera princip undantags objekt finns i `Microsoft.Authorization/policyExemptions` Åtgärds gruppen. De inbyggda rollerna [resurs princip deltagare](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) och [säkerhets administratör](../../../role-based-access-control/built-in-roles.md#security-admin) har både och har `read` `write` behörigheten och [princip insikter data Writer (för hands version)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) `read` .

Undantag har ytterligare säkerhets åtgärder på grund av konsekvenserna av att bevilja ett undantag. Förutom att du behöver `Microsoft.Authorization/policyExemptions/write` utföra åtgärden i resurspoolen eller en enskild resurs måste skaparen av ett undantag ha `exempt/Action` verbet på mål tilldelningen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [princip definitions strukturen](./definition-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).