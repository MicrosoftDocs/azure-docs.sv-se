---
title: Tjänstbegränsningar
titleSuffix: Azure Machine Learning
description: Tjänst begränsningar som används för kapacitets planering och högsta gräns för begär Anden och svar för Azure Machine Learning.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: 132c4b223b99f7110cd4553bb0dd93f1e58f4cdd
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733390"
---
# <a name="service-limits-in-azure-machine-learning"></a>Tjänst begränsningar i Azure Machine Learning

I det här avsnittet visas grundläggande kvoter och begränsnings gränser i Azure Machine Learning.

## <a name="workspaces"></a>Arbetsytor
| Gräns | Värde |
| --- | --- |
| Namn på arbetsyta | 2-32 tecken |

## <a name="runs"></a>Körningar
| Gräns | Värde |
| --- | --- |
| Körningar per arbets yta | 10 miljoner |
| RunId/ParentRunId | 256 tecken |
| DataContainerId | 261 tecken |
| DisplayName |256 tecken|
| Beskrivning |5 000 tecken|
| Antal egenskaper |50 |
| Längd på egenskaps nyckel |100 tecken |
| Längd på egenskaps värde |1 000 tecken |
| Antal Taggar |50 |
| Längd på etikett nyckel |100 |
| Längd på tagg värde |1 000 tecken |
| CancelUri / CompleteUri / DiagnosticsUri |1 000 tecken |
| Fel meddelandets längd |3 000 tecken |
| Varnings meddelandets längd |300 tecken |
| Antal indata-datauppsättningar |200 |
| Antal data uppsättningar för utdata |20 |


## <a name="metrics"></a>Mått
| Gräns | Värde |
| --- | --- |
| Mått namn per körning |50|
| Mått rader per mått namn |10 miljoner|
| Kolumner per mått rad |15|
| Längd på mått kolumn namn |255 tecken |
| Värde längd för mått kolumn |255 tecken |
| Mått rader per batch har laddats upp | 250 |

> [!NOTE]
> Om du träffar antalet mått namn per körning eftersom du formaterar variabler i mått namnet, bör du i stället använda ett rad mått där en kolumn är variabelvärdet och den andra kolumnen är måttets värde.

## <a name="artifacts"></a>Artifacts

| Gräns | Värde |
| --- | --- |
| Antal artefakter per körning |10 miljoner|
| Maximal längd på artefakt Sök väg |5 000 tecken |

## <a name="limit-increases"></a>Gräns ökningar
Vissa gränser kan ökas för enskilda arbets ytor genom att [kontakta supporten](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). 

## <a name="next-steps"></a>Nästa steg

- [Konfigurera din Azure Machine Learnings miljö](how-to-configure-environment.md)
