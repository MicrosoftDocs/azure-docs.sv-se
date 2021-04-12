---
title: Hitta och redigera ansikten i Azure Media Services v3 API | Microsoft Docs
description: 'Azure Media Services v3 tillhandahåller en för inställning för ansikts identifiering och bortredigering (oskärpa) som gör att du kan skicka in en videofil, identifiera ansikten och eventuellt använda bortredigering (oskärpa) på dem i ett enda kombinerat pass eller via en åtgärd i två steg som gör det möjligt att redigera. Den här artikeln visar hur du hittar och bortredigering-ytor med ansikts detektor-förvalet i v3-API: et.'
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 4907a81fc8cb55499fa97f2b02a3e19e7117bbbc
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286393"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Hitta och bortredigering (oskärpa) ansikten med för inställning för ansikts detektor

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3-API: et innehåller en inställning för ansikts detektor som erbjuder skalbar ansikts igenkänning och bortredigering (oskärpa) i molnet. Med ansikts bortredigering kan du ändra videon så att det blir oskarpa ytor på valda individer. Du kanske vill använda ansikts bortredigering i offentliga säkerhets-och nyhets medie scenarier. Några minuter av tagningar som innehåller flera ansikten kan ta timmar till bortredigering manuellt, men med den här inställningen krävs bara några enkla steg.

