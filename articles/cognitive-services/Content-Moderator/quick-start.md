---
title: 'Snabb start: prova Content Moderator på webben'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du verktyget för Content Moderator granskning online för att testa de grundläggande funktionerna i Content Moderator utan att behöva skriva någon kod.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content moderator, innehålls moderator
ms.openlocfilehash: f8ad9c135fea4e582e6ba47764d0401936f8c295
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221184"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Snabb start: prova Content Moderator på webben

I den här snabb starten använder du verktyget för Content Moderator granskning online för att testa de grundläggande funktionerna i Content Moderator utan att behöva skriva någon kod. Om du vill integrera den här tjänsten i din app för innehålls redigering snabbare, se de andra snabb starterna i avsnittet [Nästa steg](#next-steps) .

## <a name="prerequisites"></a>Förutsättningar

- En webbläsare

## <a name="set-up-the-review-tool"></a>Konfigurera gransknings verktyget
Verktyget Content Moderator granskning är ett webbaserat verktyg som gör det möjligt för mänskliga granskare att hjälpa kognitiva tjänster att fatta beslut. I den här guiden ska du gå igenom kort processen för att konfigurera gransknings verktyget så att du kan se hur tjänsten Content Moderator fungerar. Gå till webbplatsen för [Content moderator granska verktyg](https://contentmoderator.cognitive.microsoft.com/) och registrera dig.

![Content Moderator start sida](images/homepage.PNG)

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Skapa sedan en gransknings grupp. I ett arbets scenario är det här teamet den grupp av personer som manuellt granskar tjänstens kontroll beslut. Om du vill skapa ett team måste du välja en **region** och ange ett **grupp namn** och ett **Team-ID**. Om du vill bjuda in kollegor till teamet kan du göra det genom att ange deras e-postadresser här.

> [!NOTE]
> **Team namn** är ett eget namn för gransknings teamet. Detta är det namn som visas i Azure Portal. **Team-ID: t** är det som används för att identifiera program mässigt för gransknings teamet.

> [!div class="mx-imgBorder"]
> ![Bjud in grupp medlem](images/create-team.png)

Om du väljer att kryptera data med hjälp av en kundhanterad nyckel (CMK) uppmanas du att ange **resurs-ID** för din Content moderator-resurs på E0 pris nivå. Resursen som du anger måste vara unik för det här teamet. 

> [!div class="mx-imgBorder"]
> ![Bjud in grupp medlem med CMK](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>Ladda upp exempel innehåll

Nu är du redo att ladda upp exempel innehåll. Välj **prova > bild**, **prova > Text** eller **prova > video**.

> [!div class="mx-imgBorder"]
> ![Prova bild-eller text redigering](images/tryimagesortext.png)

Skicka in ditt innehåll för redaktörering. Du kan använda följande exempel text innehåll:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Internt kommer gransknings verktyget att anropa redigerings-API: erna för att söka igenom innehållet. När genomsökningen är klar visas ett meddelande som talar om att det finns resultat som väntar på din granskning.

> [!div class="mx-imgBorder"]
> ![Måttliga filer](images/submitted.png)

## <a name="review-moderation-tags"></a>Granska moderator Taggar

Granska de använda redigerings taggarna. Du kan se vilka taggar som tillämpades på ditt innehåll och hur poängen var i varje kategori. Se artiklar om [avbildning](image-moderation-api.md), [text](text-moderation-api.md)och [video](video-moderation-api.md) för att lära dig mer om vad olika taggar för innehåll visar.

<!-- ![Review results](images/reviewresults_text.png) -->

I ett projekt kan du eller din gransknings grupp ändra dessa taggar eller lägga till fler taggar efter behov. Du skickar dessa ändringar med knappen **Nästa** . När ditt företags program anropar moderator-API: erna kommer det taggade innehållet att placeras här, vilket är klart att granskas av de mänskliga gransknings teamen. Du kan snabbt granska stora mängder innehåll med hjälp av den här metoden.

Nu har du använt verktyget Content Moderator granskning för att se ett exempel på vad tjänsten Content Moderator kan göra. Sedan kan du antingen lära dig mer om gransknings verktyget och hur du integrerar det i ett program projekt med hjälp av gransknings-API: erna, eller så kan du gå vidare till [Nästa steg](#next-steps) -avsnitt om du vill lära dig hur du använder API: erna för kontrollanter i din app.

## <a name="learn-more-about-the-review-tool"></a>Läs mer om gransknings verktyget

Om du vill veta mer om hur du använder verktyget för Content Moderator granskning kan du titta närmare på [gransknings](Review-Tool-User-Guide/human-in-the-loop.md) verktygs guiden och se API: erna för gransknings verktyg för att lära dig att finjustera mänsklig gransknings upplevelsen:
- [Jobb-API: et](try-review-api-job.md) skannar ditt innehåll med hjälp av API: erna för kontrollanter och genererar granskningar i gransknings verktyget. 
- I [gransknings-API: et](try-review-api-review.md) skapas direkt bild-, text-eller video granskningar för de mänskliga moderatorerna utan att först skanna innehållet. 
- [Arbets flödes-API: et](try-review-api-workflow.md) skapar, uppdaterar och hämtar information om de anpassade arbets flöden som ditt team skapar.

Eller Fortsätt med nästa steg för att komma igång med redigerings-API: er i din kod.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder redigerings-API: erna själva i din app.
- Implementera avbildnings moderator. Använd [API-konsolen](try-image-api.md) eller följ [snabb](client-libraries.md) starten för att skanna bilder och identifiera potentiellt vuxna och vågat innehåll genom att använda taggar, konfidens resultat och annan extraherad information.
- Implementera text moderator. Använd [API-konsolen](try-text-api.md) eller följ en [snabb start](client-libraries.md) för att skanna text innehåll för potentiella svordomar, personliga data och annan oönskad text.
- Implementera videoinspelning. Följ anvisningarna [för videoinspelnings guiden för C# för](video-moderation-api.md) att söka igenom videor och identifiera potentiellt vuxna och vågat innehåll. 
