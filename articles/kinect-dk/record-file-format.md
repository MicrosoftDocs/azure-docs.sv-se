---
title: Använd Azure Kinect sensor SDK för att spela in fil format
description: Förstå hur du använder det inspelade fil formatet i Azure Kinect sensor SDK.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: Kinect, Azure, sensor, SDK, djup, RGB, inspelning, uppspelning, Matroska, MKV
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654599"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Använd Azure Kinect sensor SDK för att spela in fil format

För att kunna registrera sensor data används container formatet Matroska (. mkv), vilket gör att flera spår kan lagras med en mängd olika codec. Inspelnings filen innehåller spår för att lagra färg, djup, IR-bilder och IMU.

Information på låg nivå om behållar formatet. MKV finns på Matroska- [webbplatsen](https://www.matroska.org/index.html).

| Spåra namn | Codec-format                          |
|------------|---------------------------------------|
| PÅ      | Mode-Dependent (MJPEG, för NV12 eller YUY2) |
| DJUPLODANDE      | b16g (16-bitars gråskala, big-endian)   |
| IR         | b16g (16-bitars gråskala, big-endian)   |
| IMU        | Anpassad struktur, se [IMU exempel struktur](record-file-format.md#imu-sample-structure) nedan. |

## <a name="using-third-party-tools"></a>Använda verktyg från tredje part

Verktyg som `ffmpeg` eller `mkvinfo` kommandot från [mkvtoolnix](https://mkvtoolnix.download/) Toolkit kan användas för att visa och extrahera information från inspelning av filer.

Följande kommando extraherar till exempel djup spåret som en sekvens av 16-bitars PNGs till samma mapp:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1`Parametern extraherar spår index 1, som för de flesta inspelningar är djup. Om inspelningen inte innehåller något färg spår `-map 0:0` används.

`-vsync 0`Parametern tvingar ffmpeg att extrahera ramar som-är i stället för att försöka matcha en hastighet på 30 fps, 15 fps eller 5 fps.

## <a name="imu-sample-structure"></a>IMU exempel struktur

Om IMU data extraheras från filen utan att använda uppspelnings-API: t, är data i binär form.
Strukturen för IMU-data finns nedan. Alla fält är lite-endian.

| Fält                        | Typ     |
|------------------------------|----------|
| Accelerometer tidsstämpel (μs) | UInt64   |
| Accelerometer-data (x, y, z) | flyttal [3] |
| Gyroscope tidsstämpel (μs)     | UInt64   |
| Gyroscope-data (x, y, z)     | flyttal [3] |

## <a name="identifying-tracks"></a>Identifiera spår

Det kan vara nödvändigt att identifiera vilket spår som innehåller färg, djup, IR och så vidare. Att identifiera spåren behövs när du arbetar med verktyg från tredje part för att läsa en Matroska-fil.
Spår siffror varierar beroende på kamera läge och uppsättning aktiverade spår. Taggar används för att identifiera betydelsen av varje spår.

Listan över Taggar nedan är de som är kopplade till ett särskilt Matroska-element och kan användas för att leta upp motsvarande spår eller bifogade filer.

Dessa taggar kan visas med verktyg som `ffmpeg` och `mkvinfo` .
Den fullständiga listan med taggar visas på sidan [post och uppspelning](record-playback-api.md) .

| Taggnamn             | Tagga mål             | Tagg värde             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Färg spår            | Matroska Track-UID    |
| K4A_DEPTH_TRACK      | Djup spår            | Matroska Track-UID    |
| K4A_IR_TRACK         | IR-spår               | Matroska Track-UID    |
| K4A_IMU_TRACK        | IMU-spår              | Matroska Track-UID    |
| K4A_CALIBRATION_FILE | Kalibrerings bilaga | Namn på bifogad fil   |

## <a name="next-steps"></a>Nästa steg

[Spela in och spela upp](record-playback-api.md)
