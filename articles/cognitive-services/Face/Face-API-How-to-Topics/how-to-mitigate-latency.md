---
title: Så här minimerar du svars tiden när du använder ansikts tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du minimerar svars tiden när du använder ansikts tjänsten.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: b4035e2039afb6fe66d2658ebfcd3206d46e1de5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682470"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Gör så här: minimera svars tiden när du använder ansikts tjänsten

Du kan stöta på fördröjning när du använder ansikts tjänsten. Latens syftar på vilken typ av fördröjning som inträffar vid kommunikation över ett nätverk. I allmänhet är möjliga orsaker till svars tider:
- Det fysiska avståndet som varje paket måste färdas från källa till mål.
- Problem med överförings mediet.
- Fel i routrar eller växlar längs överförings vägen.
- Den tid det tar för antivirus program, brand väggar och andra säkerhetsmekanismer att inspektera paket.
- Fel i klient-eller Server program.

Det här avsnittet innehåller information om möjliga orsaker till att använda Azure-Cognitive Services och hur du kan minimera dessa orsaker.

> [!NOTE]
> Azure Cognitive Services tillhandahåller inga Serviceavtal (SLA) för svars tider.

## <a name="possible-causes-of-latency"></a>Möjliga orsaker till svars tid

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Långsam anslutning mellan kognitiva tjänster och en fjärr-URL

Vissa Azure-Cognitive Services tillhandahåller metoder som hämtar data från en fjärran sluten URL som du anger. När du till exempel anropar [DetectWithUrlAsync-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) för ansikts tjänsten kan du ange URL: en för en avbildning där tjänsten försöker identifiera ansikten.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Ansikts tjänsten måste sedan ladda ned avbildningen från fjärrservern. Om anslutningen från ansikts tjänsten till fjärrservern är långsam, påverkas svars tiden för identifierings metoden.

För att undvika detta bör du överväga att [lagra avbildningen i Azure Premium-Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet). Till exempel:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Storlek för stor uppladdning

Vissa Azure-Cognitive Services tillhandahåller metoder som hämtar data från en fil som du överför. När du till exempel anropar [DetectWithStreamAsync-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) för ansikts tjänsten kan du ladda upp en avbildning där tjänsten försöker identifiera ansikten.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Om filen som ska överföras är stor, vilket påverkar svars tiden för `DetectWithStreamAsync` metoden, av följande skäl:
- Det tar längre tid att ladda upp filen.
- Tjänsten tar längre tid att bearbeta filen, i proportion till fil storleken.

Åtgärder
- Överväg att [lagra avbildningen i Azure Premium-Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet). Till exempel:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Överväg att ladda upp en mindre fil.
    - Se rikt linjerna angående indata [för ansikts](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-detection#input-data) igenkänning och [indata för ansikts igenkänning](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-recognition#input-data).
    - När identifierings modellen används i ansikts igenkänning `DetectionModel.Detection01` ökar bild filens storlek genom att minska bearbetnings hastigheten. När du använder identifierings modellen `DetectionModel.Detection02` ökar bild filens storlek bara bearbetnings hastigheten om bild filen är mindre än 1920x1080.
    - För ansikts igenkänning påverkar inte storleken på 200x200 bild punkter precisionen för igenkännings modellen.
    - Prestandan hos- `DetectWithUrlAsync` och- `DetectWithStreamAsync` metoderna beror också på hur många ansikten som finns i en bild. Ansikts tjänsten kan returnera upp till 100 ansikten för en bild. Ansikten rangordnas efter storleken på den bakre rektangeln från stor till liten.
    - Om du behöver anropa flera tjänst metoder bör du överväga att anropa dem parallellt om din program design tillåter det. Om du till exempel behöver identifiera ansikten i två bilder för att utföra en ansikts jämförelse:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Långsam anslutning mellan beräknings resursen och ansikts tjänsten

Om datorn har en långsam anslutning till ansikts tjänsten som påverkar svars tiden för tjänst metoder.

Åtgärder
- När du skapar din ansikts prenumeration ska du se till att välja den region som ligger närmast där ditt program finns.
- Om du behöver anropa flera tjänst metoder bör du överväga att anropa dem parallellt om din program design tillåter det. Se föregående avsnitt för ett exempel.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du minimerar svars tiden när du använder ansikts tjänsten. Härnäst lär du dig hur du skalar upp från befintliga PersonGroup-och FaceList-objekt till LargePersonGroup-och LargeFaceList-objekten.

> [!div class="nextstepaction"]
> [Exempel: Använd funktionen för storskalig skalning](how-to-use-large-scale.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Referens dokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referens dokumentation (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)
