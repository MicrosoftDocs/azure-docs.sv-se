---
title: Regler för regelefterlevnad i initiativ definitioner
description: Beskriver hur du använder en initiativ definition för att gruppera principer från en myndighets domän som Access Control, konfigurations hantering och andra.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 296febc1aad62d06c8e9dfcf4f285b16cacf0a40
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544629"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Regelefterlevnad i Azure Policy

Regelefterlevnad i Azure Policy ger inbyggda initiativ definitioner för att visa en lista över **kontroll** -och **efterlevnads domäner** baserat på ansvar (_kund_, _Microsoft_, _delad_).
För Microsoft-ansvariga kontroller ger vi ytterligare information om våra gransknings resultat baserat på attestering av tredje part och vår implementerings information för att uppnå detta krav.
Microsoft-ansvariga kontroller är av `type` [statiskt](./definition-structure.md#type).

> [!NOTE]
> Regelefterlevnad är en förhands gransknings funktion. För uppdaterade inbyggda program, styr initiativen till motsvarande efterlevnad standard. Befintliga initiativ för efterlevnad håller på att uppdateras för att ge stöd för regelefterlevnad.

## <a name="regulatory-compliance-defined"></a>Reglerad efterlevnad definieras

Regelefterlevnad har skapats på [grupp](./initiative-definition-structure.md#policy-definition-groups) delen av en initiativ definition. I inbyggda program definierar varje gruppering i initiativ definitionen ett namn (**kontroll**), en kategori (**efterlevnadsprincip**) och innehåller en referens till [policyMetadata](./initiative-definition-structure.md#metadata-objects) -objektet som innehåller information om den **kontrollen**. En kontroll av efterlevnad av efterlevnad måste ha `category` egenskapen inställt på **regelefterlevnad**. Som en annan standard-initiativ definition kan du använda [parametrar](./initiative-definition-structure.md#parameters) för regelefterlevnad för att skapa dynamiska tilldelningar.

Kunder kan skapa egna regler för regelefterlevnad. Dessa definitioner kan original eller kopieras från befintliga inbyggda definitioner. Om du använder en inbyggd regel för regelefterlevnad som en referens rekommenderar vi att du övervakar källan för reglerna för regelefterlevnad i [Azure policy GitHub lagrings platsen](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance).

Om du vill länka ett anpassat kontroll initiativ för regelefterlevnad till Azure Security Center instrument panelen, se [Azure Security Center-använda anpassade säkerhets principer](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Regelefterlevnad i portalen

När en initiativ definition har skapats med [grupper](./initiative-definition-structure.md#policy-definition-groups), har sidan **kompatibilitetsinformation** i portalen för det initiativet ytterligare information. 

En ny flik, **kontroller** läggs till på sidan. Filtrering är tillgängligt för **domän** -och princip definitioner för efterlevnad grupperas efter `title` fältet från **policyMetadata** -objektet. Varje rad representerar en **kontroll** som visar dess kompatibilitetstillstånd, den **domän för regelefterlevnad** som den ingår i, ansvars information och hur många icke-kompatibla och kompatibla princip definitioner som utgör **kontrollen**.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Ett exempel på Översikt över regelefterlevnad för NIST SP 800-53 R4 inbyggd definition.":::

Om du väljer en **kontroll** öppnas en sida med information om den kontrollen. **Översikten** innehåller information från `description` och `requirements` . Under fliken **principer** finns alla enskilda princip definitioner i det initiativ som bidrar till den här **kontrollen**. Fliken **Resource Compliance (Resource Compliance** ) innehåller en detaljerad översikt över varje resurs som utvärderas av en medlems princip för den aktuella visade **kontrollen**.

> [!NOTE]
> En utvärderings typ av **Microsoft Managed** är för en [statisk](./definition-structure.md#type) princip definition `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Ett exempel på definitioner av regler för efterlevnadsprinciper i gränser för skydds kontroll av system-och kommunikations skydds domänen i NIST SP 800-53 R4 inbyggd definition.":::

Från samma **kontroll** sida visas alla resurser som den här **kontrollens**princip definitioner inkluderar på fliken för **resurs efterlevnad** . Filter är tillgängliga för namn eller ID, kompatibilitetstillstånd, resurs typ och plats.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Ett exempel på de regler för regelefterlevnad som påverkas av princip definitionerna i kontroll av system-och kommunikations skydds domänen i NIST SP 800-53 R4 inbyggd definition.":::

## <a name="regulatory-compliance-in-sdk"></a>Regelefterlevnad i SDK

Om regelefterlevnad har Aktiver ATS för en initiativ definition, returnerar utvärderings posten, händelserna och princip status SDK: n varje gång ytterligare egenskaper. Dessa ytterligare egenskaper grupperas efter kompatibilitetstillstånd och ger information om hur många grupper som finns i varje tillstånd.

Följande kod är ett exempel på tillagda resultat från ett `summarize` anrop:

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Nästa steg

- Se [initiativ definitions strukturen](./initiative-definition-structure.md)
- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Förstå policy-effekter](./effects.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
