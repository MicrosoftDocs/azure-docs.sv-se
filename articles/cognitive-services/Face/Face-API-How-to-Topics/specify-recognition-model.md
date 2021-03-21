---
title: Så här anger du en igenkännings modell – ansikte
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du väljer vilken igenkännings modell som ska användas med ditt Azure-ansikts program.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: cd4ed992557a3c333919b63cc36757beb2f70454
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433564"
---
# <a name="specify-a-face-recognition-model"></a>Ange en ansiktsigenkänningsmodell

Den här guiden visar hur du anger en ansikts igenkännings modell för ansikts igenkänning, identifiering och likhets sökning med hjälp av Azure Face service.

Ansikts tjänsten använder maskin inlärnings modeller för att utföra åtgärder på mänskliga ansikten i bilder. Vi fortsätter att förbättra noggrannheten i våra modeller baserat på kundfeedback och framsteg i forskningen, och vi levererar dessa förbättringar som modell uppdateringar. Utvecklare har möjlighet att ange vilken version av ansikts igenkännings modellen som de vill använda. de kan välja den modell som passar bäst för deras användnings fall.

Azure Face service har fyra igenkännings modeller tillgängliga. Modellerna _recognition_01_ (publicerad 2017), _recognition_02_ (publicerad 2019) och _recognition_03_ (publicerad 2020) stöds kontinuerligt för att säkerställa bakåtkompatibilitet för kunder som använder FaceLists eller **PersonGroup** s som skapats med dessa modeller. En **FaceList** eller **persongroup** använder alltid den igenkännings modell som den skapades med och nya ytor blir kopplade till den här modellen när de läggs till. Detta kan inte ändras efter att kunderna har skapats och kunder måste använda motsvarande igenkännings modell med motsvarande **FaceList** eller **PersonGroup**.

Du kan flytta till senare igenkännings modeller i din egen bekvämlighet. Du måste dock skapa nya FaceLists och PersonGroups med den igenkännings modell som du väljer.

_Recognition_04_ modellen (publicerad 2021) är den mest exakta modellen som är tillgänglig just nu. Om du är en ny kund rekommenderar vi att du använder den här modellen. _Recognition_04_ ger bättre precision för både likhets jämförelser och jämförelser med person matchningar. _Recognition_04_ förbättrar igenkänningen för registrerade användare som använder ansikte (kirurgiska masker, N95-masker, duk-masker). Nu kan du skapa säkra och sömlösa användar upplevelser som använder den senaste _detection_03_ modellen för att identifiera om en registrerad användare använder ett ansikts skydd och sedan identifiera vem de är med den senaste _recognition_04s_ modellen. Observera att varje modell fungerar oberoende av de andra och att en förtroende tröskel som har angetts för en modell inte är avsedd att jämföras i andra igenkännings modeller.

