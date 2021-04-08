---
title: Azure Kinect får innehålls spårnings resultat
description: Lär dig hur du kan få text spårnings resultat med hjälp av Azure Kinect Body tracking SDK.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, brödtext, spårning, gemensamt
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277713"
---
# <a name="get-body-tracking-results"></a>Hämta kroppsspårningsresultat

I Body tracking SDK används ett Body-spår för att bearbeta Azure Kinect DK-insamlare och genererar innehålls spårnings resultat. Den behåller också den globala statusen för spåraren, bearbetar köer och kön utdata. Det finns tre steg i att använda Body-spåraren:

- Skapa en spårare
- Avbilda djup och IR-avbildningar med Azure Kinect DK
- Placera insamlingen och ta bort resultaten.

## <a name="create-a-tracker"></a>Skapa en spårare


Det första steget i att använda text spårning är att skapa en spårare och du måste överföra sensor kalibrerings [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) strukturen. Sensor kalibreringen kan frågas med hjälp av funktionen Azure Kinect sensor SDK [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) .

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Avbilda djup och IR-avbildningar

Bild avbildning med hjälp av Azure Kinect DK beskrivs på sidan [Hämta avbildningar](retrieve-images.md) .

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED` eller `K4A_DEPTH_MODE_WFOV_2X2BINNED` lägen rekommenderas för bästa prestanda och exakthet. Använd inte `K4A_DEPTH_MODE_OFF` `K4A_DEPTH_MODE_PASSIVE_IR` lägena eller.

De Azure Kinect DK-lägen som stöds beskrivs i [maskin varu specifikationen](hardware-specification.md) för Azure Kinect DK och [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) uppräkningar.

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Köa insamlingen och pop-resultaten

Spåraren hanterar internt en indatakö och en utgående kö till asynkron bearbetning av Azure Kinect DK-avbildningar mer effektivt. Använd funktionen [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) för att lägga till en ny avbildning i kön. Använd funktionen [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) o pop ett resultat från kön utdata. Användningen av timeout-värdet är beroende av programmet och styr vänte tiden för köer.

### <a name="real-time-processing"></a>Bearbetning i real tid
Använd det här mönstret för program med enkel tråd som kräver real tids resultat och som kan hantera avbrutna ramar. `simple_3d_viewer`Exemplet som finns i [GitHub Azure – Kinect-](https://github.com/microsoft/Azure-Kinect-Samples) exempel är ett exempel på real tids bearbetning.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Synkron bearbetning
Använd det här mönstret för program som inte behöver resultat i real tid eller som inte kan hantera ramar som släppts.

Bearbetning av data flödet kan vara begränsat.

`simple_sample.exe`Exemplet som finns i [GitHub Azure-Kinect-samples](https://github.com/microsoft/Azure-Kinect-Samples) är ett exempel på synkron bearbetning.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Komma åt data i innehållsramen](access-data-body-frame.md)
