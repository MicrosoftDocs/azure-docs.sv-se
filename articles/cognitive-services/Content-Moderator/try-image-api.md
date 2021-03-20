---
title: Måttliga bilder med API-konsolen – Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Använd avbildnings redigerings-API: et i Azure Content Moderator för att initiera arbets flöden för genomsökning och gransknings redigering för bild innehåll.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 542fadd5e5ab91be7b7113064bf8c998dae08d12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912947"
---
# <a name="moderate-images-from-the-api-console"></a>Måttliga bilder från API-konsolen

Använd [avbildnings redigerings-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) i Azure Content moderator för att initiera arbets flöden för genomsökning och gransknings redigering för bild innehåll. I redigerings jobbet genomsöks innehållet efter svordomar och jämförs med anpassade och delade-blockeringslistor.

## <a name="use-the-api-console"></a>Använda API-konsolen
Innan du kan testa API: et i online-konsolen behöver du din prenumerations nyckel. Detta finns på fliken **Inställningar** i rutan **OCP-APIM-Subscription-Key** . Mer information finns i [Översikt](overview.md).

1. Gå till [bild moderator API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Sidan **bild-utvärdera** avbildnings kontroll öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Prova avbildning – utvärdera sid regions val](images/test-drive-region.png)
  
   API **-konsolen för avbildnings utvärdering** öppnas.

3. I rutan **OCP-APIM-Subscription-Key** anger du din prenumerations nyckel.

   ![Prova avbildning – utvärdera prenumerations nyckel för konsol](images/try-image-api-1.PNG)

4. I rutan **begär ande innehåll** använder du standard exempel bilden eller anger en bild som ska genomsökas. Du kan skicka själva avbildningen som binära bit data eller ange en offentligt tillgänglig URL för en avbildning. 

   I det här exemplet använder du den sökväg som anges i rutan **begär text** och väljer sedan **Skicka**. 

   ![Prova avbildning – utvärdera begär ande text för konsolen](images/try-image-api-2.PNG)

   Det här är bilden på den URL: en:

   ![Prova avbildning – utvärdera konsol exempel bild](images/sample-image.jpg) 

5. Välj **Skicka**.

6. API: et returnerar ett sannolikhets Poäng för varje klassificering. Den returnerar också en bestämning av om bilden uppfyller villkoren (**Sant** eller **falskt**). 

   ![Prova avbildning – utvärdera sannolikhets Poäng för konsol och villkors bestämning](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Ansiktsspårning

Du kan använda bild redigerings-API: et för att hitta ansikten i en bild. Det här alternativet kan vara användbart när du har sekretess frågor och vill förhindra att en speciell ansikte publiceras på din plattform. 

1. I [referens-API-referensen för bild redigering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), i den vänstra menyn, under **bild**, väljer du **hitta ansikten**. 

   Sidan **bilder – hitta ansikten** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Prova bild-hitta ansikten sidan region val](images/test-drive-region.png)

   API **-konsolen bild-hitta ansikten** öppnas.

3. Ange en bild som ska genomsökas. Du kan skicka själva avbildningen som binära bit data eller ange en offentligt tillgänglig URL till en bild. Det här exemplet länkar till en bild som används i en CNN berättelse.

   ![Prova bild-hitta ansikten exempel bild](images/try-image-api-face-image.jpg)

   ![Prova en bild – hitta ansikten exempel begär Anden](images/try-image-api-face-request.png)

4. Välj **Skicka**. I det här exemplet hittar API: t två ansikten och returnerar deras koordinater i bilden.

   ![Prova rutan för att hitta ansikten exempel svar innehåll](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Text identifiering via OCR-kapacitet

Du kan använda den Content Moderator OCR-funktionen för att identifiera text i bilder.

1. I [referens-API-referensen för bild redigering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), i den vänstra menyn, under **bild**, väljer du **OCR**. 

   Sidan **bild-OCR** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Bild – val av sid region för OCR](images/test-drive-region.png)

   API **-konsolen avbildning – OCR** öppnas.

3. I rutan **OCP-APIM-Subscription-Key** anger du din prenumerations nyckel.

4. I rutan **begär ande innehåll** använder du standard exempel bilden. Detta är samma bild som används i föregående avsnitt.

5. Välj **Skicka**. Den extraherade texten visas i JSON:

   ![Bild – innehålls rutan OCR-exempel svar](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Nästa steg

Använd REST API i din kod eller följ snabb starten för [.NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) för att lägga till avbildnings moderator i programmet.