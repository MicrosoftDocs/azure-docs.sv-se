---
title: Hämta Azure Kinect IMU-exempel
description: Lär dig hur du hämtar Azure Kinect IMU-exempel med hjälp av Azure Kinect SDK.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: Kinect, Azure, konfigurera, djup, färg, RBG, kamera, sensor, SDK, IMU, rörelse sensor, rörelse, Gyroscope, gyro, accelerometer, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277497"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Hämta Azure Kinect IMU-exempel

Azure Kinect-enheten ger till gång till inerta rörelse enheter (IMUs), inklusive både accelerometer-och Gyroscope-typerna. För att få åtkomst till IMUs-exempel måste du först öppna och konfigurera enheten och sedan avbilda IMU-data. Mer information finns i [hitta och öppna enhet](find-then-open-device.md).

IMU-exempel genereras med en mycket högre frekvens än bilder. Exempel rapporteras till värden med lägre taxa än vad som samplas. När du väntar på ett IMU-exempel blir flera prover ofta tillgängliga samtidigt.

Se [maskin varu specifikationen](hardware-specification.md) för Azure Kinect DK för mer information om rapporterings takten för IMU.

## <a name="configure-and-start-cameras"></a>Konfigurera och starta kameror

> [!NOTE]
> IMU-sensorer kan bara fungera när färg-och/eller djupets kameror körs. IMU-sensorer får inte fungera ensamt.

Starta kamerorna med [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a).

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Åtkomst till IMU-exempel

 Varje [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) innehåller en accelerometer-och Gyroscope-läsning som samlas in på nästan samma tid.

Du kan hämta IMU-exemplen antingen på samma tråd som du hämtar avbildningar eller på separata trådar.

Om du vill hämta IMU-exempel så fort de är tillgängliga kanske du vill anropa [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) i en egen tråd. API: et har också tillräckligt med intern kö för att du bara ska kunna söka efter exempel efter att varje bild har hämtats.

Eftersom det finns en intern kö av IMU-exempel kan du använda följande mönster utan att släppa några data:

1. Vänta på en avbildning, med valfri bild Rute frekvens.
2. Bearbeta avbildningen.
3. Hämta alla IMU-exempel i kö.
4. Upprepa väntar på nästa avbildning.

Om du vill hämta alla IMU-exempel som är köade kan du anropa [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) med `timeout_in_ms` 0 i en slinga tills funktionen returnerar `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` indikerar att det inte finns några köade exempel och inget har anlänt inom den angivna tids gränsen.

## <a name="usage-example"></a>Exempel på användning

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du arbetar med IMU-exempel kan du också
>[!div class="nextstepaction"]
>[Få åtkomst till mikrofonens indata](access-mics.md)
