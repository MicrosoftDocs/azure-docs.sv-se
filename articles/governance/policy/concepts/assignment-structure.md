---
title: Information om principtilldelningsstrukturen
description: Beskriver den principtilldelningsdefinition som används av Azure Policy för att relatera principdefinitioner och parametrar till resurser för utvärdering.
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 9de210b17264330e79ab5978a449e7a494054be2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535866"
---
# <a name="azure-policy-assignment-structure"></a>Tilldelningsstruktur i Azure Policy

Principtilldelningar används av Azure Policy för att definiera vilka resurser som tilldelas vilka principer eller initiativ. Principtilldelningen kan fastställa värdena för parametrar för den resursgruppen vid tilldelningen, vilket gör det möjligt att återanvända principdefinitioner som behandlar samma resursegenskaper med olika behov av efterlevnad.

Du använder JSON för att skapa en principtilldelning. Principtilldelningen innehåller element för:

- visningsnamn
- beskrivning
- metadata
- tvingande läge
- exkluderade omfång
- principdefinition
- meddelanden om bristande efterlevnad
- parametrar

Följande JSON visar till exempel en principtilldelning i _DoNotEnforce-läge_ med dynamiska parametrar:

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

Alla Azure Policy exempel finns på [Azure Policy exempel](../samples/index.md).

## <a name="display-name-and-description"></a>Visningsnamn och beskrivning

Du använder **displayName** och **beskrivning** för att identifiera principtilldelningen och ange kontext för dess användning med den specifika uppsättningen resurser. **displayName** har en maximal längd på _128_ tecken **och en** beskrivning på högst _512_ tecken.

## <a name="metadata"></a>Metadata

Den `metadata` valfria egenskapen lagrar information om principtilldelningen. Kunder kan definiera egenskaper och värden som är användbara för organisationen i `metadata` . Det finns dock några vanliga _egenskaper_ som används av Azure Policy. Varje `metadata` egenskap har en gräns på 1 024 tecken.

### <a name="common-metadata-properties"></a>Vanliga metadataegenskaper

