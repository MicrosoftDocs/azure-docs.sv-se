---
title: Azure Media Player funktions lista
description: En funktions referens för Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 4b0666b439c284fd402b3f6e04bbaed14d6aa358
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448773"
---
# <a name="feature-list"></a>Funktionslista #
Här är en lista över funktioner som har testats och funktioner som inte stöds:

| Funktion | UTFÖR | DELVIS TESTAD | Inte TESTat | SOM inte stöds | ANTECKNINGAR |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **Uppspelning**                                |        |                  |          |             |                                                                                                                      |
| Grundläggande uppspelning på begäran                | X      |                  |          |             | Stöder strömmar enbart från Azure Media Services                                                                      |
| Grundläggande direktsänd uppspelning                     | X      |                  |          |             | Stöder strömmar enbart från Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Stöder Azure Media Services Key Delivery Service                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Stöder Azure Media Services Key Delivery Service                                                                   |
| Widevine                                |        | X                |          |             | Stöder Widevine PSSH-rutor som beskrivs i manifestet                                                                    |
| FairPlay                                |        | X                |          |             | Stöder Azure Media Services Key Delivery Service                                                                   |
| **Teknik**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Återställnings punkt (blixt)                | X      |                  |          |             | Alla funktioner är inte tillgängliga på den här Tech-tekniken.                                                                         |
| Silverlight-återgångsreglerna      | X      |                  |          |             | Alla funktioner är inte tillgängliga på den här Tech-tekniken.                                                                         |
| Intern HLS-vidarekoppling (HTML5)         |        | X                |          |             | Alla funktioner är inte tillgängliga på den här Tech-tekniken på grund av plattforms begränsningar.                                            |
| **Funktioner**                                |        |                  |          |             |                                                                                                                      |
| API-stöd                             | X      |                  |          |             | Visa lista med kända problem                                                                                                |
| Grundläggande användar gränssnitt                                | X      |                  |          |                                                                                                                                    |
| Initiera genom Java Script       | X      |                  |          |             |                                                                                                                      |
| Initiera genom video tag gen        |        | X                |          |             |                                                                                                                      |
| Segment adressering-tid baserat         | X      |                  |          |             |                                                                                                                      |
| Segment adressering-index baserat        |        |                  |          | X           |                                                                                                                      |
| Segment adressering-byte baserat         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services URL-Rewriter       |        | X                |          |             |                                                                                                                      |
| Hjälpmedel – bild texter och under texter  | X      |                 |          |             |  WebVTT (på begäran), CEA 708 (på begäran och Live) och IMSC1 (på begäran och Live)                                                       |
| Hjälpmedel – snabb tangenter                 | X      |                  |          |             |                                                                                                                      |
| Hjälpmedel – Högkontrast           |        | X                |          |             |                                                                                                                      |
| Hjälpmedel – TABB-fokus               |        | X                |          |             |                                                                                                                      |
| Fel meddelanden                         |        | X                |          |             | Fel meddelanden är inkonsekventa i Techs                                                                         |
| Händelse utlöst                        | X      |                  |          |             |                                                                                                                      |
| Diagnostik                             |        | X                |          |             | Diagnostikinformation är bara tillgänglig på AzureHtml5JS-Tech och delvis tillgänglig i Silverlight-Tech. |
| Anpassningsbar teknisk ordning                 |        | X                |          |             |                                                                                                                      |
| Heuristik – grundläggande                      | X      |                  |          |             |                                                                                                                      |
| Heuristik – anpassning              |        |                  | X        |             | Anpassning är endast tillgängligt med AzureHtml5JS-Tech.                                                          |
| Discontinuities                         | X      |                  |          |             |                                                                                                                      |
| Välj bit hastighet                          | X      |                  |          |             | Det här API: et är bara tillgängligt i AzureHtml5JS-och Flash-teknikerna.                                                    |
| Data ström för flera ljud                      |        | X                |          |             | Program mässig ljud växel stöds på AzureHtml5JS-och blixt tekniska teknik och är tillgänglig via val av användar gränssnitt på AzureHtml5JS, blixt och inbyggd HTML5 (i Safari).  De flesta plattformar kräver samma codec-privata data för att växla ljud strömmar (samma codec, kanal, samplings frekvens osv.). |
| Lokalisering av användar gränssnitt                         |        | X                |          |             |                                                                                                                      |
| Uppspelning med flera instanser                 |        |                  |          | X           | Det här scenariot kan fungera för vissa tekniker, men det finns för närvarande inte stöd för och är inte testat. Du kan också få detta att fungera med iframes |
| Stöd för Ads                             |        | X                |          |             | AMP har stöd för infogning av linjära annonseringar före mitten och efter valsning från enorma kompatibla AD-servrar för VOD i AzureHtml5JS-Tech |
| Analys                               |        | X                |          |             | Med AMP kan du lyssna på analyser och diagnostiska händelser så att du kan skicka till en analys Server del som du väljer.  Alla händelser och egenskaper är inte tillgängliga i Tech på grund av plattforms begränsningar.                                                                            |
| Anpassade skal                            |        |                  | X        |             | Det här scenariot kan uppnås genom att ställa in kontroller på falskt i AMP och använda din egen HTML och CSS.           |
| Rensning av SeekBar-fält                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Endast ljud                              | X      |                  |          |           | Stöds i AzureHtml5JS. Progressiv MP3-uppspelning kan fungera med HTML5-Tech om plattformen stöder det.                                                                                                        |
| Endast video                              | X      |                  |          |           | Stöds i AzureHtml5JS.                                                                                                        |
| Presentation med flera perioder               |        |                  |          | X                                                                                                                                  |
| Flera kamera vinklar                  |        |                  |          | X           |                                                                                                                      |
| Uppspelnings hastighet                          |        | X                |          |             | Uppspelnings hastigheten stöds i de flesta scenarier, förutom det mobila fallet på grund av ett delvis fel i Chrome                 |

## <a name="next-steps"></a>Nästa steg ##
- [Azure Media Player snabb start](azure-media-player-quickstart.md)