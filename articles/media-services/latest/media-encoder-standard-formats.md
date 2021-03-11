---
title: Standard kodare format och codec-Azure
description: Den här artikeln innehåller en lista över de vanligaste import-och export fil formaten som du kan använda med StandardEncoderPreset.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: f0cf89a755090010bb80507c52f90baa2a4e804a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617632"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardformat för kodare och codec

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln innehåller en lista över de vanligaste import-och export fil formaten som du kan använda med [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Information om hur du skapar anpassade för inställningar med hjälp av **StandardEncoderPreset** finns i [skapa en transformering med en anpassad för inställning](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Indatafil/fil format

| Filformat (filnamnstillägg) | Stöds |
| --- | --- |
| FLV (med H.264- och AAC-codec) (.flv) |Ja |
| MXF    (.mxf) |Ja |
| GXF    (.gxf) |Ja |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Ja |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Ja |
| AVI (okomprimerad 8-bitars/10-bitars) (.avi) |Ja |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Ja |
| [Microsoft Digital Video Recording(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Ja |
| Matroska/WebM (.mkv) |Ja |
| WAVE/WAV (.wav) |Ja |
| QuickTime (.mov) |Ja |

### <a name="audio-formats-in-input-containers"></a>Ljud format i indata-behållare

Standard-kodare stöder följande ljud format i indatafiler:

* MXF-, GXF-och QuickTime-filer, som har ljud spår med överlagrade stereo-eller 5,1-exempel

eller

* MXF-, GXF- och QuickTime-filer där ljudet är separata PCM-spår, men kanalmappningen (till stereo eller 5.1) kan härledas från filens metadata

## <a name="input-video-codecs"></a>Codec för inspelning av video
| Codec för inspelning av video | Stöds |
| --- | --- |
| AVC 8-/10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |
| Avid DNxHD (i MXF) |Ja |
| DVCPro/DVCProHD (i MXF) |Ja |
| Digital video (DV) (i AVI-filer) |Ja |
| JPEG 2000 |Ja |
| MPEG-2 (upp till 422-profil och hög nivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422-profil |
| MPEG-1 |Ja |
| VC-1/WMV9 |Ja |
| Canopus HQ/HQX |Inga |
| MPEG-4, del 2 |Ja |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |
| YUV420, okomprimerad eller mezzanin |Ja |
| Apple ProRes 422 |Ja |
| Apple ProRes 422 LT |Ja |
| Apple ProRes 422 HQ |Ja |
| Apple ProRes Proxy |Ja |
| Apple ProRes 4444 |Ja |
| Apple ProRes 4444 XQ |Ja |
| HEVC/H. 265| Huvud profil|

## <a name="input-audio-codecs"></a>Inmatade ljud-codec
| Codec för indataljud | Stöds |
| --- | --- |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |
| MPEG Layer 2 |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |
| Windows Media Audio |Ja |
| WAV/PCM |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ja |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |
| AMR (Adaptive Multi-Rate) |Ja |
| AES (SMPTE 331M och 302M, AES3-2003) |Inga |
| Dolby® E |Inga |
| Dolby® Digital (AC3) |Inga |
| Dolby® Digital Plus (E-AC3) |Inga |

## <a name="output-formats-and-codecs"></a>Utdataformat och codec
I följande tabell visas de codecenheter och fil format som stöds för export.

| Filformat | Video-codec | Ljud-codec |
| --- | --- | --- |
| MP4 <br/><br/>(inklusive MP4-behållare med flera bit hastigheter) |H. 264 (hög, Main och bas linje profiler), HEVC (H. 265) 8-bitar |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2 – TS |H. 264 (hög, huvud och bas linje profil) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Nästa steg

[Skapa en transformering med en anpassad för inställning](customize-encoder-presets-how-to.md)
