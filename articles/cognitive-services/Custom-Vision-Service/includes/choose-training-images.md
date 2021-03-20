---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 9f8eadea198bbae3de2ffc1b3aaac48925719586
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100106213"
---
Som minst rekommenderar vi att du använder minst 30 bilder per tagg i den första inlärnings uppsättningen. Du vill också samla in några extra bilder för att testa din modell när den har tränats.

Använd bilder med visuell sort för att träna modellen effektivt. Välj bilder som varierar av:
* kamera vinkel
* blixt
* bakgrund
* visuellt format
* enskilda/grupperade ämnen
* ikoner
* typ

Se dessutom till att alla dina utbildnings bilder uppfyller följande kriterier:
* . jpg-,. png-,. bmp-eller. gif-format
* inte större än 6 MB i storlek (4 MB för förutsägelse avbildningar)
* inte mindre än 256 bild punkter på den kortaste kanten; alla bilder som är kortare än detta kommer automatiskt att skalas upp av Custom Vision Service

> [!NOTE]
> Behöver du en bredare uppsättning avbildningar för att slutföra utbildningen? Källa, ett Microsoft garage-projekt, gör att du kan samla in och köpa uppsättningar av avbildningar i utbildnings syfte. När du har samlat in dina avbildningar kan du hämta dem och sedan importera dem till ditt Custom Vision-projekt på vanligt sätt. Besök [sidan källa](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) om du vill veta mer.