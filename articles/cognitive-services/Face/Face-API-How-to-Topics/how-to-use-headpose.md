---
title: Använda attributet HeadPose
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder attributet HeadPose för att automatiskt rotera ansikts rektangeln eller identifiera huvud-gester i en video-feed.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 02/23/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: cec466fc420e435159e2882d7b39ef6d03eeea7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733490"
---
# <a name="use-the-headpose-attribute"></a>Använda attributet HeadPose

I den här hand boken får du se hur du kan använda attributet HeadPose för ett identifierat ansikte för att aktivera vissa nyckel scenarier.

## <a name="rotate-the-face-rectangle"></a>Rotera ansikts området

Den bakre rektangeln, som returneras med varje identifierad ansikte, markerar platsen och storleken på ytan i bilden. Som standard är rektangeln alltid justerad mot bilden (dess sidor är lodräta och vågräta). Detta kan vara ineffektivt för att Rama av vinklade ansikten. I situationer där du vill kunna beskära ansikten i en bild, är det bättre att kunna rotera rektangeln för beskärning.

Exempel appen [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) använder attributet HeadPose för att rotera dess identifierade ansikts rektanglar.

### <a name="explore-the-sample-code"></a>Utforska exempelkoden

Du kan rotera ansikts-rektangeln genom att använda attributet HeadPose. Om du anger det här attributet när du identifierar ansikten (se [hur du identifierar ansikten](HowtoDetectFacesinImage.md)) kommer du att kunna fråga det senare. Följande metod från [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) -appen tar en lista över **DetectedFace** -objekt och returnerar en lista över **[ansikts](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** objekt. **Face** här är en anpassad klass som lagrar ansikts data, inklusive de uppdaterade Rectangle-koordinaterna. Nya värden beräknas för **överkant**, **vänster**, **Bredd** och **höjd**, och ett nytt fält **FaceAngle** anger rotationen.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Visa den uppdaterade rektangeln

Härifrån kan du använda de returnerade **ansikts** objekten på skärmen. Följande rader från [FaceDetectionPage. XAML](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) visar hur den nya rektangeln återges från dessa data:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Identifiera huvud-gester

Du kan identifiera huvud-gester som nodding och huvud skakning genom att spåra HeadPose ändringar i real tid. Du kan använda den här funktionen som en anpassad Live-detektor.

Identifiering av direktmigreringar är en uppgift att avgöra om ett ämne är en riktig person och inte en bild eller video åter givning. En detektor för huvud-gest kan tjäna som ett sätt att kontrol lera om du bor, särskilt i stället för en bild representation av en person.

> [!CAUTION]
> Om du vill identifiera huvud-gester i real tid måste du anropa Ansikts-API med hög hastighet (mer än en gång per sekund). Om du har en prenumeration på kostnads fri nivå (F0) är det inte möjligt. Om du har en prenumeration på betald nivå ser du till att du har beräknat kostnaderna för att göra snabba API-anrop för identifiering av huvud-gester.

Se [HeadPose-exemplet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) på GitHub för ett arbets exempel på identifiering av huvud-gester.

## <a name="next-steps"></a>Nästa steg

Se [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) -appen på GitHub för ett fungerande exempel på roterade ansikts rektanglar. Eller, se [HeadPose-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) appen, som spårar HeadPose-attributet i real tid för att identifiera huvud förflyttningar.