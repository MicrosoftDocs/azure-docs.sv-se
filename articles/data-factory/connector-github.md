---
title: Anslut till GitHub
description: Använd GitHub för att ange dina gemensamma data modells entitetsreferenser
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372288"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Använda GitHub för att läsa vanliga data modells entitetsreferenser

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

GitHub-anslutaren i Azure Data Factory används bara för att ta emot entitetens referens schema för det [gemensamma data modell](format-common-data-model.md) formatet i data flödet för mappning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten GitHub.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **GitHub**. | ja
| userName | GitHub användar namn | ja |
| password | GitHub-lösenord | ja |

## <a name="next-steps"></a>Nästa steg

Skapa en [käll data uppsättning](data-flow-source.md) i mappnings data flödet.