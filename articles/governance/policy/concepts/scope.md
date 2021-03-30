---
title: Förstå omfattning i Azure Policy
description: Beskriver begreppet omfattning i Azure Resource Manager och hur det gäller Azure Policy för att kontrol lera vilka resurser Azure Policy utvärdera.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90984432"
---
# <a name="understand-scope-in-azure-policy"></a>Förstå omfattning i Azure Policy

Det finns ett antal inställningar som avgör vilka resurser som kan utvärderas och vilka resurser som utvärderas av Azure Policy. Det primära konceptet för dessa kontroller är _omfång_.
En översikt på hög nivå finns [i området Azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope).
I den här artikeln förklaras effekten av _omfattning_ i Azure policy och dess relaterade objekt och egenskaper.

## <a name="definition-location"></a>Definitions plats

Det första instans omfånget som används av Azure Policy är när en princip definition skapas. Definitionen kan sparas antingen i en hanterings grupp eller i en prenumeration. Platsen avgör omfattningen som initiativet eller principen kan tilldelas till. Resurserna måste ligga inom hierarkin för den definitions plats som ska användas som mål för tilldelningen.

Om definitions platsen är:

- Endast **prenumerations** resurser i den prenumerationen kan tilldelas princip definitionen.
- **Hanterings grupp** – endast resurser inom underordnade hanterings grupper och underordnade prenumerationer kan tilldelas princip definitionen. Om du planerar att tillämpa princip definitionen på flera prenumerationer måste platsen vara en hanterings grupp som innehåller varje prenumeration.

Platsen ska vara resurs containern som delas av alla resurser som du vill använda för att använda princip definitionen. Den här resurs behållaren är vanligt vis en hanterings grupp nära rot hanterings gruppen.

## <a name="assignment-scopes"></a>Tilldelnings omfång

En tilldelning har flera egenskaper som anger ett omfång. Användningen av dessa egenskaper avgör vilken resurs för Azure Policy som ska utvärderas och vilka resurser som räknas mot efterlevnad. Dessa egenskaper mappar till följande begrepp:

- Inkludering – en resurs-hierarki eller en enskild resurs bör utvärderas för efterlevnad av definitionen. `properties.scope`Egenskapen för ett tilldelnings objekt bestämmer vad som ska tas med och utvärderas för kompatibilitet. Mer information finns i [tilldelnings definition](./assignment-structure.md).

- Uteslutning-en resurs-hierarki eller en enskild resurs bör inte utvärderas för efterlevnad av definitionen. `properties.notScopes` _Mat ris_ egenskapen för ett tilldelnings objekt avgör vad som ska undantas. Resurser inom dessa omfattningar utvärderas inte eller ingår inte i antalet kompatibla. Mer information finns i [tilldelnings definition-undantagna omfång](./assignment-structure.md#excluded-scopes).

Utöver egenskaperna för princip tilldelningen är [princip undantags](./exemption-structure.md) objekt. Undantag förbättrar omfattnings artikeln genom att tillhandahålla en metod för att identifiera en del av en tilldelning som inte ska utvärderas.

- Undantag (**kostnads fri för hands versions** funktion) – en mappstruktur eller en enskild resurs bör utvärderas för att kontrol lera efterlevnaden av definitionen, men kommer inte att utvärderas på grund av ett avstående eller en annan metod. Resurser i det här läget visas som **undantagna** i rapporterna för efterlevnad så att de kan spåras. Objektet undantag skapas i resurspoolen eller en enskild resurs som ett underordnat objekt, vilket fastställer undantagets omfattning. En resurs-eller enskild resurs kan undantas till flera tilldelningar. Undantaget kan konfigureras att upphöra att gälla enligt ett schema med hjälp av `expiresOn` egenskapen. Mer information finns i [undantags definition](./exemption-structure.md).

  > [!NOTE]
  > På grund av effekten av att bevilja ett undantag för en resurs-hierarki eller enskild resurs, har undantagen ytterligare säkerhets åtgärder. Förutom att du behöver `Microsoft.Authorization/policyExemptions/write` utföra åtgärden i resurspoolen eller en enskild resurs måste skaparen av ett undantag ha `exempt/Action` verbet på mål tilldelningen.

## <a name="scope-comparison"></a>Jämförelse av omfång

Följande tabell är en jämförelse av omfångs alternativen:

| | Innebära | Undantag (notScopes) | Undantag |
|---|:---:|:---:|:---:|
|**Resurserna utvärderas** | &#10004; | - | - |
|**Resource Manager-objekt** | - | - | &#10004; |
|**Kräver ändring av princip tilldelnings objekt** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [princip definitions strukturen](./definition-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).