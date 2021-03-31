---
title: Få åtkomst till Azure Kinect DK-data i en innehålls ram
description: Lär dig mer om data i en textram och funktionerna för att komma åt dessa data i Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: brödtext, ram, Azure, Kinect, brödtext, spårning, tips
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277717"
---
# <a name="access-data-in-body-frame"></a>Komma åt data i innehållsramen

I den här artikeln beskrivs de data som finns i en textram och funktionerna för att komma åt dessa data.

Följande funktioner beskrivs:

- [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>Viktiga komponenter i en textram

Varje Body-ram innehåller en samling brödtext strukturer, en index karta för ett 2D-Body och den insamlade insamlaren som skapade det här resultatet.

![Body-Rams komponenter](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>Få åtkomst till samlingen av bröd strukturer

Flera kroppar kan identifieras i en enda avbildning. Du kan fråga antalet kroppar genom att anropa funktionen [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3) .

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

Du kan använda funktionerna [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) och [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb) för att iterera genom varje Body-index för att hitta bröd text-ID: t och information om den gemensamma placeringen/orienteringen.

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>Öppna rubrik kartan för brödtext

Du använder funktionen [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) för att få åtkomst till mappningen av Body-indexet. Se rubrik [kartan för brödtext](body-index-map.md) om du vill ha en detaljerad förklaring av Body index kartan. Glöm inte att släppa rubrik kartan för brödtext när den inte längre behövs.

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>Åtkomst till insamlad inspelning

Body-spåraren är ett asynkront API. Den ursprungliga insamlingen kanske redan har släppts när resultatet visas. Använd funktionen [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) för att skicka frågor till den insamlade insamlaren som används för att generera detta spår resultat. Referens antalet för k4a_capture_t ökas varje gång den här funktionen anropas. Använd [k4a_capture_release ()-](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) funktionen när insamlingen inte längre behövs.

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[SDK för kroppsspårning i Azure Kinect](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
