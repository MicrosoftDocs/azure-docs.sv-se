---
title: Vad är en bild analys?
titleSuffix: Azure Cognitive Services
description: Tjänsten för avbildnings analys använder förtränade AI-modeller för att extrahera många olika visuella funktioner från bilder.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: visuellt innehåll, program för visuellt innehåll, dator vision
ms.openlocfilehash: f262fdb49cac4ab9abe7f3f6873160d3059968c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287278"
---
# <a name="what-is-image-analysis"></a>Vad är en bild analys?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Med tjänsten Visuellt innehåll Image Analysis kan du extrahera en mängd olika visuella funktioner från dina avbildningar. Det kan till exempel ta reda på om en bild innehåller olämpligt innehåll, hitta vissa varumärken eller objekt eller hitta mänskliga ansikten.

Du kan använda bild analys via en klient biblioteks-SDK eller genom att anropa [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) direkt. Kom igång genom att följa [snabb](quickstarts-sdk/image-analysis-client-library.md) starten.

Den här dokumentationen innehåller följande typer av artiklar:
* [Snabb starterna](./quickstarts-sdk/image-analysis-client-library.md) är steg-för-steg-instruktioner som gör att du kan anropa tjänsten och få resultat under en kort tids period. 
* [Instruktions guiderna](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) innehåller instruktioner för att använda tjänsten på mer exakta eller anpassade sätt.
* De [konceptuella artiklarna](concept-tagging-images.md) innehåller djupgående förklaringar av tjänstens funktioner och funktioner.
* [Självstudierna](./tutorials/storage-lab-tutorial.md) är längre guider som visar hur du använder den här tjänsten som en komponent i bredare affärs lösningar.

## <a name="image-analysis-features"></a>Bild analys funktioner

Du kan analysera bilder för att ge insikter om sina visuella funktioner och egenskaper. Alla funktioner i listan nedan finns i [analys avbildnings](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) -API: et. Kom igång genom att följa en [snabb start](./quickstarts-sdk/image-analysis-client-library.md) .


### <a name="tag-visual-features"></a>Tagga visuella egenskaper

Identifiera och tagga visuella egenskaper i en bild, från tusentals identifierbara objekt, levande varelser, landskap och åtgärder. När taggarna är tvetydiga eller inte vanliga kunskaper innehåller API-svaret tips för att klargöra taggens kontext. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare. [Tagga visuella egenskaper](concept-tagging-images.md)

### <a name="detect-objects"></a>Upptäcka objekt

Objektidentifiering liknar taggar, men API:n returnerar avgränsningsfältets koordinater för varje tagg som tillämpas. Om en bild exempelvis innehåller en hund, en katt och en person, kommer identifieringsåtgärden visa en lista över dessa objekt tillsammans med deras koordinater i bilden. Du kan använda den här funktionen till att bearbeta ytterligare relationer mellan objekt i en bild. Du får även veta när det finns flera instanser av samma tagg i en bild. [Upptäcka objekt](concept-object-detection.md)

### <a name="detect-brands"></a>Identifiera varumärken

Identifiera varumärken i bilder och videor från en databas med tusentals olika globala logotyper. Du kan använda den här funktionen för att till exempel identifiera vilka varumärken som är mest populära på sociala medier eller förekommer oftast i medieproduktplacering. [Identifiera varumärken](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Kategorisera en bild

Identifiera och kategorisera en hel bild med hjälp av en [kategoritaxonomi](Category-Taxonomy.md) med överordnade/underordnade ärftliga hierarkier. Kategorier kan användas fristående eller med våra nya taggningsmodeller.<br/>Engelska är för närvarande det enda språket som stöds för att tagga och kategorisera bilder. [Kategorisera en bild](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Beskriv en bild

Skapa en beskrivning av en hel bild i läsbart språk med fullständiga meningar. Algoritmer för visuellt innehåll genererar olika beskrivningar som baseras på de objekt som identifierats i bilden. Beskrivningarna utvärderas och förtroendepoäng genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng. [Beskriv en bild](concept-describing-images.md)

### <a name="detect-faces"></a>Identifiera ansikten

Identifiera ansikten i en bild och ange information om varje identifierat ansikte. Visuellt innehåll returnerar koordinater, rektanglar, kön och ålder för varje identifierat ansikte.<br/>Visuellt innehåll tillhandahåller en delmängd av funktionerna för [ansikts](../face/index.yml) tjänster. Du kan använda ansikts tjänsten för mer detaljerad analys, t. ex. ansikts identifiering och identifiering. [Identifiera ansikten](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Identifiera bildtyper

Identifiera olika kännetecken om en bild som t. ex. om det är en teckning eller om den kan vara ClipArt. [Identifiera bildtyper](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Identifiera domänspecifikt innehåll

Använd domänmodeller för att upptäcka och identifiera domänspecifikt innehåll i en bild, till exempel kändisar och landmärken. Om en bild till exempel innehåller personer kan Visuellt innehåll använda en domän modell för kändisar för att avgöra om de personer som identifieras i avbildningen är kända kändisar. [Identifiera domänspecifikt innehåll](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Identifiera färgschema

Analysera användningen av färg i en bild. Visuellt innehåll kan avgöra om en bild är svartvit eller i färg samt identifiera vilka färger som är basfärger och vilka färger som är accentfärger i färgbilder. [Identifiera färgschema](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Skapa en miniatyrbild

Analysera innehållet i en bild för att generera en lämplig miniatyrbild för den. Visuellt innehåll genererar först en högkvalitativ miniatyr och sedan analyseras objekten i bilden för att fastställa *intresseområdet*. Visuellt innehåll beskär sedan bilden för att den ska uppfylla kraven för intresseområdet. Den genererade miniatyrbilden kan vid behov anges med proportioner som skiljer sig från proportionerna på den ursprungliga bilden. [Skapa en miniatyrbild](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Hämta ett intresseområde

Analysera innehållet i en bild för att returnera koordinaterna för *intresseområdet*. I stället för att beskära bilden och generera en miniatyr, returnerar Visuellt innehåll områdets avgränsnings Rams koordinater, så det anropande programmet kan ändra den ursprungliga avbildningen efter behov. [Hämta ett intresseområde](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderera innehåll i bilder

Du kan använda Visuellt innehåll för att [identifiera vuxna innehåll](concept-detecting-adult-content.md) i en bild och returnera konfidensts resultat för olika klassificeringar. Tröskelvärdet för flaggat innehåll kan ställas in på en rörlig skala för att passa dina inställningar.

## <a name="image-requirements"></a>Avbildningskrav

Bild analys används för avbildningar som uppfyller följande krav:

- Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
- Filstorleken måste vara mindre än 4 megabyte (MB)
- Bildens dimensioner måste vara större än 50 x 50 bildpunkter

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder tjänsten Visuellt innehåll känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Kom igång med bild analys genom att följa snabb starts guiden på det föredragna utvecklings språket:

- [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/image-analysis-client-library.md)
