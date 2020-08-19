---
title: Exempelmodeller
description: Visar en lista över källor för exempel modeller.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507543"
---
# <a name="sample-models"></a>Exempelmodeller

I den här artikeln visas några resurser för exempel data som kan användas för att testa tjänsten Azure Remote rendering.

## <a name="built-in-sample-model"></a>Inbyggd exempel modell

Vi tillhandahåller en inbyggd exempel modell som alltid kan läsas in med URL- **Builtin://Engine**

![Exempelmodell](./media/sample-model.png "Exempelmodell")

Modell statistik:

| Namn | Värde |
|-----------|:-----------|
| [Nödvändig Server storlek](../how-tos/session-rest-api.md#create-a-session) | standard |
| Antal trianglar | 18 700 000 |
| Antal rörliga delar | 2073 |
| Antal material | 94 |

## <a name="third-party-data"></a>Data från tredje part

Khronos-gruppen har en uppsättning glTF-exempel modeller för testning. ARR stöder glTF-formatet i text-(*. glTF*) och i binärformat (*. GLB*) form. Vi rekommenderar att du använder PBR-modellerna för bästa visuella resultat:

* [glTF exempel modeller](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Nästa steg

* [Snabb start: rendera en modell med Unity](../quickstarts/render-model.md)
* [Snabb start: konvertera en modell för åter givning](../quickstarts/convert-model.md)
