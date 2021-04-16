---
title: Vad är Azure Ansiktstjänsten?
titleSuffix: Azure Cognitive Services
description: Azures ansiktstjänst tillhandahåller AI-algoritmer som du använder för att identifiera, känna igen och analysera ansikten i bilder.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: ansiktsigenkänning, programvara för ansiktsigenkänning, ansiktsanalys, ansiktsmatchning, ansiktsigenkänningsapp, ansiktssökning efter bild, sökning efter ansiktsigenkänning
ms.openlocfilehash: a999b2100dc57d7b623cebbbe261f821a80835e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505130"
---
# <a name="what-is-the-azure-face-service"></a>Vad är Azure Ansiktstjänsten?

> [!WARNING]
> Den 11 juni 2020 meddelade Microsoft att de inte kommer att sälja teknik för ansiktsigenkänning till polismyndigheter i USA förrän starka föreskrifter som rör användningen av dessa tekniker och som bygger på de mänskliga rättigheterna har införts. Därför kan kunder inte använda funktioner för ansiktsigenkänning som ingår i Azure-tjänster, till exempel Ansiktsigenkänning eller Video Indexer, om en kund är eller tillåter användning av sådana tjänster av eller för en USA.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azures ansiktstjänst tillhandahåller AI-algoritmer som identifierar, känner igen och analyserar ansikten i bilder. Programvara för ansiktsigenkänning är viktig i många olika scenarier, till exempel säkerhet, naturligt användargränssnitt, analys och hantering av bildinnehåll, mobilappar och robotteknik.

Tjänsten Ansiktsigenkänning innehåller flera olika funktioner för ansiktsanalys som var och en beskrivs i följande avsnitt.

Den här dokumentationen innehåller följande typer av artiklar:
* [Snabbstarterna](./Quickstarts/client-libraries.md) är stegvisa instruktioner som gör att du kan göra anrop till tjänsten och få resultat på kort tid. 
* [Instruktionsguiderna innehåller](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) instruktioner för att använda tjänsten på mer specifika eller anpassade sätt.
* [Konceptuella](./concepts/face-detection.md) artiklar innehåller detaljerade förklaringar av tjänstens funktioner och funktioner.
* [Självstudierna](./enrollment-overview.md) är längre guider som visar hur du använder den här tjänsten som en komponent i bredare affärslösningar.

## <a name="face-detection"></a>Ansiktsspårning

API:et Detect identifierar ansikten i en bild och returnerar rektangelkoordinaterna för deras platser. Alternativt kan ansiktsavkänning extrahera en serie ansiktsrelaterade attribut, till exempel huvudställning, kön, ålder, känsla, ansiktshår och glasögon. Dessa attribut är allmänna förutsägelser, inte faktiska klassificeringar. 

> [!NOTE]
> Ansiktsavkänningsfunktionen är också tillgänglig via [Visuellt innehåll tjänsten](../computer-vision/overview.md). Men om du vill göra ytterligare ansiktsåtgärder som Identifiera, Verifiera, Hitta liknande eller Grupp bör du använda den här ansiktstjänsten i stället.

![En bild av en kvinna och en man, med rektanglar ritade runt ansiktena och ålder och kön visade](./Images/Face.detection.jpg)

Mer information om ansiktsavkänning finns i artikeln [Begrepp för ansiktsavkänning.](concepts/face-detection.md) Se även [referensdokumentationen för Api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) för identifiering.

## <a name="face-verification"></a>Ansiktsverifiering

