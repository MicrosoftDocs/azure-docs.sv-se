---
title: Media Encoder Standard schema | Microsoft Docs
description: I den här artikeln beskrivs några av elementen och typerna av XML-schemat som Media Encoder Standard för inställningar baseras på.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015666"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard-schema

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

I den här artikeln beskrivs några av elementen och typerna av XML-schemat som [Media Encoder Standard för inställningar](media-services-mes-presets-overview.md) baseras på. Artikeln innehåller förklaringar av element och deras giltiga värden.  

## <a name="preset-root-element"></a><a name="Preset"></a> Förval (rot element)
Definierar en kodning för förval.  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Kodning** |[Kodning](media-services-mes-schema.md#Encoding) |Rot element anger att inmatade källor ska kodas. |
| **Utdata** |[Utdata](media-services-mes-schema.md#Output) |Samling av önskade utdatafiler. |
| **StretchMode**<br/>minOccurs = "0"<br/>standard = "AutoSize|XS: String|Kontrol lera bild storlek, utfyllnad, pixel eller bild-breddförhållandet i utdata. **StretchMode** kan vara något av följande värden: **none**, **AutoSize** (default) eller **Autopassa**.<br/><br/>**Ingen**: strikt följa upplösningen för utdata (till exempel **Bredd** och **höjd** i förvalet) utan att behöva beakta pixelproportioner eller Visa bild förhållande för indata-videon. Rekommenderas i scenarier som [beskärning](media-services-crop-video.md), där utmatnings videon har olika proportioner jämfört med indata. <br/><br/>**AutoSize**: utdata-upplösningen får plats i fönstret (bredd * höjd) som anges av förval. Kodaren skapar dock en videoutgång som har fyrkant (1:1) pixelproportioner. Därför kan utgångs bredden eller utmatnings höjden åsidosättas för att matcha visnings proportionerna för indata, utan utfyllnad. Om indata till exempel är 1920x1080 och kodnings för inställningen ställer 1280x1280, åsidosätts värdet height i förvalet och utdata kommer att finnas på 1280x720, vilket bibehåller förhållandet mellan indata och 16:9. <br/><br/>**Autopassa**: Ställ in video filmen (med antingen Letterbox eller pelare) för att respektera önskad utdata-upplösning, och se till att det aktiva Video området i utdata har samma proportioner som indata. Anta till exempel att indatamängden är 1920x1080 och kodnings för inställningen frågar efter 1280x1280. Sedan kommer utdataporten att finnas på 1280x1280, men den innehåller en inre 1280x720-rektangel med "aktiv video" med proportionerna 16:9 och Letterbox regionerna 280 pixlar högt överst och längst ned. För ett annat exempel, om indata är 1440x1080 och kodnings för inställningen frågar efter 1280x720, kommer utdata att finnas på 1280x720, som innehåller en inre rektangel med 960x720 vid proportionerna 4:3 och pelare Box-regioner 160 bild punkter brett till vänster och höger. 

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Version**<br/><br/> Obligatorisk |**XS: decimal** |För inställnings versionen. Följande begränsningar gäller: XS: fractionDigits Value = "1" och XS: minInclusive Value = "1", t. ex. **version = "1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a> Inställning
Innehåller en sekvens med följande element:  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Inställningar för H. 264-kodning för video. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Inställningar för AAC-kodning av ljud. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Inställningar för BMP-bild. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Inställningar för PNG-bild. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Inställningar för JPG-bild. |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**XS: Boolean** |För närvarande stöds endast en-pass-kodning. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **standard = "00:00:02"** |**XS: Time** |Anger det fasta avståndet mellan IDR-ramar i antal sekunder. Kallas även GOP varaktighet. Se **SceneChangeDetection** för att kontrol lera om kodaren kan avvika från det här värdet. |
| **SceneChangeDetection**<br/><br/> minOccurs = "0"<br/><br/> standard = "falskt" |**XS: Boolean** |Om värdet är true försöker kodaren identifiera scen ändringar i videon och infogar en IDR-ram. |
| **Tillräckligt**<br/><br/> minOccurs = "0"<br/><br/> standard = "bal anse rad" |**XS: String** |Styr kompromissen mellan kodad hastighet och video kvalitet. Kan vara något av följande värden: **hastighet**, **bal anse rad** eller **kvalitet**<br/><br/> Standard: **balanserade** |
| **SyncMode**<br/><br/> minOccurs = "0" | |Funktionen kommer att visas i en framtida version. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Samling av utdata för video lager. |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Condition** |**XS: String** | Om inmatningen inte har någon video kanske du vill tvinga kodare att infoga ett monokromt video spår. Det gör du genom att använda Condition = "InsertBlackIfNoVideoBottomLayerOnly" (om du vill infoga en video med enbart den lägsta bit hastigheten) eller Condition = "InsertBlackIfNoVideo" (om du vill infoga en video i alla bit hastigheter för utdata). Mer information finns i [den här](media-services-advanced-encoding-with-mes.md#no_video) artikeln.|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Om du skickar ett indata till kodaren som bara innehåller ljud, och ingen video, innehåller utdata-till gången bara filer med ljud data. Vissa spelare kanske inte kan hantera sådana utgående strömmar. Du kan använda H264Video's **InsertBlackIfNoVideo** för att tvinga kodare att lägga till ett video spår till utdata i det scenariot. Mer information finns i [den här](media-services-advanced-encoding-with-mes.md#no_video) artikeln.
              
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "obunden" |[H264Layer](media-services-mes-schema.md#H264Layer) |En samling av H264,-lager. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Video gränserna baseras på de värden som beskrivs i tabellen [H264, levels](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) .  
> 
> 

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> standard = "Auto" |**XS: String** |Kan vara något av följande **XS: String** -värden: **Auto**, **baseline**, **main**, **High**. |
| **Nivå**<br/><br/> minOccurs = "0"<br/><br/> standard = "Auto" |**XS: String** | |
| **Hastigheten**<br/><br/> minOccurs = "0" |**XS: int** |Bit hastigheten som används för det här video lagret, anges i kbps. |
| **MaxBitrate**<br/><br/> minOccurs = "0" |**XS: int** |Den högsta bit hastighet som används för det här video skiktet som anges i kbps. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> standard = "00:00:05" |**XS: Time** |Video buffertens längd. |
| **Bredd**<br/><br/> minOccurs = "0" |**XS: int** |Bredden på videons utmatnings bild, i bild punkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredd och höjd måste vara jämna tal. |
| **Höjd**<br/><br/> minOccurs = "0" |**XS: int** |Höjden på videons utmatnings bild, i bild punkter.<br/><br/> För närvarande måste du ange både bredd och höjd. Bredd och höjd måste vara jämna tal.|
| **BFrames**<br/><br/> minOccurs = "0" |**XS: int** |Antal B-ramar mellan referens ramar. |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> standard = "3" |**XS: int** |Antal referens ramar i en GOP. |
| **EntropyMode**<br/><br/> minOccurs = "0"<br/><br/> standard = "CABAC" |**XS: String** |Kan vara något av följande värden: **CABAC** och **CAVLC**. |
| **Ram**<br/><br/> minOccurs = "0" |rationellt tal |Anger bild frekvensen för videoutdata. Använd standardvärdet "0/1" för att tillåta kodare att använda samma bild frekvens som Indataporten. Tillåtna värden förväntas vara vanliga bild Rute hastigheter. Alla giltiga rationella tillstånd är dock tillåtna. 1/1 är till exempel 1 fps och är giltig.<br/><br/> – 12/1 (12 fps)<br/><br/> – 15/1 (15 fps)<br/><br/> – 24/1 (24 fps)<br/><br/> – 24000/1001 (23,976 fps)<br/><br/> – 25/1 (25 fps)<br/><br/>  – 30/1 (30 fps)<br/><br/> – 30000/1001 (29,97 fps) <br/> <br/>**Obs!** Om du skapar en anpassad för inställning för kodning med flera bit hastigheter **måste** alla lager i för inställningen använda samma värde för RAM hastighet.|
| **AdaptiveBFrame**<br/><br/> minOccurs = "0" |**XS: Boolean** |Kopiera från Azure Media Encoder |
| **Segment**<br/><br/> minOccurs = "0"<br/><br/> standard = "0" |**XS: int** |Anger hur många sektorer en ram är indelad i. Rekommendera att använda standard. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 Innehåller en sekvens med följande element och grupper.  

 Mer information om AAC finns i [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> standard = "AACLC" |**XS: String** |Kan vara något av följande värden: **AACLC**, **HEAACV1** eller **HEAACV2**. |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Condition** |**XS: String** |Om du vill tvinga kodare att skapa en till gång som innehåller ett tyst ljud spår när indata inte har något ljud, anger du värdet "InsertSilenceIfNoAudio".<br/><br/> Om du skickar ett indata till kodare som bara innehåller video, och inget ljud, innehåller utdata-till gången filer som bara innehåller video data. Vissa spelare kanske inte kan hantera sådana utgående strömmar. Du kan använda den här inställningen för att tvinga kodare att lägga till ett tyst ljud spår till utdata i det scenariot. |

### <a name="groups"></a>Grupper

| Referens | Beskrivning |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Se beskrivningen av [AudioGroup](media-services-mes-schema.md#AudioGroup) för att veta hur många kanaler, samplings frekvens och bit hastighet som kan ställas in för varje profil. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> AudioGroup
Mer information om vilka värden som är giltiga för varje profil finns i tabellen "ljud-codec-information" nedan.  

### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Kanaler**<br/><br/> minOccurs = "0" |**XS: int** |Antalet ljud kanaler som har kodats. Följande är giltiga alternativ: 1, 2, 5, 6, 8.<br/><br/> Standard: 2. |
| **SamplingRate**<br/><br/> minOccurs = "0" |**XS: int** |Samplings frekvensen för ljud som anges i Hz. |
| **Hastigheten**<br/><br/> minOccurs = "0" |**XS: int** |Den bit hastighet som används när ljudet kodas, anges i kbit/s. |

### <a name="audio-codec-details"></a>Codec-information för ljud

Ljud-codec|Information  
-----------------|---  
**AACLC** |1:<br/><br/> -11025:8 &lt; = bit hastighet &lt; 16<br/><br/> -12000:8 &lt; = bit hastighet &lt; 16<br/><br/> -16000:8 &lt; = bit hastighet &lt; 32<br/><br/>-22050:24 &lt; = bit hastighet &lt; 32<br/><br/> -24000:24 &lt; = bit hastighet &lt; 32<br/><br/> -32000:32 &lt; = bit hastighet &lt; = 192<br/><br/> -44100:56 &lt; = bit hastighet &lt; = 288<br/><br/> -48000:56 &lt; = bit hastighet &lt; = 288<br/><br/> -88200:128 &lt; = bit hastighet &lt; = 288<br/><br/> -96000:128 &lt; = bit hastighet &lt; = 288<br/><br/> 2:<br/><br/> -11025:16 &lt; = bit hastighet &lt; 24<br/><br/> -12000:16 &lt; = bit hastighet &lt; 24<br/><br/> -16000:16 &lt; = bit hastighet &lt; 40<br/><br/> -22050:32 &lt; = bit hastighet &lt; 40<br/><br/> -24000:32 &lt; = bit hastighet &lt; 40<br/><br/> -32000:40 &lt; = bit hastighet &lt; = 384<br/><br/> -44100:96 &lt; = bit hastighet &lt; = 576<br/><br/> -48000:96 &lt; = bit hastighet &lt; = 576<br/><br/> -88200:256 &lt; = bit hastighet &lt; = 576<br/><br/> -96000:256 &lt; = bit hastighet &lt; = 576<br/><br/> 5/6:<br/><br/> -32000:160 &lt; = bit hastighet &lt; = 896<br/><br/> -44100:240 &lt; = bit hastighet &lt; = 1024<br/><br/> -48000:240 &lt; = bit hastighet &lt; = 1024<br/><br/> -88200:640 &lt; = bit hastighet &lt; = 1024<br/><br/> -96000:640 &lt; = bit hastighet &lt; = 1024<br/><br/> 8:<br/><br/> -32000:224 &lt; = bit hastighet &lt; = 1024<br/><br/> -44100:384 &lt; = bit hastighet &lt; = 1024<br/><br/> -48000:384 &lt; = bit hastighet &lt; = 1024<br/><br/> -88200:896 &lt; = bit hastighet &lt; = 1024<br/><br/> -96000:896 &lt; = bit hastighet &lt; = 1024  
**HEAACV1** |1:<br/><br/> -22050: bit hastighet = 8<br/><br/> -24000:8 &lt; = bit hastighet &lt; = 10<br/><br/> -32000:12 &lt; = bit hastighet &lt; = 64<br/><br/> -44100:20 &lt; = bit hastighet &lt; = 64<br/><br/> -48000:20 &lt; = bit hastighet &lt; = 64<br/><br/> -88200: bit hastighet = 64<br/><br/> 2:<br/><br/> -32000:16 &lt; = bit hastighet &lt; = 128<br/><br/> -44100:16 &lt; = bit hastighet &lt; = 128<br/><br/> -48000:16 &lt; = bit hastighet &lt; = 128<br/><br/> -88200:96 &lt; = bit hastighet &lt; = 128<br/><br/> -96000:96 &lt; = bit hastighet &lt; = 128<br/><br/> 5/6:<br/><br/> -32000:64 &lt; = bit hastighet &lt; = 320<br/><br/> -44100:64 &lt; = bit hastighet &lt; = 320<br/><br/> -48000:64 &lt; = bit hastighet &lt; = 320<br/><br/> -88200:256 &lt; = bit hastighet &lt; = 320<br/><br/> -96000:256 &lt; = bit hastighet &lt; = 320<br/><br/> 8:<br/><br/> -32000:96 &lt; = bit hastighet &lt; = 448<br/><br/> -44100:96 &lt; = bit hastighet &lt; = 448<br/><br/> -48000:96 &lt; = bit hastighet &lt; = 448<br/><br/> -88200:384 &lt; = bit hastighet &lt; = 448<br/><br/> -96000:384 &lt; = bit hastighet &lt; = 448  
**HEAACV2** |2:<br/><br/> -22050:8 &lt; = bit hastighet &lt; = 10<br/><br/> -24000:8 &lt; = bit hastighet &lt; = 10<br/><br/> -32000:12 &lt; = bit hastighet &lt; = 64<br/><br/> -44100:20 &lt; = bit hastighet &lt; = 64<br/><br/> -48000:20 &lt; = bit hastighet &lt; = 64<br/><br/> -88200:64 &lt; = bit hastighet &lt; = 64  
  
## <a name="clip"></a><a name="Clip"></a> Bilden
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **/St** |**XS: duration** |Anger start tiden för en presentation. Värdet för StartTime måste matcha de absoluta tidsstämplarna för Indataporten. Om den första bild rutan i Indataporten till exempel har en tidsstämpel på 12:00:10 000, ska StartTime vara minst 12:00:10 000 eller större. |
| **Varaktighet** |**XS: duration** |Anger varaktigheten för en presentation (till exempel utseende på ett överlägg i videon). |

## <a name="output"></a><a name="Output"></a> Utdataparametrar
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Sökväg** |**XS: String** |Namnet på utdatafilen.<br/><br/> Du kan använda makron som beskrivs i följande tabell för att bygga namnen på utdatafilerna. Exempel:<br/><br/> **"Utdata": [{"FileName": "{basename}*{resolution}*{bit bit}. mp4", "format": {"typ": "MP4Format"}}]** |

### <a name="macros"></a>Öppnas

| Makro | Beskrivning |
| --- | --- |
| **{Basename}** |Om du gör VoD-kodningen är {basename} de första 32 tecknen i egenskapen AssetFile.Name för den primära filen i indata-till gången.<br/><br/> Om inmatad till gång är ett Live-Arkiv härleds {basename} från trackName-attributen i Server manifestet. Om du skickar ett under klipp med hjälp av TopBitrate, som i: "<VideoStream \> TopBitrate</videostream \> " och utdatafilen innehåller video, är {basename} de första 32 tecknen i trackName för video lagret med den högsta bit hastigheten.<br/><br/> Om du i stället skickar ett under klipps jobb med alla bit hastigheter, till exempel "<VideoStream \> * </videostream \> " och utdatafilen innehåller video, är {basename} de första 32 tecknen i trackName för motsvarande video lager. |
| **ADPCM** |Mappar till "H264," för video och "AAC" för ljud. |
| **Hastigheten** |Video bit hastigheten om utdatafilen innehåller video och ljud, eller för att ge ljud bit hastighet om utdatafilen innehåller endast ljud. Värdet som används är bit hastigheten i kbit/s. |
| **Kanalig** |Antalet ljud kanaler om filen innehåller ljud. |
| **LED** |Bredden på videon i bild punkter i utdatafilen om filen innehåller video. |
| **Våghöjd** |Höjden på videon i bild punkter i utdatafilen om filen innehåller video. |
| **Utöka** |Ärver från egenskapen "Type" för utdatafilen. Namnet på utdatafilen har ett fil namns tillägg som är en av: "MP4", "TS", "jpg", "png" eller "bmp". |
| **Tabbindex** |Obligatoriskt för miniatyr. Bör bara finnas en gång. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Video (komplex typ ärver från codec)
### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Start** |**XS: String** | |
| **Steg** |**XS: String** | |
| **Intervall** |**XS: String** | |
| **PreserveResolutionAfterRotation** |**XS: Boolean** |Detaljerad förklaring finns i följande avsnitt: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Vi rekommenderar att du använder **PreserveResolutionAfterRotation** -flaggan i kombination med lösnings värden som uttrycks i procent villkor (bredd = "100%", height = "100%").  

Som standard är kodnings inställningarna (bredd, höjd) i för inställningarna för Media Encoder Standard (rikta) till videor med 0 grad rotation. Om din indata-video till exempel är 1280x720 med noll GRADS rotation, ser du till att utdata har samma upplösning genom att använda standard för inställningarna.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Om Indataporten har hämtats med en rotation som inte är noll (t. ex. en smartphone eller surfplatta hålls lodrätt), används inställningarna för kodnings upplösning (bredd, höjd) i inmatnings videon och sedan kompensera för rotationen. Se till exempel bilden som följer. För inställningen används width = "100%", height = "100%", som stämmer överens med att utdata måste vara 1280 bild punkter bred och 720 pixlar högt. När du har roterat videon förminskas bilden så att den passar in i fönstret, vilket leder till området med pelare-rutor till vänster och höger.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Du kan också använda flaggan **PreserveResolutionAfterRotation** och ange den som "true" (Standardvärdet är "false"). Så om din för inställning har bredd = "100%", height = "100%" och PreserveResolutionAfterRotation inställt på "true", en indata-video, som är 1280 bild punkter bred och 720 bild punkter högt med en rotation i 90 grader genererar utdata med noll GRADS rotation, men 720 bild punkter bred och 1280 bild punkter högt. Se följande bild:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (grupp)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Höjd**<br/><br/> minOccurs = "0" |**XS: int** | |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Condition** |**XS: String** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Höjd**<br/><br/> minOccurs = "0" |**XS: int** | |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Condition** |**XS: String** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Bredd**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Höjd**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Kvalitet**<br/><br/> minOccurs = "0" |**XS: int** |Giltiga värden: 1 (sämsta)-100 (bästa) |

### <a name="attributes"></a>Attribut

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Condition** |**XS: String** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "obunden" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "obunden" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "obunden" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (komplex typ ärver från video)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png-lager |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (komplex typ ärver från video)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png-lager |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (komplex typ ärver från video)
### <a name="elements"></a>Element

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png-lager |

## <a name="examples"></a>Exempel
Se exempel på XML-förval som är baserade på det här schemat, se [aktivitets för inställningar för aktiviteter (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

