---
title: Identifiera ansikten i en bild – Ansikte
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du använder ansiktsavkänning för att extrahera attribut som kön, ålder eller attityd från en viss bild.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 71e98b735b4aa4631d73f8730a48c56a8c7585ab
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497650"
---
# <a name="get-face-detection-data"></a>Hämta ansiktsavkänningsdata

Den här guiden visar hur du använder ansiktsavkänning för att extrahera attribut som kön, ålder eller attityd från en viss bild. Kodfragmenten i den här guiden skrivs i C# med hjälp Azure Cognitive Services klientbiblioteket för ansiktsuttryck. Samma funktioner är tillgängliga via [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Den här guiden visar hur du:

- Hämta ansiktens platser och dimensioner i en bild.
- Hämta platserna för olika ansiktsmärken, till exempel pupiller, näsa och näsa, i en bild.
- Gissa kön, ålder, känsla och andra attribut för ett identifierat ansikte.

## <a name="setup"></a>Konfiguration

Den här guiden förutsätter att du redan har konstruerat [ett FaceClient-objekt](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) med namnet `faceClient` , med prenumerationsnyckeln för ansiktsobjektet och slutpunkts-URL:en. Härifrån kan du använda funktionen för ansiktsavkänning genom att anropa [antingen DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), som används i den här guiden eller [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync). Följ någon av snabbstarterna om du vill ha anvisningar om hur du ställer in den här funktionen.

Den här guiden fokuserar på information om identifierings-anropet, till exempel vilka argument du kan skicka och vad du kan göra med returnerade data. Vi rekommenderar att du endast frågar efter de funktioner du behöver. Varje åtgärd tar ytterligare tid att slutföra.

## <a name="get-basic-face-data"></a>Hämta grundläggande ansiktsdata

Om du vill hitta ansikten och hämta deras platser i en bild anropar du metoden [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) eller [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) med _parametern returnFaceId_ inställd på **true**. Den här inställningen är standardinställningen.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Du kan fråga de [returnerade DetectedFace-objekten](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) efter deras unika ID och en rektangel som ger ansiktets pixelkoordinater.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Information om hur du parsar ansiktets plats och dimensioner finns [i FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Den här rektangeln innehåller vanligtvis ögon, näsa, näsa och mun. Toppen av huvudet, nosen och hakan ingår inte nödvändigtvis. Om du vill använda ansiktsrektangeln för att beskära ett fullständigt huvud eller få ett stående stående bild, kanske för en bild av foto-ID-typ, kan du expandera rektangeln i varje riktning.

## <a name="get-face-landmarks"></a>Hämta ansiktsmärken

[Ansiktsmärken](../concepts/face-detection.md#face-landmarks) är en uppsättning lätt att hitta punkter i ett ansikte, till exempel pupiller eller näsa. Om du vill hämta ansiktslandmärkesdata anger du parametern _detectionModel_ till **DetectionModel.Detection01** och _parametern returnFaceLandmarks_ till **true**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Följande kod visar hur du kan hämta platserna för näsa och pupiller:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Du kan också använda ansikts landmärken för att korrekt beräkna ansiktets riktning. Du kan till exempel definiera rotationen av ansiktet som en vektor från mitten av ögonen till mitten av ögonen. Följande kod beräknar den här vektorn:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

När du känner till ansiktets riktning kan du rotera den rektangulära ansiktsramen för att justera den mer korrekt. Om du vill beskära ansikten i en bild kan du programmatiskt rotera bilden så att ansiktena alltid visas som stående.

## <a name="get-face-attributes"></a>Hämta ansiktsattribut

Förutom ansiktsrektanglar och landmärken kan ansiktsavkännings-API:et analysera flera konceptuella attribut för ett ansikte. En fullständig lista finns i avsnittet [Konceptuella ansiktsattribut.](../concepts/face-detection.md#attributes)

Om du vill analysera ansiktsattribut anger du parametern _detectionModel_ till **DetectionModel.Detection01** och _parametern returnFaceAttributes_ till en lista [med FaceAttributeType Enum-värden.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Hämta sedan referenser till returnerade data och gör fler åtgärder efter dina behov.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Mer information om vart och ett av attributen finns i [konceptuell guide för ansiktsavkänning och](../concepts/face-detection.md) attribut.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du använder de olika funktionerna i ansiktsavkänning. Integrera sedan dessa funktioner i en app för att lägga till ansiktsdata från användare.

- [Självstudie: Lägga till användare i en ansiktstjänst](../enrollment-overview.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Referensdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referensdokumentation (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
