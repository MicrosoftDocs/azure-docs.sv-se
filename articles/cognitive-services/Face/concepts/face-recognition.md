---
title: Koncept för ansikts igenkänning
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs begreppen för att kontrol lera, hitta liknande, gruppera och identifiera ansikts igenkännings åtgärder och de underliggande data strukturerna.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518814"
---
# <a name="face-recognition-concepts"></a>Koncept för ansikts igenkänning

I den här artikeln beskrivs begreppen för att kontrol lera, hitta liknande, gruppera och identifiera ansikts igenkännings åtgärder och de underliggande data strukturerna. I stort sett beskrivs arbetet med att jämföra två olika ansikten för att avgöra om de är likartade eller som tillhör samma person.

## <a name="recognition-related-data-structures"></a>Data strukturer relaterade till igenkänning

Igenkännings åtgärderna använder huvudsakligen följande data strukturer. Dessa objekt lagras i molnet och kan refereras till av deras ID-strängar. ID-strängar är alltid unika i en prenumeration. Namn fält kan dupliceras.

|Name|Beskrivning|
|:--|:--|
|DetectedFace| Den här enskilda ansikts representationen hämtas av [ansikts identifierings](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) åtgärden. Dess ID upphör att gälla 24 timmar efter att det har skapats.|
|PersistedFace| När DetectedFace-objekt läggs till i en grupp, till exempel FaceList eller person, blir de PersistedFace objekt. De kan [hämtas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) när som helst och upphör inte att gälla.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) eller [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Den här data strukturen är en lista över PersistedFace-objekt. En FaceList har ett unikt ID, en namn sträng och eventuellt en användar data sträng.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Den här data strukturen är en lista över PersistedFace-objekt som tillhör samma person. Den har ett unikt ID, en namn sträng och eventuellt en användar data sträng.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) eller [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Den här data strukturen är en lista över person objekt. Den har ett unikt ID, en namn sträng och eventuellt en användar data sträng. En PersonGroup måste [tränas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) innan den kan användas i igenkännings åtgärder.|

## <a name="recognition-operations"></a>Igenkännings åtgärder

Det här avsnittet beskriver hur de fyra igenkännings åtgärderna använder data strukturer som tidigare beskrivits. En bred beskrivning av varje igenkännings åtgärd finns i [Översikt](../Overview.md).

### <a name="verify"></a>Verifiera

[Verifierings](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) åtgärden tar ett ansikts-ID från DetectedFace eller PersistedFace och antingen ett annat ansikts-ID eller ett person objekt och avgör om de tillhör samma person. Om du skickar ett person objekt kan du välja att skicka en PersonGroup som personen tillhör för att förbättra prestandan.

### <a name="find-similar"></a>Sök liknande

[Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) -åtgärden tar ett ansikts-ID från DetectedFace eller PersistedFace och antingen en FaceList eller en matris med andra ansikts-ID: n. Med en FaceList returneras en mindre FaceList av ansikten som liknar den aktuella ytan. Med en matris med ansikts-ID: n returnerar den ungefär en mindre matris.

### <a name="group"></a>Group

[Grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) åtgärden tar en matris med utvalda ansikts-ID: n från DetectedFace eller PersistedFace och returnerar samma ID grupperade i flera mindre matriser. Varje "Groups"-matris innehåller ansikts-ID: n som ser likadana ut. En enskild "messyGroup"-matris innehåller ansikts-ID: n för vilka inga likheter hittades.

### <a name="identify"></a>Hitta

[Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) -åtgärden tar ett eller flera ansikts-ID: n från DetectedFace eller PersistedFace och en PersonGroup och returnerar en lista med person objekt som varje ansikte kan tillhöra. Returnerade person objekt omsluts som kandidat objekt, som har ett värde för förutsägelse noggrannhet.

## <a name="input-data"></a>Indata

Använd följande tips för att se till att dina angivna bilder ger de mest exakta igenkännings resultaten:

* De inspelnings bild format som stöds är JPEG, PNG, GIF (den första ramen), BMP.
* Bild filens storlek får inte vara större än 6 MB.
* När du skapar person objekt bör du använda foton som har olika typer av vinklar och belysning.
* Vissa ansikten kanske inte kan identifieras på grund av tekniska utmaningar, till exempel:
  * Bilder med extrem belysning, till exempel hög bakgrunds belysning.
  * Hinder som blockerar en eller båda ögonen.
  * Skillnader i hår typ eller ansikts hår.
  * Ändringar i ansikts utseende på grund av ålder.
  * Extrema ansikts uttryck.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med koncepten för ansikts igenkänning kan du skriva ett skript som identifierar ansikten mot en utbildad PersonGroup.

* [Snabb start för ansikts klient bibliotek](../Quickstarts/client-libraries.md)