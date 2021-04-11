---
title: Anslut till GitHub
description: Använd GitHub för att ange dina gemensamma data modells entitetsreferenser
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: weetok
ms.openlocfilehash: 7461a0332a36509c7bb6dfdd6db5948b056b35a6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222120"
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