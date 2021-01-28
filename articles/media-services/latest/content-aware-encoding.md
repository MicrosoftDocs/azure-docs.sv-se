---
title: En för inställning för innehålls medveten kodning
description: Den här artikeln beskriver innehålls medveten kodning i Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c63ada9621fc1485742a7683fd6742f0e896854
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954437"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Använd den innehålls medveten kodnings inställningen för att hitta det optimala värdet för bit hastighet för en specifik lösning

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

För att förbereda innehåll för leverans av [direkt uppspelning med anpassningsbar bit hastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)måste video kodas med flera bit hastigheter (hög till låg). Detta säkerställer en korrekt försämring av kvaliteten, eftersom bit hastigheten sänks så är videons upplösning. Sådan kodning med flera bit hastigheter använder en så kallad kodnings steg – en tabell över lösningar och bit hastigheter finns i Media Services [inbyggda kodnings för inställningar](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Du bör vara medveten om det innehåll som du bearbetar och anpassa/finjustera kodnings stegen till den enskilda videons komplexitet. Vid varje lösning är det en bit hastighet utöver vilken eventuell ökning av kvalitet inte är Perceptive – kodaren fungerar med detta värde för optimal bit hastighet. Nästa optimerings nivå är att välja lösningarna baserat på innehållet, till exempel om en video i en PowerPoint-presentation inte drar nytta av under 720p. Om du går vidare kan kodaren utföra åtgärder för att optimera inställningarna för varje bild i videon. 

Microsofts för hands inställning för [adaptiv strömning](autogen-bitrate-ladder.md) tar delvis itu över problemet med variabiliteten i käll videoklippets kvalitet och upplösning. Våra kunder har en varierande blandning av innehåll, en del på 1080p, andra vid 720p och några få vid SD och lägre upplösning. Dessutom är inte allt käll innehåll mezzanines av hög kvalitet från film-eller TV-Studios. För hands inställningen för anpassad direkt uppspelning åtgärdar problemen genom att se till att bit hastighets stegen aldrig överskrider upplösningen eller den genomsnittliga bit hastigheten för indata-mezzaninfil. Men den här för inställningen granskar inte andra käll egenskaper än upplösning och bit hastighet.

## <a name="the-content-aware-encoding"></a>Innehålls medveten kodning 

Den innehålls medveten kodnings inställningen utökar mekanismen "anpassningsbar bit hastighet" genom att inkludera anpassad logik som gör det möjligt för kodaren att söka efter optimalt bit hastighets värde för en specifik lösning, men utan att kräva omfattande beräknings analys. Den här för inställningen ger en uppsättning GOP hastigheter. Med allt indata-innehåll utför tjänsten en inledande förenklad analys av indata-innehållet och använder resultatet för att fastställa det optimala antalet lager, lämpliga bit hastighets-och upplösnings inställningar för leverans genom anpassad direkt uppspelning. Den här för inställningen är särskilt effektiv för små och medel stora komplexitets videoklipp, där utdatafilerna får lägre bit hastigheter än för hands versionen av den anpassningsbara strömningen, men i en kvalitet som fortfarande ger en bra upplevelse till tittare. Utdata kommer att innehålla MP4-filer med video och ljud som är överlagrat

I följande exempel diagram visas jämförelsen med kvalitets mått som [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) och [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Källan skapades genom sammanfogning av korta klipp med höga komplexitets dum par från filmer och TV-program som är avsedda att stressa kodaren. Enligt definition ger den här inställningen resultat som varierar från innehåll till innehåll – det innebär också att för visst innehåll, kan det hända att det inte finns någon betydande minskning av bit hastigheten eller förbättring av kvaliteten.

![Hastighets förvrängnings kurva (RD) med PSNR](media/content-aware-encoding/msrv1.png)

**Bild 1: hastighets förvrängning (RD) kurva med PSNR mått för hög komplexitets källa**

![Hastighets förvrängnings kurva (RD) med VMAF](media/content-aware-encoding/msrv2.png)

**Bild 2: hastighets förvrängning (RD) kurva med VMAF mått för hög komplexitets källa**

Nedan visas resultatet för en annan kategori av käll innehåll, där kodaren kunde fastställa att indatamängden hade låg kvalitet (många komprimerings artefakter på grund av den låga bit hastigheten). Observera att med den innehålls medvetna för hands inställningen, beslutade kodaren att endast skapa ett utmatnings lager – till en låg bit hastighet, så att de flesta klienter kan spela upp strömmen utan att stoppas.

![RD-kurva med PSNR](media/content-aware-encoding/msrv3.png)

**Bild 3: RD-kurva som använder PSNR för låg kvalitets ineffekt (på 1080p)**

![RD-kurva med VMAF](media/content-aware-encoding/msrv4.png)

**Bild 4: RD-kurva som använder VMAF för låg kvalitet (på 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Så här använder du den innehålls medveten kodnings inställningen 

Du kan skapa transformeringar som använder den här för inställningen enligt följande. 

I avsnittet [Nästa steg](#next-steps) hittar du självstudier som använder omvandla-utdata. Utmatnings till gången kan levereras från Media Services slut punkter för direkt uppspelning i protokoll som MPEG-streck och HLS (se självstudierna).

> [!NOTE]
> Se till att använda **ContentAwareEncoding** -förinställning som inte är ContentAwareEncodingExperimental.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Kodning av jobb med för `ContentAwareEncoding` inställningarna faktureras baserat på utdata minuter. 
  
## <a name="next-steps"></a>Nästa steg

* [Självstudie: Ladda upp, koda och strömma videor med Media Services v3](stream-files-tutorial-with-api.md)
* [Självstudier: Koda en fjärrfil baserat på URL och strömma videon – REST](stream-files-tutorial-with-rest.md)
* [Självstudie: koda en fjärrfil baserat på URL och strömma video-CLI](stream-files-cli-quickstart.md)
* [Självstudie: koda en fjärrfil baserat på URL och strömma videon – .NET](stream-files-dotnet-quickstart.md)
* [Självstudie: koda en fjärrfil baserat på URL och strömma video-Node.js](stream-files-nodejs-quickstart.md)
