---
title: Smart-beskurna miniatyrer – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör generering av miniatyr bilder för bilder med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "68945221"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Skapa Smart-beskurna miniatyrer med Visuellt innehåll

En miniatyr bild är en mindre storleks representation av en bild. Miniatyrer används för att representera bilder och andra data på ett mer ekonomiskt, användarvänligt sätt. API för visuellt innehåll använder Smart beskärning, tillsammans med att ändra storlek på bilden, för att skapa intuitiva miniatyr bilder för en specifik bild.

Algoritmen Visuellt innehåll thumbnail generation fungerar på följande sätt:

1. Ta bort distraherande element från bilden och _identifiera det områden i_ &mdash; bilden där huvudobjekten visas.
1. Beskär avbildningen baserat på det identifierade _intresse fältet_.
1. Ändra bredd-breddförhållandet så att det passar mål miniatyrernas dimensioner.

## <a name="area-of-interest"></a>Intresse områden

När du laddar upp en bild analyserar API för visuellt innehåll den för att fastställa *intresse ytan*. Den kan sedan använda den här regionen för att bestämma hur avbildningen ska beskäras. Beskärnings åtgärden kommer dock alltid att matcha det önskade proportionerna om ett anges.

Du kan också få koordinaterna för den obehandlade avgränsnings rutan i samma *område av intresse* genom att anropa **areaOfInterest** -API: et i stället. Du kan sedan använda den här informationen för att ändra den ursprungliga avbildningen som du vill.

## <a name="examples"></a>Exempel

Den genererade miniatyr bilden kan variera beroende på vad du anger för höjd, bredd och smart beskärning, som du ser i följande bild.

![En mountainbike-bild bredvid olika beskärnings konfigurationer](./Images/thumbnail-demo.png)

I följande tabell visas typiska miniatyr bilder som genereras av Visuellt innehåll för exempel bilderna. Miniatyrerna skapades för en angiven mål höjd och bredd på 50 bild punkter, med Smart beskärning aktiverat.

| Bild | Miniatyr |
|-------|-----------|
|![Utomhus mountainbike vid solnedgång, med en persons Silhouette](./Images/mountain_vista.png) | ![Miniatyr av utomhus mountainbike vid solnedgång, med en persons Silhouette](./Images/mountain_vista_thumbnail.png) |
|![En vit blomma med grön bakgrund](./Images/flower.png) | ![Vision-analysera blomma-miniatyr](./Images/flower_thumbnail.png) |
|![En kvinna på taket för en lägenhets byggnad](./Images/woman_roof.png) | ![miniatyr av en kvinna som är i taket för en lägenhets byggnad](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att [Tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).
