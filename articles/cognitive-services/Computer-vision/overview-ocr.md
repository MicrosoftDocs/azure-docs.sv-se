---
title: Vad är optisk teckenläsning?
titleSuffix: Azure Cognitive Services
description: Tjänsten optisk teckenläsning (OCR) extraherar synlig text i en bild och returnerar den som strukturerade strängar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: da4ada8b505c747d24738e175a1701b5ea73b4e4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536746"
---
# <a name="what-is-optical-character-recognition"></a>Vad är optisk teckenläsning?

Med tjänsten optisk teckenläsning (OCR) kan du extrahera tryckt eller handskriven text från bilder, till exempel foton på gatuskyltar och produkter, samt från dokumentfakturor, fakturor, ekonomiska &mdash; rapporter, artiklar med mera. Den använder djupinlärningsbaserade modeller och arbetar med text på olika ytor och bakgrunder.

OCR-API:erna stöder extrahering av tryckt text på [flera språk.](./language-support.md) Följ en [snabbstart för](./quickstarts-sdk/client-library.md) att komma igång.

![OCR-demonstrationer](./Images/ocr-demo.gif)

Den här dokumentationen innehåller följande typer av artiklar:
* [Snabbstarterna](./quickstarts-sdk/client-library.md) är stegvisa instruktioner som gör att du kan göra anrop till tjänsten och få resultat på kort tid. 
* [Instruktionsguiderna innehåller](./Vision-API-How-to-Topics/call-read-api.md) instruktioner för att använda tjänsten på mer specifika eller anpassade sätt.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Språk som stöds
OCR-API:erna stöder totalt 73 språk för textutskrift. Se den fullständiga listan över [OCR-språk som stöds.](./language-support.md#optical-character-recognition-ocr) OCR i handskriven stil stöds exklusivt för engelska.

## <a name="input-requirements"></a>Indatakrav

**Anropet** Läsa tar bilder och dokument som indata. De har följande krav:

* Filformat som stöds: JPEG, PNG, BMP, PDF och TIFF
* För PDF- och TIFF-filer bearbetas upp till 2 000 sidor (endast de första två sidorna för den kostnadsfria nivån).
* Filstorleken måste vara mindre än 50 MB (4 MB för den kostnadsfria nivån) och dimensioner minst 50 x 50 bildpunkter och högst 1 0 0000 x 1 0 000 bildpunkter. 

## <a name="read-api"></a>Api för läsning 

API:et Visuellt innehåll [Read](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) är Azures senaste OCR-teknik[(lär](./whats-new.md)dig vad som är nytt) som extraherar tryckt text (på flera språk), handskriven text (endast engelska), siffror och valutasymboler från bilder och PDF-dokument med flera sidor. Den är optimerad för att extrahera text från texttunga bilder och PDF-dokument med flera sidor med blandade språk. Den stöder identifiering av både tryckt och handskriven text i samma bild eller dokument.

![Hur OCR konverterar bilder och dokument till strukturerade utdata med extraherad text](./Images/how-ocr-works.svg)

### <a name="key-features"></a>Huvudfunktioner

API:et Read innehåller följande funktioner. 

* Extrahering av text på 73 språk
* Handskriven textuttrahering på engelska
* Textrader och ord med plats- och förtroendepoäng
* Ingen språkidentifiering krävs
* Stöd för blandade språk, blandat läge (skriv ut och handskriven)
* Välj sidor och sidintervall från stora dokument med flera sidor
* Naturlig läsordning för textrader
* Handskriftsklassificering för textrader
* Tillgänglig som distrolös Docker-container för lokal distribution

Lär [dig hur du använder OCR-funktionerna](./vision-api-how-to-topics/call-read-api.md).

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Använda moln-API:et eller distribuera lokalt
Read 3.x-moln-API:erna är det föredragna alternativet för de flesta kunder eftersom det är enkelt att integrera och få snabb produktivitet. Azure och Visuellt innehåll hanterar skalnings-, prestanda-, datasäkerhets- och efterlevnadsbehov samtidigt som du fokuserar på att uppfylla dina kunders behov.

För lokal distribution gör [Read Docker-containern (förhandsversion)](./computer-vision-how-to-install-containers.md) att du kan distribuera de nya OCR-funktionerna i din egen lokala miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav.

## <a name="ocr-api"></a>OCR API

Det äldre [OCR-API:et](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) använder en äldre igenkänningsmodell, stöder endast bilder och körs synkront och returnerar omedelbart med den identifierade texten. En lista över språk som [stöds finns i](./language-support.md#optical-character-recognition-ocr) ocr-kolumnen med språk som stöds.

> [!WARNING]
> De Visuellt innehåll 2.0 RecognizeText-åtgärderna håller på att bli inaktuella för det nya [Läs-API](#read-api) som beskrivs i den här artikeln. Befintliga kunder bör [övergå till att använda läsåtgärder.](upgrade-api-versions.md)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder tjänsten Visuellt innehåll känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

- Kom igång med [snabbstarterna OCR (REST API eller klientbibliotek).](./quickstarts-sdk/client-library.md)
- Läs mer om [Read 3.2 REST API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
