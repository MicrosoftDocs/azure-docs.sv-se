---
title: Vad är bildanalys?
titleSuffix: Azure Cognitive Services
description: Tjänsten Bildanalys använder förtränade AI-modeller för att extrahera många olika visuella funktioner från bilder.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: computer vision, computer vision applications, computer vision service
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766923"
---
# <a name="what-is-image-analysis"></a>Vad är bildanalys?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Tjänsten Visuellt innehåll Image Analysis kan extrahera en mängd olika visuella funktioner från dina bilder. Den kan till exempel avgöra om en bild innehåller vuxet innehåll, hitta specifika varumärken eller objekt eller hitta ansikten.

Du kan använda bildanalys via ett klientbiblioteks-SDK eller genom att [anropa REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) direkt. Kom igång [genom att följa snabbstarten.](quickstarts-sdk/image-analysis-client-library.md)

Den här dokumentationen innehåller följande typer av artiklar:
* [Snabbstarterna](./quickstarts-sdk/image-analysis-client-library.md) är stegvisa instruktioner som gör att du kan göra anrop till tjänsten och få resultat på kort tid. 
* [Instruktionsguiderna innehåller](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) instruktioner för att använda tjänsten på mer specifika eller anpassade sätt.
* [Konceptuella](concept-tagging-images.md) artiklar innehåller detaljerade förklaringar av tjänstens funktioner och funktioner.
* [Självstudierna](./tutorials/storage-lab-tutorial.md) är längre guider som visar hur du använder den här tjänsten som en komponent i bredare affärslösningar.

## <a name="image-analysis-features"></a>Funktioner för bildanalys

Du kan analysera bilder för att ge insikter om deras visuella funktioner och egenskaper. Alla funktioner i listan nedan tillhandahålls av Analysera bild [API.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Följ en [snabbstart för](./quickstarts-sdk/image-analysis-client-library.md) att komma igång.


### <a name="tag-visual-features"></a>Tagga visuella egenskaper

Identifiera och tagga visuella egenskaper i en bild, från tusentals identifierbara objekt, levande varelser, landskap och åtgärder. När taggarna är tvetydiga eller inte är allmänt känt ger API-svaret tips som tydliggör taggens kontext. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare. [Tagga visuella egenskaper](concept-tagging-images.md)

### <a name="detect-objects"></a>Upptäcka objekt

Objektidentifiering liknar taggar, men API:n returnerar avgränsningsfältets koordinater för varje tagg som tillämpas. Om en bild exempelvis innehåller en hund, en katt och en person, kommer identifieringsåtgärden visa en lista över dessa objekt tillsammans med deras koordinater i bilden. Du kan använda den här funktionen till att bearbeta ytterligare relationer mellan objekt i en bild. Du får även veta när det finns flera instanser av samma tagg i en bild. [Upptäcka objekt](concept-object-detection.md)

### <a name="detect-brands"></a>Identifiera varumärken

Identifiera varumärken i bilder och videor från en databas med tusentals olika globala logotyper. Du kan använda den här funktionen för att till exempel identifiera vilka varumärken som är mest populära på sociala medier eller förekommer oftast i medieproduktplacering. [Identifiera varumärken](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Kategorisera en bild

Identifiera och kategorisera en hel bild med hjälp av en [kategoritaxonomi](Category-Taxonomy.md) med överordnade/underordnade ärftliga hierarkier. Kategorier kan användas fristående eller med våra nya taggningsmodeller.<br/>Engelska är för närvarande det enda språket som stöds för att tagga och kategorisera bilder. [Kategorisera en bild](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Beskriv en bild

Skapa en beskrivning av en hel bild i läsbart språk med fullständiga meningar. Algoritmer för visuellt innehåll genererar olika beskrivningar som baseras på de objekt som identifierats i bilden. Beskrivningarna utvärderas och förtroendepoäng genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng. [Beskriv en bild](concept-describing-images.md)

### <a name="detect-faces"></a>Identifiera ansikten

Identifiera ansikten i en bild och ange information om varje identifierat ansikte. Visuellt innehåll returnerar koordinater, rektanglar, kön och ålder för varje identifierat ansikte.<br/>Visuellt innehåll en delmängd av [ansiktstjänstens](../face/index.yml) funktioner. Du kan använda tjänsten Ansiktsigenkänning för mer detaljerad analys, till exempel ansiktsidentifiering och attitydidentifiering. [Identifiera ansikten](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Identifiera bildtyper

Identifiera olika kännetecken om en bild som t. ex. om det är en teckning eller om den kan vara ClipArt. [Identifiera bildtyper](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Identifiera domänspecifikt innehåll

Använd domänmodeller för att upptäcka och identifiera domänspecifikt innehåll i en bild, till exempel kändisar och landmärken. Om en bild till exempel innehåller personer kan Visuellt innehåll använda en domänmodell för kändisar för att avgöra om de personer som har identifierats i bilden är kända kändisar. [Identifiera domänspecifikt innehåll](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Identifiera färgschema

Analysera användningen av färg i en bild. Visuellt innehåll kan avgöra om en bild är svartvit eller i färg samt identifiera vilka färger som är basfärger och vilka färger som är accentfärger i färgbilder. [Identifiera färgschema](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Skapa en miniatyrbild

Analysera innehållet i en bild för att generera en lämplig miniatyrbild för den. Visuellt innehåll genererar först en högkvalitativ miniatyr och sedan analyseras objekten i bilden för att fastställa *intresseområdet*. Visuellt innehåll beskär sedan bilden för att den ska uppfylla kraven för intresseområdet. Den genererade miniatyrbilden kan vid behov anges med proportioner som skiljer sig från proportionerna på den ursprungliga bilden. [Skapa en miniatyrbild](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Hämta ett intresseområde

Analysera innehållet i en bild för att returnera koordinaterna för *intresseområdet*. I stället för att beskära bilden och generera en miniatyrbild Visuellt innehåll returnerar koordinaterna för avgränsare för regionen, så att det anropande programmet kan ändra den ursprungliga bilden efter behov. [Hämta ett intresseområde](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderera innehåll i bilder

Du kan använda Visuellt innehåll för [att identifiera vuxet innehåll](concept-detecting-adult-content.md) i en bild och returnera förtroendepoäng för olika klassificeringar. Tröskelvärdet för att flagga innehåll kan ställas in på en glidande skala för att passa dina önskemål.

## <a name="image-requirements"></a>Avbildningskrav

Bildanalys fungerar på bilder som uppfyller följande krav:

- Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
- Filstorleken måste vara mindre än 4 megabyte (MB)
- Bildens dimensioner måste vara större än 50 x 50 bildpunkter

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder tjänsten Visuellt innehåll känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Kom igång med bildanalys genom att följa snabbstartsguiden på önskat utvecklingsspråk:

- [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/image-analysis-client-library.md)