Den här artikeln innehåller information om för **inställning av ansikts detektor** och visar hur du använder den med Azure Media Services SDK för .net.

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet
 
Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av analys i Azure Media Services. Du får inte använda Azure Media Services eller någon annan Azure-tjänst på ett sätt som strider mot andras rättigheter. Innan du laddar upp videor, inklusive bio metriska data, till den Azure Media Services tjänsten för bearbetning och lagring måste du ha alla lämpliga rättigheter, inklusive alla lämpliga medgivanden, från personerna i videon. För att lära dig om efterlevnad, sekretess och säkerhet i Azure Media Services, [Cognitive Services villkoren](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)i Azure. För Microsofts sekretess avtal och hantering av dina data kan du läsa Microsofts [sekretess policy](https://privacy.microsoft.com/PrivacyStatement), [Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products) (ost) och [data bearbetnings tillägget](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Mer sekretess information, inklusive för datakvarhållning, borttagning/förstörelse, finns i OST-filen och [här](../video-indexer/faq.md). Genom att använda Azure Media Services samtycker du till att vara kopplad till Cognitive Services villkor, OST, DPA och sekretess policyn

## <a name="face-redaction-modes"></a>Lägen för ansikts bortredigering

Ansikts bortredigering fungerar genom att identifiera ansikten i varje bild ruta i videon och spåra objektet ansikte både framåt och bakåt i tiden, så att samma person kan vara suddig från andra vinklar. Den automatiserade bortredigering-processen är komplex och är inte alltid suddig varje ansikte 100% garanterad. Därför kan för inställningen användas med två pass för att förbättra kvaliteten och noggrannheten i en redigerings fas innan du skickar in filen för det slutliga suddiga steget. 

Förutom ett helt automatiskt **kombinerat** läge gör det möjligt för arbets flödet för två pass att du kan välja de ansikten som du vill göra oskarp (eller inte oskärpa) via en lista över ansikts-ID: n. För att göra godtyckliga justeringar av varje ram för för inställningen används en metadatafil i JSON-format som indata för det andra steget. Det här arbets flödet är uppdelat i **analys** -och **bortredigering** -läge.

Du kan också enkelt bara kombinera de två lägena i ett enda pass som kör båda uppgifterna i ett jobb. Det här läget kallas **kombinerat**.  I den här artikeln visar exempel koden hur du använder det förenklade steget i **kombinerat** läge för en exempel käll fil.

### <a name="combined-mode"></a>Kombinerat läge

Detta skapar en förändrad MP4-videofil i ett enda steg utan manuell redigering av JSON-filen som krävs. Utdata i mappen till gångar för jobbet är en enskild MP4-fil som innehåller oskarpa ytor med den valda oskärpa-effekt. Använd lösnings egenskapen inställd på **SourceResolution** för att uppnå bästa möjliga resultat för bortredigering.

| Fas | Filnamn | Kommentarer |
| --- | --- | --- |
| Inmatad till gång |"ignite-sample.mp4" |Video i WMV-, MOV-eller MP4-format |
| För inställnings konfiguration |Konfiguration av ansikts detektor | **läge**: FaceRedactorMode. kombinerad,  **blurType**: blurType. med, **resolution**: AnalysisResolution. SourceResolution |
| Mata ut till gång |"ignite-redacted.mp4 |Video med suddig effekt som tillämpas på ansikten |

### <a name="analyze-mode"></a>Analysera läge

Det **analyserande** passet i två-pass-arbets flödet tar en video indata och skapar en JSON-fil med en lista över ansikts platser, ansikts-ID: n och jpg-bilder för varje identifierad yta. 

| Fas | Filnamn | Kommentarer |
| --- | --- | --- |
| Inmatad till gång |"ignite-sample.mp4" |Video i WMV-, MPV-eller MP4-format |
| För inställnings konfiguration |Konfiguration av ansikts detektor |**läge**: FaceRedactorMode. analysera, **lösning**: AnalysisResolution. SourceResolution|
| Mata ut till gång |ignite-sample_annotations.jspå |Antecknings data för ansikts platser i JSON-format. Detta kan redige ras av användaren för att ändra de oskarpa avgränsnings rutorna. Se exemplet nedan. |
| Mata ut till gång |foo_thumb% 06d.jpg [foo_thumb000001.jpg foo_thumb000002.jpg] |En beskurna jpg för varje identifierad ansikte, där talet anger labelId för ansikte |

#### <a name="output-example"></a>Exempel på utdata

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Bortredigering (suddigt) läge

Det andra steget i arbets flödet tar ett större antal indata som måste kombineras till en enda till gång.

Detta inkluderar en lista med ID: n som är suddig, den ursprungliga videon och antecknings-JSON. I det här läget används anteckningarna för att använda oskärpa på ingångs videon.

Den ursprungliga videon ingår inte i resultatet från analys steget. Videon måste överföras till indata till gången för uppgiften bortredigering och väljs som primär fil.

| Fas | Filnamn | Kommentarer |
| --- | --- | --- |
| Inmatad till gång |"ignite-sample.mp4" |Video i WMV-, MPV-eller MP4-format. Samma video som i steg 1. |
| Inmatad till gång |"ignite-sample_annotations.jspå" |anteckningarnas metadatafil från fas ett, med valfria ändringar om du vill ändra ansikten oskarpt. Detta måste redige ras i ett externt program, en kod eller en text redigerare. |
| Inmatad till gång | "ignite-sample_IDList.txt" (valfritt) | Valfri ny blankstegsavgränsad lista över ansikts-ID: n till bortredigering. Om inget anges kommer alla ansikten i källan att ha oskärpa applicerade. Du kan använda listan för att selektivt välja att inte göra vissa ytor oskarpa. |
| Inställning av ansikts detektor  |Förinställd konfiguration  | **läge**: FaceRedactorMode. bortredigering, **blurType**: blurType. with |
| Mata ut till gång |"ignite-sample-redacted.mp4" |Video med oskärpa som används baserat på anteckningar |

#### <a name="example-output"></a>Exempel på utdata

Detta är resultatet från en IDList med ett ID valt.

Exempel foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Suddiga typer

I det **kombinerade** eller **bortredigering** -läget finns det fem olika oskarpa lägen som du kan välja mellan via JSON-inkonfigurationen: **låg**, **mellan,** **hög**, **ruta** och **svart**. Som standard **används** med.

Du kan hitta exempel på suddiga typer nedan.


#### <a name="low"></a>Låg

![Exempel på Inställningar för låg upplöst oskärpa.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Notifiera

![Inställnings exempel för oskärpa med medelhög upplösning.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Högt

![Exempel på Inställningar för hög upplöst oskärpa.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Box-läge som ska användas för fel sökning av utdata.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Svart

![Svart Box-läge täcker alla ytor med svarta rutor.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata

För borttagnings-MP: en finns hög precisions plats för identifiering och spårning som kan identifiera upp till 64 mänskliga ansikten i en video bild ruta. Front ansikten ger bäst resultat, medan sidan ansikten och små ytor (mindre än eller lika med 24x24 bild punkter) är utmanande.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-exempel kod

Följande program visar hur du använder det **kombinerade** läget för borttagning av enstaka steg:

- Skapa en till gång och överför en mediefil till till gången.
- Konfigurera inställningen för ansikts detektor som använder inställningarna för läge och blurType.
- Skapa en ny transformering med hjälp av ansikts detektor-förvalet
- Hämta den avbildade video filen med utdata.

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Exemplet finns i mappen [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) Öppna [appsettings.jspå](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) i det nedladdade projektet. Ersätt värdena med de autentiseringsuppgifter som du har fått från [att komma åt API: er](./access-api-howto.md).

Du kan **också** kopiera exempel filen **[. kuvert](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** i roten på lagrings platsen och fylla i informationen i den och byta namn på filen till **. kuvert** (Observera punkten på fram sidan!) så att den kan användas i alla exempel projekt i databasen.  Detta eliminerar behovet av att ha en ifylld appsettings.jspå filen i varje exempel och skyddar dig inte från att kontrol lera inställningarna i dina egna klonade databaser i git-hubben.

#### <a name="examples"></a>Exempel

Den här koden visar hur du ställer in **FaceDetectorPreset** för **en** suddigt läge.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Det här kod exemplet visar hur för inställningarna skickas till ett Transform-objekt när det skapas. När transformeringen har skapats kan jobb skickas direkt till den.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