- `assignedBy` (sträng): Det egna namnet på säkerhetsobjekt som skapade tilldelningen.
- `createdBy` (sträng): GUID för säkerhetsobjekt som skapade tilldelningen.
- `createdOn` (sträng): Formatet Universal ISO 8601 DateTime för tiden då tilldelningen skapades.
- `parameterScopes` (objekt): En samling nyckel/värde-par där nyckeln matchar ett [strongType-konfigurerat](./definition-structure.md#strongtype) parameternamn och värdet definierar det resursomfång som används i portalen för att tillhandahålla listan över tillgängliga resurser genom att matcha _strongType_. Portalen anger det här värdet om omfånget skiljer sig från tilldelningsomfånget. Om detta anges anger en redigering av principtilldelningen i portalen automatiskt omfånget för parametern till det här värdet. Omfånget är dock inte låst till värdet och kan ändras till ett annat omfång.

  Följande exempel på är `parameterScopes` för en _strongType-parameter_ med namnet **backupPolicyId** som anger ett omfång för resursval när tilldelningen redigeras i portalen.

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (sträng): Det egna namnet på säkerhetsobjekt som uppdaterade tilldelningen, om det finns några.
- `updatedOn` (sträng): Universal ISO 8601 DateTime-formatet för tilldelningens uppdateringstid, om det finns någon.

## <a name="enforcement-mode"></a>Tvingande läge

Egenskapen **enforcementMode** ger kunderna möjlighet att testa resultatet av en princip på befintliga resurser utan att initiera principeffekten eller utlösa poster i [Azure-aktivitetsloggen](../../../azure-monitor/essentials/platform-logs-overview.md). Det här scenariot kallas ofta "What If" och överensstämmer med säkra distributionsmetoder. **enforcementMode** skiljer sig från den [inaktiverade](./effects.md#disabled) effekten eftersom den effekten förhindrar att resursutvärderingen görs alls.

Den här egenskapen har följande värden:

|Läge |JSON-värde |Typ |Åtgärda manuellt |Aktivitetsloggpost |Description |
|-|-|-|-|-|-|
|Enabled |Standardvärde |sträng |Ja |Ja |Principeffekten framtvingas när resursen skapas eller uppdateras. |
|Inaktiverad |DoNotEnforce |sträng |Ja |Inga | Principeffekten tillämpas inte när resursen skapas eller uppdateras. |

Om **enforcementMode** inte anges i en princip- eller initiativdefinition används _värdet_ Default. [Reparationsåtgärder kan startas](../how-to/remediate-resources.md) för [deployIfNotExists-principer,](./effects.md#deployifnotexists) även om **enforcementMode** är inställt på _DoNotEnforce_.

## <a name="excluded-scopes"></a>Exkluderade omfång

**Tilldelningsomfånget** omfattar alla underordnade resurscontainrar och underordnade resurser. Om en underordnad resurscontainer eller underordnad resurs inte  ska ha definitionen tillämpad kan var och en undantas från utvärderingen genom att ange **notScopes**. Den här egenskapen är en matris som gör det möjligt att exkludera en eller flera resurscontainrar eller resurser från utvärderingen. **notScopes** kan läggas till eller uppdateras när den första tilldelningen har skapats.

> [!NOTE]
> En _undantagen_ resurs skiljer sig från en _undantagen_ resurs. Mer information finns i Förstå [omfånget i Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>Principdefinitions-ID

Det här fältet måste vara det fullständiga sökvägsnamnet för antingen en principdefinition eller en initiativdefinition.
`policyDefinitionId` är en sträng och inte en matris. Vi rekommenderar att du använder ett initiativ i stället om flera principer ofta [tilldelas](./initiative-definition-structure.md) tillsammans.

## <a name="non-compliance-messages"></a>Meddelanden om bristande efterlevnad

Om du vill ange ett anpassat meddelande som beskriver varför en resurs inte är kompatibel med princip- eller initiativdefinitionen anger `nonComplianceMessages` du i tilldelningsdefinitionen. Den här noden är en matris `message` med poster. Det här anpassade meddelandet är utöver standardfelmeddelandet vid in kompatibilitet och är valfritt.

> [!IMPORTANT]
> Anpassade meddelanden för icke-kompatibilitet stöds endast för definitioner eller initiativ med Resource Manager [definitionslägen.](./definition-structure.md#resource-manager-modes)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Om tilldelningen är för ett initiativ kan olika meddelanden konfigureras för varje principdefinition i initiativet. Meddelandena använder det `policyDefinitionReferenceId` värde som konfigureras i initiativdefinitionen. Mer information finns i egenskaper [för principdefinitioner.](./initiative-definition-structure.md#policy-definition-properties)

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

Det här segmentet av principtilldelningen tillhandahåller värdena för de parametrar som definieras i [principdefinitionen eller initiativdefinitionen](./definition-structure.md#parameters). Den här designen gör det möjligt att återanvända en princip- eller initiativdefinition med olika resurser, men söka efter olika affärsvärden eller affärsresultat.

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

I det här exemplet är de parametrar som tidigare definierats i principdefinitionen `prefix` och `suffix` . Den här specifika principtilldelningen `prefix` är **DeptA** `suffix` och **-LC.** Samma principdefinition kan återanvändas med en annan uppsättning parametrar för en annan avdelning, vilket minskar dupliceringen och komplexiteten för principdefinitioner samtidigt som den ger flexibilitet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [principdefinitionsstrukturen](./definition-structure.md).
- Förstå hur du [programmässigt skapar principer.](../how-to/programmatically-create.md)
- Lär dig hur du [hämtar efterlevnadsdata.](../how-to/get-compliance-data.md)
- Lär dig hur [du åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper.](../../management-groups/overview.md)
