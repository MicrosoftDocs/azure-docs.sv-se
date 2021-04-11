---
title: Vad är optisk teckensnitts igenkänning?
titleSuffix: Azure Cognitive Services
description: Tjänsten OCR (optisk tecken läsning) extraherar synlig text i en bild och returnerar den som strukturerad sträng.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3fff9f4bd34fc1defdb50f2eefbc8ac1f39b46af
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287273"
---
# <a name="what-is-optical-character-recognition"></a>Vad är optisk teckensnitts igenkänning?

Med tjänsten optisk tecken igenkänning (OCR) kan du extrahera tryckt eller handskriven text från bilder, till exempel foton av licens plattor eller behållare med serie nummer, samt från dokument &mdash; fakturor, fakturor, ekonomiska rapporter, artiklar med mera. Den använder djup inlärning baserade modeller och fungerar med text på en rad olika ytor och bakgrunder.

API: erna för OCR stöder extrahering av utskriven text på [flera språk](./language-support.md). Kom igång genom att följa en [snabb start](./quickstarts-sdk/client-library.md) .

![OCR-demonstrationer](./Images/ocr-demo.gif)

Den här dokumentationen innehåller följande typer av artiklar:
* [Snabb starterna](./quickstarts-sdk/client-library.md) är steg-för-steg-instruktioner som gör att du kan anropa tjänsten och få resultat under en kort tids period. 
* [Instruktions guiderna](./Vision-API-How-to-Topics/call-read-api.md) innehåller instruktioner för att använda tjänsten på mer exakta eller anpassade sätt.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Språk som stöds
API: erna för OCR stöder totalt 73 språk för utskrifts format text. Se den fullständiga listan över [språk som stöds av OCR](./language-support.md#optical-character-recognition-ocr). OCR med Skriv stil stöds enbart för engelska.

## <a name="input-requirements"></a>Krav för indatamängd

**Läs** anropet tar bilder och dokument som inaktuella. De har följande krav:

* Fil format som stöds: JPEG, PNG, BMP, PDF och TIFF
* För PDF-och TIFF-filer, bearbetas upp till 2000 sidor (endast de två första sidorna för den kostnads fria nivån).
* Fil storleken måste vara mindre än 50 MB (4 MB för den kostnads fria nivån) och dimensioner minst 50 x 50 bild punkter och högst 10000 x 10000 bild punkter. 
* PDF-dimensionerna måste bestå av högst 17 × 17 tum, som motsvarar legal eller a3 pappers storlekar och mindre.

## <a name="read-api"></a>Läs-API 

Visuellt innehåll [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) är Azures senaste OCR-teknik ([Läs om vad som är nytt](./whats-new.md)) som extraherar utskriven text (på flera språk), handskriven text (endast engelska), siffror och valuta symboler från bilder och PDF-dokument med flera sidor. Det är optimerat för att extrahera text från text – tunga bilder och PDF-dokument med flera sidor med blandade språk. Det stöder identifiering av både skriven och handskriven text i samma bild eller dokument.

![Hur OCR konverterar bilder och dokument till strukturerade utdata med extraherad text](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Använd moln-API eller distribuera lokalt
Moln-API: erna för att läsa 3. x är det bästa alternativet för de flesta kunder på grund av enkel integrering och snabb produktivitet. Azure och Visuellt innehåll service hanterar skalning, prestanda, data säkerhet och efterlevnad för att möta kundernas behov.

För lokal distribution kan du med hjälp av den [skrivskyddade Docker-behållaren (för hands version)](./computer-vision-how-to-install-containers.md) distribuera de nya OCR-funktionerna i din egen lokala miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav.

## <a name="ocr-api"></a>OCR-API

Den äldre [OCR-API: n](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) använder en äldre igenkännings modell, stöder bara bilder och körs synkront, och returneras omedelbart med den identifierade texten. I kolumnen OCR för [språk som stöds](./language-support.md#optical-character-recognition-ocr) finns en lista över språk som stöds.

## <a name="recognizetext-api"></a>RecognizeText-API

> [!WARNING]
> Visuellt innehåll 2,0-RecognizeText åtgärder håller på att föråldras med den nya [Read-API: n](#read-api) som beskrivs i den här artikeln. Befintliga kunder ska [övergå till att använda Läs åtgärder](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder tjänsten Visuellt innehåll känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

- Kom igång med [snabb starterna för OCR-REST API eller klient bibliotek](./quickstarts-sdk/client-library.md).
- Lär dig mer om att [läsa 3,1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Läs mer om den [offentliga för hands versionen av läs 3,2 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) med stöd för totalt 73-språk.
