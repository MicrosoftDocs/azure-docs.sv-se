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
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: visuellt innehåll, program för visuellt innehåll, dator vision
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286142"
---
# <a name="what-is-computer-vision"></a>Vad är visuellt innehåll?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azures Visuellt innehåll tjänst ger dig till gång till avancerade algoritmer som bearbetar bilder och returnerar information baserat på de visuella funktioner som du är intresse rad av. 

| Tjänst|Beskrivning|
|---|---|
|[Optisk teckenläsning (OCR)](overview-ocr.md)|Tjänsten OCR (optisk tecken läsning) extraherar text från bilder. Du kan använda den nya Read API: n för att extrahera utskrift och handskriven text från foton och dokument. Den använder djup inlärnings modeller och fungerar med text på olika ytor och bakgrunder. Det kan vara affärs dokument, fakturor, kvitton, affischer, visitkort, brev och whiteboardtavlor. API: erna för OCR stöder extrahering av utskriven text på [flera språk](./language-support.md). Kom igång genom att följa snabb starten för [OCR](quickstarts-sdk/client-library.md) .|
|[Bildanalys](overview-image-analysis.md)| Tjänsten Image Analysis extraherar många visuella funktioner från bilder, till exempel objekt, ansikten, vuxen innehåll och automatiskt genererade text beskrivningar. Kom igång genom att följa snabb starten för [bild analys](quickstarts-sdk/image-analysis-client-library.md) .|
| [Rumslig analys](intro-to-spatial-analysis-public-preview.md)| Den spatiala analys tjänsten analyserar närvaron och flytten av personer i ett video flöde och genererar händelser som andra system kan svara på. Installera [rums analys behållaren](spatial-analysis-container.md) för att komma igång.|

## <a name="computer-vision-for-digital-asset-management"></a>Visuellt innehåll för digital till gångs hantering

Visuellt innehåll kan sätta många moder scenarier för Digital Asset Management (damm). DAMM är affärs processen att organisera, lagra och hämta omfattande medie till gångar och hantera digitala rättigheter och behörigheter. Ett företag kan till exempel vilja gruppera och identifiera bilder baserat på synliga logo typer, ansikten, objekt, färger och så vidare. Eller så kanske du vill [Generera bild texter automatiskt för bilder](./Tutorials/storage-lab-tutorial.md) och bifoga nyckelord så att de är sökbara. För en allt-i-ett-damm-lösning med hjälp av Cognitive Services, Azure Kognitiv sökning och intelligent rapportering, se [vägledningen för Knowledge utvinnings Accelerator](https://github.com/Azure-Samples/azure-search-knowledge-mining) på GitHub. Andra moder exempel finns i lagrings platsen för [visuellt innehåll Solution templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) .

## <a name="image-requirements"></a>Avbildningskrav

Visuellt innehåll kan analysera bilder som uppfyller följande krav:

- Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
- Filstorleken måste vara mindre än 4 megabyte (MB)
- Bildens dimensioner måste vara större än 50 x 50 bildpunkter
  - För läsnings-API: t måste bildens mått vara mellan 50 x 50 och 10000 x 10000 bild punkter.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder tjänsten Visuellt innehåll känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ en snabb start för att implementera och köra en tjänst på önskat utvecklings språk.

* [Snabb start: optisk tecken läsning (OCR)](quickstarts-sdk/client-library.md)
* [Snabb start: bild analys](quickstarts-sdk/image-analysis-client-library.md)
* [Snabb start: rums analys behållare](spatial-analysis-container.md)
