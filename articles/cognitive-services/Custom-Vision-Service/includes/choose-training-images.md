---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 5508199212b788e13a27b3c97b8fb7174dc92f32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531803"
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
> Behöver du en bredare uppsättning avbildningar för att slutföra utbildningen? Källa, ett Microsoft garage-projekt, gör att du kan samla in och köpa uppsättningar av avbildningar i utbildnings syfte. När du har samlat in dina avbildningar kan du hämta dem och sedan importera dem till ditt Custom Vision-projekt på vanligt sätt. Besök [sidan källa](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) om du vill veta mer.