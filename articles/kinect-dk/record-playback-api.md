---
title: API för Azure Kinect-uppspelning
description: 'Lär dig hur du använder Azure Kinect sensor SDK för att öppna en inspelnings fil med hjälp av uppspelnings-API: et.'
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, djup, RGB, inspelning, uppspelning, Matroska, MKV
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277467"
---
# <a name="the-azure-kinect-playback-api"></a>API för Azure Kinect-uppspelning

Sensor-SDK: n tillhandahåller ett API för att registrera enhets data till en Matroska-fil (. mkv). Matroska container-formatet lagrar video spår, IMU-exempel och enhets kalibrering. Inspelningar kan genereras med hjälp av det tillhandahållna kommando rads verktyget [k4arecorder](record-sensor-streams-file.md) . Inspelningar kan också anpassas och registreras direkt med post-API: et.

Mer information om inspelnings-API: t finns i [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Mer information om specifikationerna för Matroska-filformat finns på sidan för [inspelning av fil format](record-file-format.md) .

## <a name="use-the-playback-api"></a>Använd uppspelnings-API: et

Inspelning av filer kan öppnas med hjälp av uppspelnings-API. Uppspelnings-API: t ger åtkomst till sensor data i samma format som resten av sensor-SDK: n.

### <a name="open-a-record-file"></a>Öppna en post fil

I följande exempel öppnar vi en inspelning med [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) , skriver ut inspelnings längden och stänger sedan filen med [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Läs avbildningar

När filen är öppen kan vi börja läsa insamlingar från inspelningen. I nästa exempel kommer alla avbildningar i filen att läsas.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Sök i en inspelning

När vi har nått slutet av filen kanske vi vill gå tillbaka och läsa den igen. Den här processen kan göras genom att läsa baklänges med [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , men den kan vara mycket långsam beroende på inspelningens längd.
I stället kan vi använda [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) funktionen för att gå till en viss punkt i filen.

I det här exemplet anger vi tidsstämplar i mikrosekunder för att söka efter olika punkter i filen.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Läs märkes information

Inspelningar kan också innehålla olika metadata, till exempel enhetens serie nummer och versioner av inbyggd program vara. Dessa metadata lagras i inspelnings koder som kan nås med hjälp av [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) funktionen.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Post märkes lista

Nedan visas en lista över alla standardtaggar som kan ingå i en inspelnings fil. Många av dessa värden är tillgängliga som en del av [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struct och kan läsas med [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) funktionen.

Om det inte finns någon tagg antas det att ha standardvärdet.

| Taggnamn                     | Standardvärde      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Sidfält | Kommentarer     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | Rund              | `color_format` / `color_resolution`  | Möjliga värden: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" och så vidare                                      |
| `K4A_DEPTH_MODE`             | Rund              | `depth_mode` / `depth_track_enabled` | Möjliga värden: "OFF," NFOV_UNBINNED "," PASSIVE_IR "och så vidare                                                |
| `K4A_IR_MODE`                | Rund              | `depth_mode` / `ir_track_enabled`    | Möjliga värden: "OFF", "ACTIVE", "passiv"                                                                    |
| `K4A_IMU_MODE`               | Rund              | `imu_track_enabled`                  | Möjliga värden: "på", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.jspå" | Ej tillämpligt                                  | Kontakta [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | 0,0                | `depth_delay_off_color_usec`         | Värdet lagras i nanosekunder, API innehåller mikrosekunder.                                                        |
| `K4A_WIRED_SYNC_MODE`        | INSTALLATIONS       | `wired_sync_mode`                    | Möjliga värden: "fristående", "MASTER", "underordnad"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | 0,0                | `subordinate_delay_off_master_usec`  | Värdet lagras i nanosekunder, API innehåller mikrosekunder.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | Ej tillämpligt                                  | Version för inbyggd enhets färg, till exempel "1. x. xx"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | Ej tillämpligt                                  | Version för enhets djupets inbyggd program vara, till exempel "1. x. xx"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | Ej tillämpligt                                  | Registrerar enhetens serie nummer                                                                                 |
| `K4A_START_OFFSET_NS`        | 0,0                | `start_timestamp_offset_usec`        | Se [synkronisering av tidsstämpel](record-playback-api.md#timestamp-synchronization) nedan.                       |
| `K4A_COLOR_TRACK`            | Inget               | Ej tillämpligt                                  | Se [spela in fil format – identifiera spår](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | Inget               | Ej tillämpligt                                  | Se [spela in fil format – identifiera spår](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | Inget               | Ej tillämpligt                                  | Se [spela in fil format – identifiera spår](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | Inget               | Ej tillämpligt                                  | Se [spela in fil format – identifiera spår](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Tidsstämpel-synkronisering

Matroska-formatet kräver att inspelningar måste börja med en tidsstämpel på noll. När [kameror synkroniseras externt](record-external-synchronized-units.md)kan den första tidsstämpeln från varje enhet vara icke-noll.

För att bevara de ursprungliga tidsstämplar från enheterna mellan inspelning och uppspelning, lagrar filen en förskjutning som gäller för tidsstämplar.

`K4A_START_OFFSET_NS`Taggen används för att ange en förskjutning för tidstämpel så att filer kan synkroniseras om efter registreringen. Den här Tidsstämpelns förskjutning kan läggas till i varje tidstämpel i filen för att rekonstruera de ursprungliga tidsstämplar för enheten.

Start förskjutningen är också tillgänglig i [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struct.
