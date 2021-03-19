---
title: Använd avbildnings omvandlingar för Azure Kinect sensor SDK
description: Lär dig hur du använder omvandlings funktionerna i Azure Kinect sensor SDK-avbildning.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: Kinect, Azure, sensor, SDK, koordinera system, kalibrering, projekt, projekt, omvandling, RGB-d, punkt moln
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277460"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Använd avbildnings omvandlingar för Azure Kinect sensor SDK

Följ de olika funktionerna för att använda och transformera bilder mellan koordinerade kamera system i din Azure Kinect DK.

## <a name="k4a_transformation-functions"></a>k4a_transformation funktioner

 Alla funktioner som har prefixet *k4a_transformation* fungerar på hela avbildningar. De kräver att Transformations referensen [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) hämtas via [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) och inte är allokerad via [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44). Du kan också läsa SDK- [transformeringen](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) som visar hur du använder de tre funktionerna i det här avsnittet.

Följande funktioner beskrivs:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Översikt

 Funktionen [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) omvandlar djup kartan från djup kamerans synvinkel till färg kamerans synvinkel. Den här funktionen är utformad för att skapa så kallade RGB-D-bilder, där D representerar en ytterligare bild kanal som registrerar djupet. Som det visas i bilden nedan ser färg bilden och resultatet av [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) ut som om de togs från samma synvinkel, det vill säga färg kamerans synvinkel.

   ![Avbildnings omvandling](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementering

 Den här Transformations funktionen är mer komplex än att bara anropa [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) för varje bild punkt. Det tänjer ett triangel från djup kamerans geometri till geometrin för färg kameran. Triangelns nät används för att undvika att skapa hål i den transformerade djup bilden. En Z-buffert säkerställer att Occlusions hanteras korrekt. GPU-acceleration är aktiverat för den här funktionen som standard.

#### <a name="parameters"></a>Parametrar

 Indataparametrar är omvandlings handtaget och en djup bild. Bild upplösningen för djupet måste matcha den som ```depth_mode``` angetts vid skapandet av Transformations referensen. Om omvandlings handtaget exempelvis skapades med 1024x1024 ```K4A_DEPTH_MODE_WFOV_UNBINNED``` -läget måste upplösningen för djup bilden vara 1024x1024 bild punkter. Utdata är en transformerad djup avbildning som behöver allokeras av användaren via anrop av [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Upplösningen för den transformerade djup bilden måste matcha den ```color_resolution``` angivna vid skapandet av omvandlings referensen. Om färg matchningen till exempel var inställd på `K4A_COLOR_RESOLUTION_1080P` måste bildens upplösning vara 1920x1080 bild punkter. Utmatnings bilden Klive ställs in på `width * sizeof(uint16_t)` , som i bilden lagras 16-bitars djup värden.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Översikt

 Funktionen [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) omvandlar djup kartan och en anpassad bild från djup kamerans synvinkel till färg kamerans synvinkel. Som ett tillägg till [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)är den här funktionen utformad för att skapa en motsvarande anpassad avbildning för vilken varje pixel matchar motsvarande pixel koordinater för färg kameran utöver den transformerade djup bilden.

#### <a name="implementation"></a>Implementering

 Den här Transformations funktionen skapar den transformerade djup bilden på samma sätt som [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). För att kunna omvandla den anpassade bilden tillhandahåller den här funktionen alternativ för linjär interpolation eller närmaste grann-interpolation. Om du använder linjär interpolation kan du skapa nya värden i den transformerade anpassade avbildningen. Om du använder närmsta granne interpolation kan inte värden som inte finns i den ursprungliga bilden visas i utmatnings bilden, men det leder till mindre smidig bild. Den anpassade bilden måste vara en enskild kanal 8-eller 16-bitars. GPU-acceleration är aktiverat för den här funktionen som standard.

#### <a name="parameters"></a>Parametrar

 Indataparametrar är omvandlings handtaget, en djup bild, en anpassad bild och typen av interpolation. Djup bilden och den anpassade bild upplösningen måste överensstämma med den `depth_mode` angivna vid skapandet av omvandlings referensen. Om omvandlings handtaget exempelvis skapades med 1024x1024 `K4A_DEPTH_MODE_WFOV_UNBINNED` -läget måste upplösningen för djup bilden och den anpassade bilden vara 1024x1024 bild punkter. `interpolation_type`Ska vara antingen `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` eller `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . Utdata är en transformerad djup bild och en transformerad anpassad avbildning som behöver allokeras av användaren via anrop av [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Upplösningen för den transformerade djup bilden och omvandlad anpassad bild måste matcha den `color_resolution` angivna vid skapandet av omvandlings referensen. Om färg matchningen till exempel var inställd på `K4A_COLOR_RESOLUTION_1080P` måste bildens upplösning vara 1920x1080 bild punkter. Bild klivet för utdata är inställt på `width * sizeof(uint16_t)` , som bilden lagrar 16-bitars djup värden. Den anpassade indata-bilden och den transformerade anpassade bilden måste ha formatet `K4A_IMAGE_FORMAT_CUSTOM8` eller `K4A_IMAGE_FORMAT_CUSTOM16` så bör motsvarande bild kliv anges i enlighet med detta. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Översikt

 Funktionen [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) omvandlar färg bilden från färg kamerans synvinkel till djup kamerans synvinkel (se figuren ovan). Den kan användas för att generera RGB-D-bilder.

#### <a name="implementation"></a>Implementering

 För varje pixel i djup kartan använder funktionen värdet för pixel djupet för att beräkna motsvarande under pixel koordinat i färg bilden. Vi letar sedan upp färgvärdet i den här koordinaten i färg bilden. Bilinjära interpolation utförs i färg bilden för att få färg värden på under pixel precision. En bild punkt som inte har någon associerad djup läsning har tilldelats ett BGRA-värde `[0,0,0,0]` i utmatnings bilden. GPU-acceleration är aktiverat för den här funktionen som standard. Eftersom den här metoden ger hål i den transformerade färg bilden och inte hanterar Occlusions, rekommenderar vi att du använder funktionen [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) i stället.

#### <a name="parameters"></a>Parametrar

Indataparametrarna är omformnings handtaget, en djup bild och en färg bild. Upplösningarna för djup-och färg bilder måste överensstämma med depth_mode och color_resolution som anges när Transformations referensen skapas. Utdata är en transformerad färg bild som behöver allokeras av användaren via anrop av [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Upplösningen för den transformerade färg bilden måste matcha depth_resolution som anges vid skapandet av Transformations referensen. Utgående avbildningen lagrar 4 8-bitars värden som representerar BGRA för varje bild punkt. Därför är bildens kliv ```width * 4 * sizeof(uint8_t)``` . Data ordningen är pixel som är överlagrad, det vill säga blått värde – pixel 0, grönt värde – pixel 0, rött värde – pixel 0, alfa värde – pixel 0, blått värde – pixel 1 och så vidare.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Översikt

Funktionen [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) konverterar en 2D-djup karta som tas av en kamera till ett 3D-moln i koordinatsystemet i samma kamera. Kameran kan därmed vara djup-eller färg kamera.

#### <a name="implementation"></a>Implementering

 Funktionen ger likvärdiga resultat för att köra [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) för varje pixel, men är mer effektivt. När du anropar [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10)beräknar vi en så kallad XY-lookup-tabell som lagrar x-och y-Scale-faktorer för varje bild punkt. När du anropar [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)får vi en pixels 3D x-koordinat genom att multiplicera pixelns x-Scale-faktor med pixelns Z-koordinat. Analogously, 3D Y-koordinaten beräknas genom multiplikation med Y-Scale-faktorn. [Cloud-exemplet med snabb punkt](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) i SDK visar hur punkt tabellen beräknas. Användare kan följa exempel koden för att implementera sin egen version av den här funktionen, till exempel för att påskynda sin GPU-pipeline.

#### <a name="parameters"></a>Parametrar

 Indataparametrarna är omvandlings handtaget, en kamera specifikation och en djup bild. Om Camera-specificeraren är inställd på djup måste upplösningen för djup bilden matcha den depth_mode som anges vid skapandet av Transformations referensen. Annars, om specificeraren har angetts till färg kameran måste upplösningen matcha den valda color_resolutionens upplösning. Utdataparametern är en XYZ-avbildning som behöver allokeras av användaren via anrop av [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). Bild upplösningen XYZ måste matcha upplösningen för mappningen av indata-djupet. Vi lagrar tre signerade 16-bitars koordinater i millimeter för varje bild punkt. XYZ-bild klivet ställs därför in på `width * 3 * sizeof(int16_t)` . Data ordningen är pixel som är överlagrad, d.v.s. X-koordinat – pixel 0, Y-koordinat – pixel 0, Z-koordinat – pixel 0, X-koordinat – pixel 1 och så vidare. Om en pixel inte kan konverteras till 3D tilldelar funktionen värdena `[0,0,0]` till bild punkten.

## <a name="samples"></a>Exempel

[Transformerings exempel](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du använder omvandlings funktionerna i Azure Kinect sensor SDK, men du kan också lära dig mer om

>[!div class="nextstepaction"]
>[Kalibrerings funktioner för Azure Kinect sensor SDK](use-calibration-functions.md)

Du kan också granska

[Koordinatsystem](coordinate-systems.md)
