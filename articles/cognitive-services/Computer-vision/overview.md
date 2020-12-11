---
title: Vad är visuellt innehåll?
titleSuffix: Azure Cognitive Services
description: Tjänsten Visuellt innehåll ger dig till gång till avancerade algoritmer för bearbetning av avbildningar och att returnera information.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: visuellt innehåll, program för visuellt innehåll, dator vision
ms.openlocfilehash: 62d82388d975f36c422f7eb1e9feff8ce2725d54
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033297"
---
# <a name="what-is-computer-vision"></a>Vad är visuellt innehåll?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azures Visuellt innehåll tjänst ger dig till gång till avancerade algoritmer som bearbetar bilder och returnerar information baserat på de visuella funktioner som du är intresse rad av. Visuellt innehåll kan till exempel avgöra om en bild innehåller innehåll som är olämpligt för barn, hitta vissa varumärken eller objekt eller hitta mänskliga ansikten.

Du kan skapa Visuellt innehåll program via ett [klient biblioteks-SDK](./quickstarts-sdk/client-library.md) eller genom att anropa [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) direkt. Den här sidan beskriver allmänt vad du kan göra med Visuellt innehåll.

## <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)

Visuellt innehåll innehåller [OCR-funktioner (optisk tecken läsning)](concept-recognizing-text.md) . Du kan använda den nya Read API: n för att extrahera utskrift och handskriven text från bilder och dokument. Den använder de senaste modellerna och fungerar med text på en rad olika ytor och bakgrunder. Det kan vara kvitton, affischer, visitkort, brev och whiteboardtavlor. De två OCR-API: erna stöder extrahering av utskriven text på [flera språk](./language-support.md). Kom igång genom att följa en [snabb start](./quickstarts-sdk/client-library.md) .

## <a name="computer-vision-for-digital-asset-management"></a>Visuellt innehåll för digital till gångs hantering

Visuellt innehåll kan sätta många moder scenarier för Digital Asset Management (damm). DAMM är affärs processen att organisera, lagra och hämta omfattande medie till gångar och hantera digitala rättigheter och behörigheter. Ett företag kan till exempel vilja gruppera och identifiera bilder baserat på synliga logo typer, ansikten, objekt, färger och så vidare. Eller så kanske du vill [Generera bild texter automatiskt för bilder](./Tutorials/storage-lab-tutorial.md) och bifoga nyckelord så att de är sökbara. För en allt-i-ett-damm-lösning med hjälp av Cognitive Services, Azure Kognitiv sökning och intelligent rapportering, se [vägledningen för Knowledge utvinnings Accelerator](https://github.com/Azure-Samples/azure-search-knowledge-mining) på GitHub. Andra moder exempel finns i lagrings platsen för [visuellt innehåll Solution templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) .

## <a name="analyze-images-for-insight"></a>Analysera bilder för att få information

Du kan analysera bilder för att ge insikter om sina visuella funktioner och egenskaper. Alla funktioner i tabellen nedan tillhandahålls av [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)-API:et. Kom igång genom att följa en [snabb start](./quickstarts-sdk/client-library.md) .


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

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-behållare

Använd Visuellt innehåll behållare för att distribuera API-funktioner lokalt. Med dessa Docker-behållare kan du ta tjänsten närmare dina data för efterlevnad, säkerhet eller andra drift orsaker. Visuellt innehåll erbjuder följande behållare:

* I [visuellt innehåll Read OCR container (för hands version)](computer-vision-how-to-install-containers.md) kan du identifiera skriven och handskriven text i bilder.
* Med [visuellt innehåll rums analys behållare (för hands version)](spatial-analysis-container.md) kan du analysera real tids strömnings video för att förstå spatiala relationer mellan människor och deras rörelse genom fysiska miljöer.

## <a name="image-requirements"></a>Avbildningskrav

Visuellt innehåll kan analysera bilder som uppfyller följande krav:

- Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
- Filstorleken måste vara mindre än 4 megabyte (MB)
- Bildens dimensioner måste vara större än 50 x 50 bildpunkter
  - För läsnings-API: t måste bildens mått vara mellan 50 x 50 och 10000 x 10000 bild punkter.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder tjänsten Visuellt innehåll känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Kom igång med Visuellt innehåll genom att följa snabb starts guiden på det föredragna utvecklings språket:

- [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/client-library.md)
