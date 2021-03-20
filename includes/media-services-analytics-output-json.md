---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187757"
---
Jobbet skapar en JSON-utdatafil som innehåller metadata om identifierade och spårade ansikten. Metadata innehåller koordinater som visar platsen för ansikten, samt ett ansikts-ID-nummer som visar spårningen av den enskilda. Ansikts-ID-nummer är känsliga att återställa under förhållanden när front ytan tappas bort eller överlappar varandra i ramen, vilket leder till att vissa personer får flera ID: n.

Utdata-JSON innehåller följande element:

### <a name="root-json-elements"></a>JSON-element för rot

| Element | Beskrivning |
| --- | --- |
| version |Detta avser versionen av video-API: et. |
| tidsplan |"Tickar" per sekund för videon. |
| offset |Detta är tids förskjutningen för tidsstämplar. I version 1,0 av video-API: er, är det alltid 0. I framtida scenarier som vi stöder kan detta värde ändras. |
| bredd, High |Bredden och High för video rutan för utdata i bild punkter.|
| ram |Bildrutor per sekund i videon. |
| [fragment](#fragments-json-elements) |Metadata delas upp i olika segment som kallas fragment. Varje fragment innehåller en start, varaktighet, intervallnummer och händelser. |

### <a name="fragments-json-elements"></a>Fragmenterar JSON-element

|Element|Beskrivning|
|---|---|
| start |Start tiden för den första händelsen i "ticks". |
| varaktighet |Längden på fragmentet i "tickas". |
| index | (Gäller endast Azure Media Redactor) definierar ram indexet för den aktuella händelsen. |
| interval |Intervallet för varje händelse post i fragmentet, i "ticker". |
| händelser |Varje händelse innehåller ansikten som har identifierats och spårats inom den tids perioden. Det är en matris med händelser. Den yttre matrisen representerar ett visst tidsintervall. Den inre matrisen består av 0 eller fler händelser som inträffade vid den tidpunkten. En tom hak paren tes [] innebär att inga ansikten har identifierats. |
| id |ID för den ansikte som spåras. Det här talet kan oavsiktligt ändras om ett ansikte blir oidentifierat. En viss individ bör ha samma ID i den övergripande videon, men det kan inte garanteras på grund av begränsningar i detektions algoritmen (ocklusion osv.). |
| x, y |De övre vänstra X-och Y-koordinaterna för den bakre gräns rutan i en normaliserad skala på 0,0 till 1,0. <br/>-X-och Y-koordinaterna är relativa till liggande Always, så om du har en stående video (eller upp-ned, i mån av iOS) måste du transponera koordinaterna. |
| bredd, höjd |Bredden och höjden på den bakre gräns rutan i en normaliserad skala på 0,0 till 1,0. |
| facesDetected |Detta finns i slutet av JSON-resultaten och sammanfattar antalet ansikten som algoritmen identifierade under videon. Eftersom ID: na kan återställas oavsiktligt om ett ansikte blir oidentifierat (t. ex. visas skärmen, inte alltid lika med det verkliga antalet ansikten i videon). |
