---
title: Hämta data från Azure Kinect-avbildning
description: Lär dig hur du hämtar data från Azure Kinect-avbildningar med hjälp av Kinect-sensor SDK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Hämta, sensor, kamera, SDK, djup, RGB, bilder, färg, avbildning, upplösning, buffert
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "87538922"
---
# <a name="retrieve-azure-kinect-image-data"></a>Hämta data från Azure Kinect-avbildning

Den här sidan innehåller information om hur du hämtar bilder från Azure-Kinect. Artikeln visar hur du samlar in och använder bilder som koordineras mellan enhetens färg och djup. För att få åtkomst till avbildningar måste du först öppna och konfigurera enheten. sedan kan du skapa avbildningar.
Innan du konfigurerar och avbildar en avbildning måste du [hitta och öppna enheten](find-then-open-device.md).

Du kan också se exempel på [SDK-direktuppspelning](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) som visar hur du använder funktionerna i den här artikeln.

Följande funktioner beskrivs:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Konfigurera och starta enheten

De två kameror som är tillgängliga på din Kinect-enhet har stöd för flera lägen, lösningar och utdataformat. En fullständig lista finns i [maskin varu specifikationer](hardware-specification.md)för Azure Kinect Development Kit.

Konfigurationen av strömningen anges med värden i [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) strukturen.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

När kameror har startats fortsätter de att samla in data tills [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) anropas eller så stängs enheten.

## <a name="stabilization"></a>Stabiliserings

När du startar enheter med funktionen för synkronisering av flera enheter rekommenderar vi att du gör detta med hjälp av en fast exponerings inställning.
Med en manuell exponerings uppsättning kan det ta upp till åtta avbildningar från enheten innan avbildningar och RAM hastighet stabiliseras. Med automatisk exponering kan det ta upp till 20 avbildningar innan bilder och ram hastigheten stabiliseras.

## <a name="get-a-capture-from-the-device"></a>Hämta en avbildning från enheten

Avbildningar samlas in från enheten på ett korrelerat sätt. Varje avbildning som skapats innehåller en djup avbildning, en IR-avbildning, en färg bild eller en kombination av bilder.

Som standard returnerar API: t bara en avbildning när den har tagit emot alla begärda avbildningar för strömnings läget. Du kan konfigurera API: t att returnera partiella avbildningar med bara djup-eller färg bilder så fort de är tillgängliga genom att rensa [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) parametern för [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

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

När API: et har returnerat en avbildning måste du anropa [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) när du har slutfört användningen av objektet samla in.

## <a name="get-an-image-from-the-capture"></a>Hämta en bild från avbildningen

Om du vill hämta en avbildning, anropar du lämplig funktion för varje avbildnings typ. En av:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

Du måste anropa [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) alla [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) referenser som returneras av dessa funktioner när du är klar med avbildningen.

## <a name="access-image-buffers"></a>Åtkomst till avbildnings-buffertar

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) har många accessor-funktioner för att hämta egenskaperna för avbildningen.

Använd [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)för att komma åt avbildningens minnesbuffert.

I följande exempel visas hur du får åtkomst till en avbildning med en avbildning. Samma princip gäller för andra typer av avbildningar. Se dock till att ersätta bild typs variabeln med rätt bildtyp, till exempel IR eller färg.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du samlar in och koordinerar kamerornas bilder mellan färgen och djupet med hjälp av din Azure Kinect-enhet. Du kan också:

>[!div class="nextstepaction"]
>[Hämta IMU-exempel](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Åtkomst till mikrofoner](access-mics.md)
