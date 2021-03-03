---
title: Identifiera ansikten i en bild – ansikte
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du använder ansikts igenkänning för att extrahera attribut som kön, ålder eller attityd från en specifik bild.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a15cce45c527a92c99e0488661e0b67bb8e2371
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713073"
---
# <a name="get-face-detection-data"></a>Hämta ansikts identifierings data

Den här guiden visar hur du använder ansikts igenkänning för att extrahera attribut som kön, ålder eller attityd från en specifik bild. Kodfragmenten i den här hand boken skrivs i C# med hjälp av klient biblioteket för Azure Cognitive Services Face. Samma funktioner är tillgängliga via [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Den här guiden visar hur du:

- Hämta platser och dimensioner för ansikten i en bild.
- Få plats för olika ansikts landmärken, till exempel elever, näsa och mun, i en bild.
- Gissa kön, ålder, känslo och andra attribut för ett identifierat ansikte.

## <a name="setup"></a>Installation

Den här guiden förutsätter att du redan har skapat ett [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) -objekt `faceClient` med namnet, med en ansikts prenumerations nyckel och en slut punkts-URL. Härifrån kan du använda funktionen för ansikts igenkänning genom att anropa antingen [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), som används i den här hand boken eller [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync). Instruktioner för hur du konfigurerar den här funktionen finns i en av snabb starterna.

Den här guiden fokuserar på de olika uppgifterna i identifierings anropet, till exempel vilka argument du kan skicka och vad du kan göra med returnerade data. Vi rekommenderar att du frågar efter enbart de funktioner du behöver. Varje åtgärd tar ytterligare tid att slutföra.

## <a name="get-basic-face-data"></a>Hämta grundläggande ansikts data

Om du vill hitta ansikten och hämta sina platser i en bild anropar du metoden [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) eller [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) med parametern _returnFaceId_ inställd på **True**. Den här inställningen är standardinställningen.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Du kan fråga de returnerade [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) -objekten för sina unika ID: n och en rektangel som ger facets pixel koordinater.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Information om hur du analyserar facets plats och dimensioner finns i [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Den här rektangeln innehåller vanligt vis ögonen, eyebrows, näsa och munnen. Huvud-, öron-och Chin är inte nödvändigt vis inkluderade. Om du vill använda ansikts rektangeln för att beskära en hel rubrik eller få ett mellanliggande porträtt, kanske för en bild av bild-ID-typ, kan du expandera rektangeln i varje riktning.

## <a name="get-face-landmarks"></a>Hämta ansikts landmärken

[Ansikts landmärken](../concepts/face-detection.md#face-landmarks) är en uppsättning enkla punkter på ett ansikte, till exempel elever eller spets. Om du vill hämta ansikts landmärkes data anger du parametern _detectionModel_ till **detectionModel. Detection01** och _returnFaceLandmarks_ -parametern till **True**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Följande kod visar hur du kan hämta platserna för näsan och elever:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Du kan också använda ansikts landmärkes data för att korrekt beräkna riktningen på ytan. Du kan till exempel definiera rotationen för ansiktst som en vektor från munnen i mitten till centrum för ögonen. Följande kod beräknar den här vektorn:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

När du känner till ansikts riktningen kan du rotera den rektangulära ytan för att justera den på ett mer korrekt sätt. Om du vill beskära ansikten i en bild kan du program mässigt rotera bilden så att ansikten alltid visas på rätt sätt.

## <a name="get-face-attributes"></a>Hämta ansikts attribut

Förutom ansikts rektanglar och landmärken kan ansikts igenkännings-API: et analysera flera konceptuella attribut för en ansikte. En fullständig lista finns i avsnittet koncept för [ansikts attribut](../concepts/face-detection.md#attributes) .

Om du vill analysera ansikts attribut anger du parametern _detectionModel_ till **detectionModel. Detection01** och _returnFaceAttributes_ -parametern till en lista med [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) -värden.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Hämta sedan referenser till de data som returnerades och utför fler åtgärder utifrån dina behov.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Mer information om varje attribut finns i konceptuell guide för [ansikts igenkänning och attribut](../concepts/face-detection.md) .

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du använder olika funktioner för ansikts igenkänning. Sedan integrerar du dessa funktioner i din app genom att följa en djupgående själv studie kurs.

- [Självstudie: Skapa en WPF-app för att visa ansiktsinformation i en bild](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Referens dokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referens dokumentation (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