Läs vidare om du vill veta hur du anger en vald modell i olika ansikts operationer samtidigt som du undviker modell konflikter. Om du är en avancerad användare och vill bestämma om du ska växla till den senaste modellen går du till avsnittet [utvärdera olika modeller](#evaluate-different-models) för att utvärdera den nya modellen och jämför resultat med den aktuella data uppsättningen.


## <a name="prerequisites"></a>Förutsättningar

Du bör känna till begreppen AI-ansikts identifiering och identifiering. Om du inte gör det, se de här guiderna först:

* [Koncept för ansikts igenkänning](../concepts/face-detection.md)
* [Koncept för ansikts igenkänning](../concepts/face-recognition.md)
* [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansikts igenkänning identifierar de visuella landmärken av mänskliga ansikten och hittar deras platser för avgränsnings rutor. Den extraherar också ansikts funktionerna och lagrar dem för användning i identifiering. All den här informationen utgör en representation av ett ansikte.

Igenkännings modellen används när ansikts funktionerna extraheras, så du kan ange en modell version när du utför identifierings åtgärden.

När du använder API: et för [ansikts igenkänning] tilldelar du modell versionen med `recognitionModel` parametern. De tillgängliga värdena är:
* recognition_01
* recognition_02
* recognition_03
* recognition_04


Alternativt kan du ange parametern _returnRecognitionModel_ (standard **falskt**) för att ange om _recognitionModel_ ska returneras som svar. En fråge-URL för [ansikts igenkännings] REST API ser därför ut så här:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Om du använder klient biblioteket kan du tilldela värdet för `recognitionModel` genom att skicka en sträng som representerar versionen. Om du lämnar den otilldelad, används en standard modell version av `recognition_01` . Se följande kod exempel för .NET-klient biblioteket.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifiera ansikten med angiven modell

Ansikts tjänsten kan extrahera ansikts data från en avbildning och koppla den till ett **person** objekt (via [Lägg till ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) -API-anrop, till exempel) och flera **person** objekt kan lagras tillsammans i en **PersonGroup**. Sedan kan en ny ansikte jämföras med en **PersonGroup** (med [ansikts identifiera] -anropet) och den matchande personen i gruppen kan identifieras.

En **PersonGroup** bör ha en unik igenkännings modell för all **person** s, och du kan ange detta med hjälp av `recognitionModel` parametern när du skapar gruppen ([PersonGroup-Create] eller [LargePersonGroup-Create]). Om du inte anger den här parametern används den ursprungliga `recognition_01` modellen. En grupp använder alltid den igenkännings modell som den skapades med, och nya ytor blir kopplade till den här modellen när de läggs till. Detta kan inte ändras efter att en grupp har skapats. Om du vill se vilken modell en **PersonGroup** har kon figurer ATS med använder du [PersonGroup-get-] API med parametern _returnRecognitionModel_ inställd på **True**.

Se följande kod exempel för .NET-klient biblioteket.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

I den här koden skapas en **PersonGroup** med ID `mypersongroupid` och den har kon figurer ats att använda _recognition_02s_ modellen för att extrahera ansikts funktioner.

På motsvarande sätt måste du ange vilken modell som ska användas för att identifiera ansikten som ska jämföras med den här **PersonGroup** (via API för [ansikts igenkänning] ). Modellen du använder bör alltid vara konsekvent med **PersonGroup**-konfigurationen. annars fungerar inte åtgärden på grund av inkompatibla modeller.

Det finns ingen ändring i API för [ansikts identifiering] . du behöver bara ange modell versionen som ska identifieras.

## <a name="find-similar-faces-with-specified-model"></a>Hitta liknande ansikten med angiven modell

Du kan också ange en igenkännings modell för sökning efter likheter. Du kan tilldela modell versionen med `recognitionModel` när du skapar ansikts listan med [FaceList-Create] API eller [LargeFaceList-Create]. Om du inte anger den här parametern `recognition_01` används modellen som standard. En ansikts lista använder alltid den igenkännings modell som den skapades med och nya ytor blir kopplade till den här modellen när de läggs till i listan. Du kan inte ändra det när du har skapat. Om du vill se vilken modell en ansikts lista har kon figurer ATS med använder du [FaceList-get-] API med parametern _returnRecognitionModel_ inställd som **Sant**.

Se följande kod exempel för .NET-klient biblioteket.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

Den här koden skapar en ansikts lista `My face collection` med namnet med hjälp av _recognition_04s_ modellen för funktions extrahering. När du söker i den här ansikts listan efter liknande ansikten till en ny identifierad ansikte, måste det ansikte ha identifierats ([upptäcka]) med _recognition_04_ modellen. Som i föregående avsnitt måste modellen vara konsekvent.

Det finns ingen ändring i [FACET-identifiera liknande] API; du anger bara modell versionen som ska identifieras.

## <a name="verify-faces-with-specified-model"></a>Verifiera ansikten med angiven modell

API för [ansikts verifiering] kontrollerar om två ansikten tillhör samma person. Det finns ingen ändring i verifierings-API: t avseende igenkännings modeller, men du kan bara jämföra ansikten som upptäcktes med samma modell.

## <a name="evaluate-different-models"></a>Utvärdera olika modeller

Om du vill jämföra prestanda för olika igenkännings modeller på dina egna data måste du:
1. Skapa fyra PersonGroups med _recognition_01_, _recognition_02_, _recognition_03_ respektive _recognition_04_ .
1. Använd dina avbildnings data för att identifiera ansikten och registrera dem på **personens** inom fyra **PersonGroup** s. 
1. Träna din PersonGroups med hjälp av PersonGroup-träna API.
1. Testa med ansikte – identifiera för alla fyra **PersonGroup** s och jämför resultaten.


Om du vanligt vis anger ett konfidens tröskelvärde (ett värde mellan noll och ett som avgör hur trygg modellen måste vara att identifiera en ansikts) kan du behöva använda olika tröskelvärden för olika modeller. Ett tröskelvärde för en modell är inte avsett att delas till en annan och kommer inte nödvändigt vis att ge samma resultat.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du anger vilken igenkännings modell som ska användas med olika API: er för ansikts tjänster. Följ sedan en snabb start för att komma igång med ansikts igenkänning.

* [Ansikte .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Ansikts python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Ansikte go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Ansiktsigenkänning – Känna igen]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Ansikte – hitta liknande]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Ansiktsigenkänning – identifiera]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Bekräfta ansikte]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup – Hämta]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList – skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList – Hämta]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – skapa]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc