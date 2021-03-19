---
title: Leta upp och öppna Azure Kinect-enheten
description: Lär dig att hitta och öppna en Azure Kinect-enhet med hjälp av Azure Kinect senor SDK.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, djup, RGB, enhet, Sök, öppna
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277473"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Leta upp och öppna Azure Kinect-enheten

I den här artikeln beskrivs hur du kan hitta och sedan öppna din Azure Kinect DK. Artikeln beskriver hur du hanterar det fall där det finns flera enheter anslutna till datorn.

Du kan också läsa SDK- [uppräknings exemplet](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) som visar hur du använder funktionerna i den här artikeln.

Följande funktioner beskrivs:
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>Identifiera antalet anslutna enheter

Börja med att hämta antalet för tillfället anslutna Azure Kinect-enheter med [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>Öppna en enhet

Om du vill hämta information om en enhet eller läsa data från den måste du först öppna en referens till enheten med hjälp av [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) .

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

`index`Parametern för [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) anger vilken enhet som ska öppnas om det finns fler än en ansluten. Om du bara förväntar dig att en enda enhet ska ansluta kan du skicka ett argument till `K4A_DEVICE_DEFAULT` eller 0 för att ange den första enheten.

När som helst öppnar du en enhet som du behöver anropa [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) när du är klar med referensen. Det går inte att öppna andra referenser till samma enhet förrän du har stängt referensen.

## <a name="identify-a-specific-device"></a>Identifiera en speciell enhet

Beställnings enheterna räknas upp efter indexet inte förrän enheterna är anslutna eller frånkopplade. För att identifiera en fysisk enhet bör du använda enhetens serie nummer.

Om du vill läsa serie numret från enheten använder [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) du funktionen när du har öppnat en referens.

Det här exemplet visar hur du allokerar rätt mängd minne för att lagra serie numret.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>Öppna standardenheten

I de flesta program kommer det bara finnas en enda Azure Kinect DK som är kopplad till samma dator. Om du bara behöver ansluta till den enda förväntade enheten kan du anropa [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) med `index` för `K4A_DEVICE_DEFAULT` att öppna den första enheten.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
>[Hämta bilder](retrieve-images.md)

>[!div class="nextstepaction"]
>[Hämta IMU-exempel](retrieve-imu-samples.md)

