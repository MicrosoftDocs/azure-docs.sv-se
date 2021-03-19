---
title: Azure Kinect DK-inspelare
description: Lär dig hur du registrerar data strömmar från sensorn SDK till en fil med hjälp av Azure Kinect-inspelaren.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, post, uppspelning, läsare, Matroska, MKV, strömmar, djup, RGB, kamera, färg, IMU, ljud
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277670"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK-inspelare

Den här artikeln beskriver hur du kan använda `k4arecorder` kommando rads verktyget för att registrera data strömmar från sensorn SDK till en fil.

>[!NOTE]
>Azure Kinect Recorder spelar inte in ljud.

## <a name="recorder-options"></a>Inspelnings alternativ

`k4arecorder`Innehåller olika kommando rads argument för att ange utdatafilen och inspelnings lägena.

Inspelningar lagras i [formatet Matroska. MKV](record-file-format.md). Inspelningen använder flera video spår för färg och djup och även ytterligare information, till exempel kamera kalibrering och metadata.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Registrera filer

Exempel 1. NFOV unbinned (640x576) för Record djupet, RGB 1080p vid 30 fps med IMU.
Tryck på **CTRL-C-** tangenterna för att stoppa inspelningen.

```
k4arecorder.exe output.mkv
```

Exempel 2. Registrera WFOV non-diskretiseras (1MP), RGB-3072p på 15 fps utan IMU, i 10 sekunder.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Exempel 3. Registrera WFOV 2x2 diskretiseras vid 30 fps i 5 sekunder och spara till output. MKV.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>Du kan använda [Azure Kinect Viewer](azure-kinect-viewer.md) för att konfigurera RGB-kamera kontroller innan du registrerar (t. ex. för att ange manuella vita saldon).

## <a name="verify-recording"></a>Verifiera inspelning

Du kan öppna filen output. mkv med [Azure Kinect Viewer](azure-kinect-viewer.md).

Om du vill extrahera spår eller Visa fil information, verktyg som `mkvinfo` är tillgängliga som en del av [mkvtoolnix](https://mkvtoolnix.download/) Toolkit.

## <a name="next-steps"></a>Nästa steg

[Använda inspelning med externa synkroniserade enheter](record-external-synchronized-units.md)