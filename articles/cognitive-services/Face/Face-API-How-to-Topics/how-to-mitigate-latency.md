---
title: Så här minskar du svarstiden när du använder ansiktstjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du minskar svarstiden när du använder ansiktstjänsten.
services: cognitive-services
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.openlocfilehash: a306883573387a2a5c20a53c7015c6dbd3eddf65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878676"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Så här minimerar du svarstiden när du använder ansiktstjänsten

Du kan stöta på svarstider när du använder ansiktstjänsten. Svarstid avser alla typer av fördröjningar som inträffar vid kommunikation över ett nätverk. I allmänhet är möjliga orsaker till svarstider:
- Det fysiska avståndet som varje paket måste färdas från källa till mål.
- Problem med överföringsmediet.
- Fel i routrar eller växlar längs överföringsvägen.
- Den tid som krävs av antivirusprogram, brandväggar och andra säkerhetsmekanismer för att inspektera paket.
- Fel i klient- eller serverprogram.

Det här avsnittet handlar om möjliga orsaker till svarstider som är specifika för Azure Cognitive Services och hur du kan åtgärda dessa orsaker.

> [!NOTE]
> Azure Cognitive Services tillhandahåller inga Serviceavtal (SLA) angående svarstid.

## <a name="possible-causes-of-latency"></a>Möjliga orsaker till svarstid

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Långsam anslutning mellan Cognitive Service och en fjärr-URL

Vissa Azure Cognitive Services metoder som hämtar data från en fjärr-URL som du anger. När du till exempel anropar metoden [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) för ansiktstjänsten kan du ange URL:en för en bild där tjänsten försöker identifiera ansikten.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Ansiktstjänsten måste sedan ladda ned bilden från fjärrservern. Om anslutningen från ansiktstjänsten till fjärrservern är långsam påverkar det svarstiden för identifieringsmetoden.

Du kan åtgärda detta genom [att överväga att lagra avbildningen i Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Exempel:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Stor uppladdningsstorlek

Vissa Azure Cognitive Services metoder som hämtar data från en fil som du laddar upp. När du till exempel anropar [metoden DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) för ansiktstjänsten kan du ladda upp en bild där tjänsten försöker identifiera ansikten.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Om filen som ska laddas upp är stor påverkar det svarstiden för `DetectWithStreamAsync` metoden av följande skäl:
- Det tar längre tid att ladda upp filen.
- Det tar längre tid för tjänsten att bearbeta filen, i proportion till filstorleken.

Mitigations:
- Överväg [att lagra avbildningen i Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Exempel:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Överväg att ladda upp en mindre fil.
    - Se riktlinjerna för [indata för ansiktsavkänning och](../concepts/face-detection.md#input-data) [indata för ansiktsigenkänning.](../concepts/face-recognition.md#input-data)
    - När du använder identifieringsmodellen för ansiktsigenkänning ökar bearbetningshastigheten om du `DetectionModel.Detection01` minskar bildfilens storlek. När du använder identifieringsmodellen ökar en minskning av bildfilens storlek endast bearbetningshastigheten om bildfilen är mindre än `DetectionModel.Detection02` 1 920 x 1 080.
    - För ansiktsigenkänning påverkar inte en minskning av ansiktsstorleken till 200 x 200 bildpunkter noggrannheten för igenkänningsmodellen.
    - Prestanda för metoderna `DetectWithUrlAsync` och beror också på hur många ansikten som finns i en `DetectWithStreamAsync` bild. Ansiktstjänsten kan returnera upp till 100 ansikten för en bild. Ansikten rangordnas efter ansiktsrektangelns storlek från stor till liten.
    - Om du behöver anropa flera tjänstmetoder kan du överväga att anropa dem parallellt om din programdesign tillåter det. Om du till exempel behöver identifiera ansikten i två bilder för att utföra en ansiktsjämförelse:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Långsam anslutning mellan beräkningsresursen och ansiktstjänsten

Om datorn har en långsam anslutning till ansiktstjänsten påverkar det svarstiden för tjänstmetoderna.

Mitigations:
- När du skapar ansiktsprenumerationen ska du välja den region som är närmast där programmet finns.
- Om du behöver anropa flera tjänstmetoder kan du anropa dem parallellt om din programdesign tillåter det. Se föregående avsnitt för ett exempel.
- Om längre svarstider påverkar användarupplevelsen väljer du ett tröskelvärde för tidsgräns (t.ex. max 5 sekunder) innan du försöker igen med API-anropet.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du minskar svarstiden när du använder ansiktstjänsten. Lär dig sedan att skala upp från befintliga PersonGroup- och FaceList-objekt till LargePersonGroup- respektive LargeFaceList-objekt.

> [!div class="nextstepaction"]
> [Exempel: Använda den storskaliga funktionen](how-to-use-large-scale.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Referensdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referensdokumentation (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
