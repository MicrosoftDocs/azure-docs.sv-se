---
title: Lär dig att granska verktygs koncept – Content Moderator
titleSuffix: Azure Cognitive Services
description: Lär dig mer om Content Moderator gransknings verktyget, en webbplats som samordnar en kombinerad AI-och mänsklig gransknings ansträngning.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146645"
---
# <a name="content-moderator-review-tool"></a>Content Moderator gransknings verktyget

Azure Content Moderator tillhandahåller tjänster för att kombinera maskin inlärnings innehåll med mänsklig granskning. Webbplatsen för [gransknings verktyg](https://contentmoderator.cognitive.microsoft.com) är en användarvänlig klient som ger detaljerad åtkomst till dessa tjänster.

## <a name="what-it-does"></a>Vad läget gör

I [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com), när det används tillsammans med de verktyg för redigering av datorbaserade redigerings program, kan du utföra följande uppgifter i innehålls moderator processen:

- Använd en uppsättning verktyg för att måttligt innehåll i flera format (text, bild och video).
- Automatisera skapandet av mänsklig [granskningar](../review-api.md#reviews) när du får kontroll-API-resultat.
- Tilldela eller eskalera innehålls granskningar till flera gransknings grupper, sorterade efter innehålls kategori eller miljö nivå.
- Använd standard-eller anpassade logik filter ([arbets flöden](../review-api.md#workflows)) för att sortera och spåra innehåll utan att skriva någon kod.
- Använd [kopplingar](./configure.md#connectors) för att bearbeta innehåll med Microsoft PhotoDNA, textanalys och ansikts tjänster utöver Content moderator-API: er.
- Hämta viktiga prestanda mått för dina innehålls moderator processer.

## <a name="review-tool-dashboard"></a>Instrument panel för gransknings verktyg

På fliken **instrument panel** kan du se viktiga mått för innehålls granskningar som har gjorts i verktyget. Se antalet totala, fullständiga och väntande recensioner för bild-, text-och video innehåll. 

I tabellen **väntande granskningar** visas en nedbrytning av användare och under grupper som har väntande eller slutförda granskningar, samt den återstående SLA-tiden. Du kan välja objekt i tabellen för att gå till granskningarna. I sökrutan ovanför tabellen kan du filtrera resultaten efter grupp namn, och med **filter** ikonen kan du filtrera efter andra mått.

Om du växlar till fliken **slutförda recensioner** visas det totala antalet objekt som har bearbetats eller slutförts av användare och under grupper. Du kan filtrera dessa data på samma sätt som de väntande granskningarna.

Om du klickar på texten i det övre högra hörnet på instrument panelen visas de dagliga personliga måtten, som rapporterar antalet granskningar som har slutförts för varje innehålls typ.

> [!div class="mx-imgBorder"]
> ![Instrument panelen för gransknings verktyg i en webbläsare](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Granska autentiseringsuppgifterna för verktyget

När du registrerar dig med [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com)uppmanas du att välja en Azure-region för ditt konto. Detta beror på att [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) genererar en kostnads fri utvärderings nyckel för Azure Content moderator Services. Du behöver den här nyckeln för att få åtkomst till alla tjänster från ett REST-anrop eller klient-SDK. Du kan visa URL: en för din nyckel och API-slutpunkt genom att välja **admin**-  >  **autentiseringsuppgifter**.

> [!div class="mx-imgBorder"]
> ![Content Moderator autentiseringsuppgifter](images/settings-6-credentials.png)

## <a name="next-steps"></a>Nästa steg

Se [Konfigurera gransknings verktyget](./configure.md) för att lära dig att komma åt resurser för gransknings verktyget och ändra inställningar.