Verifierings-API:et bygger på Identifiering och tar upp frågan "Är dessa två bilder samma person?". Verifiering kallas även "en-till-en"-matchning eftersom avsökningsavbildningen jämförs med endast en registrerad mall. Verifiering kan användas i scenarier för identitetsverifiering eller åtkomstkontroll för att verifiera att en bild matchar en tidigare bild (till exempel från ett foto från ett id-kort som utfärdats av en myndighet). Mer information finns i [begreppsguiden för ansiktsigenkänning](concepts/face-recognition.md) eller [referensdokumentationen Verifiera API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="face-identification"></a>Ansiktsidentifiering

API:et Identifiera börjar också med Identifiering och svarar på frågan "Kan det identifierade ansiktet matchas mot alla registrerade ansikten i en databas?" Eftersom det är som ansiktsigenkänningssökning kallas det även för "en-till-många"-matchning. Kandidatmatchning returneras baserat på hur nära avsökningsmallen med det identifierade ansiktet matchar var och en av de registrerade mallarna.

Följande bild visar ett exempel på en databas med namnet `"myfriends"` . Varje grupp kan innehålla upp till 1 miljon olika personobjekt. Varje personobjekt kan ha upp till 248 registrerade ansikten.

![Ett rutnät med tre kolumner för olika personer, var och en med tre rader med ansiktsbilder](./Images/person.group.clare.jpg)

När du har skapat och tränat en databas kan du identifiera den mot gruppen med ett nytt identifierat ansikte. Om ansiktet identifieras som en person i gruppen, så returneras personobjektet.

Mer information om personidentifiering finns i [begreppsguiden för ansiktsigenkänning](concepts/face-recognition.md) eller [referensdokumentationen för API:et](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) Identifiera.

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Find Similar-API:et gör ansiktsmatchning mellan målansiktet och en uppsättning kandidatansikten, och hittar en mindre uppsättning ansikten som ser ut ungefär som målansiktet. Detta är användbart för att göra en ansiktssökning efter bild. 

Två arbetslägen, **matchPerson** och **matchFace,** stöds. Läget **matchPerson** returnerar liknande ansikten efter filtrering för samma person med hjälp av [API:et Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Läget **matchFace** ignorerar samma-person-filtret. Den returnerar en lista över liknande kandidatansikten som kan tillhöra samma person.

I följande exempel visas måltavlan:

![En leende kvinna](./Images/FaceFindSimilar.QueryFace.jpg)

Och de här bilderna är kandidatansiktena:

![Fem bilder på leende människor. Bilder a och b visar samma person.](./Images/FaceFindSimilar.Candidates.jpg)

Om du vill hitta fyra liknande ansikten **returnerar läget matchPerson** ett och b, som visar samma person som måltavlan. Läget **matchFace** returnerar a, b, c och d exakt fyra kandidater, även om vissa inte är samma person som målet eller &mdash; har låg likhet. Mer information finns i [begreppsguiden för ansiktsigenkänning](concepts/face-recognition.md) eller hitta [liknande API-referensdokumentation.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Ansiktsgruppering

API:et för gruppering delar in en uppsättning okända ansikten i flera grupper baserat på likhet. Varje grupp är en åtskild delmängd av den ursprungliga uppsättningen ansikten. Alla ansikten i en grupp tillhör sannolikt samma person. Det kan finnas flera olika grupper för en enskild person. Grupperna särskiljs med en annan faktor, till exempel uttryck. Mer information finns i [begreppsguiden för ansiktsigenkänning](concepts/face-recognition.md) eller i [referensdokumentationen för grupp-API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)


## <a name="sample-apps"></a>Exempelappar

Följande exempelprogram visar några sätt att använda ansiktstjänsten:

- [Ansikts-API: Windows-klientbibliotek](https://github.com/Microsoft/Cognitive-Face-Windows) och -exempel är en WPF-app som demonstrerar flera scenarier för ansiktsigenkänning, analys och identifiering.
- [FamilyNotes UWP-appen](https://github.com/Microsoft/Windows-appsample-familynotes) är en Universell Windows-plattform-app (UWP) som använder ansiktsidentifiering tillsammans med tal, Cortana, pennanteckningar och kamera i ett scenario med delning av familjeanteckningar.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla Cognitive Services måste utvecklare som använder ansiktstjänsten vara medvetna om Microsofts principer för kunddata. Mer information finns på [Cognitive Services på](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Följ en snabbstart för att koda de grundläggande komponenterna i en ansiktsigenkänningsapp på det språk du väljer.

- [Snabbstart för klientbibliotek.](quickstarts/client-libraries.md)
